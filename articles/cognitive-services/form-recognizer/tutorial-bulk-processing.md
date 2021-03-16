---
title: 'Kurz: hromadné extrakce dat formuláře pomocí nástroje pro rozpoznávání formulářů Azure Data Factory'
titleSuffix: Azure Cognitive Services
description: Nastavte aktivity Azure Data Factory pro aktivaci školení a spouštění modelů pro rozpoznávání formulářů a k digitalizaci velkých nevyřízených položek dokumentů.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: 0c009a87a5834997cdc489efc75ebb16f9459754
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467098"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Kurz: hromadné extrakce dat formuláře pomocí Azure Data Factory

V tomto kurzu se podíváme na to, jak používat služby Azure k ingestování rozsáhlých dávek formulářů do digitálních médií. V tomto kurzu se dozvíte, jak automatizovat příjem dat z Azure Data Lake z dokumentů do Azure SQL Database. Budete moct rychle naučit modely a zpracovávat nové dokumenty několika kliknutími.

## <a name="business-need"></a>Obchodní potřeby

Většina organizací si teď vědoma hodnoty dat, která mají v různých formátech (PDF, obrázky, videa). Hledají osvědčené postupy a nejúčinnější způsob, jak tyto prostředky digitalizovat.

Také naši zákazníci mají často různé typy formulářů, které pocházejí z mnoha klientů a zákazníků. Na rozdíl od [rychlých startů](./quickstarts/client-library.md)v tomto kurzu se dozvíte, jak automaticky naučit model s novými a různými typy formulářů pomocí přístupu založeného na metadatech. Pokud pro daný typ formuláře nemáte existující model, systém ho vytvoří a poskytne vám ID modelu. 

Po extrahování dat z formulářů a jejich kombinování se stávajícími operačními systémy a datovými sklady můžou podniky získat přehledy a dodat jejich potřebám zákazníkům a obchodním uživatelům.

Nástroj pro rozpoznávání formulářů Azure pomáhá organizacím používat svá data, automatizovat procesy (platby na faktuře, zpracovávat daně atd.), ušetřit peníze a čas a využívat lepší přesnost dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte Azure Data Lake pro ukládání formulářů.
> * K vytvoření tabulky ukazatelů použijte Azure Database.
> * K ukládání citlivých přihlašovacích údajů použijte Azure Key Vault.
> * Prohlaste si model rozpoznávání formulářů v Azure Databricks poznámkovém bloku.
> * Extrahujte data formuláře pomocí poznámkového bloku datacihly.
> * Automatizujte školení a extrakci formulářů pomocí Azure Data Factory.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/).
* Až budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení prostředku vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Do kódu později v tomto rychlém startu vložíte svůj klíč a koncový bod.
    * Službu můžete vyzkoušet pomocí bezplatné cenové úrovně (F0). Pak můžete upgradovat později na placenou úroveň pro produkční prostředí.
* Sada alespoň pěti forem stejného typu. V ideálním případě má tento pracovní postup podporovat velké sady dokumentů. Tipy a možnosti pro vložení datové sady školení najdete v tématu [Vytvoření školicí datové sady](./build-training-data-set.md) . Pro účely tohoto kurzu můžete použít soubory ve složce výuka [ukázkové datové sady](https://go.microsoft.com/fwlink/?linkid=2128080).


## <a name="project-architecture"></a>Architektura projektu 

Tento projekt představuje sadu Azure Data Factorych kanálů pro spuštění poznámkových bloků Pythonu, které vyškolí, analyzují a extrahují data z dokumentů v účtu úložiště Azure Data Lake.

REST API pro rozpoznávání formulářů vyžaduje jako vstup některé parametry. Z bezpečnostních důvodů se některé z těchto parametrů uloží do trezoru klíčů Azure. V tabulce Parametrizace ve službě Azure SQL Database budou uloženy i méně citlivé parametry, jako je název složky objektů BLOB úložiště.

Pro každý typ formuláře, který se má analyzovat, naplní datové technici nebo odborníci přes data řádek tabulky parametrů. Pak použijí Azure Data Factory k iterování v seznamu zjištěných typů formulářů a předají příslušné parametry do poznámkového bloku datacihly ke školení nebo přeučení modelů pro rozpoznávání formulářů. Tady můžete použít funkci Azure Function.

Poznámkový blok Azure Databricks pak pomocí proškolených modelů extrahuje data formuláře. Exportuje tato data do databáze SQL Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagram znázorňující architekturu projektu.":::


## <a name="set-up-azure-data-lake"></a>Nastavit Azure Data Lake

Vaše nevyřízené položky formulářů můžou být ve vašem místním prostředí nebo na serveru FTP (s). Tento kurz používá formuláře v účtu Azure Data Lake Storage Gen2. Soubory můžete přenést pomocí Azure Data Factory, Průzkumník služby Azure Storage nebo AzCopy. Datové sady pro školení a bodování můžou být v různých kontejnerech, ale školicí datové sady pro všechny typy formulářů musí být ve stejném kontejneru. (Můžou být v různých složkách.)

Pokud chcete vytvořit nový datový Lake, postupujte podle pokynů v části [Vytvoření účtu úložiště pro použití s Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Vytvoření tabulky Parametrizace

V dalším kroku vytvoříme tabulku metadat ve službě Azure SQL Database. Tato tabulka bude obsahovat necitlivá data, která vyžaduje REST API rozpoznávání formuláře. Pokaždé, když je v datové sadě nový typ formuláře, do této tabulky vložíme nový záznam a spustíme kanály školení a bodování. (Tyto kanály budeme implementovat později.)

Tato pole se použijí v tabulce:

* **form_description**. Není nutné jako součást školení. Toto pole poskytuje popis typu formulářů, pro který model sledujeme (například "klientské formuláře", "Hotel B Forms").
* **training_container_name**. Název kontejneru účtu úložiště, do kterého jsme uložili datovou sadu školení. Může to být stejný kontejner jako **scoring_container_name**.
* **training_blob_root_folder**. Složka v účtu úložiště, do které budeme ukládat soubory pro školení modelu.
* **scoring_container_name**. Název kontejneru účtu úložiště, ve kterém jsme uložili soubory, ze kterých se mají extrahovat páry klíč/hodnota. Může to být stejný kontejner jako **training_container_name**.
* **scoring_input_blob_folder**. Složka v účtu úložiště, ze které ukládáme soubory pro extrakci dat
* **model_id**. ID modelu, který chceme znovu naučit. Pro první spuštění musí být hodnota nastavená na-1, což způsobí, že školicí Poznámkový blok vytvoří nový vlastní model pro vlak. Školicí Poznámkový blok vrátí nové ID modelu do instance Azure Data Factory. Když použijete aktivitu uložené procedury, aktualizujeme tuto hodnotu ve službě Azure SQL Database.

  Kdykoli budete chtít ingestovat nový typ formuláře, je nutné ručně resetovat ID modelu na hodnotu-1, než bude model vyškolit.

* **file_type**. Podporované typy formulářů jsou `application/pdf` , `image/jpeg` , `image/png` a `image/tif` .

  Pokud máte formuláře v jiných typech souborů, budete muset tuto hodnotu a **model_id** při učení nového typu formuláře změnit.
* **form_batch_group_id**. V průběhu času můžete mít více typů formulářů, které provedete v rámci stejného modelu. Pole **form_batch_group_id** vám umožní zadat všechny typy formulářů, které byly školeními s konkrétním modelem.

### <a name="create-the-table"></a>Vytvoření tabulky


[Vytvořte databázi SQL Azure](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)a potom v [Editoru dotazů](../../azure-sql/database/connect-query-portal.md) spusťte tento skript SQL a vytvořte požadovanou tabulku:


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

Spuštěním tohoto skriptu vytvoříte proceduru, která po jeho školení automaticky aktualizuje **model_id** .

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

### <a name="create-an-azure-key-vault"></a>Vytvoření trezoru klíčů Azure

[Vytvořte prostředek Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Po vytvoření Key Vault prostředku potom v části **Nastavení** vyberte **tajné klíče** a přidejte parametry.

Zobrazí se nové okno. Vyberte **Generovat/importovat**. Zadejte název parametru a jeho hodnotu a pak vyberte **vytvořit**. Pro následující parametry proveďte tyto kroky:

* **CognitiveServiceEndpoint**. Adresa URL koncového bodu rozhraní API pro rozpoznávání formulářů
* **CognitiveServiceSubscriptionKey**. Přístupový klíč pro službu rozpoznávání formulářů 
* **StorageAccountName**. Účet úložiště, do kterého se ukládají datové sady školení a formuláře, z nichž chcete extrahovat páry klíč/hodnota. Pokud jsou tyto položky v různých účtech, zadejte název každého účtu jako samostatný tajný klíč. Mějte na paměti, že datové sady pro školení musí být ve stejném kontejneru pro všechny typy formulářů. Můžou být v různých složkách.
* **StorageAccountSasKey**. Sdílený přístupový podpis (SAS) účtu úložiště. Adresu URL SAS načtete tak, že přejdete do svého prostředku úložiště. Na kartě **Průzkumník služby Storage** přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou vybrána oprávnění **číst** a **seznam** , a pak vyberte **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Výuka modelu rozpoznávání formulářů v poznámkovém bloku datacihly

Pomocí Azure Databricks uložíte a spustíte kód Pythonu, který komunikuje se službou rozpoznávání formulářů.

### <a name="create-a-notebook-in-databricks"></a>Vytvoření poznámkového bloku v datacihlech

[Vytvoří prostředek Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) v Azure Portal. Po vytvoření prostředku přejít na prostředek a otevřete pracovní prostor.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Vytvoření oboru tajného kódu, který je zálohovaný pomocí Azure Key Vault


Pokud chcete odkazovat na tajné klíče v trezoru klíčů Azure, který jste vytvořili výše, budete muset v datacihlech vytvořit obor tajného klíče. Postupujte podle kroků uvedených tady: [Vytvoření oboru tajného klíče, který je zálohovaný Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Vytvoření clusteru Databricks

Cluster je kolekce prostředků výpočtu datacihly. Vytvoření clusteru:

1. V levém podokně vyberte tlačítko **clustery** .
1. Na stránce **clustery** vyberte **vytvořit cluster**.
1. Na stránce **vytvořit cluster** zadejte název clusteru a potom v seznamu **Databricks Runtime verze** vyberte **7,2 (Scala 2,12, Spark 3.0.0)** .
1. Vyberte **Vytvořit cluster**.

### <a name="write-a-settings-notebook"></a>Zapsat Poznámkový blok nastavení

Nyní jste připraveni přidat poznámkové bloky Pythonu. Nejprve vytvořte Poznámkový blok s názvem **Nastavení**. Tento Poznámkový blok přiřadí hodnoty v tabulce Parametrizace do proměnných ve skriptu. Azure Data Factory bude později předávat hodnoty jako parametry. Do proměnných také přiřadíme hodnoty z tajných kódů v trezoru klíčů. 

1. Poznámkový blok pro **Nastavení** vytvoříte tak, že vyberete tlačítko **pracovní prostor** . Na kartě nový Vyberte rozevírací seznam a vyberte **vytvořit** a pak **Poznámkový blok**.
1. V automaticky otevíraném okně zadejte název poznámkového bloku a pak jako výchozí jazyk vyberte **Python** . Vyberte svůj cluster datacihly a pak vyberte **vytvořit**.
1. V první buňce poznámkového bloku načtoume parametry předané Azure Data Factory:

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

1. Do druhé buňky načteme tajné kódy z Key Vault a přiřadíme je do proměnných:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Napsat školicí notebook

Teď, když jsme dokončili Poznámkový blok **Nastavení** , můžeme vytvořit Poznámkový blok pro výuku modelu. Jak je uvedeno výše, použijeme soubory uložené ve složce v účtu Azure Data Lake Storage Gen2 (**training_blob_root_folder**). Název složky byl předán jako proměnná. Každá sada typů formulářů bude ve stejné složce. Při cyklické smyčce nad tabulkou parametrů budeme pomocí všech typů formulářů vyškolit model. 

1. Vytvořte Poznámkový blok s názvem **TrainFormRecognizer**. 
1. V první buňce spusťte Poznámkový blok **Nastavení** :

    ```python
    %run "./Settings"
    ```

1. V další buňce přiřaďte proměnné ze souboru nastavení a dynamicky proškolujte model pro každý typ formuláře a použijte kód v [rychlém startu REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

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
        # Request headers.
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
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
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
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Posledním krokem v procesu školení je získat výsledek školení ve formátu JSON:

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

## <a name="extract-form-data-by-using-a-notebook"></a>Extrakce dat formuláře pomocí poznámkového bloku

### <a name="mount-the-azure-data-lake-storage"></a>Připojení Azure Data Lakeho úložiště

Dalším krokem je vyznámení s různými formami, které máme pomocí trained model. Připojíme účet Azure Data Lake Storage v datacihlách a v průběhu procesu příjmu bude odkazovat na připojení.

Stejně jako jsme ve fázi školení, použijeme Azure Data Factory k vyvolání extrakce párů klíč/hodnota z formulářů. Přeskočíme na formuláře ve složkách, které jsou zadané v tabulce parametrů.

1. Vytvořte Poznámkový blok pro připojení účtu úložiště v datacihlech. Zavolejte ho **MountDataLake**. 
1. Nejdřív budete muset zavolat Poznámkový blok **Nastavení** :

    ```python
    %run "./Settings"
    ```

1. V druhé buňce Definujte proměnné pro citlivé parametry, které načteme z našich Key Vault tajných kódů:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Pokus o odpojení účtu úložiště v případě, že byl dříve připojen:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Připojte účet úložiště:


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
    > Připojili jsme se jenom školicí účet úložiště. V tomto případě jsou školicí soubory a soubory, ze kterých chceme extrahovat páry klíč/hodnota z, ve stejném účtu úložiště. Pokud se vaše bodování a školicí účty úložiště liší, budete muset připojit oba účty úložiště. 

### <a name="write-the-scoring-notebook"></a>Napsat Poznámkový blok pro vyhodnocování

Nyní můžeme vytvořit hodnoticí Poznámkový blok. Budeme postupovat podobně jako v školicím poznámkovém bloku: budeme používat soubory uložené ve složkách na účtu Azure Data Lake Storage, který jsme právě připojili. Název složky se předává jako proměnná. Přeskočíme všechny formuláře v zadané složce a z nich se extrahují páry klíč/hodnota. 

1. Vytvořte Poznámkový blok a zavolejte ho **ScoreFormRecognizer**. 
1. Spusťte **Nastavení** a poznámkové bloky **MountDataLake** :

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Přidejte tento kód, který volá rozhraní API pro [analýzu](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) :

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
        # Request headers.
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

1. V další buňce získáme výsledky extrakce páru klíč/hodnota. Tato buňka bude mít za následek výstup výsledku. Chceme mít výsledek ve formátu JSON, abychom ho mohli dál zpracovávat Azure SQL Database nebo Azure Cosmos DB. Proto zapíšeme výsledek do souboru. JSON. Název výstupního souboru bude název souboru s skóre, který je zřetězený s "_output.json". Soubor bude uložen ve stejné složce jako zdrojový soubor.

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

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Automatizace školení a bodování pomocí Azure Data Factory

Jediným krokem je nastavení služby Azure Data Factory pro automatizaci procesů školení a bodování. Nejprve postupujte podle kroků v části [Vytvoření datové továrny](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Po vytvoření prostředku Azure Data Factory budete muset vytvořit tři kanály: jednu pro školení a dvě pro bodování. (Vysvětlíme se později.)

### <a name="training-pipeline"></a>Školicí kanál

První aktivita v kanálu školení je vyhledávání pro čtení a vrácení hodnot v tabulce Parametrizace ve službě Azure SQL Database. Všechny datové sady školení budou ve stejném účtu úložiště a kontejneru (ale potenciálně jiné složky). Proto ponecháme výchozí hodnotu atributu **první řádek** v nastavení aktivity vyhledávání. Pro každý typ formuláře pro výuku modelu, provedeme model pomocí všech souborů v **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Snímek obrazovky, který zobrazuje školicí kanál v Data Factory.":::

Uložená procedura používá dva parametry: **model_id** a **form_batch_group_id**. Kód pro návrat ID modelu z poznámkového bloku datacihly je `dbutils.notebook.exit(model_id)` . Kód pro čtení kódu v aktivitě uložené procedury v Data Factory je `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Kanály bodování

Chcete-li extrahovat páry klíč/hodnota, prohledáme všechny složky v tabulce Parametrizace. Pro každou složku extrahujeme páry klíč/hodnota všech souborů, které jsou v něm. Azure Data Factory v současné době nepodporuje vnořené smyčky ForEach. Místo toho vytvoříme dva kanály. První kanál provede vyhledávání z tabulky Parametrizace a předá seznam složek jako parametr druhému kanálu.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Snímek obrazovky, který zobrazuje první vyhodnocování kanálu v Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Snímek obrazovky zobrazující podrobnosti prvního bodování kanálu v Data Factory.":::

Druhý kanál použije k získání seznamu souborů ve složce getmeta aktivitu a předáte ji jako parametr do poznámkového bloku pro bodování datacihly.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Snímek obrazovky, který zobrazuje druhý kanál bodování v Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Snímek obrazovky, který ukazuje podrobnosti pro druhý kanál bodování v Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Zadat stupeň paralelismu

V kanálech školení a bodování můžete určit míru paralelismu, abyste mohli současně zpracovat více formulářů.

Nastavení stupně paralelismu v kanálu Azure Data Factory:

1. Vyberte aktivitu **foreach** .
1. Zrušte zaškrtnutí políčka **sekvenční** .
1. Nastaví stupeň paralelismu v poli **počet dávek** . Pro účely bodování doporučujeme maximální počet dávek na 15.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Snímek obrazovky zobrazující konfiguraci paralelismu pro aktivitu bodování v Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>Jak používat kanál

Teď máte automatizovaný kanál k digitalizaci nevyřízených položek formulářů a provádění některých analýz nad ním. Když do existující složky úložiště přidáte nové formy známého typu, stačí znovu spustit bodování kanálů. Budou aktualizovat všechny výstupní soubory, včetně výstupních souborů pro nové formuláře. 

Pokud přidáte nové formy nového typu, budete také muset odeslat školicí datovou sadu do příslušného kontejneru. V dalším kroku přidáte nový řádek do tabulky Parametrizace a zadáte umístění nových dokumentů a jejich datovou sadu školení. Zadejte hodnotu-1 pro **model_ID** , která označuje, že pro formuláře musí být vyškolený nový model. Pak spusťte školicí kanál v Azure Data Factory. Kanál se přečte z tabulky, provede výuku modelu a přepíše ID modelu v tabulce. Pak můžete volat kanály bodování a začít psát výstupní soubory.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte Azure Data Factory kanály pro aktivaci školení a spouštění modelů pro rozpoznávání formulářů a k digitalizaci velkých nevyřízených položek souborů. Dále si Prozkoumejte rozhraní API pro rozpoznávání formulářů, abyste viděli, co s tím někdo s tím můžete dělat.

* [REST API pro rozpoznávání formulářů](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
