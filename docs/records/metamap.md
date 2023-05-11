# MetaMap

> by [@xxrjun](https://github.com/xxrjun)

- [MetaMap](#metamap)
  - [閱讀文件](#閱讀文件)
  - [安裝 MetaMap 於 Ubuntu](#安裝-metamap-於-ubuntu)
    - [Prerequisites](#prerequisites)
    - [安裝步驟](#安裝步驟)
  - [運行 MetaMap](#運行-metamap)
    - [我自己覺得比較會用的(不確定)](#我自己覺得比較會用的不確定)
    - [簡單使用一下](#簡單使用一下)
  - [運行 MetaMap Server](#運行-metamap-server)

## 閱讀文件

- 使用 MetaMap Java API 文件[Using the MetaMap Java API](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/Docs/README_javaapi.html)
- 期刊文章 [MetaMap Lite: an evaluation of a new Java implementation of MetaMap](https://academic.oup.com/jamia/article/24/4/841/2961848)

## 安裝 MetaMap 於 Ubuntu

安裝的版本為 [MetaMap 2020 Linux 版本](https://data.lhncbc.nlm.nih.gov/umls-restricted/ii/tools/MetaMap/download/public_mm_linux_main_2020.tar.bz2) （64 位 - Bzip2 Tar - 1.72 GB），因為此主版本不包含 Java API，因此還需要下載 [適用於 Linux v2 的 MetaMap Java API 版本](https://data.lhncbc.nlm.nih.gov/umls-restricted/ii/tools/MetaMap/download/public_mm_linux_javaapi_2020v2.tar.bz2) (Bzip2 Tar - 60 MB) ]

### Prerequisites

- 最低 16GB 磁碟空間
- 最低 2GB RAM、建議至少 4GB
- JRE 版本 >= 1.7

### 安裝步驟

> 按照 MetaMap 安裝文檔 [Documentation: Installation](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/documentation/Installation.html) 進行安裝

解壓縮這個巨大檔案

- `-x` 表示要解壓縮 archive
- `-v` 表示要輸出詳細的解壓縮過程(看起來有在跑比較安心)
- `-f` 表示後面要接 archive 的文件名稱。

```shell
$ tar -xvf public_mm_linux_main_2020.tar.bz2
```

進入解壓縮後出現的 `public_mm` 資料夾

```shell
cd public_mm
```

找出 Java 安裝位置並設置環境變數 `JAVA_HOME`

```shell
which java
```

因為我們 Java 安裝位置為 `usr/bin/java`，因此將 JAVA_HOME 設成 `usr/bin/java`

```shell
# in bash
export JAVA_HOME=usr/bin/java
```

將 `<parent dir>/public_mm/bin` 目錄添加到程序路徑中，先使用 `pwd` 查看當前路徑，並將得到的路徑取代 `<parent dir>` (不確定相對路徑能不能，所以我是用絕對路徑)

```shell
# in bash
export PATH=<parent dir>/public_mm/bin:$PATH
```

接著可以運行安裝腳本了

```shell
./bin/install.sh
```

安裝過程會問一些問題，前面都設定好的話這邊都按 Enter 即可。

```shell
glib minimun required version: 2.12
Enter basedir of installation [<parent dir>/public_mm]

Basedir is set to <parent dir>/public_mm.

The WSD Server requires Sun's Java Runtime Environment (JRE);
Sun's Java Developer Kit (JDK) will work as well. if the
command: "which" java returns /usr/local/jre1.4.2/bin/java, then the
JRE resides in /usr/local/jre1.4.2/.

Where does your distribution of Sun\'s JRE reside?
Enter home path of JRE (JDK) [/usr/bin/java]:

Using /usr/bin/java for JAVA_HOME.

<parent dir>/public_mm/WSD_Server/config/disambServer.cfg generated
<parent dir>/public_mm/WSD_Server/config/log4j.properties generated
Created directory <parent dir>/public_mm/WSD_Server/log
Created directory <parent dir>/public_mm/Tagger_server/log
Setting up bin directory scripts:
<parent dir>/public_mm/bin/metamap2020.TEMPLATE generated.
<parent dir>/public_mm/bin/metamap20 generated.
<parent dir>/public_mm/bin/metamap generated.
<parent dir>/public_mm/bin/SKRenv.20 generated.
<parent dir>/public_mm/bin/skrmedpostctl generated.
<parent dir>/public_mm/bin/SKRrun.20 generated.
<parent dir>/public_mm/bin/uninstall.sh generated.
<parent dir>/public_mm/bin/wsdserverctl generated.
Setting up test suite:
<parent dir>/public_mm/TestSuite/runTest_2009.sh generated.
<parent dir>/public_mm/TestSuite/runTest_2010.sh generated.
<parent dir>/public_mm/TestSuite/runTest_2011.bat generated.
<parent dir>/public_mm/TestSuite/runTest_2011.sh generated.
<parent dir>/public_mm/TestSuite/runTest_2012.sh generated.
<parent dir>/public_mm/TestSuite/runTest_2013.sh generated.
<parent dir>/public_mm/TestSuite/runTest_2014.sh generated.
<parent dir>/public_mm/TestSuite/runTest.sh generated.
Checking for required datafiles
Checking for optional datafiles (WSD)

Public MetaMap Install complete.

Public MetaMap Install Settings:

Public MetaMap basedir: <parent dir>/public_mm
Public MetaMap Program Dir: <parent dir>/public_mm/bin
Java Home dir: /usr/bin/java
```

MetaMap 有兩個伺服器

- [SKR/MedPost Part-of-Speech Tagger Server](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/additional-tools/MedPostSKRTagger.html) **(Required)**
  - 用於進行詞性標記。將一個句子中的每個單字標記為其對應的詞性，如名詞、動詞、形容詞。
- [Word Sense Disambiguation (WSD) Server](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/additional-tools/WSDServer.html) **(Optional)**
  - 用於詞義消歧（Word Sense Disambiguation）。對於同一個詞在不同的語境下可能有不同的含義，需要通過上下文來確定該詞的正確含義的過程。

我覺得兩個都蠻重要的所以就跑唄。

```shell
./bin/skrmedpostctl start
./bin/wsdserverctl start
```

如果要停掉的話可以把 `start` 改成 `stop` 即可

```shell
./bin/skrmedpostctl stop
./bin/wsdserverctl stop
```

確認一下伺服器有沒有正常運作

```shell
ps -ef | grep java
```

題外話，我一開始執行 WSD Server 時發現 Port 5554 被占用，有兩種解決方式

- 終止占用這個 port 的程序

  ```shell
  sudo lsof -i :5554
  kill <PID>
  ```

- 修改 WSD Server 預設的 Port

  ```shell
  cd WDS_Server/config
  vim disambServer.cfg
  ```

  將 `DISAM_SERVER_TCP_PORT` 改成其他未占用的 Port

前面都完成後即可測試 MetaMap

輸入 `echo "lung cancer" | ./bin/metamap -I` 應該要得到相似的輸出

```shell
metamap (2020)

Control options:
  composite_phrases=4
  lexicon=db
  mm_data_year=2020AA
  show_cuis
Processing USER.tx.1: lung cancer

Phrase: lung cancer
Meta Mapping (1000):
  1000   C0242379:Lung Cancer (Malignant neoplasm of lung) [Neoplastic Process]
Meta Mapping (1000):
  1000   C0684249:LUNG CANCER (Carcinoma of lung) [Neoplastic Process]
Meta Mapping (1000):
  1000   C1306460:Lung cancer (Primary malignant neoplasm of lung) [Neoplastic Process]
```

都沒問題的話即可運行 MetaMap

## 運行 MetaMap

可按照以下方式

```shell
./bin/metamap
```

使用方式可參考

1. MetaMap Readme for Linux,
   http://lhncbc.nlm.nih.gov/ii/tools/MetaMap/Docs/MM09_Readme.html
2. MetaMap 2009 Usage,
   http://lhncbc.nlm.nih.gov/ii/tools/MetaMap/Docs/MM09_Usage.html
3. Effective Mapping of Biomedical Text to the UMLS Metathesaurus: The MetaMap Program, 2001,
   http://skr.nlm.nih.gov/papers/references/metamap_01AMIA.pdf
4. MetaMap: Mapping Text to the UMLS Metathesaurus, July 2006,
   http://skr.nlm.nih.gov/papers/references/metamap06.pdf
5. MetaMap Options and Examples, September 2006,
6. http://skr.nlm.nih.gov/papers/references/MetaMap_Examples_06.pdf

### 我自己覺得比較會用的(不確定)

輸入選項

- `-+` (--bracketed_output)
  用 `>>>>>` 和 `<<<<<` 括號將輸出的短語、候選詞和映射部分括起來。例如，

  ```plaintext
  >>>>> Phrase
  heart attack
  <<<<< Phrase
  and similarly for Candidates and Mappings.
  ```

- `-S` (--tagger OPTION)
  指定運行用於標記的標記服務器的主機名
- `-u` (--unique_acros_abbrs_only)
  將首字母縮略詞/縮寫變體的生成限制為具有獨特擴展的那些形式。此選項比允許所有形式的首字母縮略詞/縮寫變體產生更好的結果，但它仍然更好地防止所有此類變體。
- `-U` (--allow_duplicate_concept_names)
  要求兩個概念的 CUI 匹配（除了 Metathesaurus 概念本身及其在當前短語中的位置），以便將評估視為多餘。
- `-y` (--word_sense_disambiguation)
  導致 MetaMap 嘗試消除在匹配輸入文本中得分相同的概念之間的歧義。MetaMap Word Sense Disambiguation 的初始實現使用單一方法，該方法為出現歧義的上下文選擇具有最可能語義類型的概念（或多個概念）。

輸出選項

- `-G` (--sources)
  在輸出中顯示每個候選項和映射的 Metathesaurus 來源。
- `-I` (--show_cuis)
  顯示每個顯示概念的 UMLS CUI。
- `-r` (--threshold) <integer>
  將輸出限制為評估分數等於或超過指定閾值的候選人。明智地使用此選項可以防止 MetaMap 在某些輸入文本在 Metathesaurus 中沒有緊密匹配的情況下出錯。通常可以通過檢查給定應用程序中典型文本的 MetaMap 輸出來簡單地確定適當的閾值。
- `-T` (--tagger_output)
  顯示 MedPost/SKR 標註器的輸出，將輸入詞排在一行，它們的標籤排在下面一行。
- `-%` (--XML) <option>
  生成 XML 輸出。選項有 format、noformat、format1 和 noformat1。選項 format 和 format1 提供格式化的、打印精美的 XML 輸出，而 noformat 和 noformat1 提供簡潔的、未格式化的 XML 輸出。有關詳細信息，請參閱“MetaMap 2009 發行說明”(http://metamap.nlm.nih.gov/MM09__Release__Notes.shtml)。

### 簡單使用一下

```shell
~/public_mm$ ./bin/metamap20 -+  -G -I
~/public_mm/bin/SKRrun.20 ~/public_mm/bin/metamap20.BINARY.Linux --lexicon db -Z 2020AA -+ -G -I
Berkeley DB databases (USAbase 2020AA strict model) are open.
Static variants will come from table varsan in ~/public_mm/DB/DB.USAbase.2020AA.strict.
Derivational Variants: Adj/noun ONLY.
Variant generation mode: static.
Established connection $stream(140509864552672) to TAGGER Server on localhost.

metamap (2020)

Control options:
  composite_phrases=4
  lexicon=db
  mm_data_year=2020AA
  bracketed_output
  sources
  show_cuis
|: lung cancer
|:
Processing USER.tx.1: lung cancer

Phrase: lung cancer
>>>>> Phrase
lung cancer
<<<<< Phrase
>>>>> Mappings
Meta Mapping (1000):
  1000   C0242379:Lung Cancer (Malignant neoplasm of lung {AOD,CHV,COSTAR,CSP,HPO,ICD10CM,LCH_NW,LNC,MEDLINEPLUS,MSH,MTH,NCI,NCI_CTRP,NLMSubSyn,OMIM,PDQ,SNMI,SNOMEDCT_US}) [Neoplastic Process]
Meta Mapping (1000):
  1000   C0684249:LUNG CANCER (Carcinoma of lung {CHV,COSTAR,CST,MTH,NCI,NCI_CPTAC,NCI_CTEP-SDC,NCI_CTRP,NCI_NCI-GLOSS,NLMSubSyn,OMIM,PDQ,SNOMEDCT_US}) [Neoplastic Process]
Meta Mapping (1000):
  1000   C1306460:Lung cancer (Primary malignant neoplasm of lung {MTH,NLMSubSyn,SNOMEDCT_US}) [Neoplastic Process]
<<<<< Mappings
```

## 運行 MetaMap Server

```shell
~/public_mm$ ./bin/mmserver
```

測試 API

```shell
~public_mm/bin$ ./testapi.sh laboratory culture
serverport: 8066
options: []
terms: laboratory culture
input text:
 laboratory culture
Utterance:
 Id: 00000000.tx.1
 Utterance text: laboratory culture
 Position: (0, 18)
Phrase:
 text: laboratory culture
 Minimal Commitment Parse: [head([lexmatch([laboratory culture]),inputmatch([laboratory,culture]),tag(noun),tokens([laboratory,culture])])]
Candidates:
Mappings:
 Map Score: -1000
   Score: -1000
   Concept Id: C0430400
   Concept Name: Laboratory culture
   Preferred Name: Laboratory culture
   Matched Words: [laboratory, culture]
   Semantic Types: [lbpr]
   MatchMap: [[[1, 2], [1, 2], 0]]
   MatchMap alt. repr.: [[[phrase start: 1, phrase end: 2], [concept start: 1, concept end: 2], lexical variation: 0]]
   is Head?: true
   is Overmatch?: false
   Sources: [CHV, LNC, MTH, NLMSubSyn, SNOMEDCT_US]
   Positional Info: [(0, 18)]
   Pruning Status: 0
   Negation Status: 0
```

因為我想使用其他裝置連接部署在 Ubuntu 上的 MetaMap Server，所以需要修改配置文件 `./bin/SKRrun.20`

```shell
~/public_mm$ vim /bin/SKRrun.20
```

找到 `ACCEPTED_HOSTS="['127.0.0.1']"`，將想要允許的 IP 地址加入到列表中，例如：

我們想要使用 111.111.111.111 這個 IP 連接 MetaMap，所以修改為

```shell
ACCEPTED_HOSTS="['127.0.0.1']"
```

> 目前使用 `telnet` 可以成功連上，不過還不知道如何使用 API，需要再研究。
