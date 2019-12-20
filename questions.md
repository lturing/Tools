- keras fit 
```
记 x_train 为 训练数据的输入，y_train 为 label，epoch为2 
当训练完第一个epoch后，第二epoch的第一个step突然降很多

x_train = x_train * 2
y_train = y_train * 2 
epoch = 1
则这个loss不会突然降很多(step=len(x_train) // 2) 起)

可能原因是len(x_train) % batch_size != 0 ? 
```
