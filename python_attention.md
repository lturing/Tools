- re.sub
```python
import re
log = "you may feel fine, but the muscle spasms  could start at any--"
res = re.sub(r'[-]{2, }', '...', log)
res == log # True
res = re.sub(r'[-]{2,}', '...', log)
res == log # False, res = "you may feel fine, but the muscle spasms  could start at any..."

```

- [defining python source code encodings](https://www.python.org/dev/peps/pep-0263/)
```python
# coding=<encoding name>
or 
# -*- coding: <encoding name> -*-
```
