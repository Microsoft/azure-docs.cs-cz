---
title: Kurz nasazení modelu pro službu Azure Machine Learning
description: V tomto kurzu na pokračování se dozvíte, jak komplexně používat službu Azure Machine Learning. Toto je třetí část, ve které se probírá nasazení modelu.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2eb6eb5090b0a68a189e2d4f1148d3238bc3ee0d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946608"
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>Kurz 3: Klasifikace Iris – Nasazení modelu

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Azure Machine Learning (Preview) představuje integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce. Datoví vědci pomocí nich můžou připravovat data, vyvíjet experimenty a nasazovat modely na úrovni cloudu.

Tento kurz je **třetí částí z třídílné série**. V této části kurzu provedete pomocí služby Machine Learning (Preview) následující úlohy:

> [!div class="checklist"]
> * Vyhledání souboru modelu
> * Vygenerování hodnoticího skriptu a souboru schématu
> * Příprava prostředí
> * Vytvoření webové služby v reálném čase
> * Spuštění webové služby v reálném čase
> * Zkoumání výstupních dat objektů blob 

Tento kurz používá nadčasovou [datovou sadu Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
- Účet Experimentování a nainstalovanou aplikaci Azure Machine Learning Workbench, jak je popsáno v tomto [rychlém startu](quickstart-installation.md).
- Model klasifikace z [části 2 tohoto kurzu](tutorial-classifying-iris-part-2.md).
- Nainstalovaný a místně spuštěný modul Docker.

## <a name="download-the-model-pickle-file"></a>Stažení souboru pickle modelu
V předchozí části kurzu se skript **iris_sklearn.py** spouštěl místně v aplikaci Machine Learning Workbench. Tento krok zajistil serializaci modelu logistické regrese pomocí oblíbeného balíčku serializace objektů Python [pickle](https://docs.python.org/3/library/pickle.html). 

1. Otevřete aplikaci Machine Learning Workbench. Pak otevřete projekt **myIris**, který jste vytvořili v předchozích částech této série kurzů.

1. Po otevření projektu výběrem tlačítka **Soubory** (ikona složky) v levém podokně otevřete seznam souborů ve složce vašeho projektu.

1. Vyberte soubor **iris_sklearn.py**. V aplikaci Workbench se na nové kartě textového editoru otevře kód Pythonu.

1. Podívejte se do souboru **iris_sklearn.py**, abyste věděli, kde se soubor pickle vygeneroval. Pomocí kombinace kláves CTRL+F otevřete dialogové okno **Vyhledávání** a potom v kódu Pythonu vyhledejte slovo **pickle**.

   Tento fragment kódu ukazuje, jak se vygeneroval výstupní soubor pickle. Výstupní soubor pickle má na disku název **model.pkl**. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

1. Vyhledejte soubor pickle modelu ve výstupních souborech některého z předchozích spuštění.
   
   Po spuštění skriptu **iris_sklearn.py** se soubor modelu zapsal do složky **outputs** s názvem **model.pkl**. Tato složka se nachází ve spouštěcím prostředí, které pro spuštění skriptu vyberete, a ne v místní složce projektu. 
   
   a. Pokud chcete soubor vyhledat, výběrem tlačítka **Spuštění** (ikona hodin) v levém podokně otevřete seznam **Všechna spuštění**. 

   b. Otevře se karta **Všechna spuštění**. V tabulce spuštění vyberte některé z nedávných spuštění s cílem **local** a názvem skriptu **iris_sklearn.py**. 

   c. Otevře se podokno **Vlastnosti spuštění**. V pravé horní části podokna si všimněte části **Výstupy**.

   d. Stáhněte soubor pickle tím, že zaškrtnete políčko vedle souboru **model.pkl** a pak vyberete **Stáhnout**. Uložte soubor do kořenové složky projektu. Tento soubor je potřeba v následujících krocích.

   ![Stažení souboru pickle](media/tutorial-classifying-iris/download_model.png)

   Další informace o složce `outputs` najdete v tématu [Čtení a zapisování velkých datových souborů](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Získání hodnoticího skriptu a souborů schématu
Pokud společně se souborem modelu chcete nasadit i webovou službu, potřebujete také hodnoticí skript. Volitelně potřebujete schéma pro vstupní data webové služby. Hodnoticí skript načte soubor **model.pkl** z aktuální složky a použije ho k vytvoření nových předpovědí.

1. Otevřete aplikaci Machine Learning Workbench. Pak otevřete projekt **myIris**, který jste vytvořili v předchozí části této série kurzů.

1. Po otevření projektu výběrem tlačítka **Soubory** (ikona složky) v levém podokně otevřete seznam souborů ve složce vašeho projektu.

1. Vyberte soubor **score_iris.py**. Otevře se skript Pythonu. Tento soubor se používá jako soubor vyhodnocení.

   ![Soubor vyhodnocení](media/tutorial-classifying-iris/model_data_collection.png)

1. Pokud chcete získat soubor schématu, spusťte skript. Na příkazovém řádku vyberte prostředí **local** a skript **score_iris.py** a pak vyberte **Spustit**. 

   Tento skript vytvoří v části **Výstupy** soubor JSON, ve kterém je zaznamenané schéma vstupních dat vyžadované modelem.

1. Všimněte si podokna **Úlohy** na pravé straně podokna **Řídicí panel projektu**. Počkejte, až se u nejnovější úlohy **score_iris.py** zobrazí zelený stav **Dokončeno**. Potom vyberte hypertextový odkaz **score_iris.py** u nejnovějšího spuštění úlohy a prohlédněte si podrobnosti o spuštění. 

1. V podokně **Vlastnosti spuštění** v části **Výstupy** vyberte nově vytvořený soubor **service_schema.json**. Zaškrtněte políčko vedle názvu souboru a pak vyberte **Stáhnout**. Uložte soubor do kořenové složky projektu.

1. Vraťte se na předchozí kartu, na které jste otevřeli skript **score_iris.py**. Díky použití shromažďování dat můžete zaznamenávat vstupy modelu a předpovědi z webové služby. Pro shromažďování dat jsou zajímavé především následující kroky.

1. Projděte si kód v horní části souboru, který importuje třídu **ModelDataCollector**, protože obsahuje funkci shromažďování dat modelu:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

1. Projděte si následující řádky kódu ve funkci **init()**, která vytváří instanci třídy **ModelDataCollector**:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

1. Projděte si následující řádky kódu ve funkci **run(input_df)**, která shromažďuje data vstupu a předpovědí:

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Teď můžete začít připravovat své prostředí na zprovoznění modelu.

## <a name="prepare-to-operationalize-locally-for-development-and-testing-your-service"></a>Příprava na místní zprovoznění [pro vývoj a testování vaší služby]
Pomocí nasazení v _místním režimu_ proveďte spuštění v kontejnerech Docker v místním počítači.

_Místní režim_ můžete použít pro vývoj a testování. K provedení následujících kroků pro zprovoznění modelu je potřeba, aby byl modul Docker spuštěný místně. Pomocí příznaku `-h` na konci každého příkazu můžete zobrazit odpovídající zprávu nápovědy.

>[!NOTE]
>Pokud nemáte modul Docker v místním počítači, můžete místo toho v Azure vytvořit cluster k nasazení. Cluster můžete ponechat pro opakované použití nebo ho po těchto kurzech odstranit, aby vám zbytečně nenabíhaly poplatky.

>[!NOTE]
>Webové služby nasazené místně se nezobrazí v seznamu služeb na webu Azure Portal. Budou spuštěné v Dockeru na místním počítači.

1. Otevřete rozhraní příkazového řádku (CLI).
   V aplikaci Machine Learning Workbench v nabídce **Soubor** vyberte **Otevřít příkazový řádek**.

   Příkazový řádek se otevře v aktuálním umístění složky projektu **c:\temp\myIris>**.


1. Ujistěte se, že je ve vašem předplatném zaregistrovaný poskytovatel prostředků Azure **Microsoft.ContainerRegistry**. Tohoto poskytovatele prostředků musíte zaregistrovat před vytvořením prostředí v kroku 3. To, jestli už je zaregistrovaný, můžete zkontrolovat pomocí následujícího příkazu:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Měl by se zobrazit výstup podobný tomuto: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Pokud není poskytovatel **Microsoft.ContainerRegistry** zaregistrovaný, můžete ho zaregistrovat pomocí následujícího příkazu:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   Registrace může trvat několik minut. Stav registrace můžete zkontrolovat pomocí předchozího příkazu **az provider list** nebo následujícího příkazu:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   Ve třetím řádku výstupu se zobrazí **"registrationState": "Registrace"**. Chvíli počkejte a pak příkaz **show** opakujte, dokud se ve výstupu nezobrazí **"registrationState": "Registrováno"**.

   >[!NOTE] 
   Pokud provádíte nasazení do clusteru ACS, musíte naprosto stejným způsobem zaregistrovat také poskytovatele prostředků **Microsoft.ContainerService**.

1. Vytvořte prostředí. Tento krok je potřeba provést jednou pro každé prostředí. Například jej provedete jednou pro vývojové a jednou pro produkční prostředí. Pro první prostředí použijte _místní režim_. Později můžete zkusit v tomto příkazu použít přepínač `-c` nebo `--cluster` a nastavit prostředí v _režimu clusteru_.

   Následující příkaz pro nastavení vyžaduje přístup k předplatnému na úrovni Přispěvatel. Pokud ho nemáte, potřebujete přístup na úrovni Přispěvatel alespoň ke skupině prostředků, do které nasazujete. V druhém případě je potřeba zadat název skupiny prostředků jako součást příkazu pro nastavení pomocí příznaku `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Podle pokynů na obrazovce zřiďte účet úložiště pro ukládání imagí Dockeru, registr kontejnerů Azure, který obsahuje seznam imagí Dockeru, a účet Azure Application Insights, který shromažďuje telemetrická data. Pokud použijete přepínač `-c`, příkaz navíc vytvoří i cluster Container Service.
   
   Název clusteru nabízí způsob, jak identifikovat příslušné prostředí. Umístění by mělo být stejné jako umístění účtu služby Správa modelů vytvořeného na webu Azure Portal.

   Pokud chcete ověřit úspěšné nastavení prostředí, pomocí následujícího příkazu zkontrolujte jeho stav:

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Před nastavení prostředí v kroku 5 se ujistěte, že Stav zřizování má hodnotu Úspěšně, jak je znázorněno níže:

   ![Stav zřizování](media/tutorial-classifying-iris/provisioning_state.png)
 
1. Pokud jste nevytvořili účet Správy modelů v předchozích částech tohoto kurzu, vytvořte ho nyní. Jedná se o jednorázové nastavení.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
1. Nastavte účet služby Správa modelů.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

1. Nastavte prostředí.

   Po dokončení nastavení nastavte pomocí následujícího příkazu proměnné prostředí nutné k zprovoznění prostředí. Použijte stejný název prostředí, který jste použili v kroku 3. Použijte stejný název skupiny prostředků, který byl výstupem v příkazovém okně při dokončení procesu nastavení.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

1. Pokud chcete zkontrolovat, jestli jste zprovozněné prostředí pro nasazení místní webové služby nastavili správně, zadejte následující příkaz:

   ```azurecli
   az ml env show
   ```

Teď můžete vytvořit webovou službu v reálném čase.

>[!NOTE]
>Svůj účet služby Správa modelů a prostředí můžete znovu použít k dalším nasazením webových služeb. Nemusíte je vytvářet pro každou webovou službu zvlášť. K účtu nebo prostředí může být přiřazeno několik webových služeb.

## <a name="create-a-real-time-web-service-in-one-command"></a>Vytvoření webové služby v reálném čase pomocí jednoho příkazu
1. K vytvoření webové služby v reálném čase použijte následující příkaz:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s ./output/service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Tento příkaz vygeneruje ID webové služby, které můžete použít později. Pokud jste v poznámkovém bloku, vynechte výstupní adresář.

   Pro příkaz **az ml service create realtime** se používají následující přepínače:

   * `-f`: Název souboru hodnoticího skriptu.

   * `--model-file`: Soubor modelu. V tomto případě je to soubor pickle model.pkl.

   * `-s`: Schéma služby. To se vygenerovalo v předchozím kroku místním spuštěním skriptu **score_iris.py**.

   * `-n`: Název aplikace, který musí obsahovat jenom malá písmena.

   * `-r`: Modul runtime daného modelu. V tomto případ je to model Python. Platné moduly runtime jsou `python` a `spark-py`.

   * `--collect-model-data true`: Tento přepínač povoluje shromažďování dat.

   * `-c`: Cesta k souboru závislostí systému Conda se zadanými dalšími balíčky.

   >[!IMPORTANT]
   >Název služby, který je zároveň názvem nové image Dockeru, musí obsahovat jenom malá písmena. Jinak dojde k chybě. 

1. Když příkaz spustíte, do účtu úložiště, který jste vytvořili v rámci vytváření prostředí, se nahraje model a soubory vyhodnocení. Proces nasazení sestaví image Dockeru obsahující váš model, schéma a soubor vyhodnocení a odešle ji do registru kontejneru Azure: **\<název_ACR\>.azurecr.io/\<název_image\>:\<verze\>**. 

   Příkaz tuto image přetáhne do místního počítače a na základě této image spustí kontejner Dockeru. Pokud je vaše prostředí nakonfigurované v režimu clusteru, kontejner Dockeru se místo toho nasadí do clusteru Azure Cloud Services Kubernetes.

   V rámci nasazení se v místním počítači vytvoří koncový bod HTTP REST pro webovou službu. Po několika minutách by se měl příkaz dokončit a zobrazit zprávu o úspěšném provedení. Webová služba je připravená k použití.

1. Spuštěný kontejner Dockeru můžete zobrazit pomocí příkazu **docker ps**:

   ```azurecli
   docker ps
   ```

## <a name="optional-alternative-create-a-real-time-web-service-by-using-separate-commands"></a>[Volitelná alternativa] Vytvoření webové služby v reálném čase pomocí samostatných příkazů
Jako alternativu k příkazu **az ml service create realtime** uvedenému výše můžete také provést jednotlivé kroky samostatně. 

Nejprve zaregistrujte model. Potom vygenerujte manifest, sestavte image Dockeru a vytvořte webovou službu. Tento přístup vám zajistí větší flexibilitu v každém kroku. Navíc můžete použít entity vygenerované v předchozích krocích a další entity vytvářet jenom v případě potřeby.

1. Zaregistrujte model zadáním názvu souboru pickle.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Tento příkaz vygeneruje ID modelu.

1. Vytvořte manifest.

   K vytvoření manifestu použijte následující příkaz a zadejte výstup ID modelu z předchozího kroku:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s ./output/service_schema.json -c aml_config\conda_dependencies.yml
   ```
   Tento příkaz vygeneruje ID manifestu.  Pokud jste v poznámkovém bloku, vynechte výstupní adresář.

1. Vytvořte image Dockeru.

   K vytvoření image Dockeru použijte následující příkaz a zadejte hodnotu ID manifestu, která je výsledkem předchozího kroku. Pokud chcete také zahrnout závislosti systému Conda, použijte přepínač `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> 
   ```
   Tento příkaz vygeneruje ID image Dockeru.
   
1. Vytvořte službu.

   K vytvoření služby použijte následující příkaz a zadejte výstup ID image z předchozího kroku:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Tento příkaz vygeneruje ID webové služby.

Teď můžete webovou službu spustit.

## <a name="run-the-real-time-web-service"></a>Spuštění webové služby v reálném čase

Spuštěnou webovou službu **irisapp** můžete otestovat pomocí zakódovaného záznamu JSON obsahujícího pole čtyř náhodných čísel.

1. Webová služba zahrnuje ukázková data. Při spuštění v místním režimu můžete volat příkaz **az ml service usage realtime**. Toto volání načte ukázkový příkaz pro spuštění, který můžete použít k otestování služby. Volání také načte hodnoticí adresu URL, pomocí které můžete službu začlenit do vlastní aplikace.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

1. Pokud chcete službu otestovat, spusťte vrácený příkaz ke spuštění služby:
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d '{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}'
   ```

   Výstup je **„Iris-setosa“**, což je předpokládaná třída. (Váš výsledek se může lišit.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Zobrazení shromážděných dat ve službě Azure Blob Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte své účty úložiště. Provedete to tak, že vyberete **Všechny služby**.

1. Do vyhledávacího pole zadejte **Účty úložiště** a pak stiskněte Enter.

1. Ve vyhledávacím poli **Účty úložiště** vyberte prostředek **Účet úložiště** odpovídající vašemu prostředí. 

   > [!TIP]
   > Určení používaného účtu:
   > 1. Otevřete aplikaci Machine Learning Workbench.
   > 1. Vyberte projekt, na kterém pracujete.
   > 1. Z nabídky **Soubor** otevřete příkazový řádek.
   > 1. Na příkazovém řádku zadejte `az ml env show -v` a zkontrolujte hodnotu *storage_account*. Toto je název vašeho účtu úložiště.

1. Jakmile se otevře podokno **Účet úložiště**, v části **Služby** vyberte **Objekty blob**. Najděte kontejner s názvem **modeldata**. 
 
   Pokud se nezobrazí žádná data, možná budete muset počkat až 10 minut od prvního požadavku na webovou službu, aby se data rozšířila do účtu úložiště.

   Data budou téci do objektů blob s následující cestou kontejneru:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

1. Tato data můžete využívat ve službě Azure Blob Storage. Existuje řada různých nástrojů, které používají software Microsoftu i open source nástroje, jako například:

   * Machine Learning: Otevřete soubor CSV tím, že ho přidáte jako zdroj dat.

   * Excel: Otevřete denní soubory CSV jako tabulku.

   * [Power BI:](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/) Vytvářejte grafy na základě dat získaných z dat ve formátu CSV v objektech blob.

   * [Hive:](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) Načtěte data ve formátu CSV do tabulky Hive a spouštějte dotazy SQL přímo na objektech blob.

   * [Spark:](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview) Vytvořte datový rámec s velkou částí dat ve formátu CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky
V této třetí části třídílné série kurzů jste se naučili, jak pomocí služby Machine Learning provádět tyto úlohy:
> [!div class="checklist"]
> * Vyhledání souboru modelu
> * Vygenerování hodnoticího skriptu a souboru schématu
> * Příprava prostředí
> * Vytvoření webové služby v reálném čase
> * Spuštění webové služby v reálném čase
> * Zkoumání výstupních dat objektů blob 

Úspěšně jste spustili trénovací skript v různých výpočetních prostředích. Také jste vytvořili, serializovali a zprovoznili model prostřednictvím webové služby založené na Dockeru. 

Teď jste připravení na provádění pokročilé přípravy dat:
> [!div class="nextstepaction"]
> [Kurz 4 – Pokročilá příprava dat](tutorial-bikeshare-dataprep.md)
