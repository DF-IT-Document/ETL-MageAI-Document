# 資料轉換

在實務上，有時會需要將資料進行轉換

例如本來的資料如下

| AAA                                  | AAB      | AAC                         |
|--------------------------------------|----------|-----------------------------|
| 246ea0f9-eeb5-40c0-8691-2176545968f0 | sysadmin | admin@example.com           |
| 5245f7e7-dd1d-4d18-a3d9-b5e5368be99c | john114  | john.due@example.com        |
| 3a6c8cf6-2ca2-455f-858b-d84e36526e16 | smith514 | smith.white0514@example.com |

但希望匯出的資料變成

| id                                   | username | email                       |
|--------------------------------------|----------|-----------------------------|
| 246ea0f9-eeb5-40c0-8691-2176545968f0 | sysadmin | admin@example.com           |
| 5245f7e7-dd1d-4d18-a3d9-b5e5368be99c | john114  | john.due@example.com        |
| 3a6c8cf6-2ca2-455f-858b-d84e36526e16 | smith514 | smith.white0514@example.com |


詳細可參閱官方文件：[https://docs.mage.ai/guides/blocks/transformer-blocks](https://docs.mage.ai/guides/blocks/transformer-blocks)


這裡提供兩種情境作為範例：

- 欄位轉換
- 內容轉換


