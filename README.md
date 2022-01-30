# 自然語言處理期末報告 - AIDEA 醫病資料去識別化競賽

## 競賽簡介
- 主要目標：希望能藉由人工智慧模型，自動捕捉醫病對話中的敏感資訊，並對其進行去識別化處理
- 主要任務：建立能捕捉醫病對話中敏感資訊之模型，並能判斷該敏感資訊所屬之類別

## 模型架構
- 我們將此競賽任務視為 Sequence Labeling (NER) 任務，使用 BIO Tagging 做為標注方式，主要模型架構如下
> **BERT Embedding + 2-Layer BiLSTM + CRF + Rule Based Filters**

## 實驗流程
1. 首先透過 HuggingFace Pretrained BERT Tokenizer 與 BERT Model，計算每篇文章中，各個字元的 BERT Embedding，將其作為進入下一階段 BiLSTM 模型的 Input Feature
2. 搭建並訓練 2-Layer BiLSTM Model，進行 Sequence Labeling (NER) 任務，並將其結果作為進入下一階段 CRF 模型的 Input Feature 之一
3. 搭建並訓練 CRF Model，此模型的 Input Feature 包含以下兩部分
    - 上一階段 BiLSTM 每個字元的 Output Hidden State
    - 每個字元的 Character, Bigram 與 Trigram Feature
4. CRF 模型所得到的結果，會再透過我們額外撰寫的 Rule-based 演算法進行進一步過濾與優化，其中優化方式包含以下兩部分
    - 透過外部中文 NER 套件進行 NER 任務，抓出常見的特徵標籤 (Ex. 時間...)
    - 透過正則表達式 (RegEx)，抓出某些格式固定的類別標籤 (Ex. 電話號碼...)

## 額外調整
- 針對 Loss Function，我們有添加 Positive Class Weight，以解決資料不均問題
- 由於 Rule-based 演算法屬於經驗法則，我們假設其準確率高於 NN Model，因此若是兩模型有重疊之預測，則 Rule-based 會直接取代 NN Model 之標注結果

## 最終結果
> F1 Score
    > Public Test Dataset: 76.16%
    > Private Test Dataset: 76.43%
> 競賽成績
    > 計畫辦公室獎狀: 前標