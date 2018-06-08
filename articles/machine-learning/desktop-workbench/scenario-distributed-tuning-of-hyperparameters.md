---
title: Distribuované ladění z Hyperparameters pomocí Azure Machine Learning Workbench | Microsoft Docs
description: Tento scénář popisuje, jak to provést, distribuované ladění hyperparameters pomocí Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: c6eccda4329572a181b6a7e7e3870ace4bfac13b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832743"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Distribuované ladění z hyperparameters pomocí Azure Machine Learning Workbench

Tento scénář popisuje, jak pomocí Azure Machine Learning Workbench škálovat ladění z hyperparameters algoritmů strojového učení, které implementují scikit-další rozhraní API. Ukážeme, jak nakonfigurovat a použít vzdálené kontejner Docker a Spark cluster jako provádění back-end pro ladění hyperparameters.

## <a name="link-of-the-gallery-github-repository"></a>Odkaz úložiště GitHub Galerie
Toto je odkaz na veřejné úložiště GitHub: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Přehled případu použití

Mnoho algoritmy strojového učení mít jeden nebo více knoflíky, názvem hyperparameters. Tyto knoflíky povolit ladění algoritmů k optimalizaci výkonu přes budoucí data, měří podle metriky definované uživatelem (například přesnost AUC, RMSE). Data vědecký pracovník musí zajistit hodnoty hyperparameters při sestavování model přes Cvičná data a před zobrazuje budoucí testovacích datech. Jak podle může data známé školení jsme nastavit hodnoty hyperparameters, aby model má dobrý výkon přes neznámé testovací data? 
    
Oblíbené technika pro ladění hyperparameters je *mřížky vyhledávání* v kombinaci s *křížové ověření*. Křížového ověření je technika, který vyhodnocuje, jak dobře model trénink na sadu školení předpovídá přes testovací sada. Touto technikou jsme nejprve rozdělení datovou sadu na tisíc složení a pak cvičení časy tisíc algoritmus v kruhového dotazování. Provedeme to na všech ale jeden z složení nazývá "násobek uchovávat out". Průměrná hodnota metriky modelů tisíc jsme výpočetním přes složení tisíc uchovávat na více systémů. Tato průměrná hodnota volána *odhad výkonu ověřit mezi*, závisí na hodnotách hyperparameters použít při vytváření modelů kB. Při ladění hyperparameters, budeme prohledávat prostor candidate hyperparameter hodnoty a zjistit, že ty, které optimalizace výkonu křížové ověření odhad. Hledání mřížky je běžné technika vyhledávání. V mřížce hledání je místo hodnoty candidate více hyperparameters smíšený produkt sady candidate hodnoty jednotlivých hyperparameters. 

Mřížky vyhledávání pomocí křížového ověření může být časově náročná. Pokud algoritmus má pět hyperparameters každý s pěti hodnot candidate, použijeme složení tisíc = 5. Jsme dokončete vyhledávání mřížky podle cvičení 5<sup>6</sup>= 15625 modelů. Naštěstí mřížky vyhledávání pomocí křížového ověření je jednoduše paralelně zpracovatelné postupu a všechny tyto modely můžete Trénink paralelně.

## <a name="prerequisites"></a>Požadavky

* [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [instalace a vytvoření rychlý Start](../service/quickstart-installation.md) k instalaci nástroje Workbench a vytvořte účty.
* Tento scénář předpokládá, že jsou spuštěny Azure ML Workbench na Windows 10 nebo systému MacOS s modulu Docker místně nainstalován. 
* Pokud chcete spustit tento scénář s vzdálené kontejner Docker, zřídit Ubuntu datové vědy virtuálního počítače (DSVM) podle následujících [pokyny](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Doporučujeme použít virtuální počítač s minimálně 8 jader a 28 Gb paměti. D4 instance virtuálních počítačů mají takové kapacity. 
* Pokud chcete spustit tento scénář s clusterem Spark, zřízení clusteru Spark HDInsight pomocí následujících tyto [pokyny](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Doporučujeme vám, že cluster s následující konfigurací v záhlaví a pracovní uzly:
    - čtyři uzly pracovního procesu
    - osm jader
    - Paměť o velikosti 28 Gb  
      
  D4 instance virtuálních počítačů mají takové kapacity. 

     **Řešení potíží s**: Azure vaše předplatné může mít kvótu na počet jader, které lze použít. Portál Azure nepovoluje vytvoření clusteru s celkový počet jader překračuje kvótu. Najít kvóty, přejděte na portálu Azure v části předplatná, klikněte na předplatné použité k nasazení clusteru a potom klikněte na **využití + kvóty**. Obvykle kvóty se definují pro jednotlivé oblasti Azure a je možné nasadit cluster Spark v oblasti, kde je k dispozici dostatek volného jader. 

* Vytvořte účet úložiště Azure, který se použije k uložení datové sady. Postupujte podle [pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k vytvoření účtu úložiště.

## <a name="data-description"></a>Popis dat

Používáme [datovou sadu TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Tato datová sada má události z aplikací v mobilních telefonech. Cílem je k předvídání pohlaví a stáří kategorie mobilní telefon uživatele daného typu telefonu a události, které uživatel generují nedávno.  

## <a name="scenario-structure"></a>Struktura scénář
Tento scénář má více složek v úložišti GitHub. Kód a konfiguračních souborů jsou v **kód** , veškerá dokumentace je složka **dokumentace** složku a všechny bitové kopie jsou **bitové kopie** složky. Kořenová složka obsahuje soubor README, který obsahuje stručné souhrnné informace o tomto scénáři.

### <a name="getting-started"></a>Začínáme
Klikněte na ikonu Azure Machine Learning Workbench a spustit Azure Machine Learning Workbench vytvořte projekt ze šablony "Distribuované ladění z Hyperparameters". Najdete podrobné pokyny o tom, jak vytvořit nový projekt v [instalovat a vytvořte rychlý Start](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfigurace prostředí provádění
Ukážeme, jak spouštěním kódu v vzdálené kontejner Docker a Spark cluster. Začneme s popisem nastavení, které jsou společné pro obou prostředích. 

Používáme [scikit-Další](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), a [azure-storage](https://pypi.python.org/pypi/azure-storage) balíčky, které nejsou součástí výchozí kontejner Docker nástroje Azure Machine Learning Workbench. balíček Azure-storage vyžaduje instalaci [kryptografie](https://pypi.python.org/pypi/cryptography) a [azure](https://pypi.python.org/pypi/azure) balíčky. K instalaci těchto balíčků v kontejner Docker a uzly clusteru Spark, jsme upravit soubor conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Upravené conda\_dependencies.yml soubor je uložen v adresáři aml_config kurzu. 

V dalších krocích jsme prostředí pro spuštění připojit k účtu Azure. Klikněte na nabídku souborů z levého horního rohu AML Workbench. A zvolte "spusťte příkazový řádek". Spusťte v rozhraní příkazového řádku

    az login

Zobrazí zprávu

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Přejděte k této webové stránce, zadejte kód a přihlaste se k účtu Azure. Po provedení tohoto kroku spustit v rozhraní příkazového řádku

    az account list -o table

a najít ID předplatného Azure, který má váš účet AML Workbench prostoru. Nakonec spustit v rozhraní příkazového řádku

    az account set -s <subscription ID>

k připojení k předplatnému Azure.

V následujících dvou částech ukážeme, jak k dokončení konfigurace vzdálené docker a Spark cluster.

#### <a name="configuration-of-remote-docker-container"></a>Konfigurace vzdálené kontejner Docker

 Chcete-li nastavit vzdálené kontejner Docker, spusťte v rozhraní příkazového řádku

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

IP adresa, uživatelské jméno a heslo v DSVM. IP adresa DSVM naleznete v části Přehled DSVM stránky na portálu Azure:

![IP adresa virtuálního počítače](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfigurace clusteru Spark

Chcete-li nastavit Spark prostředí, spusťte v rozhraní příkazového řádku

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

s názvem clusteru, cluster SSH uživatelské jméno a heslo. Výchozí hodnota uživatelské jméno SSH je `sshuser`, pokud jste změnili při zřizování clusteru. Název clusteru naleznete v části Vlastnosti clusteru stránky na portálu Azure:

![Název clusteru](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Spark jako prostředí pro spuštění pro distribuované ladění hyperparameters jsme použít spark sklearn balíčku. Jsme spark_dependencies.yml soubor k instalaci tohoto balíčku, pokud se používá prostředí pro spuštění Spark upravil:

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Upravené spark\_dependencies.yml soubor je uložen v adresáři aml_config kurzu. 

### <a name="data-ingestion"></a>Přijímání dat
Kód v tomto scénáři se předpokládá, že jsou data uložena v úložišti objektů blob Azure. Ukážeme původně stažení data z lokality Kaggle do počítače a nahrajte ho do úložiště objektů blob. Potom jsme ukazují, jak číst data z úložiště objektů blob. 

Chcete-li data z Kaggle, přejděte na [datovou sadu stránky](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) a klikněte na tlačítko Stáhnout. Zobrazí se výzva k přihlášení do Kaggle. Po přihlášení budete přesměrováni zpět na stránku datovou sadu. Pak stáhněte nejprve sedm soubory v levém sloupci jejich výběrem a kliknutím na tlačítko Stáhnout. Celková velikost stažené soubory je 289 Mb. Pokud chcete nahrát tyto soubory do úložiště objektů blob, vytvořte kontejner úložiště objektů blob, datové sady, ve vašem účtu úložiště. Můžete to udělat tak, že přejdete na stránku účtu úložiště, kliknutím na objekty BLOB a potom kliknutím na + kontejner Azure. Jako název zadejte "datové sady a klikněte na tlačítko OK. Na následujících snímcích obrazovky popisují tyto kroky:

![Otevřete blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![otevřete kontejner](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Potom vyberte datovou sadu kontejneru ze seznamu a klikněte na tlačítko Nahrát. Portál Azure vám umožní nahrát víc souborů současně. V části "Nahrát objekt blob" klikněte na tlačítko složky, vyberte všechny soubory z datové sady, klikněte na Otevřít a klikněte nahrávání. Následující snímek obrazovky znázorňuje tyto kroky:

![Nahrát objekt blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Nahrávání souborů trvá několik minut, v závislosti na připojení k Internetu. 

V našem kódu používáme [sada SDK úložiště Azure](https://azure-storage.readthedocs.io/en/latest/) ke stažení datové sady z úložiště objektů blob pro aktuální prováděcí prostředí. Stahování se provádí v zatížení\_data() funkce ze souboru load_data.py. Pokud chcete použít tento kód, je třeba nahradit < ACCOUNT_NAME > a < ACCOUNT_KEY > zadejte název a primární klíč účtu úložiště, který je hostitelem datovou sadu. Zobrazí se název účtu v levém horním rohu stránky účtu úložiště Azure. Chcete-li získat účet klíč, vyberte přístupové klíče v Azure stránce úložiště účet (viz první snímek obrazovky v části přijímání dat) a poté zkopírujte dlouhý řetězec v prvním řádku sloupce klíče:
 
![Přístupový klíč](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Následující kód z funkce load_data() stáhne do jednoho souboru:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Všimněte si, že není potřeba load_data.py soubor spustit ručně. Je volána z jiných souborů později.

### <a name="feature-engineering"></a>Návrh funkcí
Kód pro výpočty všechny funkce je ve funkci\_engineering.py souboru. Není nutné ručně spustit feature_engineering.py souboru. Později ji bude volat z jiných souborů.

Nemůžeme vytvořit více sady funkcí:
* Jeden horkou kódování značka a model mobilní telefon (jeden\_aktivní\_brand_model funkce)
* Podíl události generované uživatelem v každý den v týdnu (den v týdnu\_hour_features funkce)
* Podíl události generované uživatelem v každou hodinu (den v týdnu\_hour_features funkce)
* Podíl události generované uživatelem v každé kombinaci den v týdnu a hodinu (den v týdnu\_hour_features funkce)
* Podíl události generované uživatelem v každé aplikaci (jeden\_aktivní\_app_labels funkce)
* Podíl událostí generovaných uživatele v každý popisek aplikace (jeden\_aktivní\_app_labels funkce)
* Podíl události generované uživatelem v každé kategorii aplikace (text\_category_features funkce)
* Ukazatel funkce kategorií aplikací, které byly použité k vygeneruje události (jeden\_hot_category funkce)

Tyto funkce byly INSPIROVANÉ jádra Kaggle [model lineární na aplikace a popisky](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Výpočet těchto funkcí vyžaduje značné množství paměti. Původně jsme se pokusili výpočetní funkce v místním prostředí s 16 GB paměti RAM. Jsme byli schopni výpočetní první čtyři sady funkcí, ale přijata, nedostatek paměti' Chyba při výpočtu páté sada funkcí. Výpočet sad první čtyři funkce je v souboru singleVMsmall.py a mohou být provedeny v místním prostředí tak, že spustíte 

     az ml experiment submit -c local .\singleVMsmall.py   

v okně příkazového řádku.

Vzhledem k tomu, že místní prostředí je příliš malá pro výpočty, že všechny sady funkcí, jsme přepnout na vzdálené DSVM, který má větší paměti. Provádění uvnitř DSVM provádí uvnitř kontejner Docker, který je spravovaný nástrojem AML Workbench. Pomocí této DSVM jsme se moct výpočetní všechny funkce a cvičení modely a ladit hyperparameters (viz další část). soubor singleVM.py obsahuje kompletní funkci výpočtu a modelování kódu. V další části jsme se ukazují, jak spouštět singleVM.py ve vzdálené DSVM. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Ladění pomocí vzdáleného DSVM hyperparameters
Používáme [xgboost](https://anaconda.org/conda-forge/xgboost) zvyšovat skóre přechodu stromu implementace [1]. Používáme [scikit-Další](http://scikit-learn.org/) balíčku pro optimalizaci hyperparameters xgboost. I když xgboost není součástí scikit-další balíčku, implementuje scikit-další rozhraní API a proto je možné společně s hyperparameter ladění funkce scikit-Další informace. 

Xgboost má osm hyperparameters, popsané [sem](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* dílčí
* Cíl  
 
Na začátku použít vzdálené DSVM a ladit hyperparameters z malých mřížky candidate hodnot:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Mřížce má čtyři kombinace hodnot hyperparameters. Používáme 5-fold křížového ověření, což vede k 4 × 5 = 20 spustí z xgboost. K měření výkonu modelů, použijeme metrika ztrátě záporné protokolu. Následující kód vyhledá hodnoty hyperparameters z mřížky, které maximalizovat ztrátě ověřit mezi záporné protokolu. Kód také používá tyto hodnoty pro trénování modelu konečné přes úplné trénovací sady:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Po vytvoření modelu, uložíme výsledky hyperparameter ladění. Používáme protokolování Workbench AML rozhraní API pro uložení doporučené hodnoty hyperparameters a odpovídající ověřit mezi odhad ztráty záporné protokolu:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Jsme také vytvořit soubor sweeping_results.txt mezi ověřen, záporné protokolu ztráty všechny kombinace hodnot hyperparameter v mřížce.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Tento soubor je uložen ve speciální. / výstupy adresáře. Později na ukážeme, jak se stáhne.  

 Před spuštěním singleVM.py ve vzdálené DSVM poprvé, vytvoříme spuštěním existuje kontejner Docker 

    az ml experiment prepare -c dsvm

v rozhraní příkazového řádku systému windows. Kontejner vytvoření Docker trvá několik minut. Potom jsme spusťte singleVM.py v DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Tento příkaz dokončí v 1 hodina 38 minut po DSVM má 8 jader a 28 Gb paměti. Zaznamenané hodnoty lze zobrazit v okně Spustit historie nástroje AML Workbench:

![Historie spouštění](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Ve výchozím nastavení historii běhů okno zobrazuje hodnoty a grafy první zaznamenané hodnoty 1 – 2. Pokud chcete zobrazit úplný seznam vybrané hodnoty hyperparameters, klikněte na ikonu nastavení označené jako červené kolečko v předchozím snímku obrazovky. Pak vyberte hyperparameters, která se má zobrazit v tabulce. Také vyberte grafy, které jsou zobrazeny v horní části okna historii běhů, klikněte na ikonu nastavení označené jako modrý kruh a vyberte v grafech ze seznamu. 

Vybrané hodnoty hyperparameters můžete také prověřit, v okně Vlastnosti spustit: 

![Spustit vlastnosti](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

V pravém horním rohu okna Vlastnosti spustit je oddíl výstupní soubory se seznamem všech souborů, které byly vytvořeny v '. \output' složky. komínů\_výsledky.txt si můžete stáhnout zde jeho výběrem a kliknutím na tlačítko Stáhnout. sweeping_results.txt musí mít následující výstup:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Ladění hyperparameters pomocí clusteru Spark
Horizontální navýšení kapacity ladění hyperparameters a používat větší mřížky používáme clusteru Spark. Je naše nové mřížky

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Mřížce má 16 kombinace hodnot hyperparameters. Vzhledem k tomu, že používáme 5-fold křížového ověření, jsme běžet xgboost 16 × 5 = 80 časy.

scikit-další balíček nemá nativní podporu služby ladění hyperparameters pomocí clusteru Spark. Naštěstí [spark sklearn](https://spark-packages.org/package/databricks/spark-sklearn) balíček z Databricks doplní během této poměrně. Tento balíček poskytuje GridSearchCV funkce, která má skoro stejný rozhraní API jako funkce GridSearchCV v scikit-Další informace. Použít spark sklearn a ladit hyperparameters pomocí Spark je potřeba vytvořit Spark kontext

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Potom jsme nahradit 

    from sklearn.model_selection import GridSearchCV

with 

    from spark_sklearn import GridSearchCV

Také jsme nahradit volání GridSearchCV z scikit-postup ze spark sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Poslední kódu k ladění hyperparameters pomocí Spark v distribuci\_sweep.py souboru. Rozdíl mezi singleVM.py a distributed_sweep.py je v definici mřížky a další čtyři řádky kódu. Všimněte si také, že kvůli AML Workbench služby kód protokolování nezmění při změně prostředí pro spuštění ze vzdáleného DSVM na clusteru Spark.

Než spustíte distributed_sweep.py v clusteru Spark poprvé, potřebujeme nainstalovat balíčky Pythonu existuje. Toho lze dosáhnout spuštěním 

    az ml experiment prepare -c spark

v rozhraní příkazového řádku systému windows. Tato instalace trvá několik minut. Potom jsme distributed_sweep.py běžet na clusteru Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Tento příkaz dokončí v 1 hodina 6 minut po clusteru Spark má 6 uzlů pracovního procesu s 28 Gb paměti. Výsledky ladění hyperparameter přístupná v nástroji Azure Machine Learning Workbench stejným způsobem jako vzdálené spuštění DSVM. (konkrétně protokoly, doporučené hodnoty hyperparameters a sweeping_results.txt soubor)

### <a name="architecture-diagram"></a>Diagram architektury

Následující diagram znázorňuje celkového pracovního postupu: ![architektura](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Závěr 

V tomto scénáři jsme vám ukázal, jak pomocí Azure Machine Learning Workbench provést optimalizaci hyperparameters ve vzdálené virtuální počítače a clustery Spark. Jsme viděli, že Azure Machine Learning Workbench poskytuje nástroje pro snadné konfiguraci prostředí provádění. Také umožňuje snadno přepínání mezi nimi. 

## <a name="references"></a>Odkazy

[1] T. Svoboda a C. Guestrin. [XGBoost: Škálovatelná stromu zvyšovat skóre systému](https://arxiv.org/abs/1603.02754). KDD 2016.




