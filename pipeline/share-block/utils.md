## 共用 Python function / variable code

如果要共用 python code，MageAI 有提供 `utils` 資料夾。

例如我現在有一個 function: 

```python
def add_one(x):
    return x + 1
```

我可以將之儲存成 `utils/add_num.py`。

之後，在 data loader / exporter 之類的 python code 就可以 import 這個 function：

```python
from my_project.utils.add_num import add_one


def my_data_loader():
    # 前略
    result = add_one(5)
    print(result)  # 6

```

### import 解說

import 語法是 `from { MageAI 專案名稱 }.utils.{ 檔案名稱 } import { function名稱 }`。

這邊專案的名稱是 `my_project`，所以才會是 `from my_project.utils.add_num import add_one`。