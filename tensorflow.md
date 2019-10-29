- 从3个不同的checkpoint来恢复对应3个不同的模型中的参数，记为(A, B, C)，这3个模型事先训练好
- 固定模型A，B，C中的参数
- 用模型A，B，C来构建模型D
- 模型D中会引入新的参数，在训练过程中，模型A，B，C中的参数不会更新
- 新建的模型D会将模型A，B，C中的参数以及对应name导入新的session中，故模型D的checkpoint中会保留模型A，B，C中的对应参数
- 一旦模型D训练好，用D保存的checkpoint可以同时恢复模型A，B，C中的对应的参数

```python
import sys 
import tensorflow as tf  
from utils import category_to_id

sys.path.append('./../')

from self_attention_jieba_32.classifier import Classifier as attention_classifier
from cnn_based_4.classifier import Classifier as cnn_classifier
from dnn_based_4.classifier import Classifier as dnn_classifier 
from hparams import hparams as hp
from utils import category_to_id

class Classifier():
    def __init__(self, sess, txt_sequences, txt_length, txt_tfidf, txt_sparse_idx, labels, mask, aids, restore_step, is_training=False, is_valid=False):
        self.sess = sess
        self.hp = hp
        self.txt_sequences = txt_sequences  
        self.txt_length = txt_length
        self.txt_tfidf = txt_tfidf
        self.txt_sparse_idx = txt_sparse_idx
        self.labels = labels
        self.mask = mask
        self.aids = aids
        self.is_training = is_training
        self.is_valid = is_valid
        self.is_dropout = True if is_training or is_valid else False
    
        with tf.variable_scope('cnn_classifier') as scope:
            self.cnn_classifier = cnn_classifier()# restore from checkpoint
            self.cnn_classifier.build(inputs=self.txt_sequences, inputs_length=self.txt_length, labels=self.labels, aids=self.aids, mask=self.mask, is_training=False, is_valid=False)
    
        if restore_step < 1:
            self.cnn_varlist = {v.op.name.replace('model/cnn_classifier', 'model', 1): v for v in tf.get_collection(tf.GraphKeys.VARIABLES, scope="model/cnn_classifier")}
            self.cnn_saver = tf.train.Saver(var_list = self.cnn_varlist)
            self.cnn_saver.restore(self.sess, self.hp.cnn_ckpt) #'/home/work/mazhen1/project/cnn_based_3/checkpoint/relu_0.005/model.ckpt-92100'
            print('succeed in loading cnn model')

        with tf.variable_scope('dnn_classifier') as scope:
            self.dnn_classifier = dnn_classifier()
            self.dnn_classifier.build(inputs_value=self.txt_tfidf, inputs_idx=self.txt_sparse_idx, labels=self.labels, aids=self.aids, is_training=False, is_valid=False)

        if restore_step < 1:
            self.dnn_varlist = {v.op.name.replace('model/dnn_classifier', 'model', 1): v for v in tf.get_collection(tf.GraphKeys.VARIABLES, scope="model/dnn_classifier")}
            self.dnn_saver = tf.train.Saver(var_list = self.dnn_varlist)
            self.dnn_saver.restore(self.sess, self.hp.dnn_ckpt) #'/home/work/mazhen1/project/dnn_based_3/checkpoint/relu_0.005/model.ckpt-900'
            print('succeed in loading dnn model')
    
        with tf.variable_scope('attention_classifier') as scope:
            self.attention_classifier = attention_classifier()
            self.attention_classifier.build(inputs=self.txt_sequences, inputs_length=self.txt_length, labels=self.labels, aids=self.aids, mask=self.mask, is_training=False, is_valid=False)
    
        if restore_step < 1:
            self.attention_varlist = {v.op.name.replace('model/attention_classifier', 'model', 1): v for v in tf.get_collection(tf.GraphKeys.VARIABLES, scope="model/attention_classifier")}
            self.attention_saver = tf.train.Saver(var_list = self.attention_varlist)
            self.attention_saver.restore(self.sess, self.hp.att_ckpt)#'/home/work/mazhen1/project/self_attention_jieba_31/checkpoint/relu_0.0005/model.ckpt-186200'
            print('succeed in loading attention model')

        self.dnn_outputs = self.dnn_classifier.outputs
        self.cnn_outputs = self.cnn_classifier.outputs
        self.attention_outputs = self.attention_classifier.outputs


    def inference(self):

        outputs = tf.concat([self.cnn_outputs, self.dnn_outputs, self.attention_outputs], axis=-1)
        with tf.variable_scope('models_mix_outputs') as scope:
            self.outputs = tf.layers.dropout(outputs, rate=self.hp.dropout_rate, training=self.is_dropout)
            self.logits = tf.layers.dense(inputs=outputs, units=len(category_to_id), activation=self.hp.activator)
            self.dist = tf.nn.softmax(self.logits)
            self.preds = tf.argmax(self.dist, axis=-1)


    def add_loss(self):
        with tf.variable_scope('mmodels_mix_loss') as scope:
            self.smooth_label = self.label_smoothing(self.labels)
            cross_entropy_loss = tf.nn.softmax_cross_entropy_with_logits(logits=self.logits,
                                                    labels=self.smooth_label)

            self.loss = tf.reduce_mean(cross_entropy_loss)

    def optimizer(self, global_step):
        self.add_loss()
        with tf.variable_scope('models_mix_optimizer') as scope:
            learning_rate = self.learning_rate_decay(self.hp.learning_rate, global_step)
            #all_vars = tf.get_collection(tf.GraphKeys.VARIABLES)
            all_vars = tf.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)
            self.trainable_vars = []
            for v in all_vars:
                if 'models_mix' in v.op.name:
                    self.trainable_vars.append(v)

            #print(self.trainable_vars)
            self.optim = tf.train.AdamOptimizer(learning_rate).minimize(loss=self.loss,
                                                                var_list = self.trainable_vars,
                                                                global_step=global_step)

    def get_accuracy(self):
        self.labels_id = tf.argmax(self.labels, axis=-1)
        correct_pred = tf.equal(self.labels_id, self.preds)
        self.acc = tf.reduce_mean(tf.cast(correct_pred, tf.float32))

    def learning_rate_decay(self, init_lr, global_step):
        return init_lr * self.hp.factor
        warmup_step = 2500
        step = tf.cast(global_step + 1, dtype=tf.float32)

        return init_lr * warmup_step ** 0.5 * tf.minimum(step * warmup_step ** (-1.5), step ** (-0.5))


    def label_smoothing(self, inputs, epsilon=0.1):
        '''Applies label smoothing. See https://arxiv.org/abs/1512.00567.

        Args:
          inputs: A 3d tensor with shape of [N, T, V], where V is the number of vocabulary.
          epsilon: Smoothing rate.

        '''
        K = inputs.get_shape().as_list()[-1] # number of channels
        return ((1-epsilon) * tf.cast(inputs, dtype=tf.float32)) + (epsilon / K)

```
