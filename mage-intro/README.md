# Mage AI 基本說明

## 簡介

MageAI 是一個開源的資料管道（data pipeline）與資料編排平台，主打以 Python 為核心、低程式碼的方式來建構、執行與管理資料流程。它提供直觀的介面來撰寫與串接資料轉換邏輯，內建排程、版本控制與可觀測性功能，讓資料工程師能更有效率地進行 ETL／ELT、機器學習資料準備與分析工作，同時保持高度彈性與可擴充性。

> 以上內容由 ChatGPT 生成，僅供參考，請以官方文件為準。

## Pipeline

在 MageAI 中，pipeline 是用來定義與管理資料處理流程的核心單位，亦即是說，像是「資料複製」、「資料轉換」……等等，都是一個 Pipeline。


以下以表格方式簡單整理 **MageAI pipeline 的種類**與用途：

| Pipeline 類型          | 說明               |
| -------------------- | --------------- | -------------------- |
| Standard (Batch) Pipeline       | 最基本的 pipeline。通常是由 python 撰寫。 |
| Integration Pipeline | 處理資料庫資料的 pipeline，除非有特殊需求（例如欄位轉換……等），否則不需要撰寫任何 python 程式碼 |
| Streaming Pipeline   |    |

