---
title: Distribuované ladění z Hyperparameters pomocí Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Tento scénář popisuje, jak provést distribuované ladění hyperparametrů pomocí Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 920b019640df9d2da174101e2b1b90dfd4da6f56
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578731"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Distribuované ladění hyperparametrů pomocí Azure Machine Learning Workbench

Tento scénář popisuje, jak používat Azure Machine Learning Workbench pro horizontální navýšení kapacity ladění hyperparametrů algoritmů strojového učení, které implementují scikit-další rozhraní API. Vám ukážeme, jak nakonfigurovat a použít vzdáleném kontejneru Dockeru a Spark cluster jako back-endu spuštění pro ladění hyperparameters.

## <a name="link-of-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie
Tady je odkaz na veřejné úložiště GitHub: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Přehled případu použití

Mnoho algoritmů strojového učení mít jeden nebo více knoflíky, volá hyperparameters. Tyto knoflíky povolit ladění algoritmy pro optimalizaci výkonu budoucí daty prováděno podle zadaného uživatelem metriky (například přesnost AUC, RMSE). Mezi odborníky přes data je potřeba zadat hodnoty hyperparametrů při vytváření modelu přes trénovacích dat a než data budoucích testů. Jak podle dat může známé školení nastavíme hodnoty hyperparameters tak, aby model má neznámý testovací data dobrý výkon? 
    
Je technika oblíbených pro ladění hyperparameters *mřížky hledání* v kombinaci s *křížového ověření*. Křížové ověření je technika, která vyhodnocuje, jak dobře model, školení na trénovací sady předpovídá v testovací sady. Tímto způsobem jsme nejprve rozdělte do K přeložení datovou sadu a pak natrénování K časy algoritmus způsobem kruhové dotazování. Provedeme to ve všech, ale jedna složení nazývá "nachází mimo fold". Můžeme následujícím způsobem vypočítat průměrná hodnota metriky modely K přes K uložené mimo složení. Tuto průměrnou hodnotu volána *odhad výkonu křížové ověření*, závisí na hodnotách hyperparameters použili při vytváření modelů tis. Při ladění hyperparameters, budeme prohledávat místo hodnoty hyperparameter Release candidate a zjistit, že ty, které optimalizovat výkon křížového ověření odhadnout. Hledání mřížky je běžná technika vyhledávání. V mřížce vyhledávání je místo hodnoty Release candidate více hyperparametrů mezi produkty sady hodnot Release candidate jednotlivé hyperparametrů. 

Mřížka hledání s využitím křížového ověření může být časově náročné. Algoritmus, který má pět hyperparameters každý s pěti hodnot Release candidate, použijeme K = 5 složení. Dokončíme hledat tabulky podle cvičení 5<sup>6</sup>= 15625 modely. Naštěstí mřížky vyhledávání pomocí křížového ověření je jednoduše paralelně zpracovatelné postup a tyto modely mohou Trénink paralelně.

## <a name="prerequisites"></a>Požadavky

* [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [instalace a vytvoření rychlý Start](../service/quickstart-installation.md) aplikaci Workbench můžete nainstalovat a vytvořte účty.
* Tento scénář předpokládá, že spustíte aplikaci Azure ML Workbench ve Windows 10 nebo MacOS s místně nainstalovaný modul Docker. 
* Chcete-li spustit scénář s vzdáleném kontejneru Dockeru, zřiďte Ubuntu dat virtuálního počítače VĚDY podle [pokyny](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Doporučujeme použít virtuální počítač s alespoň s 8 jádry a 28 Gb paměti. D4 instance virtuálních počítačů mají takové kapacity. 
* Pokud chcete spustit tento scénář s clusterem Spark, zřízení clusteru HDInsight Spark pomocí těchto [pokyny](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Doporučujeme vám, máte v záhlaví a pracovní uzly clusteru s použitím následující konfigurace:
    - čtyři uzly pracovního procesu
    - osmi jádry
    - 28 Gb paměti  
      
  D4 instance virtuálních počítačů mají takové kapacity. 

     **Řešení potíží s**: vašeho předplatného Azure může mít kvóta počtu jader, které lze použít. Na webu Azure portal neumožňuje vytvoření clusteru se celkový počet jader překročení této kvóty. Chcete-li najít kvótu, přejděte na webu Azure portal do části předplatná, klikněte na předplatné použité k nasazení clusteru a potom klikněte na **využití a kvóty**. Obvykle kvóty jsou definovány v jedné oblasti Azure a můžete také nasadit cluster Spark v oblasti, kde máte dostatek volných jader. 

* Vytvoření účtu služby Azure storage, který se použije k uložení datové sady. Postupujte podle [pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k vytvoření účtu úložiště.

## <a name="data-description"></a>Popis dat

Používáme [datovou sadu TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Tato datová sada obsahuje události z aplikací v mobilní telefony. Cílem je předpovědět, pohlaví nebo věku kategorie mobilní telefon uživatele daný typ telefonu a události, které uživatel generují nedávno.  

## <a name="scenario-structure"></a>Struktura scénář
Tento scénář má více složek v úložišti GitHub. Kód a konfigurační soubory jsou v **kód** , veškerou dokumentaci je složka **dokumentace** složka a všechny bitové kopie jsou **Imagí** složky. Kořenová složka obsahuje soubor README, který obsahuje stručný popis tohoto scénáře.

### <a name="getting-started"></a>Začínáme
Klikněte na ikonu aplikace Azure Machine Learning Workbench a spusťte aplikaci Azure Machine Learning Workbench vytvořte projekt ze šablony "Distribuované ladění z Hyperparameters". Můžete najít podrobné pokyny o tom, jak vytvořit nový projekt v [instalace a vytvoření rychlý Start](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfigurace spouštěcí prostředí
Vám ukážeme, jak náš kód se spouští ve vzdáleném kontejneru Dockeru a v clusteru Spark. Začínáme s popisem nastavení, která jsou společná pro obě prostředí. 

Používáme [scikit-informace](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), a [služby azure storage](https://pypi.python.org/pypi/azure-storage) balíčky, které nejsou k dispozici ve výchozím kontejneru Dockeru ze služby Azure Machine Learning Workbench. vyžaduje instalaci balíčku Azure-storage [kryptografie](https://pypi.python.org/pypi/cryptography) a [azure](https://pypi.python.org/pypi/azure) balíčky. K instalaci těchto balíčků v kontejneru Dockeru a uzlů v clusteru Spark, upravíme conda_dependencies.yml souboru:

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

V dalších krocích jsme prostředí pro spouštění připojení k účtu Azure. Klikněte na nabídku soubor z levého horního rohu aplikace Workbench AML. A zvolte možnost "otevřít příkazový řádek". Potom spusťte v rozhraní příkazového řádku

    az login

Dostat zprávu

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Přejděte na tuto webovou stránku, zadejte kód a přihlaste se do svého účtu Azure. Po provedení tohoto kroku spustit v rozhraní příkazového řádku

    az account list -o table

a najít ID předplatného Azure, který má váš účet pracovního prostoru aplikace Workbench AML. Nakonec spusťte nástroj v rozhraní příkazového řádku

    az account set -s <subscription ID>

Abyste mohli dokončit připojení k předplatnému Azure.

V následujících dvou částech vám ukážeme, jak dokončit konfiguraci vzdáleného dockeru a clusteru Spark.

#### <a name="configuration-of-remote-docker-container"></a>Konfigurace vzdáleném kontejneru Dockeru

 Chcete-li nastavit vzdáleném kontejneru Dockeru, spusťte v rozhraní příkazového řádku

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

s IP adresu, uživatelské jméno a heslo v DSVM. IP adresa DSVM najdete v oddílu přehled DSVM stránky na webu Azure portal:

![IP adresa virtuálního počítače](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfigurace clusteru Spark

Chcete-li nastavit prostředí Spark, spusťte v rozhraní příkazového řádku

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

Název clusteru, uživatelské jméno SSH clusteru a heslo. Výchozí hodnota uživatelské jméno SSH je `sshuser`, pokud jste změnili během zřizování clusteru. Název clusteru najdete v části Vlastnosti clusteru stránky na webu Azure portal:

![Název clusteru](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Používáme balíček skriptu spark-sklearn mít Spark jako spouštěcí prostředí pro distribuované ladění hyperparametrů. Jsme modifikovali spark_dependencies.yml sloužící k instalaci tohoto balíčku, při použití prostředí pro spouštění Spark:

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
Kód v tomto scénáři se předpokládá, že jsou data uložená ve službě Azure blob storage. Ukazujeme nejdřív stáhnout data z lokality Kaggle do svého počítače a nahrajte ho do úložiště objektů blob. Pak vám ukážeme, jak přečíst data z úložiště objektů blob. 

Stáhnout data z Kaggle, přejděte na [datovou sadu stránky](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) a klikněte na tlačítko Stáhnout. Zobrazí výzva k přihlášení do Kaggle. Po přihlášení budete přesměrováni zpět na stránku datové sady. Pak si stáhněte nejprve sedm souborů v levém sloupci vyberte je a klikněte na tlačítko Stáhnout. Celková velikost stažené soubory je 289 Mb. K odeslání těchto souborů do úložiště objektů blob, vytvoření kontejneru úložiště objektů blob 'dataset' ve vašem účtu úložiště. Uděláte to tak, že přejdete na stránku Azure z vašeho účtu úložiště, kliknutím na objekty BLOB a potom kliknutím na + kontejner. Zadejte 'parametr dataset' jako název a klikněte na tlačítko OK. Na následujících snímcích obrazovky ukazují tyto kroky:

![Otevřít objekt blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![otevřít kontejneru](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Potom vyberte kontejner datovou sadu ze seznamu a klikněte na tlačítko Nahrát. Portál Azure vám umožní nahrát více souborů současně. V části "Nahrát objekt blob" klikněte na tlačítko složku, vyberte všechny soubory z datové sady, klikněte na Otevřít a pak klikněte na tlačítko Odeslat. Následující snímek obrazovky znázorňuje tyto kroky:

![Nahrát objekt blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Nahrávání souborů trvá několik minut, v závislosti na připojení k Internetu. 

V našem kódu používáme [sadu SDK služby Azure Storage](https://docs.microsoft.com/python/azure/) stáhnout datovou sadu z úložiště objektů blob pro aktuální prováděcí prostředí. Stahování se provádí v zatížení\_data() funkce ze souboru load_data.py. Chcete-li tento kód použít, je třeba nahradit < název_účtu > a < ACCOUNT_KEY > s názvem a primární klíč účtu úložiště, který je hostitelem datové sady. Zobrazí se název účtu v levém horním rohu stránky vašeho účtu úložiště Azure. Získat účet klíč, vyberte přístupové klávesy stránce na webu Azure Storage account (zobrazit první snímek obrazovky sekce příjem dat) a zkopírujte dlouhý řetězec v první řádek klíčový sloupec:
 
![Přístupový klíč](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Následující kód z funkce load_data() soubory ke stažení jednoho souboru:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Všimněte si, že není nutné ke spuštění souboru load_data.py ručně. Je volána z jiných souborů později.

### <a name="feature-engineering"></a>Návrh funkcí
Kód pro výpočet všechny funkce je ve funkci\_engineering.py souboru. Není nutné ke spuštění souboru feature_engineering.py ručně. Později bude zavolána z jiných souborů.

Vytvoříme několik sad funkcí:
* Jedna hot kódování značky a modelu mobilní telefon (jeden\_horké\_brand_model funkce)
* Část události generované modulem uživatele v každý den v týdnu (den v týdnu\_hour_features funkce)
* Část události generované modulem uživatele za každou hodinu (den v týdnu\_hour_features funkce)
* Část události generované modulem uživatelů v každé kombinaci den v týdnu a hodinu (den v týdnu\_hour_features funkce)
* Část události generované modulem uživatelů v každé aplikaci (jeden\_horké\_app_labels funkce)
* Část události generované modulem uživatelů v každé aplikaci popisku (jeden\_horké\_app_labels funkce)
* Část události generované modulem uživatelů v každé kategorii aplikace (text\_category_features funkce)
* Ukazatel funkce pro kategorie aplikací, které se používá k vygeneruje události (jeden\_hot_category funkce)

Tyto funkce se inspirovat Kaggle jádra [lineární model aplikací a popisky](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Výpočet tyto funkce vyžaduje značné množství paměti. Nejprve pokusili jsme se výpočetní funkce v místním prostředí s 16 GB paměti RAM. Jsme byli schopni compute první čtyři sady funkcí, ale byla přijata chyba "nedostatek paměti' při výpočtu páté sadě funkcí. Výpočet sady první čtyři funkcí je v souboru singleVMsmall.py a mohou být provedeny v místním prostředí spuštěním 

     az ml experiment submit -c local .\singleVMsmall.py   

v okně příkazového řádku.

Místní prostředí je příliš malá pro výpočty, že všechny sady funkcí, můžeme Přepnout na vzdálené DSVM, který má větší kapacitu paměti. Spuštění uvnitř DSVM se provádí v kontejneru Dockeru, který je spravovaný nástrojem AML Workbench. Pomocí této DSVM nemůžeme výpočetních funkcí a trénování modelů a ladění hyperparameters (viz další část). singleVM.py soubor obsahuje kód pro modelování a kompletní funkce výpočtu. V další části vám ukážeme, jak spustit singleVM.py ve vzdálené DSVM. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Ladění pomocí vzdáleného DSVM hyperparameters
Používáme [xgboost](https://anaconda.org/conda-forge/xgboost) zvýšení skóre přechodu stromu implementace [1]. Použijeme také [scikit-informace](http://scikit-learn.org/) balíček, který chcete ladit hyperparameters xgboost. Přestože xgboost není součástí scikit-další balíček, implementuje scikit-další rozhraní API a proto ji můžete použít společně s hyperparameter ladění funkce scikit-Další informace. 

Xgboost má osm hyperparameters popsané [tady](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* dílčí
* Cíl  
 
Na začátku použít vzdálené DSVM a vyladit hyperparameters z Jemná mřížka Release candidate hodnot:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Tato mřížka obsahuje čtyři kombinace hodnot hyperparametrů. Používáme 5-fold křížového ověření, výsledkem je 4 × 5 = 20 spuštěními xgboost. K měření výkonu modelů používáme metrika ztráty negativní protokolu. Následující kód najde hodnoty hyperparameters z mřížky, který maximalizovat ztráty křížové ověření negativní protokolu. Kód také používá tyto hodnoty pro trénování modelu konečné přes úplné trénovací sady:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Po vytvoření modelu, můžeme uložit výsledky hyperparametrů. Uložte doporučené hodnoty hyperparameters a odpovídající křížové ověření odhad ztráty negativní protokolu používáme protokolování rozhraní API AML Workbench:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Můžeme také vytvořit soubor sweeping_results.txt s křížové ověření, záporná protokolu ztráty všechny kombinace hodnot hyperparameter v mřížce.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Tento soubor je uložen ve zvláštním. / výstupy adresáře. Později vám ukážeme, jak ho můžete stáhnout.  

 Před spuštěním singleVM.py ve vzdálené DSVM poprvé, vytvoříme kontejner Dockeru existuje spuštěním 

    az ml experiment prepare -c dsvm

v rozhraní příkazového řádku systému windows. Kontejner Dockeru vytvoření trvá několik minut. Následně provozujeme v DSVM singleVM.py:

    az ml experiment submit -c dsvm .\singleVM.py

Tento příkaz se dokončí v 1 hodina 38 minut po DSVM má 8 jader a 28 Gb paměti. V okně historie spuštění aplikace Workbench AML lze zobrazit zaznamenané hodnoty:

![Historie spuštění](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Ve výchozím nastavení historie běhů okno zobrazuje hodnoty a grafy první zaznamenané hodnoty 1-2. Pokud chcete zobrazit úplný seznam vybrané hodnoty hyperparameters, klikněte na ikonu nastavení označené červené kolečko v předchozím snímku obrazovky. Vyberte hyperparameters zobrazený v tabulce. Také vyberte v grafech, které jsou zobrazeny v horní části okna historie spuštění, klikněte na ikonu nastavení označené kruh a vyberte v grafech ze seznamu. 

Vybrané hodnoty hyperparameters můžete prozkoumat také v okně vlastností spuštění: 

![Vlastnosti běhu](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

V pravém horním rohu okna vlastností spuštění je část výstupních souborů se seznamem všech souborů, které byly vytvořeny v ". \output" složky. cílit na konkrétní\_výsledky.txt si můžete stáhnout z něj vyberte ho a klikněte na tlačítko Stáhnout. sweeping_results.txt by měl mít následující výstup:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Ladění hyperparameters pomocí clusteru Spark
Horizontální navýšení kapacity hyperparameters optimalizace a použít větší mřížky používáme clusteru Spark. Je naše nové mřížky

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Tato mřížka má 16 kombinace hodnot hyperparametrů. Protože používáme 5-fold křížového ověření, zajišťuje každodenní provoz xgboost 16 × 5 = 80 časy.

scikit-informace balíček nemá nativní podporu ladění hyperparameters pomocí clusteru Spark. Naštěstí [skriptu spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) balíček z Databricks vyplní překonání tohoto rozdílu. Tento balíček poskytuje GridSearchCV funkce, která má téměř stejné rozhraní API jako GridSearchCV funkce v scikit-informace. Použití sparku skriptu sklearn a ladění pomocí Sparku hyperparameters potřebujeme vytvořit kontext Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Pak nahradíme 

    from sklearn.model_selection import GridSearchCV

with 

    from spark_sklearn import GridSearchCV

Také jsme nahraďte volání GridSearchCV z scikit-se naučíte, jak z spark skriptu sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Konečný kód pro ladění hyperparameters pomocí Sparku je v distribuované\_sweep.py souboru. Rozdíl mezi singleVM.py a distributed_sweep.py je v definici tabulky a další čtyři řádky kódu. Všimněte si také, že kvůli službám AML Workbench kód protokolování nezmění při změně prostředí pro spouštění z vzdálené DSVM ke clusteru Spark.

Před spuštěním distributed_sweep.py v clusteru Spark poprvé, potřebujeme nainstalovat balíčky Pythonu existuje. Toho lze dosáhnout spuštěním 

    az ml experiment prepare -c spark

v rozhraní příkazového řádku systému windows. Tato instalace trvá několik minut. Po tomto spustíme v clusteru Spark distributed_sweep.py:

    az ml experiment submit -c spark .\distributed_sweep.py

Tento příkaz se dokončí v 1 hodina 6 minut po clusteru Spark má 6 pracovní uzly bez 28 Gb paměti. Výsledky hyperparametrů je možný stejným způsobem jako vzdálené spuštění DSVM v aplikaci Azure Machine Learning Workbench. (konkrétně protokoly, doporučené hodnoty hyperparameters a sweeping_results.txt souboru)

### <a name="architecture-diagram"></a>Diagram architektury

Následující diagram znázorňuje celkového pracovního postupu: ![architektury](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Závěr 

V tomto scénáři jsme vám ukázali jak používat Azure Machine Learning Workbench k provedení ladění hyperparametrů ve virtuálním počítačům a clustery Spark. Jsme viděli, že Azure Machine Learning Workbench poskytuje nástroje pro snadnou konfiguraci pracovních prostředích. Také umožňuje snadno přepínat mezi nimi. 

## <a name="references"></a>Odkazy

[1] T. Svoboda a C. Guestrin. [XGBoost: Škálovatelná stromu posilování systému](https://arxiv.org/abs/1603.02754). KONFERENCE KDD 2016.




