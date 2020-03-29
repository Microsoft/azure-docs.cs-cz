---
title: Správa webových služeb pomocí správy rozhraní API
titleSuffix: ML Studio (classic) - Azure
description: Průvodce, který ukazuje, jak spravovat webové služby AzureML pomocí správy rozhraní API. Spravujte koncové body rozhraní REST API definováním přístupu uživatelů, omezení využití a monitorování řídicího panelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218026"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Správa webových služeb Azure Machine Learning Studio (klasické) pomocí správy rozhraní API

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Přehled
Tato příručka vám ukáže, jak rychle začít používat službu API Management ke správě webových služeb Azure Machine Learning Studio (klasické).

## <a name="what-is-azure-api-management"></a>Co je Azure API Management?
Azure API Management je služba Azure, která umožňuje spravovat koncové body rozhraní REST API definováním přístupu uživatelů, omezení využití a monitorování řídicího panelu. Další podrobnosti najdete na [webu pro správu rozhraní Azure API.](https://azure.microsoft.com/services/api-management/) Pokud chcete začít se správou rozhraní Azure API, [přečtěte si průvodce importem a publikováním](/azure/api-management/import-and-publish). Tato další příručka, na které je tato příručka založena, popisuje další témata, včetně konfigurací oznámení, cen úrovní, zpracování odpovědí, ověřování uživatelů, vytváření produktů, předplatných vývojářů a řídicích panelů využití.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tuto příručku, potřebujete:

* Účet Azure.
* Účet AzureML.
* Pracovní prostor, služba a api_key pro experiment AzureML nasazený jako webová služba. Podrobnosti o tom, jak vytvořit experiment AzureML, naleznete v [úvodním panelu studio](create-experiment.md). Informace o tom, jak nasadit experiment Studio (klasické) jako webové služby, najdete v tématu [studio nasazení návod](deploy-a-machine-learning-web-service.md) na podrobnosti o tom, jak nasadit experiment AzureML jako webové služby. Dodatek A má také pokyny k vytvoření a testování jednoduchého experimentu AzureML a jeho nasazení jako webové služby.

## <a name="create-an-api-management-instance"></a>Vytvoření instance služby API Management

Webovou službu Azure Machine Learning můžete spravovat pomocí instance api management.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **+ Vytvořit prostředek**.
3. Do vyhledávacího pole zadejte "Správa rozhraní API" a vyberte prostředek "Správa rozhraní API".
4. Klikněte na **Vytvořit**.
5. Název **Name** Hodnota bude použita k vytvoření jedinečné adresy URL (v tomto příkladu používá "demoazureml").
6. Vyberte **předplatné**, **skupinu prostředků**a **umístění** pro instanci služby.
7. Zadejte hodnotu názvu **organizace** (tento příklad používá "demoazureml").
8. Zadejte **e-mail správce** – tento e-mail bude použit pro oznámení ze systému správy rozhraní API.
9. Klikněte na **Vytvořit**.

Vytvoření nové služby může trvat až 30 minut.

![vytvořit službu](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Vytvoření rozhraní API
Po vytvoření instance služby je dalším krokem vytvoření rozhraní API. Rozhraní API se skládá ze sady operací, které můžete vyvolat z klientské aplikace. Operace rozhraní API se odesílají přes proxy servery do existujících webových služeb. Tato příručka vytvoří api, která proxy pro stávající AzureML RRS a BES webové služby.

Vytvoření rozhraní API:

1. Na webu Azure Portal otevřete instanci služby, kterou jste vytvořili.
2. V levém navigačním podokně vyberte **možnost API**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klepněte na tlačítko **Přidat rozhraní API**.
2. Zadejte **název webového rozhraní API** (tento příklad používá "AzureML Demo API").
3. Pro adresu URL webové`https://ussouthcentral.services.azureml.net` **služby**zadejte " ".
4. Zadejte příponu URL **Web API". To se stane poslední část adresy URL, které zákazníci budou používat pro odesílání požadavků na instanci služby (tento příklad používá "azureml-demo").
5. V **případě schématu adresy URL webového rozhraní API**vyberte možnost **HTTPS**.
6. V **případě produktů**vyberte možnost **Startér**.
7. Klikněte na **Uložit**.


## <a name="add-the-operations"></a>Přidání operací

Operace jsou přidány a nakonfigurovány do rozhraní API na portálu vydavatele. Pokud chcete získat přístup k portálu vydavatele, klikněte na **portálu Vydavatel** na portálu Azure pro vaši službu Api Management, vyberte **rozhraní API**, **Operace**a potom klikněte na **Přidat operaci**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Zobrazí se okno **Nová operace** a ve výchozím nastavení bude vybrána karta **Podpis.**

## <a name="add-rrs-operation"></a>Přidat operaci rrs
Nejprve vytvořte operaci pro službu AzureML RRS:

1. Pro **sloveso HTTP**vyberte **POST**.
2. Do **šablony adresy**URL`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`zadejte " ".
3. Zadejte **zobrazovaný název** (tento příklad používá "RRS Execute").

   ![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Vlevo klikněte**na** **Přidat odpovědi** > a vyberte **200 OK**.
5. Chcete-li tuto operaci uložit, klepněte na tlačítko **Uložit.**

   ![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Přidat operace BES

> [!NOTE]
> Snímky obrazovky zde nejsou zahrnuty pro operace BES, protože jsou velmi podobné těm, které jsou pro přidání operace RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Odeslat (ale ne spustit) úlohu dávkového spuštění

1. Chcete-li přidat operaci BES do rozhraní API, klepněte na tlačítko **Přidat operaci.**
2. Pro **sloveso HTTP**vyberte **POST**.
3. Do **šablony adresy**URL`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`zadejte " ".
4. Zadejte **zobrazovaný název** (tento příklad používá "BES Submit").
5. Vlevo klikněte**na** **Přidat odpovědi** > a vyberte **200 OK**.
6. Klikněte na **Uložit**.

### <a name="start-a-batch-execution-job"></a>Spuštění úlohy dávkového spuštění

1. Chcete-li přidat operaci BES do rozhraní API, klepněte na tlačítko **Přidat operaci.**
2. Pro **sloveso HTTP**vyberte **POST**.
3. Pro **sloveso**HTTP`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`zadejte " ".
4. Zadejte **zobrazovaný název** (tento příklad používá "BES Start").
6. Vlevo klikněte**na** **Přidat odpovědi** > a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Získání stavu nebo výsledku úlohy dávkového spuštění

1. Chcete-li přidat operaci BES do rozhraní API, klepněte na tlačítko **Přidat operaci.**
2. Pro **sloveso HTTP**vyberte **PŘÍKAZ**.
3. Do **šablony adresy**URL`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`zadejte " ".
4. Zadejte **zobrazovaný název** (tento příklad používá "Stav BES").
6. Vlevo klikněte**na** **Přidat odpovědi** > a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="delete-a-batch-execution-job"></a>Odstranit úlohu dávkového spuštění

1. Chcete-li přidat operaci BES do rozhraní API, klepněte na tlačítko **Přidat operaci.**
2. U **slovesa HTTP**vyberte **příkaz DELETE**.
3. Do **šablony adresy**URL`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`zadejte " ".
4. Zadejte **zobrazovaný název** (tento příklad používá "BES Delete").
5. Vlevo klikněte**na** **Přidat odpovědi** > a vyberte **200 OK**.
6. Klikněte na **Uložit**.

## <a name="call-an-operation-from-the-developer-portal"></a>Volání operace z portálu pro vývojáře

Operace lze volat přímo z portálu pro vývojáře, který poskytuje pohodlný způsob zobrazení a testování operací rozhraní API. V tomto kroku budete volat **rrs spuštění** metody, která byla přidána do **rozhraní AzureML Demo API**. 

1. Klikněte na **portál pro vývojáře**.

   ![vývojářský portál](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klikněte na **rozhraní API** v horní nabídce a potom klikněte na **AzureML Demo API** zobrazíte dostupné operace.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Vyberte **možnost Spuštění rrs** pro operaci. Klepněte na **tlačítko Vyzkoušet**.

   ![zkuste-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Pro **parametry požadavku**zadejte **pracovní prostor** a **službu**, zadejte "2.0 pro **apiversion**a "true" pro **podrobnosti**. Pracovní **prostor** a **službu** najdete na řídicím panelu webové služby AzureML (viz **Testování webové služby** v dodatku A).

   U **záhlaví požadavku**klepněte na tlačítko Přidat **záhlaví** a zadejte "Content-Type" a "application/json". Klepněte na tlačítko **Přidat záhlaví** znovu a zadejte "Autorizace" a "Nosič * \<vaší služby API-KEY\>*". Klíč API najdete na řídicím panelu webové služby AzureML (viz **Testování webové služby** v dodatku A).

   Pro **tělo**požadavku `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`zadejte .

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klikněte na **Odeslat**.

   ![Odeslat](./media/manage-web-service-endpoints-using-api-management/send.png)

Po vyvolání operace se na vývojářském portálu zobrazí **požadovaná adresa URL** ze služby back-end, **stav odpověď**, **záhlaví odpovědí**a veškerý **obsah odpovědi**.

![stav odpovědi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A – vytvoření a testování jednoduché webové služby AzureML
### <a name="creating-the-experiment"></a>Vytvoření experimentu
Níže jsou uvedeny kroky pro vytvoření jednoduchého experimentu AzureML a jeho nasazení jako webové služby. Webová služba bere jako vstup sloupec libovolného textu a vrátí sadu funkcí reprezentované jako celá čísla. Například:

| Text | Zahasovaný text |
| --- | --- |
| Tohle je dobrý den. |1 1 2 2 0 2 0 1 |

Nejprve pomocí prohlížeče podle vašeho výběru přejděte na: [https://studio.azureml.net/](https://studio.azureml.net/) a zadejte své přihlašovací údaje pro přihlášení. Dále vytvořte nový prázdný experiment.

![hledat-experiment-šablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Přejmenujte jej na **SimpleFeatureHashingExperiment**. Rozbalte **uložené datové sady** a přetáhněte recenze knih z **Amazonu** do experimentu.

![jednoduchý-funkce-hash-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozbalte transformaci a **manipulaci s** **daty** a přetáhněte **možnost Vybrat sloupce v datové sadě** do experimentu. Připojte **recenze knih z Amazonu** k **vybraným sloupcům v datové sadě**.

![Připojení modulu datové sady revizí knihy k modulu Sloupce projektu](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klepněte na **vybrat sloupce v datové sadě** a potom klepněte na příkaz Spustit výběr **sloupců** a vyberte **možnost Col2**. Chcete-li tyto změny použít, klepněte na políčko zaškrtnutí.

![Výběr sloupců pomocí názvů sloupců](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozbalte **analýzu textu** a přetáhněte **funkci Hashing** do experimentu. Připojte **vybrané sloupce v datové sadě** k nastavení **hash prvků**.

![připojit-projekt-sloupce](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Zadejte **3** pro **bitvelikost hashování**. Tím se vytvoří 8 (23) sloupců.

![velikost bitů hash](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

V tomto okamžiku můžete klepnout na tlačítko **Spustit** a otestovat experiment.

![Spustit](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Vytvoření webové služby
Nyní vytvořte webovou službu. Rozbalte **webovou službu** a přetáhněte **položku** do experimentu. Připojte **vstup** k **funkci Hash .** Také přetáhněte **výstup** do experimentu. Připojte **výstup** k **funkci Hashing**.

![hash výstupu k funkci](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klepněte na **tlačítko Publikovat webovou službu**.

![publikovat web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Chcete-li experiment publikovat, klepněte na tlačítko **Ano.**

![ano-publikovat](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Otestování webové služby
Webová služba AzureML se skládá z koncových bodů RSS (služba požadavku a odpovědi) a bes (služba dávkového spuštění). RSS je pro synchronní provádění. BES je pro asynchronní provádění úloh. Chcete-li otestovat webovou službu s ukázkovým zdrojem Pythonu níže, možná budete muset stáhnout a nainstalovat Azure SDK pro Python (viz: [Jak nainstalovat Python](/azure/python/python-sdk-azure-install)).

Budete také potřebovat **pracovní prostor**, **službu**a **api_key** experimentu pro níže uvedený ukázkový zdroj. Pracovní prostor a službu můžete najít kliknutím na **požadavek/odpověď** nebo **dávkové spuštění** experimentu na řídicím panelu webové služby.

![najít pracovní prostor a službu](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

**api_key** můžete najít kliknutím na experiment na řídicím panelu webové služby.

![najít-api-klíč](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Koncový bod testu RRS
##### <a name="test-button"></a>Tlačítko Test
Snadný způsob testování koncového bodu RRS je kliknout na **tlačítko Testovat** na řídicím panelu webové služby.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **To je dobrý den** pro **col2**. Klikněte na zaškrtnutí.

![zadávání dat](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Uvidíte něco jako

![vzorek-výstup](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Příklad kódu
Dalším způsobem, jak otestovat váš RRS je z kódu klienta. Pokud kliknete na **požadavek/odpověď** na řídicím panelu a posunete se dolů, zobrazí se ukázkový kód pro C#, Python a R. Zobrazí se také syntaxe požadavku na rrs, včetně identifikátoru URI požadavku, záhlaví a těla.

Tato příručka ukazuje funkční příklad Pythonu. Budete jej muset upravit pomocí **pracovního prostoru**, **služby**a **api_key** experimentu.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Koncový bod bes testu
Na řídicím panelu klikněte na **Dávkové spuštění** a posuňte se dolů. Uvidíte ukázkový kód pro C#, Python a R. Uvidíte také syntaxi požadavků BES na odeslání úlohy, zahájení úlohy, získání stavu nebo výsledků úlohy a odstranění úlohy.

Tato příručka ukazuje funkční příklad Pythonu. Je třeba jej upravit pomocí **pracovního prostoru**, **služby**a **api_key** experimentu. Kromě toho je třeba upravit **název účtu úložiště**, klíč účtu **úložiště**a **název kontejneru úložiště**. Nakonec budete muset změnit umístění **vstupního souboru** a umístění **výstupního souboru**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
