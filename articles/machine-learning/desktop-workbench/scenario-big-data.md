---
title: Prognózování úloh serveru s terabajty dat – Azure | Dokumentace Microsoftu
description: Jak pro trénování modelu strojového učení na velké objemy dat s využitím Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ROBOTS: NOINDEX
ms.openlocfilehash: 4a3329c7f08dfabdf1bb8a010ad5bc865fc509f4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241646"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognózování úloh serveru s terabajty dat

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Tento článek popisuje, jak můžete použít Azure Machine Learning Workbench na vývoj řešení, které vyžadují použití velkých objemů dat odborníky přes data. Můžete spustit z ukázky velkých datových sad, iteraci v rámci přípravy dat, vytváření funkcí a strojové učení a potom rozšiřte proces velké celou datovou sadu. 

Dozvíte se o následující klíčové funkce Machine Learning Workbench:
* Snadné přepínání mezi cílových výpočetních prostředí. Můžete nastavit cíle různými výpočetními a jejich použití v experimentování ve službě. V tomto příkladu použijete Ubuntu DSVM a cluster Azure HDInsight jako cílových výpočetních prostředí. Můžete také nakonfigurovat cílových výpočetních prostředí, v závislosti na dostupnosti prostředků. Zejména po horizontální navýšení kapacity clusteru Spark s více uzly pracovního procesu, můžete použít prostředky prostřednictvím aplikace Machine Learning Workbench pro urychlení spuštění experimentu.
* Sledování historie spuštění. Machine Learning Workbench můžete sledovat výkon strojového učení modely a jiné metriky, které vás zajímají.
* Operacionalizace modelu strojového učení. Integrované nástroje Machine Learning Workbench můžete použít k nasazení trénovaného modelu strojového učení jako webovou službu ve službě Azure Container Service. Webové služby můžete použít také k získání předpovědi zkrácené batch prostřednictvím volání rozhraní REST API. 
* Podpora terabajty dat.

> [!NOTE]
> Ukázky kódu a další materiály týkající se v tomto příkladu najdete v tématu [Githubu](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Přehled případu použití

Prognózování úloh na serverech je běžné obchodní potřebu technologie společnosti, které spravují své vlastní infrastrukturu. Pokud chcete snížit náklady na infrastrukturu, služby spuštěné na serverech v rámci používá by měl být seskupeny ke spuštění na menší počet počítačů. Služby provozované na Nepromyšlené servery by se měly provádět další počítače ke spuštění. 

V tomto scénáři vám soustředit se na předpověď zatížení pro každý počítač (nebo serveru). Zejména použijte data relace na každém serveru v budoucnu předpovědět třída pracovního vytížení serveru. Klasifikovat zatížení jednotlivých serverů do nízká, střední a vysokou třídy pomocí náhodného třídění doménové struktury v [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Machine learningu, techniky a pracovní postup v tomto příkladu je možné snadno rozšířit na jiné podobné problémy. 


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

* [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Instalace a vytvoření pracovního prostoru, najdete v článku [instalační příručky rychlý Start](quickstart-installation.md). Pokud máte více předplatných, můžete si [nastavte požadované předplatné bude aktuální aktivní předplatné](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (podle pokynů v tomto příkladu jsou obvykle stejné pro systémy macOS).
* Data virtuálního počítače VĚDY pro Linux (Ubuntu), pokud možno v oblasti USA – východ, kde vyhledá data. DSVM Ubuntu můžete zřídit pomocí následujících [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Můžete také zobrazit [v tomto rychlém startu](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Doporučujeme použít virtuální počítač s alespoň s 8 jádry a 32 GB paměti. 

Postupujte podle [instrukce](../desktop-workbench/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) povolit přístup bez hesla sudoer na virtuálním počítači AML Workbench.  Můžete použít [ověřování pomocí klíče SSH pro vytváření a používání virtuálního počítače v aplikaci Workbench AML](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). V tomto příkladu používáme heslo pro přístup k virtuálnímu počítači.  Uložte následující tabulku s DSVM informace o dalších krocích:

 Název pole| Hodnota |  
 |------------|------|
DSVM IP adresa | xxx|
 Uživatelské jméno  | xxx|
 Heslo   | xxx|


 Můžete použít jakýkoli virtuální počítač s [modul Docker](https://docs.docker.com/engine/) nainstalované.

* Cluster HDInsight Spark, Spark verze a verze datovou platformou Hortonworks 3.6 2.1.x, pokud možno v oblasti USA – východ, kde vyhledá data. Navštivte [vytvořit cluster Apache Spark v Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) podrobnosti o tom, jak vytvářet clustery HDInsight. Doporučujeme používat cluster tři-pracovní proces s každému pracovnímu procesu s 16 jádry a 112 GB paměti. Nebo můžete zvolit typ virtuálního počítače pouze `D12 V2` pro hlavní uzel a `D14 V2` pracovního uzlu. Nasazení clusteru trvá přibližně 20 minut. Budete potřebovat název clusteru, SSH uživatelské jméno a heslo pro vyzkoušení v tomto příkladu. Uložte v následující tabulce se informace o clusteru Azure HDInsight pro pozdější kroky:

 Název pole| Hodnota |  
 |------------|------|
 Název clusteru| xxx|
 Uživatelské jméno  | xxx (sshuser ve výchozím nastavení)|
 Heslo   | xxx|


* Účet služby Azure Storage. Můžete postupovat podle [tyto pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k jejímu vytvoření. Také, vytvořte dva kontejnery privátní objekt blob s názvy `fullmodel` a `onemonthmodel` v tomto účtu úložiště. Účet úložiště se používá k uložení výsledků zprostředkující výpočetní a modely strojového učení. Je nutné klíč účtu úložiště název a přístup můžete vyzkoušet na tomto příkladu. Uložte v následující tabulce se informace o účtu úložiště Azure pro pozdější kroky:

 Název pole| Hodnota |  
 |------------|------|
 Název účtu úložiště| xxx|
 Přístupový klíč  | xxx|


Datové VĚDY se systémem Ubuntu a v seznamu požadovaných součástí clusteru Azure HDInsight jsou cílových výpočetních prostředí. Výpočetní cíle jsou výpočetních prostředků v rámci aplikace Machine Learning Workbench, které mohou být odlišné od počítače, ve kterém běží aplikace Workbench.   

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvoření nového projektu s použitím v tomto příkladu jako šablony:
1.  Otevřete aplikaci Machine Learning Workbench.
2.  Na **projekty** stránky, vyberte **+** přihlášení a výběr **nový projekt**.
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt.
4.  V **Hledat v šablonách projektů** vyhledávacího pole, typ **Prognózování úloh na terabajty dat**a vyberte šablonu.
5.  Vyberte **Vytvořit**.

Můžete vytvořit projekt aplikace Workbench pomocí úložiště git předem vytvořené pomocí následujících [tento pokyn](./tutorial-classifying-iris-part-1.md).  
Spustit `git status` ke kontrole stavu souborů pro verzi sledování.

## <a name="data-description"></a>Popis dat

Data použitá v tomto příkladu je dat syntetizovaný server úloh. Je hostován v účtu úložiště objektů Blob v Azure, který je veřejně dostupná v oblasti USA – východ. Informace o účtu konkrétní úložiště najdete v `dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) ve formátu "wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Můžete použít data přímo z úložiště objektů Blob. Pokud úložiště používá mnoho uživatelů současně, můžete použít [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) si chcete stáhnout data do vlastního úložiště pro lepší prostředí pro experimentování ve službě. 

Celkovou velikost dat je přibližně 1 TB. Každý soubor je přibližně 1 – 3 GB a je ve formátu souboru CSV, bez záhlaví. Každý řádek dat představuje zatížení transakce na konkrétním serveru. Podrobné informace o schématu dat je následující:

Číslo sloupce | Název pole| Typ | Popis |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datum a čas |    Čas spuštění relace
2  |`SessionEnd`    | Datum a čas | Čas ukončení relace
3 |`ConcurrentConnectionCounts` | Integer | Počet souběžných připojení
4 | `MbytesTransferred` | Double | Normalizovaná data přenesená v MB
5 | `ServiceGrade` | Integer |  Služba na podnikové úrovni pro relaci
6 | `HTTP1` | Integer|  Používá relace HTTP1 nebo HTTP2
7 |`ServerType` | Integer   |Typ serveru
8 |`SubService_1_Load` | Double |   Což 1 zatížení
9 | `SubService_2_Load` | Double |  Což 2 zatížení
10 | `SubService_3_Load` | Double |     Což 3 zatížení
11 |`SubService_4_Load` | Double |  Což 4 zatížení
12 | `SubService_5_Load`| Double |      Což 5 zatížení
13 |`SecureBytes_Load`  | Double | Načíst zabezpečené bajtů
14 |`TotalLoad` | Double | Celkové zatížení na serveru
15 |`ClientIP` | Řetězec|    IP adresa klienta
16 |`ServerIP` | Řetězec|    IP adresa serveru



Všimněte si, že na očekávané datové typy jsou uvedeny v předchozí tabulce. Z důvodu chybějící hodnoty a problémy nesprávné dat neexistuje žádná záruka, že datové typy, které jsou ve skutečnosti podle očekávání. Zpracování dat třeba vzít v úvahu. 


## <a name="scenario-structure"></a>Struktura scénář

Soubory v tomto příkladu jsou průchody uspořádány takto.

| Název souboru | Typ | Popis |
|-----------|------|-------------|
| `Code` | Složka | Složka obsahuje všechny kódem v příkladu. |
| `Config` | Složka | Složka obsahuje konfigurační soubory. |
| `Image` | Složka | Složka pro ukládání imagí pro soubor README. |
| `Model` | Složka | Složku, která slouží k ukládání modelu stáhnout z úložiště objektů Blob. |
| `Code/etl.py` | Soubor Pythonu | Soubor Pythonu pro přípravu dat a vytváření funkcí. |
| `Code/train.py` | Soubor Pythonu | Soubor Pythonu využívají k tréninku modelu tři třídy multi zařazení.  |
| `Code/webservice.py` | Soubor Pythonu | Soubor Pythonu použití při operacionalizaci.  |
| `Code/scoring_webservice.py` | Soubor Pythonu |  Soubor Pythonu používá pro transformaci dat a volání webové služby. |
| `Code/O16Npreprocessing.py` | Soubor Pythonu | Soubor Pythonu pro předzpracování dat pro scoring_webservice.py.  |
| `Code/util.py` | Soubor Pythonu | Soubor Pythonu, který obsahuje kód pro čtení a zápis objektů BLOB Azure.  
| `Config/storageconfig.json` | Soubor JSON | Konfigurační soubor pro kontejner objektů blob v Azure, která ukládá mezilehlých výsledků a model pro zpracování a školení na jeden měsíc data. |
| `Config/fulldata_storageconfig.json` | Soubor JSON | Konfigurační soubor pro kontejner objektů blob v Azure, která ukládá mezilehlých výsledků a model pro zpracování a školení na úplné datové sadě.|
| `Config/webservice.json` | Soubor JSON | Konfigurační soubor pro scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Soubor YAML | Souboru závislostí systému Conda. |
| `Config/conda_dependencies_webservice.yml` | Soubor YAML | Souboru závislostí systému Conda pro webovou službu.|
| `Config/dsvm_spark_dependencies.yml` | Soubor YAML | V souboru Spark závislost Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | Soubor YAML | Soubor závislostí Spark pro cluster HDInsight Spark. |
| `README.md` | Soubor markdownu | V souboru README markdownu. |
| `Code/download_model.py` | Soubor Pythonu | Soubor Pythonu používá ke stahování souborů modelu z Azure blob na místní disk. |
| `Docs/DownloadModelsFromBlob.md` | Soubor markdownu | Soubor markdownu, který obsahuje pokyny, jak spustit `Code/download_model.py`. |



### <a name="data-flow"></a>Tok dat

Kód v [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) načítá data z kontejneru veřejně přístupná (`dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Zahrnuje přípravy dat a vytváření funkcí a uloží výsledky zprostředkující výpočetní a modely do privátního kontejneru. Kód v [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) načte výsledky zprostředkující výpočetní z kontejneru soukromého, trénovat klasifikační roc model a zapisuje do kontejneru soukromého model trénovaného machine learning. 

Měli byste použít jeden kontejner pro služby experimentování ve službě na jeden měsíc datové sady a jinou pro experimentování ve službě na úplné datové sadě. Protože dat a modelů jsou uloženy jako soubory Parquet, každý soubor je ve skutečnosti složce v kontejneru, který obsahuje víc objektů BLOB. Výsledný kontejneru by měl vypadat takto:

| Předpona názvu objektu BLOB | Typ | Popis |
|-----------|------|-------------|
| featureScaleModel | Parquet | Standardní scaler model pro číselné funkce. |
| stringIndexModel | Parquet | Model indexer řetězec pro jiné než číselné funkce.|
| oneHotEncoderModel|Parquet | Kodér horkou jeden model zařazené do kategorií funkcí. |
| mlModel | Parquet | Model trénovaného strojového učení. |
| informace| Soubor pickle Pythonu | Informace o Transformovaná data, včetně počáteční školení, vzdělávání end, doba trvání, časové razítko pro trénování a testování rozdělení a sloupce pro indexování a za běhu jeden kódování.

Všechny soubory a objekty BLOB v předchozí tabulce se používají při operacionalizaci.


### <a name="model-development"></a>Vývoj modelu

#### <a name="architecture-diagram"></a>Diagram architektury


Následující diagram znázorňuje – komplexní pracovní postup používání aplikace Machine Learning Workbench k vývoji modelu: ![architektury](media/scenario-big-data/architecture.PNG)

V následujících částech vám ukážeme vývoje modelů pomocí funkce vzdálené výpočetní cíle v aplikaci Machine Learning Workbench. Jsme první malé množství ukázková data načíst a spusťte skript [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) na Ubuntu DSVM pro rychlé iterace. Budeme dále omezit pracovní uděláme v [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) předáním nadbytečný argument pro rychlejší iterace. Nakonec používáme clusteru služby HDInsight k trénování s úplná data.     

[ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) Načte soubor a připraví data a provádí vytváření funkcí. Přijímá dva argumenty:
* Konfigurační soubor pro kontejner úložiště objektů Blob pro ukládání výsledků zprostředkující výpočetní a modely.
* Argument konfigurace ladění pro rychlejší iterace.

První argument `configFilename`, je místnímu konfiguračnímu souboru, kam ukládat informace o úložiště objektů Blob a určete, kam chcete načíst data. Ve výchozím nastavení je to [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), a chce použít v datech měsíčního spustit. Také zahrnujeme [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), které je třeba použít na celou sadu dat. spustit. Obsah v konfiguraci je následujícím způsobem: 

| Pole | Typ | Popis |
|-----------|------|-------------|
| storageAccount | Řetězec | Název účtu služby Azure Storage |
| storageContainer | Řetězec | Kontejner v účtu Azure Storage k ukládání mezilehlých výsledků |
| klíč úložiště | Řetězec |Přístupový klíč účtu Azure Storage |
| datový soubor|Řetězec | Soubory zdroje dat  |
| doba trvání| Řetězec | Doba trvání dat ve zdrojových souborech dat|

Měnit parametry `Config/storageconfig.json` a `Config/fulldata_storageconfig.json` ke konfiguraci účtu úložiště, klíč úložiště a kontejner objektů blob pro ukládání průběžné výsledky. Ve výchozím kontejneru objektů blob pro jeden měsíc dat, spuštění je `onemonthmodel`, a je kontejner objektů blob pro úplnou datovou sadu spuštění `fullmodel`. Ujistěte se, že vytvoříte tyto dva kontejnery v účtu úložiště. `dataFile` Pole [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) nakonfiguruje, jaká data jsou načítána v [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). `duration` Pole konfiguruje rozsah data obsahují. Pokud doba trvání je nastavena na ONE_MONTH, třeba data načtena pouze jeden soubor .csv mezi sedm souborů dat z června 2016. Pokud doba trvání se plnou, je načten úplná sada dat (1 TB). Není nutné změnit `dataFile` a `duration` v těchto dvou konfigurační soubory.

Druhý argument je LADIT. Nastavení na FILTER_IP umožňuje rychlejší iterace. Použití tohoto parametru je užitečné, pokud chcete ladit skript.

> [!NOTE]
> Ve všech z následujících příkazů nahraďte jakoukoli proměnnou argument jeho skutečnou hodnotu.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Model vývoje Docker Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Nastavení cílové výpočetní prostředí

Spusťte příkazový řádek aplikace Machine Learning Workbench kliknutím na položku **souboru** > **otevřít příkazový řádek**. Potom následujícím příkazem: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Následující dva soubory jsou vytvořeny ve složce aml_config vašeho projektu:

-  dockerdsvm.COMPUTE: Tento soubor obsahuje informace o připojení a konfigurace pro vzdálené spuštění cíl.
-  dockerdsvm.runconfig: Tento soubor je sada možností spuštění použít v aplikaci Workbench.

Přejděte do dockerdsvm.runconfig a měnit konfiguraci z těchto polí takto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Příprava prostředí projektu spuštěním:

```az ml experiment prepare -c dockerdsvm```


S `PrepareEnvironment` nastavenou na hodnotu true, Machine Learning Workbench vytvoří běhové prostředí při každém odeslání úlohy. `Config/conda_dependencies.yml` a `Config/dsvm_spark_dependencies.yml` obsahovat přizpůsobení prostředí modulu runtime. Úpravou těchto dvou souborů YMAL můžete vždycky změnit závislosti systému Conda, konfiguraci Sparku a Spark závislosti. V tomto příkladu jsme přidali `azure-storage` a `azure-ml-api-sdk` jako dodatečné balíčky Pythonu v `Config/conda_dependencies.yml`. Přidali jsme také `spark.default.parallelism`, `spark.executor.instances`, a `spark.executor.cores` v `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Příprava dat a vytváření funkcí na DSVM Dockeru

Spusťte skript `etl.py` na DSVM Dockeru. Použijte ladicí parametr, který filtruje načtená data s IP adresami konkrétní server:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Přejděte na postranní panel a vyberte **spustit** zobrazíte historii spuštění tohoto `etl.py`. Všimněte si, že čas spuštění je asi 2 minuty. Pokud budete chtít změnit váš kód přikazující zahrnutí nových funkcí, poskytuje FILTER_IP jako druhý argument zajišťuje rychlejší iterace. Může být potřeba tento krok spustit více než jednou při práci s vlastním strojového učení problémy prozkoumat datové sady nebo vytvořit nové funkce. 

Vlastní omezení na jaká data načtena a další závislé filtrování toho, jaká data ke zpracování můžete urychlit proces iterace vývojem vašeho modelu. Jak můžete experimentovat, by měl pravidelně uložit změny v kódu do úložiště Git. Všimněte si, že jsme použili následující kód do `etl.py` umožňuje přístup k privátním kontejneru:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


V dalším kroku spusťte skript `etl.py` v Dockeru DSVM bez parametru ladění FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Přejděte na postranní panel a vyberte **spustit** zobrazíte historii spuštění tohoto `etl.py`. Všimněte si, že čas spuštění je přibližně čtyři minuty. Zpracovaná výsledek tohoto kroku se uloží do kontejneru a je načtena pro školení v train.py. Kromě toho řetězec indexery, kodér kanálů a standardní scalers se uloží do privátního kontejneru. Ty se používají v operacionalizace. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Cvičení modelu na DSVM Dockeru

Spusťte skript `train.py` na DSVM Dockeru:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Tento krok načte výsledky zprostředkující výpočetní ze spuštění `etl.py`a trénovat model strojového učení. Tento krok trvá asi 2 minuty.

Až úspěšně dokončíte experimentování ve službě na malá data, můžete nadále spouštět experimentování na úplné datové sadě. Můžete spustit pomocí stejný kód a potom experimentovat s argumentem a compute cíl změny.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Vývoj modelu v clusteru HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Vytvořte cílové výpočetní prostředí v aplikaci Machine Learning Workbench pro HDInsight cluster

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Následující dva soubory jsou vytvořeny ve složce aml_config:
    
-  myhdi.COMPUTE: Tento soubor obsahuje informace o připojení a konfigurace pro vzdálené spuštění cíl.
-  myhdi.runconfig: Tento soubor je sada možností spuštění použít v aplikaci Workbench.


Přejděte do myhdi.runconfig a měnit konfiguraci z těchto polí takto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Příprava prostředí projektu spuštěním:

```az ml experiment prepare -c myhdi```

Tento krok může trvat až sedm minut.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Příprava dat a vytváření funkcí v clusteru HDInsight

Spusťte skript `etl.py`, s plnou data v clusteru HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Tato úloha trvá poměrně dlouho (přibližně dvě hodiny), takže můžete využívat `-a` zakázat výstupní datové proudy. Při dostatečně uklidil, v **historie běhů**, můžete zobrazit protokoly ovladačů a kontroleru. Pokud máte větší cluster, můžete vždycky změnit konfiguraci konfigurací v `Config/hdi_spark_dependencies.yml` používat více instancí nebo jader. Například pokud máte cluster se čtyřmi. pracovní uzly, můžete zvýšit hodnotu `spark.executor.instances` od 5 do 7. Zobrazí se výstup v tomto kroku **fullmodel** kontejneru v účtu úložiště. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Cvičení modelu v clusteru HDInsight

Spusťte skript `train.py` v clusteru HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Tato úloha trvá poměrně dlouho (přibližně 30 minut), takže můžete využívat `-a` zakázat výstupní datové proudy.

#### <a name="run-history-exploration"></a>Zkoumání historie spuštění

Historii spuštění je funkce, která umožňuje sledování vaší služby experimentování ve službě Machine Learning Workbench. Ve výchozím nastavení sleduje dobu trvání experimentování. V našem konkrétním příkladu, při přesunu do úplné datové sady pro `Code/etl.py` v experimentování, zjistíme, že doba trvání výrazně zvyšuje. Můžete také protokolovat určité metriky pro účely sledování. Povolit sledování metrik, přidejte následující řádky kódu na jednoho souboru Pythonu:
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

Na pravém boční panel v aplikaci Workbench, přejděte do **spuštění** zobrazíte historii spuštění pro každý soubor v jazyce Python. Můžete také přejít do vašeho úložiště GitHub. Novou větev s názvem počínaje "AMLHistory," Vytvoření ke sledování změn, které jste provedli vašeho skriptu při každém spuštění. 


### <a name="operationalize-the-model"></a>Zprovoznění modelu

V této části zprovoznění modelu, který jste vytvořili v předchozím kroku jako webovou službu. Také se dozvíte, jak používat webová služba pro předpověď zatížení. Pomocí rozhraní příkazového řádku operacionalizace strojového jazyka (rozhraní příkazového řádku) zabalit kód a závislosti jako Image Dockeru a model publikujte jako kontejnerizované webové služby.

Příkazovém řádku v aplikaci Machine Learning Workbench můžete použít ke spuštění rozhraní příkazového řádku.  Můžete také spustit rozhraní příkazového řádku na Ubuntu Linuxu pomocí následujících [Průvodce instalací](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> Ve všech následujících příkazů nahraďte jeho skutečná hodnota libovolné proměnné argumentu. Trvá přibližně 40 minut na dokončení této části.
> 

Zvolte jedinečný řetězec jako prostředí operacionalizace. Tady používáme řetězec "[jedinečný]" k reprezentaci řetězce, které zvolíte.

1. Vytvořit prostředí pro operacionalizaci a vytvořte skupinu prostředků.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Všimněte si, že můžete používat službu Container Service jako prostředí s využitím `--cluster` v `az ml env setup` příkazu. Můžete zprovoznit model machine learning na [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Používá [Kubernetes](https://kubernetes.io/) pro automatizaci nasazení, škálování a správu kontejnerizovaných aplikací. Tento příkaz trvá přibližně 20 minut. Pomocí následujícího postupu zkontrolujte, pokud nasazení úspěšně proběhlo úspěšně: 

        az ml env show -g [unique]rg -n [unique]

   Nastavení prostředí nasazení, které jste právě vytvořili, spuštěním následujícího:

        az ml env set -g [unique]rg -n [unique]

2. Vytvoření a použití účtu služby Správa modelů. Chcete-li vytvořit účet správy modelů, spusťte následující příkaz:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Pomocí správy modelů operacionalizace spuštěním následujícího:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Účet správy modelů slouží ke správě modelů a webových služeb. Na webu Azure Portal uvidíte, že se vytvořil nový účet služby Správa modelů. Zobrazí se modely, manifesty, Image Dockeru a služby, které jsou vytvořené pomocí tohoto účtu služby Správa modelů.

3. Stáhněte si a zaregistrujte modely.

   Stáhněte si modelů v **fullmodel** kontejneru do svého místního počítače v adresáři kódu. Nestahovat parquet datový soubor s názvem "vmlSource.parquet." Není soubor modelu; je výsledek zprostředkující výpočetní prostředky. Můžete také znovu použít model soubory zahrnuté v úložišti Git. Další informace najdete v tématu [Githubu](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Přejděte `Model` následuje modely jako složku v rozhraní příkazového řádku a registrace:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Výstup jednotlivých příkazů obsahuje ID modelu, který je potřeba v dalším kroku. Uložte do textového souboru pro budoucí použití.

4. Vytvoření manifestu pro webovou službu.

   Manifest obsahuje kód pro webové služby, modely machine learning a závislosti prostředí modulu runtime. Přejděte `Code` složky v rozhraní příkazového řádku a spusťte následující příkaz:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Výstup obsahuje Identifikátor manifestu na další krok. 

   Budete mít všechno pod `Code` adresáře a vy můžete otestovat webservice.py spuštěním následujícího: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Vytvořte image Dockeru. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Výstup obsahuje ID bitové kopie k dalšímu kroku, tato image dockeru se používá ve službě Container Service. 

6. Nasazení webové služby do clusteru služby Container Service.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Výstup obsahuje ID služby. Budete muset využít k získání autorizačního klíče a adresu URL služby.

7. Volání webové služby v kódu Pythonu ke stanovení skóre v dávkách mini.

   Chcete-li získat autorizační klíč, použijte následující příkaz:

         az ml service keys realtime -i $ServiceID 

   Chcete-li získat službu adresa URL bodování použijte následující příkaz:

        az ml service usage realtime -i $ServiceID

   Upravit obsah `./Config/webservice.json` s požadovanou službu vyhodnocování adresy URL a autorizačního klíče. Zachovat "Nosiče" v původním souboru a nahradí část "xxx". 
   
   Přejděte do kořenového adresáře vašeho projektu a testování webové službě kvůli zkrácené dávkového vyhodnocování s použitím následující:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Škálování webové služby. 

   Další informace najdete v tématu [škálování operacionalizace v clusteru Azure Container Service](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>Další postup

V tomto příkladu se dozvíte, jak používat Machine Learning Workbench k trénování velkých objemů dat model strojového učení a zprovoznění trénovaného modelu. Zejména jste zjistili, jak nakonfigurovat a používat různými výpočetními cíle a spustit historie sledování metrik a používat během různých spuštění.

Můžete rozšířit kódu a prozkoumejte křížového ověření a hyperparametrické ladění. Další informace o křížového ověření a hyperparametrické ladění, naleznete v tématu [tento prostředek Githubu](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Další informace o vytváření prognóz časových řad, naleznete v tématu [tento prostředek Githubu](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).