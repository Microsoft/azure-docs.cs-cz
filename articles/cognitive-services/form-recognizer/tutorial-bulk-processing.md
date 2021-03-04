---
title: 'Kurz: hromadné extrakce dat formuláře pomocí nástroje pro rozpoznávání formulářů Azure Data Factory'
titleSuffix: Azure Cognitive Services
description: Nastavte aktivity Azure Data Factory pro aktivaci školení a spuštění modelů pro rozpoznávání formulářů, abyste mohli digitalizovat velké nevyřízené položky dokumentů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 6faa612f55b4114b4242c48d43aae9aac8c56582
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699993"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Kurz: hromadné extrakce dat formuláře pomocí Azure Data Factory

V tomto kurzu se podíváme na to, jak používat služby Azure k ingestování rozsáhlých dávek formulářů do digitálních médií. V tomto kurzu se dozvíte, jak automatizovat příjem dat z Azure Data Lake dokumentů do Azure SQL Database. Budete moct rychle naučit modely a zpracovávat nové dokumenty několika kliknutími.

## <a name="business-need"></a>Obchodní potřeby

Většina organizací si teď uvědomuje, jak cenná data v různých formátech (PDF, obrázky, videa) jsou. Hledají osvědčené postupy a nejúčinnější způsob, jak tyto prostředky digitalizovat.

Kromě toho naši zákazníci mají často různé typy formulářů přicházejících z mnoha klientů a zákazníků. Na rozdíl od [rychlých startů](./quickstarts/client-library.md)v tomto kurzu se dozvíte, jak automaticky naučit model s novými a různými typy formulářů pomocí přístupu založeného na metadatech. Pokud pro daný typ formuláře nemáte existující model, systém ho vytvoří za vás a poskytne vám ID modelu. 

Po extrahování dat z formulářů a jejich kombinování se stávajícími operačními systémy a datovými sklady můžou podniky získat přehledy a dodat jejich potřebám zákazníkům a obchodním uživatelům.

Pomocí nástroje pro rozpoznávání formulářů Azure pomůžeme organizacím využívat svá data, automatizovat procesy (platby na faktuře, zpracování daní atd.), ušetřit peníze a čas a využívat lepší přesnost dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení Azure Data Lake pro ukládání formulářů
> * Vytvoření tabulky pro parametry pomocí databáze Azure
> * Uložení citlivých přihlašovacích údajů pomocí Azure Key Vault
> * Výuka modelu rozpoznávání formulářů v poznámkovém bloku datacihly
> * Extrakce dat formuláře pomocí poznámkového bloku datacihly
> * Automatizace školení a extrakce formulářů pomocí Azure Data Factory

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Sada alespoň pěti forem stejného typu. V ideálním případě má tento pracovní postup podporovat velké sady dokumentů. Tipy a možnosti pro vložení sady školicích dat najdete v tématu [Vytvoření školicích dat](./build-training-data-set.md) . Pro účely tohoto kurzu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Architektura projektu 

Tento projekt představuje sadu Azure Data Factorych kanálů pro spuštění poznámkových bloků Pythonu, které vyškolí, analyzují a extrahují data z dokumentů v účtu úložiště Azure Data Lake.

REST přístupový bod pro rozpoznávání formulářů vyžaduje jako vstup některé parametry. Z bezpečnostních důvodů se některé z těchto parametrů uloží do Azure Key Vault, zatímco jiné méně citlivé parametry, jako je název složky objektů BLOB úložiště, budou uložené v tabulce Parametrizace ve službě Azure SQL Database.

Pro analýzu typu formuláře, který se má analyzovat, naplní data technici nebo datové specialisty řádek tabulky parametrů. Pak používají Azure Data Factory k iterování v seznamu zjištěných typů formulářů a předání příslušných parametrů do poznámkového bloku datacihly ke školení nebo přeučení modelů pro rozpoznávání formulářů. Tady můžete použít funkci Azure Function.

Azure Databricks Poznámkový blok pak pomocí proškolených modelů extrahuje data formuláře a exportuje je do databáze SQL Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Architektura projektu":::


## <a name="set-up-azure-data-lake"></a>Nastavit Azure Data Lake

Vaše nevyřízené položky formulářů můžou být ve vašem místním prostředí nebo na serveru FTP (s). Tento kurz používá formuláře v účtu úložiště Azure Data Lake Gen 2. Soubory můžete přenést pomocí Azure Data Factory, Průzkumník služby Azure Storage nebo AzCopy. Datové sady pro školení a bodování můžou být v různých kontejnerech, ale školicí datové sady pro všechny typy formulářů musí být ve stejném kontejneru (i když můžou být v různých složkách).

Pokud chcete vytvořit nový Data Lake, postupujte podle pokynů v části [Vytvoření účtu úložiště pro použití s Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Vytvoření tabulky Parametrizace

V dalším kroku vytvoříme tabulku metadat ve Azure SQL Database. Tato tabulka bude obsahovat necitlivá data, která vyžaduje REST API rozpoznávání formuláře. Pokaždé, když je v naší datové sadě nějaký nový typ formuláře, do této tabulky vložíme nový záznam a spustíme kanál školení a bodování (bude se implementovat později).

V tabulce se použijí následující pole:

* **form_description**: Toto pole není v rámci školení vyžadováno. Poskytuje popis typu formulářů, pro které model provádíme (například "klientské formuláře", "hotelu B Forms").
* **training_container_name**: Toto pole je název kontejneru účtu úložiště, do kterého jsme uložili datovou sadu školení. Může to být stejný kontejner jako **scoring_container_name**.
* **training_blob_root_folder**: složka v účtu úložiště, do které budeme ukládat soubory pro školení modelu.
* **scoring_container_name**: Toto pole je název kontejneru účtu úložiště, ve kterém jsme uložili soubory, ze kterých se mají extrahovat páry klíč-hodnota. Může to být stejný kontejner jako **training_container_name**.
* **scoring_input_blob_folder**: složka v účtu úložiště, ze které budeme ukládat soubory pro extrakci dat.
* **model_id**: ID modelu, který chceme znovu naučit. Pro první spuštění musí být hodnota nastavená na-1, což způsobí, že školicí Poznámkový blok vytvoří nový vlastní model pro vlak. Školicí Poznámkový blok vrátí nově vytvořené ID modelu do instance Azure Data Factory a pomocí aktivity uložené procedury tuto hodnotu aktualizujeme ve službě Azure SQL Database.

  Kdykoli budete chtít ingestovat nový typ formuláře, bude nutné ručně resetovat ID modelu na hodnotu-1 před školením modelu.

* **file_type**: podporované typy formulářů jsou `application/pdf` , `image/jpeg` , `image/png` a `image/tif` .

  Pokud máte formuláře různých typů souborů, budete muset při výuce nového typu formuláře změnit tuto hodnotu a **model_id** .
* **form_batch_group_id**: v průběhu času můžete mít více typů formulářů, které provedete v rámci stejného modelu. **Form_batch_group_id** vám umožní zadat všechny typy formulářů, které byly školením pomocí konkrétního modelu.

### <a name="create-the-table"></a>Vytvoření tabulky

[Vytvořte Azure SQL Database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)a potom v [Editoru dotazů](../../azure-sql/database/connect-query-portal.md) spusťte následující skript SQL, abyste vytvořili potřebnou tabulku.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Spusťte následující skript, který vytvoří postup pro automatickou aktualizaci **model_id** po jeho školení.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Uložení citlivých přihlašovacích údajů pomocí Azure Key Vault

Z bezpečnostních důvodů nepotřebujeme ukládat určité citlivé informace v tabulce Parametrizace ve službě Azure SQL Database. Citlivé parametry ukládáme jako Azure Key Vault tajných kódů.

### <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

[Vytvořte prostředek Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Potom přejděte k prostředku Key Vault po jeho vytvoření a v části **Nastavení** vyberte možnost **tajné klíče** a přidejte parametry.

Zobrazí se nové okno, vyberte **Generovat/importovat**. Zadejte název parametru a jeho hodnotu a klikněte na vytvořit. Udělejte to pro následující parametry:

* **CognitiveServiceEndpoint**: adresa URL koncového bodu rozhraní API pro rozpoznávání formulářů
* **CognitiveServiceSubscriptionKey**: přístupový klíč pro službu rozpoznávání formulářů. 
* **StorageAccountName**: účet úložiště, ve kterém je uložená datová sada a formuláře pro školení, ze kterých chceme extrahovat páry klíč-hodnota. Pokud jsou v různých účtech, zadejte názvy jednotlivých účtů jako samostatné tajné kódy. Mějte na paměti, že datové sady pro školení musí být ve stejném kontejneru pro všechny typy formulářů, ale mohou být v různých složkách.
* **StorageAccountSasKey**: sdílený přístupový podpis (SAS) účtu úložiště. Adresu URL SAS načtete tak, že přejdete do svého prostředku úložiště a vyberete kartu **Průzkumník služby Storage** . Přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Výuka modelu rozpoznávání formulářů v poznámkovém bloku datacihly

Pomocí Azure Databricks uložíte a spustíte kód Pythonu, který komunikuje se službou rozpoznávání formulářů.

### <a name="create-a-notebook-in-databricks"></a>Vytvoření poznámkového bloku v datacihlech

[Vytvoří prostředek Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) v Azure Portal. Po vytvoření přejděte k prostředku a spusťte pracovní prostor.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Vytvoření oboru tajného kódu, který je zálohovaný pomocí Azure Key Vault

Pokud chcete odkazovat na tajné kódy v Azure Key Vault jsme vytvořili výše, budete muset vytvořit obor tajného kódu v datacihlách. Postupujte podle kroků v části [Vytvoření oboru tajného klíče, který je Azure Key Vault zálohovaný](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Vytvoření clusteru Databricks

Cluster je kolekce prostředků výpočtu datacihly. Vytvoření clusteru:

1. Na bočním panelu klikněte na tlačítko **clustery** .
1. Na stránce **clustery** klikněte na **vytvořit cluster**.
1. Na stránce **vytvořit cluster** zadejte název clusteru a v rozevíracím seznamu verze Databricks Runtime vyberte **7,2 (Scala 2,12, Spark 3.0.0)** .
1. Klikněte, že chcete **vytvořit cluster**.

### <a name="write-a-settings-notebook"></a>Zapsat Poznámkový blok nastavení

Teď jste připraveni přidat poznámkové bloky Pythonu. Nejdřív vytvořte Poznámkový blok s názvem **Nastavení**; Tento Poznámkový blok přiřadí hodnoty v tabulce Parametrizace do proměnných ve skriptu. Hodnoty budou později předány jako parametry Azure Data Factory. Také přiřadíme hodnoty z tajných kódů v Key Vault k proměnným. 

1. Poznámkový blok pro **Nastavení** vytvoříte tak, že kliknete na tlačítko **pracovní prostor** , na kartě nový kliknete na rozevírací seznam a vyberete **vytvořit** a **Poznámkový blok**.
1. V automaticky otevíraném okně zadejte název, který chcete Poznámkový blok zadat, a jako výchozí jazyk vyberte **Python** . Vyberte svůj cluster datacihly a vyberte **vytvořit**.
1. V první buňce poznámkového bloku načtoume parametry předané Azure Data Factory.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Ve druhé buňce načteme tajné kódy z Key Vault a přiřadíme je do proměnných.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Napsat školicí notebook

Teď, když jsme dokončili Poznámkový blok **Nastavení** , můžeme vytvořit Poznámkový blok pro výuku modelu. Jak je uvedeno výše, použijeme soubory uložené ve složce v Azure Data Lake účet úložiště Gen 2 (**training_blob_root_folder**). Název složky byl předán jako proměnná. Každá sada typů formulářů bude ve stejné složce a jako smyčka v tabulce parametrů provedeme model pomocí všech typů formuláře. 

1. Vytvořte nový Poznámkový blok s názvem **TrainFormRecognizer**. 
1. V první buňce spusťte Poznámkový blok nastavení:

    ```python
    %run "./Settings"
    ```

1. V další buňce přiřaďte proměnné ze souboru **Nastavení** a dynamicky proškolujte model pro každý typ formuláře a použijte kód v [rychlém startu REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Posledním krokem v procesu školení je získat výsledek školení ve formátu JSON.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Extrakce dat formuláře pomocí poznámkového bloku

### <a name="mount-the-azure-data-lake-storage"></a>Připojení Azure Data Lakeho úložiště

Dalším krokem je vyznámení s různými formami, které používá trained model. Připojíme Azure Data Lake účet úložiště v datacihlách a v průběhu procesu ingestování se dozvíte o připojení.

Stejně jako ve fázi školení použijeme Azure Data Factory k vyvolání extrakce párů klíč-hodnota z formulářů. Přeskočíme na formuláře ve složkách zadaných v tabulce parametrů.

1. Pojďme vytvořit Poznámkový blok pro připojení účtu úložiště v datacihlech. Budeme volat IT **MountDataLake**. 
1. Nejdřív budete muset zavolat Poznámkový blok **Nastavení** :

    ```python
    %run "./Settings"
    ```

1. Do druhé buňky definujeme proměnné pro citlivé parametry, které načteme z našich Key Vault tajných klíčů.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. V dalším kroku se pokusíme účet úložiště odpojit v případě, že byl dříve připojen.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Nakonec připojíme účet úložiště.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > V tomto případě jsme připojili jenom účet školení úložiště &mdash; . školicí soubory a soubory, ze kterých chceme extrahovat páry klíč-hodnota, jsou ve stejném účtu úložiště. Pokud se vaše bodování a školicí účty úložiště liší, budete muset oba účty úložiště připojit. 

### <a name="write-the-scoring-notebook"></a>Napsat Poznámkový blok pro vyhodnocování

Teď můžeme vytvořit hodnoticí Poznámkový blok. Podobně jako školicí Poznámkový blok budeme používat soubory uložené ve složkách v účtu úložiště Azure Data Lake, který jsme právě připojili. Název složky se předává jako proměnná. Přeskočíme všechny formuláře v zadané složce a z nich se extrahují páry klíč-hodnota. 

1. Vytvořte nový Poznámkový blok a zavolejte ho **ScoreFormRecognizer**. 
1. Spusťte **Nastavení** a Poznámkový blok **MountDataLake** .

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Pak přidejte následující kód, který volá rozhraní API pro [analýzu](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) .

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. V další buňce získáme výsledky extrakce páru klíč-hodnota. Tato buňka bude mít za následek výstup výsledku. Vzhledem k tomu, že chceme, aby výsledek ve formátu JSON dál zpracovával Azure SQL Database nebo Cosmos DB, napíšeme výsledek do souboru. JSON. Název výstupního souboru bude název souboru s skóre, který je zřetězený s "_output.json". Soubor bude uložen ve stejné složce jako zdrojový soubor.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Postup zápisu souborů do nové buňky:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Automatizace školení a bodování pomocí Azure Data Factory

Jediným krokem je nastavení služby Azure Data Factory (ADF) pro automatizaci procesů školení a bodování. Nejprve postupujte podle kroků v části [Vytvoření datové továrny](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Po vytvoření prostředku ADF budete muset vytvořit tři kanály: jednu pro školení a dvě pro bodování (vysvětlení najdete níže).

### <a name="training-pipeline"></a>Školicí kanál

První aktivita v kanálu školení je vyhledávání pro čtení a vrácení hodnot v tabulce Parametrizace ve službě Azure SQL Database. Jak budou všechny datové sady pro školení ve stejném účtu úložiště a v kontejneru (ale potenciálně jiné složky), zachová se výchozí hodnota atribut **pouze první řádek** v nastavení aktivity vyhledávání. Pro každý typ formuláře pro výuku modelu vytvoříme model pomocí všech souborů v **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="školicí kanál v datové továrně":::

Uložená procedura používá dva parametry: **model_id** a **form_batch_group_id**. Kód pro návrat ID modelu z poznámkového bloku datacihly je `dbutils.notebook.exit(model_id)` a kód pro čtení kódu v aktivitě uložené procedury ve službě Data Factory je `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Kanály bodování

Chcete-li extrahovat páry klíč-hodnota, prohledáme všechny složky v tabulce Parametrizace a pro každou složku extrahujeme páry klíč-hodnota všech souborů, které jsou v ní. V dnešní době ADF nepodporuje vnořené smyčky ForEach. Místo toho vytvoříme dva kanály. První kanál provede vyhledávání z tabulky Parametrizace a předá seznam složek jako parametr druhému kanálu.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="první bodování kanálu v datové továrně":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="první bodování kanálu v datové továrně, podrobnosti":::

Druhý kanál použije k získání seznamu souborů ve složce getmeta aktivitu a předáte ji jako parametr do poznámkového bloku pro bodování datacihly.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="druhý kanál bodování v datové továrně":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="druhý bodování kanálu v datové továrně, podrobnosti":::

### <a name="specify-a-degree-of-parallelism"></a>Zadat stupeň paralelismu

V kanálech školení a bodování můžete určit míru paralelismu pro zpracování více formulářů současně.

Nastavení stupně paralelismu v kanálu ADF:

* Vyberte aktivitu foreach.
* Zrušte kontrolu **sekvenčního** pole.
* Nastaví stupeň paralelismu v textovém poli **počet dávek** . Pro účely bodování doporučujeme maximální počet dávek na 15.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Konfigurace paralelismu pro aktivity bodování v ADF":::

## <a name="how-to-use"></a>Způsob použití

Teď máte automatizovaný kanál k digitalizaci nevyřízených položek formulářů a provádění některých analýz nad ním. Když do existující složky úložiště přidáte nové formy známého typu, stačí znovu spustit kanály bodování a budou aktualizovat všechny výstupní soubory, včetně výstupních souborů pro nové formuláře. 

Pokud přidáte nové formy nového typu, budete také muset odeslat školicí datovou sadu do příslušného kontejneru. Pak přidejte nový řádek do tabulky Parametrizace a zadejte umístění nových dokumentů a jejich datovou sadu školení. Zadejte hodnotu-1 pro **model_ID** , která označuje, že pro tyto formuláře musí být vyškolený nový model. Pak spusťte školicí kanál v ADF. Načte se z tabulky, vytvoří výuku modelu a přepíše ID modelu v tabulce. Pak můžete volat kanály bodování, abyste mohli začít psát výstupní soubory.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte Azure Data Factory kanály pro aktivaci školení a spuštění modelů pro rozpoznávání formulářů, abyste mohli digitalizovat velké nevyřízené položky souborů. Dále si Prozkoumejte rozhraní API pro rozpoznávání formulářů, abyste viděli, co s tím někdo s tím můžete dělat.

* [REST API pro rozpoznávání formulářů](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)