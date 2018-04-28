---
title: Server předpovědi pracovního vytížení na terabajtů dat – Azure | Microsoft Docs
description: Jak pro trénování modelu strojového učení velkých objemů dat pomocí Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: 424af2ffd1b7931701036aeb819cbb8879cb7a41
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognózování úloh serveru s terabajty dat

Tento článek popisuje, jak můžete použít Azure Machine Learning Workbench pro vývoj řešení, které vyžadují použití velkých objemů dat datových vědců. Můžete spustit z ukázkové velkých datových sad, iteraci v rámci přípravy dat, technici funkce a strojové učení a potom rozšíří proces celé velké datové sady. 

Získáte informace o následujících klíčových funkcí nástroje Machine Learning Workbench:
* Snadné přepínání mezi výpočetní cíle. Můžete nastavit různé výpočetní cíle a použít je v experimenty. V tomto příkladu použijete DSVM Ubuntu a cluster Azure HDInsight jako výpočetní cíle. Můžete také nakonfigurovat výpočetní cíle, v závislosti na dostupnosti prostředků. Zejména po škálování clusteru Spark s více uzly pracovního procesu, můžete použít prostředkům prostřednictvím Machine Learning Workbench pro urychlení spustí experimentu.
* Spusťte sledování historie. Machine Learning Workbench můžete sledovat výkon strojového učení modely a další metriky, které vás zajímají.
* Operationalization model strojového učení. Integrované nástroje v rámci Machine Learning Workbench můžete nasadit vyškolení strojového učení model jako webovou službu v Azure Container Service. Můžete také webovou službu získat zkrácená batch předpovědi prostřednictvím volání rozhraní REST API. 
* Podpora pro terabajtů data.

> [!NOTE]
> Ukázky kódu a další materiály související s Tento příklad najdete v tématu [Githubu](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Přehled případu použití

Vytváření prognóz zatížení na serverech je běžné obchodní potřebu technologie společností, které můžete spravovat své vlastní infrastrukturu. Abyste snížili náklady na infrastrukturu, by měl být služeb spuštěných na serverech v rámci použít seskupeny dohromady ke spuštění na menší počet počítačů. Služby provozované na Nepromyšlené servery by měly mít více počítačů, které poběží. 

V tomto scénáři můžete soustředit na předpověď zatížení pro každý počítač (nebo serveru). Zejména použijte data relace na každém serveru k předvídání třída pracovního vytížení serveru v budoucnosti. Klasifikovat zatížení jednotlivých serverů do nízká, střední a vysokou třídy pomocí náhodného třídění doménové struktury ve [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Strojového učení technik a pracovní postup v tomto příkladu lze snadno rozšířit na jiné podobné problémy. 


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

* [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). K instalaci programu a vytvořit pracovní prostor, najdete v článku [rychlé spuštění Průvodce instalací](../service/quickstart-installation.md). Pokud máte více předplatných, můžete [nastavte požadované předplatné na aktuální aktivní předplatné](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (podle pokynů v tomto příkladu jsou obvykle stejné systémů systému macOS).
* Na datové vědě virtuálního počítače (DSVM) pro Linux (Ubuntu), pokud možno v oblasti Východ USA, kde vyhledá data. Můžete zřídit DSVM Ubuntu pomocí následujících [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Můžete také zjistit [tento rychlý Start](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Doporučujeme použít virtuální počítač s minimálně 8 jader a 32 GB paměti. 

Postupujte podle [instrukce](../service/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) na povolení přístupu bez sudoer ve virtuálním počítači pro AML Workbench.  Můžete použít [ověřování na základě klíčů SSH pro vytváření a používání virtuálních počítačů v AML Workbench](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). V tomto příkladu používáme heslo pro přístup k virtuálnímu počítači.  V následující tabulce uložte s DSVM informace o dalších krocích:

 Název pole| Hodnota |  
 |------------|------|
DSVM IP adresa | xxx|
 Uživatelské jméno  | xxx|
 Heslo   | xxx|


 Můžete použít žádné virtuální počítače s [modulu Docker](https://docs.docker.com/engine/) nainstalována.

* Clusteru Spark HDInsight, bez Spark a verze softwaru Hortonworks Data Platform 3.6 2.1.x, pokud možno v oblasti Východ USA, kde vyhledá data. Navštivte [vytvářet cluster Apache Spark v Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) podrobnosti o tom, jak HDInsight vytvářet clustery. Doporučujeme používat cluster tři pracovní s každou pracovního procesu s 16 jader a 112 GB paměti. Nebo můžete zvolit typ virtuálního počítače právě `D12 V2` pro hlavního uzlu a `D14 V2` pracovního uzlu. Nasazení clusteru trvá přibližně 20 minut. Potřebujete název clusteru, SSH uživatelské jméno a heslo můžete vyzkoušet na tomto příkladu. Uložte s informacemi clusteru Azure HDInsight na pozdější kroky v následující tabulce:

 Název pole| Hodnota |  
 |------------|------|
 Název clusteru| xxx|
 Uživatelské jméno  | xxx (sshuser ve výchozím nastavení)|
 Heslo   | xxx|


* Účet služby Azure Storage. Můžete postupovat podle [tyto pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k jeho vytvoření. Navíc vytvoření kontejnerů dva privátní objektů blob s názvy `fullmodel` a `onemonthmodel` v rámci tohoto účtu úložiště. Účet úložiště se používá pro uložení výsledků zprostředkující výpočetní a modelů strojového učení. Je nutné klíč účtu úložiště název a přístup můžete vyzkoušet na tomto příkladu. Uložte s informace o účtu úložiště Azure pro pozdější kroky v následující tabulce:

 Název pole| Hodnota |  
 |------------|------|
 Název účtu úložiště| xxx|
 Přístupový klíč  | xxx|


Ubuntu DSVM a cluster Azure HDInsight vytvořené v seznamu požadovaných jsou výpočetní cíle. Výpočetní cíle jsou výpočetních prostředků v rámci Machine Learning Workbench, což může být odlišný od počítače, kde je spuštěna Workbench.   

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete aplikaci Machine Learning Workbench.
2.  Na **projekty** vyberte **+** přihlásit a vybrat **nový projekt**.
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt.
4.  V **šablony projektů vyhledávání** vyhledávací pole, typ **předpovědi pracovního vytížení na terabajtů dat**a vyberte šablonu.
5.  Vyberte **Vytvořit**.

Můžete vytvořit projekt Workbench s úložiště git předem vytvořené pomocí následujících [tento pokyn](./tutorial-classifying-iris-part-1.md).  
Spustit `git status` Kontrola stavu souborů pro verzi sledování.

## <a name="data-description"></a>Popis dat

Data použitá v tomto příkladu je dat syntetizovaná server úloh. Je umístěn v účtu úložiště objektů Blob v Azure, který je veřejně dostupné v oblasti Východ USA. Informace o účtu konkrétní úložiště najdete v `dataFile` pole z [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) ve formátu "wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Data přímo z úložiště objektů Blob můžete použít. Pokud úložiště je používá mnoho uživatelů současně, můžete použít [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) stahovat data do vlastního úložiště pro lepší uživatelské možnosti experimenty. 

Celkové velikosti dat je přibližně 1 TB. Každý soubor je přibližně 1 – 3 GB a je ve formátu souboru CSV, bez hlavičky. Každý řádek dat představuje zatížení transakce na konkrétním serveru. Podrobné informace o schématu dat je následující:

Číslo sloupce | Název pole| Typ | Popis |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datum a čas |    Čas spuštění relace
2  |`SessionEnd`    | Datum a čas | Čas ukončení relace
3 |`ConcurrentConnectionCounts` | Integer | Počet souběžných připojení
4 | `MbytesTransferred` | Dvojitý | Normalizovaný data přenesená v megabajtech
5 | `ServiceGrade` | Integer |  Úrovni služby pro relaci
6 | `HTTP1` | Integer|  Relace používá HTTP1 nebo HTTP2
7 |`ServerType` | Integer   |Typ serveru
8 |`SubService_1_Load` | Dvojitý |   Zatížení subservice 1
9 | `SubService_2_Load` | Dvojitý |  Zatížení subservice 2
10 | `SubService_3_Load` | Dvojitý |     Zatížení subservice 3
11 |`SubService_4_Load` | Dvojitý |  Zatížení subservice 4
12 | `SubService_5_Load`| Dvojitý |      Zatížení subservice 5
13 |`SecureBytes_Load`  | Dvojitý | Zabezpečené bajtů zatížení
14 |`TotalLoad` | Dvojitý | Celkový počet zatížení na serveru
15 |`ClientIP` | Řetězec|    IP adresa klienta
16 |`ServerIP` | Řetězec|    IP adresa serveru



Všimněte si, že očekávané datové typy jsou uvedeny v předchozí tabulce. Z důvodu chybějící hodnoty a problémy nesprávné data není zaručeno, že datové typy jsou ve skutečnosti podle očekávání. Zpracování dat třeba vzít v úvahu. 


## <a name="scenario-structure"></a>Struktura scénář

Soubory v tomto příkladu jsou uspořádány následujícím způsobem.

| Název souboru | Typ | Popis |
|-----------|------|-------------|
| `Code` | Složka | Složka obsahuje všechny kód v ukázce. |
| `Config` | Složka | Složka obsahuje konfigurační soubory. |
| `Image` | Složka | Složka používá pro uložení bitové kopie souboru README. |
| `Model` | Složka | Složka používá pro uložení modelu soubory stahované z úložiště objektů Blob. |
| `Code/etl.py` | Soubor Python | Soubor Python použitý pro přípravu dat a funkce inženýrství. |
| `Code/train.py` | Soubor Python | Soubor Python použity při cvičení modelu tři třídy více klasifikace.  |
| `Code/webservice.py` | Soubor Python | Soubor Python použitý pro operationalization.  |
| `Code/scoring_webservice.py` | Soubor Python |  Soubor Python pro transformaci dat a volání webové služby. |
| `Code/O16Npreprocessing.py` | Soubor Python | Soubor Python použitý k předběžně zpracovat data pro scoring_webservice.py.  |
| `Code/util.py` | Soubor Python | Soubor Python, který obsahuje kód pro čtení a zápis objektů Azure BLOB.  
| `Config/storageconfig.json` | Soubor JSON | Konfigurační soubor pro kontejner objektů blob v Azure, který ukládá mezilehlých výsledků a model pro zpracování a školení na jeden měsíc data. |
| `Config/fulldata_storageconfig.json` | Soubor JSON | Konfigurační soubor pro kontejner objektů blob v Azure, který ukládá mezilehlých výsledků a model pro zpracování a školení na úplné datové sady.|
| `Config/webservice.json` | Soubor JSON | Konfigurační soubor pro scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Soubor YAML | Soubor Conda závislostí. |
| `Config/conda_dependencies_webservice.yml` | Soubor YAML | Soubor Conda závislostí pro webovou službu.|
| `Config/dsvm_spark_dependencies.yml` | Soubor YAML | V souboru Spark závislostí Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | Soubor YAML | Soubor závislostí Spark pro cluster HDInsight Spark. |
| `README.md` | Soubor markdownu | Soubor README markdownu. |
| `Code/download_model.py` | Soubor Python | Soubor Python, používaný ke stahování souborů modelu z Azure blob na místní disk. |
| `Docs/DownloadModelsFromBlob.md` | Soubor markdownu | Soubor markdownu, který obsahuje pokyny, jak spustit `Code/download_model.py`. |



### <a name="data-flow"></a>Tok dat

Kód v [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) načte data z kontejneru veřejně přístupná (`dataFile` pole z [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Obsahuje Příprava dat a funkce inženýrství a uloží výsledky zprostředkující výpočetní a modely pro vlastní privátní kontejner. Kód v [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) načte výsledky zprostředkující výpočetní z kontejneru privátní, nastaví model více třída klasifikace a zapisuje model vyškolení machine learning ke kontejneru privátní. 

Měli byste použít jeden kontejner pro experimenty na jeden měsíc datové sady a další pro experimenty na úplné datové sady. Protože dat a modely jsou uložena jako soubor Parquet, každý soubor je ve skutečnosti složku v kontejneru, který obsahuje více objektů BLOB. Výsledný kontejneru vypadá takto:

| Předpona názvu objektu BLOB | Typ | Popis |
|-----------|------|-------------|
| featureScaleModel | Parquet | Standardní scaler model pro číselné funkce. |
| stringIndexModel | Parquet | Řetězec indexer model pro jiné než číselné funkce.|
| oneHotEncoderModel|Parquet | Kodér horkou jeden model pro kategorií funkce. |
| mlModel | Parquet | Model vyškolení strojového učení. |
| Informace o| Soubor okurky Python | Informace o Transformovaná data, včetně počáteční školení, konec učení, doba trvání, časové razítko pro train-test rozdělení a sloupce pro indexování a jeden horkou kódování.

Všechny soubory a objekty BLOB v předchozí tabulce se používají pro operationalization.


### <a name="model-development"></a>Vývoj pro model

#### <a name="architecture-diagram"></a>Diagram architektury


Následující diagram znázorňuje-komplexní pracovní postup použití Machine Learning Workbench k vývoji modelu: ![architektura](media/scenario-big-data/architecture.PNG)

V následujících částech ukážeme modelu vývoj pomocí funkce vzdálené výpočetní cíle v nástroji Machine Learning Workbench. Nám nejdřív načíst malé množství ukázkových dat a spusťte skript [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) na DSVM Ubuntu pro rychlé iterací. Budeme dál omezit pracovní My v [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) předáním nadbytečný argument pro rychlejší iterací. V konečném používáme clusteru služby HDInsight ke cvičení úplné daty.     

[ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) Soubor načte a připraví data a provádí funkce inženýrství. Přijímá dva argumenty:
* Konfigurační soubor pro kontejner úložiště objektů Blob, pro ukládání mezilehlých výpočetní výsledky a modely.
* Argument konfigurace ladění pro rychlejší iterací.

První argument `configFilename`, je místní konfigurační soubor, kam ukládat informace o úložiště objektů Blob a určete, kam chcete načíst data. Ve výchozím nastavení, je [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), která je pro použití v spuštěním data jeden měsíc. Můžeme také zahrnovat [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), které je nutné použít na úplnou sadu dat spustit. Obsah v konfiguraci je následující: 

| Pole | Typ | Popis |
|-----------|------|-------------|
| StorageAccount | Řetězec | Název účtu Azure Storage |
| storageContainer | Řetězec | Kontejneru v účtu Azure Storage k ukládání mezilehlých výsledků |
| klíč úložiště | Řetězec |Azure přístupový klíč účtu úložiště |
| datový soubor|Řetězec | Soubory zdroje dat  |
| Doba trvání| Řetězec | Doba trvání dat v soubory zdroje dat|

Změňte oba `Config/storageconfig.json` a `Config/fulldata_storageconfig.json` ke konfiguraci účtu úložiště, klíč úložiště a kontejneru objektů blob k ukládání mezilehlých výsledků. Ve výchozím kontejneru objektů blob pro jeden měsíc data spustit je `onemonthmodel`, a je kontejner objektů blob pro úplnou datovou sadu spustit `fullmodel`. Ujistěte se, že vytvoříte tyto dvě kontejnery v účtu úložiště. `dataFile` Pole [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) nakonfiguruje, jaká data je načten do [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). `duration` Pole konfiguruje rozsah obsahuje data. Pokud doba trvání je nastavené na ONE_MONTH, třeba data načíst jenom jeden soubor .csv mezi sedm soubory dat z června 2016. Pokud doba trvání je plná, je načten úplné datové sady (1 TB). Není třeba měnit `dataFile` a `duration` v těchto dvou konfiguračních souborech.

Druhý argument je ladění. Jeho nastavení na hodnotu FILTER_IP umožňuje rychlejší iterací. Použití tohoto parametru je užitečné, pokud chcete ladit vašeho skriptu.

> [!NOTE]
> Ve všech z následujících příkazů nahraďte všechny proměnné argument jeho skutečnou hodnotu.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Model vývoje Docker Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Nastavit výpočetní cíl

Spusťte příkazový řádek z Machine Learning Workbench výběrem **soubor** > **spusťte příkazový řádek**. Potom spusťte: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Následující dva soubory jsou vytvořeny ve složce aml_config projektu:

-  dockerdsvm.COMPUTE: Tento soubor obsahuje informace o připojení a konfigurace vzdálené spuštění cíl.
-  dockerdsvm.runconfig: Tento soubor je sada spuštění možnosti, které se používá v rámci aplikace Workbench.

Přejděte do dockerdsvm.runconfig a změnit konfiguraci těchto polí takto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Příprava prostředí projektu spuštěním:

```az ml experiment prepare -c dockerdsvm```


S `PrepareEnvironment` nastaven na hodnotu true, Machine Learning Workbench vytvoří běhové prostředí vždy, když odeslání úlohy. `Config/conda_dependencies.yml` a `Config/dsvm_spark_dependencies.yml` obsahovat přizpůsobení běhové prostředí. Úpravou tyto dva soubory YMAL můžete upravit vždy Conda závislosti, Spark konfigurace a závislosti Spark. V tomto příkladu jsme přidali `azure-storage` a `azure-ml-api-sdk` jako další balíčky Python v `Config/conda_dependencies.yml`. Jsme přidali i `spark.default.parallelism`, `spark.executor.instances`, a `spark.executor.cores` v `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Příprava dat a funkce inženýrství na DSVM Docker

Spusťte skript `etl.py` na DSVM Docker. Použijte parametr ladění, který filtruje načtená data s konkrétní server IP adresy:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Přejděte na straně panelu a vyberte **spustit** zobrazíte historii spouštění z `etl.py`. Všimněte si, že čas spuštění je asi 2 minuty. Pokud chcete změnit svůj kód zahrnují nové funkce, poskytuje FILTER_IP jako druhý argument zajišťuje rychlejší iterací. Možná budete muset tento krok spustit několikrát při plánování práce s vlastními strojového učení problémů, prozkoumejte datové sady nebo vytvořit nové funkce. 

Vlastní omezení na jaké zatížení a další filtrování jaká data ke zpracování dat můžete urychlit proces iterace vývojem vašeho modelu. Proto byste, měli byste pravidelně uložit změny v kódu do úložiště Git. Všimněte si, že jsme použili následující kód v `etl.py` umožňující přístup ke kontejneru privátní:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Potom spusťte skript `etl.py` na DSVM Docker bez parametru ladění FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Přejděte na straně panelu a vyberte **spustit** zobrazíte historii spouštění z `etl.py`. Všimněte si, že čas spuštění je asi čtyři minut. Zpracování výsledku tento krok je uložena do kontejneru a je pro školení v train.py načtená. Kromě toho řetězec indexery, kodér kanálů a standardní scalers se uloží do privátní kontejneru. Ty se používají v operationalization. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Model školení na DSVM Docker

Spusťte skript `train.py` na DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Tento krok načte výsledky zprostředkující výpočetní ze spouštění z `etl.py`a nastaví model strojového učení. Tento krok trvá asi 2 minuty.

Po úspěšném dokončení experimentování na malá data, můžete nadále spouštět experimenty na úplné datové sady. Můžete začít používat stejný kód a pak experimentovat s argumentem a výpočetní cíl změny.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Vývoj pro model v clusteru HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Vytvořit cíl výpočetní v nástroji Machine Learning Workbench pro HDInsight cluster

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Následující dva soubory jsou vytvořeny ve složce aml_config:
    
-  myhdi.COMPUTE: Tento soubor obsahuje informace o vzdálené spuštění cíl připojení a konfiguraci.
-  myhdi.runconfig: Tento soubor je sada spuštění možnosti, které se používá v rámci aplikace Workbench.


Přejděte do myhdi.runconfig a změnit konfiguraci těchto polí takto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Příprava prostředí projektu spuštěním:

```az ml experiment prepare -c myhdi```

Tento krok může trvat až sedm minut.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Příprava dat a funkce inženýrství na clusteru HDInsight

Spusťte skript `etl.py`, úplná daty v clusteru HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Protože tato úloha trvá poměrně dlouho (přibližně dvě hodiny), můžete použít `-a` zakázat výstup streamování. Když úloha se provádí, v **historii běhů**, můžete zobrazit protokoly ovladačů a řadiče. Pokud máte větší cluster, můžete vždy překonfigurovat konfigurace v `Config/hdi_spark_dependencies.yml` používat další instance nebo jader. Například pokud máte cluster se čtyřmi. pracovní uzly, můžete zvýšit hodnotu `spark.executor.instances` z 5 na 7. Zobrazí se výstup v tomto kroku **fullmodel** kontejneru v účtu úložiště. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Model školení na clusteru HDInsight

Spusťte skript `train.py` na clusteru HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Protože tato úloha trvat poměrně dlouhou dobu (přibližně 30 minut), můžete použít `-a` zakázat výstup streamování.

#### <a name="run-history-exploration"></a>Spustit zkoumání historie

Historie spouštění je funkce, která umožňuje sledování vaší experimenty v nástroji Machine Learning Workbench. Ve výchozím nastavení sleduje dobu trvání experimenty. V našem příkladu konkrétní, když jsme přesunout k úplné datové sady pro `Code/etl.py` v experimentování, zjistíme, že doba trvání výrazně zvyšuje. Můžete také protokolovat určité metriky pro účely sledování. Povolit sledování metriky, přidejte následující řádky kódu do head Python souboru:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Tady je příklad sledovat určité metriky:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Na pravém bočním panelu nástroje Workbench, přejděte do **spustí** zobrazíte historii spouštění pro každý soubor Python. Můžete také přejít do úložiště GitHub. Nové větve, s názvem "AMLHistory," počínaje se vytvoří sledovat změny provedené u vašeho skriptu při každém spuštění. 


### <a name="operationalize-the-model"></a>Zprovoznit model

V této části zprovoznit model, který jste vytvořili v předchozích krocích jako webovou službu. Můžete také další informace o použití webovou službu pro předpověď zatížení. Pomocí rozhraní příkazového řádku operationalization počítač jazyk (CLIs) do balíčku kódu a závislosti jako imagí Dockeru a k publikování modelu jako kontejnerizované webovou službu.

Příkazovém řádku v nástroji Machine Learning Workbench můžete použít ke spuštění CLIs.  Můžete taky spustit CLIs na Ubuntu Linux podle [Průvodce instalací](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> Ve všech následujících příkazů nahraďte všechny proměnné argument jeho skutečnou hodnotu. Trvá asi 40 minut na dokončení této části.
> 

Vyberte jedinečný řetězec jako prostředí pro operationalization. Tady používáme "[jedinečné]" řetězec představující řetězec, který zvolíte.

1. Vytvoření prostředí pro operationalization a vytvořte skupinu prostředků.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Všimněte si, že můžete použít službu kontejneru jako prostředí pomocí `--cluster` v `az ml env setup` příkaz. Můžete zprovoznit model machine learning na [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Používá [Kubernetes](https://kubernetes.io/) pro automatizaci nasazení, škálování a správu kontejnerizované aplikací. Tento příkaz Spustit trvá asi 20 minut. Použijte následující postupy ke kontrole, pokud nasazení úspěšně proběhlo úspěšně: 

        az ml env show -g [unique]rg -n [unique]

   Nastavte nasazení prostředí jako ten, který jste právě vytvořili, tak, že spustíte následující:

        az ml env set -g [unique]rg -n [unique]

2. Vytvořit a použít účet pro správu modelu. Pokud chcete vytvořit účet pro správu modelu, spusťte následující:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Použití modelu správy pro operationalization spuštěním následující:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Účet pro správu modelu slouží ke správě modely a webové služby. Z portálu Azure uvidíte, že byl vytvořen nový účet správy modelu. Uvidíte modely, manifesty, imagí Dockeru a služby, které jsou vytvořené pomocí tohoto účtu správy modelu.

3. Stáhněte si a zaregistrujte modely.

   Stáhnout modely v **fullmodel** kontejneru do místního počítače v adresáři kódu. Nestahovat parquet datového souboru s názvem "vmlSource.parquet." Není soubor modelu; je výsledek zprostředkující výpočty. Také můžete znovu použít model soubory obsažené v úložiště Git. Další informace najdete v tématu [Githubu](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Přejděte na `Model` odpovídá složce v rozhraní příkazového řádku a zaregistrujte modely jako:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Výstup každé příkazu dává ID modelu, který je nutný v dalším kroku. Uložte je do textového souboru pro budoucí použití.

4. Vytvoření manifestu pro webovou službu.

   Manifest obsahuje kód pro webové služby, modely strojové učení a závislosti prostředí runtime. Přejděte na `Code` složky na rozhraní příkazového řádku a spusťte následující příkaz:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Výstup dává manifestu ID pro další krok. 

   Zůstat v `Code` directory a můžete otestovat webservice.py spuštěním následující: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Vytvořte image Dockeru. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Výstup obsahuje ID bitové kopie pro další krok, tuto bitovou kopii docker se používá v kontejneru služby. 

6. Nasazení webové služby ke clusteru Container Service.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Výstup obsahuje ID služby Budete muset použít k získání klíče autorizace a adresu URL služby.

7. Volání webové služby v kódu jazyka Python skóre v dávkách malé.

   Získat klíč autorizace, použijte následující příkaz:

         az ml service keys realtime -i $ServiceID 

   Získat služby vyhodnocování adresy URL použijte následující příkaz:

        az ml service usage realtime -i $ServiceID

   Upravit obsah `./Config/webservice.json` správné službou vyhodnocování adresy URL a autorizační klíč. Zachovat "Nosiče" v původní soubor a nahraďte část "xxx". 
   
   Přejděte do kořenového adresáře projektu a otestovat webovou službu pro zkrácená dávkového vyhodnocování s použitím následující:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Škálování webové služby. 

   Další informace najdete v tématu [postup škálování operationalization v clusteru Azure Container Service](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>Další postup

V tomto příkladu se dozvíte, jak používat Machine Learning Workbench ke cvičení strojového učení modelu na velké objemy dat a zprovoznit naučeného modelu. Konkrétně jste zjistili, jak nakonfigurovat a používat různé výpočetní cíle a spusťte historii sledování metrik a použít jiný spustí.

Můžete rozšířit kód a prozkoumejte křížové ověření a technologie hyper parametr ladění. Další informace o vyladění křížové ověření a technologie hyper parametrů najdete v tématu [tento prostředek Githubu](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Další informace o vytváření prognóz časové řady, najdete v části [tento prostředek Githubu](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
