# 從 API 抓取資料

API 的處理方式與 [Oracle 11]一樣使用 Standard (batch) Pipeline 來處理。


![alt text](add-standard-batch-pipeline.png)

![](standard-batch-pipeline.png)

## 資料來源

抓取資料選擇 `Data Loader` > `Python` > `API`

![alt text](image-7.png)

然後根據需求撰寫 API 

以下是幾個API的範例

### 送 GET API

以下範例會從 `https://example.com/api/data` 抓取資料，而 API 回傳的資料為 JSON 格式。

```python,editable
import io  
import requests
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader
if 'test' not in globals():
    from mage_ai.data_preparation.decorators import test


@data_loader
def load_data_from_api(*args, **kwargs):
    """
    Template for loading data from API
    """
    

    url = "https://example.com/api/data"
    resp = requests.get(url)
    
    return resp.json()


@test
def test_output(output, *args) -> None:
    """
    Template code for testing the output of the block.
    """
    assert output is not None, 'The output is undefined'

```

### 送 POST API（JSON body）


以下範例會從 `https://example.com/api/data` 抓取資料，API 回傳的資料為 JSON 格式。

POST body 為 
```
{
	"param1": "value1",
	"param2": "value2"
}
```


```python,editable
# import 省略

@data_loader
def load_data_from_api(*args, **kwargs):
    url = "https://example.com/api/data"
    data = {
        "param1": "value1",
        "param2": "value2"
    }
    headers = { 
        "Content-type": "application/json",
    }
 
    resp = requests.post(url, headers=headers, json=data)
    
    return resp.json()


# @test 區塊省略
```

### 使用 OAuth 取 Token + 送 API

以下範例有兩個 function：`load_token()`用來進行 OAuth 的登入（取得 token）；`load_data_from_api(*args, **kwar)` 處理後續的資料抓取。

這邊使用了 Mage 的 Secrets 功能來存放 OAuth 所需的參數

Secrets 設定畫面於此：

![Secrets Setting](secrets.png)

要使用 Secrets ，得使用到 `mage_ai.data_preparation.shared.secrets` 裡的 `get_secret_value` 函式來取得參數值

```python,editable
import io
import pandas as pd
from mage_ai.data_preparation.shared.secrets import get_secret_value
import requests
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader
if 'test' not in globals():
    from mage_ai.data_preparation.decorators import test

def load_token():     
    tenant_id = get_secret_value('AZ_TENANT_ID')
    client_id = get_secret_value('AZ_CLIENT_ID')
    client_secret = get_secret_value('AZ_CLIENT_SECRET')

    url = f"https://example.org/api/{tenant_id}/oauth2/token"
    data = {
        "client_id": client_id,
        "client_secret": client_secret,
        "scope": ".default",
        "grant_type": "client_credentials",
    }
    resp = requests.post(url, data = data)

    try:
        resp.raise_for_status()
    except requests.HTTPError:
        print("取得 access token 失敗")
        raise
    
    return resp.json()["access_token"]


@data_loader
def load_data_from_api(*args, **kwargs):
    token = load_token()

    headers = {
        "Authorization": f"Bearer {token}",
        "Content-type": "application/json",
    }
 
    url = "https://example.org/api/data"

    resp = requests.post(url, headers=headers, json={"foo": "bar"})
    return result
# @test 區塊省略
```

以上範例使用到三個 Secrets ： `AZ_TENANT_ID` 、 `AZ_CLIENT_ID` 、 `AZ_CLIENT_SECRET` ，請依照實際需求建立並設定 Secrets 。


## 匯出資料


匯出資料的部分與 [Oracle 11] 一樣，請參考 [Oracle 11 的《匯出資料》章節](oracle-source-pipeline.md#匯出資料) 。
