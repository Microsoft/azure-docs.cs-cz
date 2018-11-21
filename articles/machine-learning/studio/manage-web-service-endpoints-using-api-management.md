---
title: Zjistěte, jak spravovat webové služby Azure ml pomocí služby API Management | Dokumentace Microsoftu
description: Příručka ukazuje, jak spravovat webové služby Azure ml pomocí služby API Management.
keywords: ve službě Machine learning, api management
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: bc0c8f40c8e2fb75e95e1745b53332aa8e4588ca
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265215"
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Zjistěte, jak spravovat webové služby Azure ML pomocí služby API Management
## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak rychle začít používat ke správě webové služby Azure ml API Management.

## <a name="what-is-azure-api-management"></a>Co je Azure API Management?
Azure API Management je služba Azure, která vám umožní spravovat definováním přístup uživatelů, omezení využití a řídicího panelu monitorování koncových bodů rozhraní REST API. Klikněte na tlačítko [tady](https://azure.microsoft.com/services/api-management/) podrobné informace o službě Azure API Management. Klikněte na tlačítko [tady](../../api-management/api-management-get-started.md) pokyny o tom, jak začít pracovat s Azure API Management. Tato další příručku, která je na základě této příručce, popisuje další témata, včetně konfigurace oznámení, ceny, zpracování odpovědi, ověřování uživatelů, vytvoření produkty, předplatná pro vývojáře a mnoha využití.

## <a name="what-is-azureml"></a>Co je Azure ml?
Azure ml je služba Azure pro strojové učení, který umožňuje snadno vytvářet, nasazovat a sdílet Pokročilá analytická řešení. Klikněte na tlačítko [tady](https://azure.microsoft.com/services/machine-learning/) podrobné informace o Azure ml.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce, budete potřebovat:

* Účet Azure. Pokud nemáte účet Azure, klikněte na tlačítko [tady](https://azure.microsoft.com/pricing/free-trial/) podrobnosti o tom, jak vytvořit Bezplatný zkušební účet.
* Účet Azure ml. Pokud nemáte účet Azure ml, klikněte na tlačítko [tady](https://studio.azureml.net/) podrobnosti o tom, jak vytvořit Bezplatný zkušební účet.
* Pracovní prostor, služby a klíč rozhraní API pro Azure ml experiment nasadit jako webovou službu. Klikněte na tlačítko [tady](create-experiment.md) podrobnosti o tom, jak vytvořit AzureML experiment. Klikněte na tlačítko [tady](publish-a-machine-learning-web-service.md) pro podrobnosti o tom, jak nasadit Azure ml experiment jako webové služby. Příloha A můžete také obsahuje pokyny, jak vytvořit a otestovat jednoduchý experiment AzureML a nasadit ho jako webovou službu.

## <a name="create-an-api-management-instance"></a>Vytvoření instance služby API Management

Webová služba Azure Machine Learning s instance služby API Management můžete spravovat.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **+ Vytvořit prostředek**.
3. Do vyhledávacího pole zadejte "API management" a pak vyberte prostředek "API management".
4. Klikněte na možnost **Vytvořit**.
5. **Název** hodnota se použije k vytvoření jedinečnou adresu URL (v tomto příkladu "demoazureml").
6. Vyberte **předplatné**, **skupiny prostředků**, a **umístění** vaší instance služby.
7. Zadejte hodnotu pro **název organizace** (v tomto příkladu "demoazureml").
8. Zadejte vaše **e-mailu správce** -tento e-mail se bude používat pro oznámení ze systému API Management.
9. Klikněte na možnost **Vytvořit**.

Může trvat až 30 minut, pro který se má vytvořit novou službu.

![vytvoření služby](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Vytvoření rozhraní API
Po vytvoření instance služby je dalším krokem je vytvoření rozhraní API. Rozhraní API se skládá ze sady operací, které můžete vyvolat z klientské aplikace. Operace rozhraní API se odesílají přes proxy servery do existujících webových služeb. Tento průvodce vytvoří tento proxy server do existující webové služby Azure ml RRS a BES rozhraní API.

Vytvoření rozhraní API:

1. Na webu Azure Portal otevřete místní instance služby, kterou jste právě vytvořili.
2. V levém navigačním podokně vyberte **rozhraní API**.

   ![Nabídka Správa rozhraní API](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klikněte na tlačítko **přidat rozhraní API**.
2. Zadejte **název webového rozhraní API** (v tomto příkladu "AzureML ukázku rozhraní API").
3. Pro **adresu URL webové služby**, zadejte "`https://ussouthcentral.services.azureml.net`".
4. Zadejte ** přípona adresy URL webového rozhraní API ". To se stane poslední část adresy URL, které zákazníci budou používat pro odesílání požadavků na instanci služby (v tomto příkladu "azureml-demo").
5. Pro **schéma adresy URL webového rozhraní API**vyberte **HTTPS**.
6. Pro **produkty**vyberte **Starter**.
7. Klikněte na **Uložit**.


## <a name="add-the-operations"></a>Přidání operací

Operace jsou přidány a nakonfigurovat tak, aby rozhraní API portálu vydavatele. Pro přístup k portálu pro vydavatele, klikněte na tlačítko **portál pro vydavatele** na webu Azure portal pro vaši službu API Management vyberte **rozhraní API**, **operace**, pak klikněte na tlačítko **Operace přidání**.

![Přidání operace](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**Novou operaci** zobrazí se okno a **podpis** ve výchozím nastavení bude vybraná karta.

## <a name="add-rrs-operation"></a>RRS operace přidání
Nejprve vytvořte operace pro službu Azure ml RRS:

1. Pro **příkaz HTTP**vyberte **příspěvek**.
2. Pro **adresu URL šablony**, typ "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Zadejte **zobrazovaný název** (v tomto příkladu "RRS spuštění").

   ![Přidat rrs operace podpisu](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klikněte na tlačítko **odpovědi** > **přidat** na levé straně a vyberte **200 OK**.
5. Klikněte na tlačítko **Uložit** uložte tuto operaci.

   ![Přidat rrs operace – odpověď](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Přidání operací BES

> [!NOTE]
> Snímky obrazovky nejsou zahrnuty zde pro BES operace jsou velmi podobné těm, které pro přidání operace RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Odeslání (ale není spuštěno) úlohy provedení dávky služby

1. Klikněte na tlačítko **operace přidání** přidáte BES operaci rozhraní API.
2. Pro **příkaz HTTP**vyberte **příspěvek**.
3. Pro **adresu URL šablony**, typ "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Zadejte **zobrazovaný název** (v tomto příkladu "BES odeslat").
5. Klikněte na tlačítko **odpovědi** > **přidat** na levé straně a vyberte **200 OK**.
6. Klikněte na **Uložit**.

### <a name="start-a-batch-execution-job"></a>Spuštění úlohy provedení dávky služby

1. Klikněte na tlačítko **operace přidání** přidáte BES operaci rozhraní API.
2. Pro **příkaz HTTP**vyberte **příspěvek**.
3. Pro **příkaz HTTP**, typ "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Zadejte **zobrazovaný název** (v tomto příkladu "BES Start").
6. Klikněte na tlačítko **odpovědi** > **přidat** na levé straně a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Získat stav nebo výsledek úlohy provedení dávky služby

1. Klikněte na tlačítko **operace přidání** přidáte BES operaci rozhraní API.
2. Pro **příkaz HTTP**vyberte **získat**.
3. Pro **adresu URL šablony**, typ "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Zadejte **zobrazovaný název** (v tomto příkladu "BES stav").
6. Klikněte na tlačítko **odpovědi** > **přidat** na levé straně a vyberte **200 OK**.
7. Klikněte na **Uložit**.

### <a name="delete-a-batch-execution-job"></a>Odstraňování úlohy provedení dávky služby

1. Klikněte na tlačítko **operace přidání** přidáte BES operaci rozhraní API.
2. Pro **příkaz HTTP**vyberte **odstranit**.
3. Pro **adresu URL šablony**, typ "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Zadejte **zobrazovaný název** (v tomto příkladu "BES odstranění").
5. Klikněte na tlačítko **odpovědi** > **přidat** na levé straně a vyberte **200 OK**.
6. Klikněte na **Uložit**.

## <a name="call-an-operation-from-the-developer-portal"></a>Volání operace z portálu pro vývojáře

Operace lze volat přímo z portálu pro vývojáře, která poskytuje pohodlný způsob, jak zobrazit a testování operací v rozhraní API. V tomto kroku budete volat **RRS provést** metodu, která byla přidána do **Azure ml API ukázka**. 

1. Klikněte na tlačítko **portál pro vývojáře**.

   ![portál pro vývojáře](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klikněte na tlačítko **rozhraní API** z hlavní nabídky a pak klikněte na tlačítko **Azure ml API ukázka** zobrazíte dostupné operace.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Vyberte **provést RRS** pro operaci. Klikněte na tlačítko **vyzkoušet**.

   ![Try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Pro **parametry žádosti**, typ vašeho **pracovní prostor** a **služby**, typ "2.0 pro **apiversion**a"true"pro **podrobnosti**. Můžete najít vaše **pracovní prostor** a **služby** AzureML řídicím panelu webové služby (naleznete v tématu **testovat webovou službu** v dodatku A).

   Pro **hlavičky požadavku**, klikněte na tlačítko **přidat záhlaví** a zadejte "Content-Type" a "application/json". Klikněte na tlačítko **přidat záhlaví** znovu a zadejte "Autorizace" a "nosiče  *\<vaše služba API-KEY\>*". KLÍČ rozhraní API můžete najít na řídicím panelu služby Azure ml web (naleznete v tématu **testovat webovou službu** v dodatku A).

   Pro **text žádosti**, typ `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klikněte na tlačítko **odeslat**.

   ![Odeslat](./media/manage-web-service-endpoints-using-api-management/send.png)

Po vyvolání operace zobrazí portál pro vývojáře **požadovaná adresa URL** z back-end služby **stav odpovědi**, **hlavičky odpovědi**a jakékoli  **Obsah odpovědi**.

![Stav odpovědi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Příloha A – vytvoření a otestování jednoduché AzureML webové služby
### <a name="creating-the-experiment"></a>Vytvoření experimentu
Níže je uvedený postup pro vytvoření jednoduchého experimentu v Azure ml a jeho nasazení jako webové služby. Přijímá webové služby, jako vstupní sloupce libovolného textu a vrátí sadu funkcí vyjádřena jako celá čísla. Příklad:

| Text | Hodnoty hash Text |
| --- | --- |
| To je dobrý den |1 1 2 2 0 2 0 1 |

Pomocí prohlížeče podle vašeho výběru, nejprve přejděte do: [ https://studio.azureml.net/ ](https://studio.azureml.net/) a zadejte svoje přihlašovací údaje pro přihlášení. Dále vytvořte nový experiment prázdné.

![Hledat experiment šablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Přejmenujte ho na **SimpleFeatureHashingExperiment**. Rozbalte **uložení datové sady** a přetáhněte **revize kniha od Amazonu** do experimentu.

![jednoduché – funkce – algoritmu hash experimentu](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozbalte **transformace dat** a **manipulaci s** a přetáhněte **výběr sloupců v datové sadě** do experimentu. Připojit **rezervuje kontroly od Amazonu** k **výběr sloupců v datové sadě**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klikněte na tlačítko **výběr sloupců v datové sadě** a potom klikněte na tlačítko **spustit selektor sloupců** a vyberte **Col2**. Kliknutím na značku zaškrtnutí k použití těchto změn.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozbalte **rozhraní Text Analytics** a přetáhněte **Hashování** do experimentu. Připojit **výběr sloupců v datové sadě** k **Hashování**.

![Připojte se sloupce projektu](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** pro **Hashování bitsize**. Tím se vytvoří 8 (23) sloupce.

![hashování bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

V tomto okamžiku můžete kliknout na **spustit** k otestování experimentu.

![Spuštění](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Vytvoření webové služby
Teď vytvořte webovou službu. Rozbalte **webová služba** a přetáhněte **vstup** do experimentu. Připojit **vstup** k **Hashování**. Také přetáhnout **výstup** do experimentu. Připojit **výstup** k **Hashování**.

![výstup na--hashování](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klikněte na tlačítko **publikovat webovou službu**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klikněte na tlačítko **Ano** publikovat experimentu.

![Ano k publikování](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Test webové služby
Webové služby Azure ml se skládá z kanálu RSS (požadavku nebo odpovědi služby) a BES (služba batch execution) koncových bodů. RSS je pro synchronní zpracování. BES je pro provádění asynchronní úlohy. K otestování webové služby s vzorový zdroj Python níže, budete muset stáhnout a nainstalovat sadu Azure SDK pro Python (viz: [instalace Pythonu](../../python-how-to-install.md)).

Budete také potřebovat **pracovní prostor**, **služby**, a **klíč rozhraní API** experimentu pro následující vzorový zdroj. Pracovní prostor a služby zjistíte kliknutím na možnost **žádostí a odpovědí** nebo **Batch Execution** pro experiment v řídicím panelu webové služby.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Můžete najít **klíč rozhraní API** kliknutím na experiment v řídicím panelu webové služby.

![klíč rozhraní api pro hledání](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Koncový bod RRS testu
##### <a name="test-button"></a>Tlačítko Test
Snadný způsob, jak otestovat RRS koncový bod je na **testování** na řídicím panelu webové služby.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **to je dobrý den** pro **col2**. Kliknutím na značku zaškrtnutí.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zobrazí se něco jako

![Ukázkový výstup](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Vzorový kód
Dalším způsobem, jak testovat vaše RRS je z klientského kódu. Vyberete-li **žádostí a odpovědí** na řídicí panel a přejděte do dolní části uvidíte ukázkový kód pro C#, Python a R. Syntaxe požadavku RRS, včetně identifikátoru URI žádosti, zobrazí se vám také záhlaví a text.

Tento průvodce ukazuje funkční příklad v Pythonu. Budete muset změnit **pracovní prostor**, **služby**, a **klíč rozhraní API** experimentu.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
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
Klikněte na tlačítko **spuštění dávky** na řídicí panel a přejděte do dolní části. Zobrazí se ukázkový kód pro C#, Python a R. Zobrazí se také syntaxi BES požadavků na odeslání úlohy, spustit úlohu, získat stav nebo výsledků úlohy a úlohu můžete odstranit.

Tento průvodce ukazuje funkční příklad v Pythonu. Budete muset změnit **pracovní prostor**, **služby**, a **klíč rozhraní API** experimentu. Kromě toho budete muset upravit **název účtu úložiště**, **klíč účtu úložiště**, a **název kontejneru úložiště**. A konečně, budete muset změnit umístění **vstupní soubor** a umístění **výstupní soubor**.

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
    with open(output_file, "w+") as f:
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
