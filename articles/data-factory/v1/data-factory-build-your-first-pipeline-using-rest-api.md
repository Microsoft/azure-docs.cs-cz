---
title: Sestavení prvního objektu pro vytváření dat (REST) | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte pomocí rozhraní REST API služby Data Factory ukázkový kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7e0a2465-2d85-4143-a4bb-42e03c273097
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2465dd6c22567a3d8b50a7cfad4e26491bbe773e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885196"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Kurz: Sestavení prvního objektu pro vytváření dat Azure pomocí rozhraní REST API služby Data Factory
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [rychlý start: Vytvoření datové továrny pomocí Azure Data Factory](../quickstart-create-data-factory-rest-api.md).

V tomto článku vytvoříte první objekt pro vytváření dat Azure pomocí rozhraní REST API služby Data Factory. Pokud chcete udělat kurz pomocí jiných nástrojů nebo sad SDK, vyberte jednu z možností z rozevíracího seznamu.

Kanál v tomto kurzu má jednu aktivitu: **Aktivita HDInsight Hive**. Tato aktivita spouští skript Hive v clusteru Azure HDInsight, který transformuje vstupní data pro vytvoření výstupních dat. Spuštění kanálu je naplánované jednou za měsíc mezi zadaným počátečním a koncovým časem.

> [!NOTE]
> Tento článek nepopisuje všechny možnosti rozhraní REST API. Úplnou dokumentaci o rozhraní REST API najdete v článku [Rozhraní REST API služby Data Factory – referenční informace](/rest/api/datafactory/).
> 
> Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace najdete v tématu [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="prerequisites"></a>Požadavky
* Přečtěte si článek [Přehled kurzu](data-factory-build-your-first-pipeline.md) a proveďte **nutné** kroky.
* Nainstalujte na svůj počítač nástroj [Curl](https://curl.haxx.se/dlwiz/). Pomocí nástroje CURL a příkazů REST vytvoříte objekt pro vytváření dat.
* Postupujte podle pokynů v [tomto článku](../../active-directory/develop/howto-create-service-principal-portal.md) a proveďte následující:
  1. V Azure Active Directory vytvořte webovou aplikaci s názvem **ADFGetStartedApp**.
  2. Získejte **ID klienta** a **tajný klíč**.
  3. Získejte **ID tenanta**.
  4. Přiřaďte aplikaci **ADFGetStartedApp** k roli **Přispěvatel Data Factory**.
* Nainstalujte [Azure PowerShell](/powershell/azure/overview).
* Spusťte **PowerShell** a potom spusťte následující příkaz. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu.
  1. Spusťte příkaz **Connect-AzureRmAccount** a zadejte uživatelské jméno a heslo, které používáte k přihlášení na webu Azure Portal.
  2. Spuštěním příkazu **Get-AzureRmSubscription** zobrazte všechna předplatná pro tento účet.
  3. Spuštěním rutiny **Get-AzureRmSubscription -SubscriptionName NazevPredplatnehoAzure | Set-AzureRmContext** vyberte předplatné, se kterým chcete pracovat. Místo **NazevPredplatnehoAzure** uveďte název svého předplatného Azure.
* Spuštěním následujícího příkazu v prostředí PowerShell vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup**:

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Některé kroky v tomto kurzu vychází z předpokladu, že používáte skupinu prostředků s názvem ADFTutorialResourceGroup. Pokud používáte jinou skupinu prostředků, použijte v postupech v tomto kurzu místo skupiny ADFTutorialResourceGroup název vaší skupiny prostředků.

## <a name="create-json-definitions"></a>Vytvoření definic JSON
Vytvořte následující soubory JSON ve složce, ve které je umístěn soubor curl.exe.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Název musí být globálně jedinečný, takže můžete přidat předponu nebo příponu k názvu ADFCopyTutorialDF tak, aby byl jedinečný.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Položky **accountname** a **accountkey** nahraďte názvem svého účtu Azure Storage a jeho klíčem. Chcete-li zjistit, jak získat přístupový klíč k úložišti, přečtěte si informace o zobrazení, kopírování a opětovném vygenerování přístupových klíčů k úložišti v tématu [Správa účtu úložiště](../../storage/common/storage-account-manage.md#access-keys).
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

```JSON
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

| Vlastnost | Popis |
|:--- |:--- |
| ClusterSize |Velikost clusteru HDInsight. |
| TimeToLive |Určuje dobu nečinnosti před odstraněním clusteru HDInsight. |
| linkedServiceName |Určuje účet úložiště, který se používá k ukládání protokolů generovaných clusterem HDInsight |

Je třeba počítat s následujícím:

* Pomocí výše uvedeného kódu JSON služba Data Factory vytvoří cluster HDInsight **se systémem Linux** za vás. Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
* Místo clusteru HDInsight na vyžádání můžete použít také **vlastní cluster HDInsight**. Podrobnosti najdete v tématu [Propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
* Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je zpracován určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní.

    Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů používají následující formát: „adf**název_vašeho_objektu_pro_vytváření_dat**-**název_propojené_služby**-razítko_data_a_času“. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).

Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

Kód JSON určuje datovou sadu s názvem **AzureBlobInput**, která představuje vstupní data pro aktivitu v kanálu. Kromě toho určuje, že se vstupní data nachází v kontejneru objektů blob s názvem **adfgetstarted** ve složce s názvem **inputdata**.

Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

| Vlastnost | Popis |
|:--- |:--- |
| type |Vlastnost type je nastavená na hodnotu AzureBlob, protože se data nachází ve službě Azure Blob Storage. |
| linkedServiceName |Odkazuje na službu StorageLinkedService, kterou jste vytvořili předtím. |
| fileName |Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto případě se zpracovává jenom soubor input.log. |
| type |Soubory protokolů jsou v textovém formátu, takže použijeme hodnotu TextFormat. |
| columnDelimiter |Sloupce v souborech protokolu jsou oddělené znakem čárky (,) |
| frequency/interval |Frekvence je nastavená na hodnotu Month (Měsíc) a interval je 1, takže vstupní řezy jsou dostupné jednou za měsíc. |
| external |Pokud vstupní data nevygenerovala služba Data Factory, je tato vlastnost nastavená na hodnotu true. |

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Kód JSON definuje datovou sadu s názvem **AzureBlobOutput**, která představuje výstupní data pro aktivitu v kanálu. Kromě toho určuje, že se mají výsledky ukládat do kontejneru objektů blob s názvem **adfgetstarted** do složky s názvem **partitioneddata**. Oddíl **availability** určuje, že se výstupní sada vytváří jednou měsíčně.

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Nahraďte **storageaccountname** názvem vašeho účtu služby Azure Storage.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "HDInsightOnDemandLinkedService"
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

V tomto fragmentu kódu JSON vytváříte kanál sestávající z jediné aktivity, která zpracovává data v clusteru HDInsight pomocí skriptu Hive.

Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService s názvem **StorageLinkedService**) a ve složce **script** v kontejneru **adfgetstarted**.

Oddíl **defines** určuje nastavení běhového prostředí, které se předá skriptu Hive jako konfigurační hodnoty Hive (např. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Vlastnosti kanálu **start** a **end** určují období aktivity kanálu.

V kódu JSON aktivity určujete, že má skript Hive běžet ve výpočetní službě určené vlastností **linkedServiceName**, tedy **HDInsightOnDemandLinkedService**.

> [!NOTE]
> V části kódu JSON kanálu v tématu [Kanály a aktivity ve službě Azure Data Factory](data-factory-create-pipelines.md) najdete podrobnosti o vlastnostech JSON použitých v předchozím příkladu.
>
>

## <a name="set-global-variables"></a>Nastavení globálních proměnných
V prostředí Azure PowerShell spusťte následující příkazy (po nahrazení ukázkových hodnot vašimi vlastními):

> [!IMPORTANT]
> Postup získání ID klienta, tajného klíče klienta, ID tenanta a ID předplatného naleznete v sekci [Požadavky](#prerequisites).
>
>

```PowerShell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Ověření pomocí ADD

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku vytvoříte službu Azure Data Factory s názvem **FirstDataFactoryREST**. Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu HDInsight Hive pro spuštění skriptu Hive, který umožňuje transformovat data. Spuštěním následujícího příkazu vytvořte objekt pro vytváření dat:

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    Zkontrolujte, že název objektu pro vytváření dat zadaný zde (ADFCopyTutorialDF) odpovídá názvu zadanému v souboru **datafactory.json**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byl objekt pro vytváření dat vytvořen úspěšně, v části **výsledky** se zobrazí JSON pro příslušný objekt pro vytváření dat. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí ve výsledcích zobrazí chyba: **Název objektu pro vytváření dat "FirstDataFactoryREST" není k dispozici**, proveďte následující kroky:
  1. Změňte název (například vaše_jménoFirstDataFactoryREST) v souboru **datafactory.json**. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
  2. V prvním příkazu, kde je proměnné **$cmd** přiřazena hodnota, nahraďte FirstDataFactoryREST novým názvem a spusťte příkaz.
  3. Spuštěním následujících dvou příkazů vyvoláte rozhraní REST API za účelem vytvoření objektu pro vytváření dat a tisku výsledků operace.
* Instance služby Data Factory můžete vytvářet jenom tehdy, když jste přispěvatelem/správcem předplatného Azure.
* Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.
* Pokud se zobrazí chybová zpráva: "**Toto předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory**", proveďte jednu z následujících akcí a znovu zkuste název publikovat:

  * Spuštěním následujícího příkazu v prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory:

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Spuštěním následujícího příkazu si můžete ověřit, zda je zprostředkovatel služby Data Factory zaregistrovaný:
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Přihlaste se na web [Azure Portal ](https://portal.azure.com) pomocí předplatného Azure a přejděte do okna Objekt pro vytváření dat nebo na webu Azure Portal vytvořte objekt pro vytváření dat. Zprostředkovatel se při takovém postupu zaregistruje automaticky.

Před vytvořením kanálu je nejdřív potřeba vytvořit několik entit služby Data Factory. Nejdřív vytvoříte propojené služby, které spojí úložiště dat / výpočetní služby s vaším úložištěm dat, definujete vstupní a výstupní datové sady reprezentující data v propojených úložištích dat.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku propojíte svůj účet služby Azure Storage a cluster Azure HDInsight na vyžádání s objektem pro vytváření dat. Účet služby Azure Storage v této ukázce obsahuje vstupní a výstupní data pro kanál. Propojená služba HDInsight slouží v této ukázce ke spuštění skriptu Hive určeného v aktivitě kanálu.

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku propojíte se svým objektem pro vytváření dat svůj účet služby Azure Storage. V tomto kurzu použijete tento účet služby Azure Storage taky k uložení vstupních/výstupních dat a souboru skriptu HQL.

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla propojená služba vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto propojenou službu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Vytvoření propojené služby Azure HDInsight
V tomto kroku propojíte se svým objektem pro vytváření dat cluster HDInsight na vyžádání. Cluster HDInsight se automaticky vytvoří za běhu, a až dokončí zpracování, po určité zadané době nečinnosti se odstraní. Místo clusteru HDInsight na vyžádání můžete použít také vlastní cluster HDInsight. Podrobnosti najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md).

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla propojená služba vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto propojenou službu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data pro zpracování Hive. Tyto datové sady odkazují na službu **StorageLinkedService**, kterou už jste v tomto kurzu vytvořili. Propojená služba odkazuje na účet služby Azure Storage a datové sady určují kontejner, složku a název souboru v úložišti, který obsahuje vstupní a výstupní data.

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
V tomto kroku vytvoříte vstupní datovou sadu, která bude představovat vstupní data ve službě Azure Blob Storage.

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla datová sada vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto datovou sadu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady
V tomto kroku vytvoříte výstupní datovou sadu, která bude představovat výstupní data ve službě Azure Blob Storage.

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla datová sada vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto datovou sadu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte svůj první kanál s aktivitou **HDInsightHive**. Vstupní řez je dostupný jednou měsíčně (frekvence: Měsíc, interval: 1), výstupní řez se generuje jednou měsíčně a vlastnost scheduler pro aktivitu je také nastavena na jednou měsíčně. Nastavení výstupní datové sady a vlastnosti scheduler se musí shodovat. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte.

Ujistěte se, že se ve složce **adfgetstarted/inputdata** ve službě Azure Blob Storage nachází soubor **input.log**, a spuštěním následujícího příkazu kanál nasaďte. Časy **start** a **end** jsou nastavené na minulost a vlastnost **isPaused** má hodnotu false, takže se kanál (aktivita v kanálu) spustí hned po nasazení.

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla datová sada vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto datovou sadu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell
    Write-Host $results
    ```
4. Úspěšně jste vytvořili první kanál pomocí prostředí Azure PowerShell, blahopřejeme!

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku použijete rozhraní REST API služby Data Factory k monitorování řezů vytvářených kanálem.

```PowerShell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá (přibližně 20 minut). Proto počítejte s tím, že zpracování řezu kanálem bude trvat **přibližně 30 minut**.
>
>

Spouštějte Invoke-Command a další příkaz, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. Pokud je řez ve stavu Připraveno, zkontrolujte, jestli se ve složce **partitioneddata** v kontejneru **adfgetstarted** ve službě Blob Storage nachází výstupní data.  Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá.

![Výstupní data](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> Po úspěšném zpracování řezu se vstupní soubor odstraní. Pokud tedy chcete spustit řez znovu nebo si znovu projít tento kurz, načtěte vstupní soubor (input.log) do složky inputdata v kontejneru adfgetstarted.
>
>

Azure Portal můžete použít také k monitorování řezů a odstraňování jakýchkoli potíží. Přečtěte si podrobnosti o [monitorování kanálů pomocí webu Azure Portal](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline).

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro zpracování dat Azure, který zpracovává data pomocí skriptu Hive v clusteru HDInsight Hadoop. Pomocí editoru služby Data Factory na webu Azure Portal jste provedli tyto kroky:

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste dvě **propojené služby**:
   1. Propojená služba **Azure Storage** spojuje úložiště objektů blob Azure, které obsahuje vstupní/výstupní soubory, s objektem pro vytváření dat.
   2. Propojená služba na vyžádání **Azure HDInsight** spojuje cluster na vyžádání HDInsight Hadoop s objektem pro vytváření dat. Služba Azure Data Factory vytvoří cluster HDInsight Hadoop ve chvíli, kdy je potřeba zpracovat vstupní data a vygenerovat výstupní data.
3. Vytvořili jste dvě **datové sady**, které popisují vstupní a výstupní data aktivity HDInsight Hive v kanálu.
4. Vytvořili jste **kanál** s aktivitou **HDInsight Hive**.

## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili kanál s aktivitou transformace (aktivita HDInsight), která v clusteru Azure HDInsight na vyžádání spouští skript Hive. Jak používat aktivitu kopírování ke kopírování dat z objektu Blob Azure do Azure SQL najdete v tématu [kurzu: Kopírování dat z objektu Blob Azure do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Viz také
| Téma | Popis |
|:--- |:--- |
| [Rozhraní REST API služby Data Factory – referenční informace](/rest/api/datafactory/) |Tady najdete rozsáhlou dokumentaci o rutinách služby Data Factory. |
| [Kanály](data-factory-create-pipelines.md) |Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a porozumět tomu, jak se dají ve vaší situaci nebo firmě použít k sestavení kompletních pracovních postupů založených na datech. |
| [Datové sady](data-factory-create-datasets.md) |Tento článek vám pomůže pochopit datové sady ve službě Azure Data Factory. |
| [Plánování a provádění](data-factory-scheduling-and-execution.md) |Tento článek vysvětluje aspekty plánování a provádění aplikačního modelu služby Azure Data Factory. |
| [Monitorování a správa kanálů pomocí monitorovací aplikace](data-factory-monitor-manage-app.md) |Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí aplikace pro monitorování a správu. |
