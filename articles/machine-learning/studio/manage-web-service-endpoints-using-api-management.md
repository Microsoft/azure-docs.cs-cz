---
title: Správa webových služeb pomocí API Management
titleSuffix: ML Studio (classic) - Azure
description: Příručka ukazuje, jak spravovat webové služby Azure ml pomocí služby API Management. Správa koncových bodů rozhraní REST API tak, že definujete uživatelský přístup, omezení využití a řídicí panel monitorování.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218026"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Správa webových služeb Azure Machine Learning Studio (Classic) pomocí API Management

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Přehled
V této příručce se dozvíte, jak rychle začít používat API Management ke správě webových služeb Azure Machine Learning Studio (Classic).

## <a name="what-is-azure-api-management"></a>Co je Azure API Management?
Azure API Management je služba Azure, která vám umožní spravovat definováním přístup uživatelů, omezení využití a řídicího panelu monitorování koncových bodů rozhraní REST API. Další podrobnosti najdete na [webu Azure API Management](https://azure.microsoft.com/services/api-management/) . Informace o tom, jak začít s Azure API Management, najdete v [příručce pro import a publikování](/azure/api-management/import-and-publish). Tato další příručku, která je na základě této příručce, popisuje další témata, včetně konfigurace oznámení, ceny, zpracování odpovědi, ověřování uživatelů, vytvoření produkty, předplatná pro vývojáře a mnoha využití.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce, budete potřebovat:

* Účet Azure.
* Účet Azure ml.
* Pracovní prostor, služby a klíč rozhraní API pro Azure ml experiment nasadit jako webovou službu. Podrobnosti o tom, jak vytvořit experiment AzureML, najdete v tématu [rychlý Start studia](create-experiment.md). Informace o tom, jak nasadit experiment (klasický) Studio jako webovou službu, najdete v tématu [nasazení studia](deploy-a-machine-learning-web-service.md) , které popisuje, jak nasadit experimenty AzureML jako webovou službu. Příloha A můžete také obsahuje pokyny, jak vytvořit a otestovat jednoduchý experiment AzureML a nasadit ho jako webovou službu.

## <a name="create-an-api-management-instance"></a>Vytvoření instance služby API Management

Webová služba Azure Machine Learning s instance služby API Management můžete spravovat.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **+ Vytvořit prostředek**.
3. Do vyhledávacího pole zadejte "API management" a pak vyberte prostředek "API management".
4. Klikněte na možnost **Vytvořit**.
5. Hodnota **název** se použije k vytvoření jedinečné adresy URL (v tomto příkladu se používá "demoazureml").
6. Vyberte **předplatné**, **skupinu prostředků**a **umístění** pro instanci služby.
7. Zadejte hodnotu pro **název organizace** (Tento příklad používá "demoazureml").
8. Zadejte **e-mailovou zprávu správce** – tento e-mail se bude používat pro oznámení z API Managementho systému.
9. Klikněte na možnost **Vytvořit**.

Může trvat až 30 minut, pro který se má vytvořit novou službu.

![vytvoření služby](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Vytvoření rozhraní API
Po vytvoření instance služby je dalším krokem je vytvoření rozhraní API. Rozhraní API se skládá ze sady operací, které můžete vyvolat z klientské aplikace. Operace rozhraní API se odesílají přes proxy servery do existujících webových služeb. Tento průvodce vytvoří tento proxy server do existující webové služby Azure ml RRS a BES rozhraní API.

Vytvoření rozhraní API:

1. V Azure Portal otevřete instanci služby, kterou jste vytvořili.
2. V levém navigačním podokně vyberte **rozhraní API**.

   ![Nabídka Správa rozhraní API](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klikněte na **Přidat rozhraní API**.
2. Zadejte **název webového rozhraní API** (Tento příklad používá rozhraní API pro ukázku demo).
3. Jako **adresu URL webové služby**zadejte "`https://ussouthcentral.services.azureml.net`".
4. Zadejte ** přípona adresy URL webového rozhraní API ". To se stane poslední část adresy URL, které zákazníci budou používat pro odesílání požadavků na instanci služby (v tomto příkladu "azureml-demo").
5. V případě **schématu URL webového rozhraní API**vyberte **https**.
6. V případě **produktů**vyberte **Starter**.
7. Klikněte na **Uložit**.


## <a name="add-the-operations"></a>Přidání operací

Operace jsou přidány a nakonfigurovat tak, aby rozhraní API portálu vydavatele. Chcete-li získat přístup k portálu vydavatele, klikněte na **portál vydavatele** v Azure Portal služby API Management, vyberte možnost **rozhraní API**, **operace**a klikněte na tlačítko **přidat operaci**.

![Přidání operace](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Zobrazí se **nové okno operace** a ve výchozím nastavení se vybere karta **podpis** .

## <a name="add-rrs-operation"></a>RRS operace přidání
Nejprve vytvořte operace pro službu Azure ml RRS:

1. Jako **příkaz HTTP**vyberte post ( **Odeslat**).
2. Pro **šablonu adresa URL**zadejte "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Zadejte **Zobrazovaný název** (v tomto příkladu se používá "záznamy k provedení").

   ![Přidat rrs operace podpisu](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klikněte na tlačítko **odpovědi** > **přidejte** vlevo a vyberte **200 OK**.
5. Kliknutím na **Uložit** uložte tuto operaci.

   ![Přidat rrs operace – odpověď](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Přidání operací BES

> [!NOTE]
> Snímky obrazovky nejsou zahrnuty zde pro BES operace jsou velmi podobné těm, které pro přidání operace RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Odeslání (ale není spuštěno) úlohy provedení dávky služby

1. Kliknutím na **přidat operaci** přidejte do rozhraní API operaci BES.
2. Jako **příkaz HTTP**vyberte post ( **Odeslat**).
3. Pro **šablonu adresa URL**zadejte "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Zadejte **Zobrazovaný název** (v tomto příkladu se používá "BES Submit").
5. Klikněte na tlačítko **odpovědi** > **přidejte** vlevo a vyberte **200 OK**.
6. Klikněte na **Uložit**.

### <a name="start-a-batch-execution-job"></a>Spuštění úlohy provedení dávky služby

1. Kliknutím na **přidat operaci** přidejte do rozhraní API operaci BES.
2. Jako **příkaz HTTP**vyberte post ( **Odeslat**).
3. Pro **příkaz HTTP**zadejte "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Zadejte **Zobrazovaný název** (v tomto příkladu se používá "BES Start").
6. Klikněte na tlačítko **odpovědi** > **přidejte** vlevo a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Získat stav nebo výsledek úlohy provedení dávky služby

1. Kliknutím na **přidat operaci** přidejte do rozhraní API operaci BES.
2. Pro **příkaz HTTP**vyberte **získat**.
3. Pro **šablonu adresa URL**zadejte "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Zadejte **Zobrazovaný název** (v tomto příkladu se používá "BES status").
6. Klikněte na tlačítko **odpovědi** > **přidejte** vlevo a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="delete-a-batch-execution-job"></a>Odstraňování úlohy provedení dávky služby

1. Kliknutím na **přidat operaci** přidejte do rozhraní API operaci BES.
2. Pro **příkaz HTTP**vyberte **Odstranit**.
3. Pro **šablonu adresa URL**zadejte "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Zadejte **Zobrazovaný název** (v tomto příkladu se používá "BES Delete").
5. Klikněte na tlačítko **odpovědi** > **přidejte** vlevo a vyberte **200 OK**.
6. Klikněte na **Uložit**.

## <a name="call-an-operation-from-the-developer-portal"></a>Volání operace z portálu pro vývojáře

Operace lze volat přímo z portálu pro vývojáře, která poskytuje pohodlný způsob, jak zobrazit a testování operací v rozhraní API. V tomto kroku zavoláte metodu **Execute RR** , která byla přidána do **rozhraní API demo demo**. 

1. Klikněte na **portál pro vývojáře**.

   ![portál pro vývojáře](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. V horní nabídce klikněte na **rozhraní API** a potom kliknutím na **ukázka rozhraní API AzureML** zobrazte dostupné operace.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Vyberte položku **RR Execute** pro operaci. Klikněte na tlačítko **vyzkoušet**.

   ![Try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. V části **parametry žádosti**zadejte svůj **pracovní prostor** a **službu**, do pole **apiversion**zadejte "2,0" a "true" ( **Podrobnosti**). **Pracovní prostor** a **službu** můžete najít na řídicím panelu webové služby AzureML (viz **testování webové služby** v dodatku A).

   V případě **hlaviček žádosti**klikněte na **Přidat hlavičku** a zadejte typ Content-Type a Application/JSON. Klikněte znovu na **Přidat hlavičku** a zadejte "Authorization" a "nosič *\<rozhraní API služby\>* ". Svůj klíč API-KEY můžete najít na řídicím panelu webové služby AzureML (viz **Test webové služby** v dodatku A).

   V části **Text žádosti**zadejte `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klikněte na **Odeslat**.

   ![Odeslat](./media/manage-web-service-endpoints-using-api-management/send.png)

Po vyvolání operace se na portálu pro vývojáře zobrazí **požadovaná adresa URL** ze služby back-end, **stavu odpovědi**, **hlaviček odpovědí**a libovolného **obsahu odpovědi**.

![Stav odpovědi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Příloha A – vytvoření a otestování jednoduché AzureML webové služby
### <a name="creating-the-experiment"></a>Vytvoření experimentu
Níže je uvedený postup pro vytvoření jednoduchého experimentu v Azure ml a jeho nasazení jako webové služby. Přijímá webové služby, jako vstupní sloupce libovolného textu a vrátí sadu funkcí vyjádřena jako celá čísla. Příklad:

| Text | Hodnoty hash Text |
| --- | --- |
| To je dobrý den |1 1 2 2 0 2 0 1 |

Nejprve v prohlížeči podle svého výběru přejděte na: [https://studio.azureml.net/](https://studio.azureml.net/) a zadejte přihlašovací údaje pro přihlášení. Dále vytvořte nový experiment prázdné.

![Hledat experiment šablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Přejmenujte ho na **SimpleFeatureHashingExperiment**. Rozšiřte **uložené datové sady** a přetáhněte **recenze knih z Amazon** do svého experimentu.

![jednoduché – funkce – algoritmu hash experimentu](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozbalte **transformaci** a **manipulaci** s daty a přetáhněte na svůj experiment **možnost vybrat sloupce v datové sadě** . Umožňuje propojit **recenze knih z Amazon** a **Vybrat sloupce v datové sadě**.

![Připojit modul sady dat recenze knihy k modulu sloupce projektu](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klikněte na **Vybrat sloupce v datové sadě** a pak klikněte na **Spustit selektor sloupců** a vyberte **col2**. Kliknutím na značku zaškrtnutí k použití těchto změn.

![Vybrat sloupce s použitím názvů sloupců](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozbalí **Analýza textu** a přetáhnout **funkci hashování funkcí** do experimentu. Připojit **Výběr sloupců v datové sadě** k **funkci hashování funkcí**

![Připojte se sloupce projektu](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Jako **hodnotu hash bitsize**zadejte **3** . Tím se vytvoří 8 (23) sloupce.

![hashování bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

V tomto okamžiku můžete chtít otestovat experiment kliknutím na tlačítko **Spustit** .

![Spuštění](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Vytvoření webové služby
Teď vytvořte webovou službu. Rozbalte položku **Webová služba** a přetáhněte **vstup** do experimentu. Připojte **vstup** k **algoritmu hashování funkcí**. Také přetáhněte **výstup** do experimentu. Připojte **výstup** k **funkcím hashování funkcí**.

![výstup na--hashování](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klikněte na **publikovat webovou službu**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Pro publikování experimentu klikněte na tlačítko **Ano** .

![Ano k publikování](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Test webové služby
Webové služby Azure ml se skládá z kanálu RSS (požadavku nebo odpovědi služby) a BES (služba batch execution) koncových bodů. RSS je pro synchronní zpracování. BES je pro provádění asynchronní úlohy. Pokud chcete otestovat webovou službu pomocí níže uvedeného ukázkového zdroje Pythonu, možná budete muset stáhnout a nainstalovat sadu Azure SDK pro Python (viz: [Jak nainstalovat Python](/azure/python/python-sdk-azure-install)).

K níže uvedenému zdroji ukázek budete potřebovat **pracovní prostor**, **službu**a **api_key** svého experimentu. Pracovní prostor a službu můžete najít tak, že na řídicím panelu webové služby kliknete buď na **žádost, odpověď** , nebo na **provedení dávky** .

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

**Api_key** můžete najít kliknutím na experiment na řídicím panelu webové služby.

![klíč rozhraní api pro hledání](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Koncový bod RRS testu
##### <a name="test-button"></a>Tlačítko Testovat
Snadný způsob, jak otestovat koncový bod záznamů, je kliknout na **test** na řídicím panelu webové služby.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **je dobrý den** pro **col2**. Kliknutím na značku zaškrtnutí.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zobrazí se něco jako

![Ukázkový výstup](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Vzorový kód
Dalším způsobem, jak testovat vaše RRS je z klientského kódu. Pokud na řídicím panelu kliknete na **požadavek/odpověď** a posuňte se k dolnímu okraji, C#zobrazí se ukázka kódu pro, Python a R. Zobrazí se také syntaxe žádosti o záznamy o prostředku, včetně identifikátoru URI, hlaviček a textu žádosti.

Tento průvodce ukazuje funkční příklad v Pythonu. Budete ho muset upravit pomocí **pracovního prostoru**, **služby**a **api_key** svého experimentu.

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

#### <a name="test-bes-endpoint"></a>Koncový bod BES testu
Na řídicím panelu klikněte na **spuštění dávky** a posuňte se k dolnímu. Zobrazí se ukázkový kód pro C#, Python a R. Zobrazí se také syntaxi BES požadavků na odeslání úlohy, spustit úlohu, získat stav nebo výsledků úlohy a úlohu můžete odstranit.

Tento průvodce ukazuje funkční příklad v Pythonu. Musíte ho upravit pomocí **pracovního prostoru**, **služby**a **api_key** svého experimentu. Kromě toho je potřeba upravit **název účtu úložiště**, **klíč účtu úložiště**a **název kontejneru úložiště**. Nakonec budete muset změnit umístění **vstupního souboru** a umístění **výstupního souboru**.

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
