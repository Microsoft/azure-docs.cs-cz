---
title: 'Kurz: Použití rozhraní REST API k vytvoření kanálu Azure Data Factory | Dokumentace Microsoftu'
description: V tomto kurzu pomocí rozhraní REST API vytvoříte kanál Azure Data Factory s aktivitou kopírování pro kopírování dat z úložiště objektů blob v Azure do databáze Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ce344292577dd286abcd7fbf9e067800da0e0b3
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958985"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Kurz: Použití rozhraní REST API k vytvoření kanálu Azure Data Factory pro kopírování dat 
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-rest-api.md). 

V tomto článku se naučíte, jak používat rozhraní REST API, abyste vytvořili datovou továrnu s kanálem, který kopíruje data z úložiště objektů blob v Azure do databáze SQL v Azure. Pokud s Azure Data Factory začínáte, přečtěte si článek [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto kurzem začnete.   

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Tento článek nepopisuje všechny možnosti rozhraní REST API služby Data Factory. Úplnou dokumentaci o rutinách služby Data Factory najdete v článku [Rozhraní REST API služby Data Factory - referenční informace](/rest/api/datafactory/).
>  
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky
* Projděte si [Přehled kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a proveďte **nutné** kroky.
* Nainstalujte na svůj počítač nástroj [Curl](https://curl.haxx.se/dlwiz/). Pomocí nástroje Curl a příkazů REST vytvoříte objekt pro vytváření dat. 
* Postupujte podle pokynů v [tomto článku](../../active-directory/develop/howto-create-service-principal-portal.md) a proveďte následující: 
  1. V Azure Active Directory vytvořte webovou aplikaci s názvem **ADFCopyTutorialApp**.
  2. Získejte **ID klienta** a **tajný klíč**. 
  3. Získejte **ID tenanta**. 
  4. Přiřaďte aplikaci **ADFCopyTutorialApp** k roli **Přispěvatel Data Factory**.  
* Nainstalujte [Azure PowerShell](/powershell/azure/overview).  
* Spusťte **PowerShell** a potom proveďte následující kroky. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu.
  
  1. Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal:
    
    ```PowerShell 
    Connect-AzureRmAccount
    ```   
  2. Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```PowerShell     
    Get-AzureRmSubscription
    ``` 
  3. Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **&lt;NameOfAzureSubscription**&gt; zadejte název svého předplatného Azure. 
     
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
  4. Spuštěním následujícího příkazu v prostředí PowerShell vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup**:  

    ```PowerShell     
      New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
     
      Pokud skupina prostředků už existuje, určete, jestli se má aktualizovat (Y), nebo ponechat tak, jak je (N). 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Položky **accountname** a **accountkey** nahraďte názvem svého účtu Azure Storage a jeho klíčem. Informace o tom, jak získat přístupový klíč k úložišti, najdete v článku o [zobrazení, kopírování a opětovném vytváření přístupových klíčů úložiště](../../storage/common/storage-account-manage.md#access-keys).

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

Podrobné informace o vlastnostech JSON najdete v tématu [Propojená služba Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuersqllinkedservicejson"></a>azuersqllinkedservice.json
> [!IMPORTANT]
> Položky **servername**, **databasename**, **username** a **password** nahraďte názvem serveru SQL Azure, názvem databáze SQL, uživatelským účtem a heslem pro daný účet.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Podrobné informace o vlastnostech JSON najdete v tématu [Propojená služba Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

| Vlastnost | Popis |
|:--- |:--- |
| type | Vlastnost type je nastavená na hodnotu **AzureBlob**, protože se data nachází ve službě Azure Blob Storage. |
| linkedServiceName | Odkazuje na službu **AzureStorageLinkedService**, kterou jste vytvořili předtím. |
| folderPath | Určuje **kontejner** objektů blob a **složku** obsahující vstupní objekty blob. V tomto kurzu je adftutorial kontejnerem objektů blob a složka je kořenová složka. | 
| fileName | Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto kurzu má fileName hodnotu **emp.txt**, takže se zpracuje pouze tento soubor. |
| format -> type |Vstupní soubor je v textovém formátu, takže použijeme **TextFormat**. |
| columnDelimiter | Sloupce ve vstupním souboru jsou oddělené **znakem čárky (`,`)**. |
| frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, takže vstupní řezy jsou dostupné **každou hodinu**. Jinými slovy služba Data Factory každou hodinu vyhledá vstupní data v kořenové složce kontejneru objektů blob (**adftutorial**), který jste zadali. Vyhledává data v rámci kanálu mezi časy spuštění a ukončení, ne před nebo po této době.  |
| external | Pokud data nevygeneroval tento kanál, je tato vlastnost nastavená na hodnotu **true**. Vstupní data v tomto kurzu jsou v souboru emp.txt, který není generován tímto kanálem, proto jsme tuto vlastnost nastavili na hodnotu true. |

Další informace o těchto vlastnostech JSON najdete v článku [Konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

| Vlastnost | Popis |
|:--- |:--- |
| type | Vlastnost type je nastavena na hodnotu **AzureSqlTable**, protože data se kopírují do tabulky v databázi Azure SQL. |
| linkedServiceName | Odkazuje na službu **AzureSqlLinkedService**, kterou jste vytvořili předtím. |
| tableName | Určuje **tabulku**, do které se kopírují data. | 
| frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, což znamená, že výstupní řezy se tvoří **každou hodinu** mezi časy spuštění a ukončení, ne před nebo po této době.  |

V tabulce emp v databázi jsou k dispozici tři sloupce – **ID**, **FirstName** a **LastName**. ID je sloupec identity, takže je zde třeba zadat pouze položky **FirstName** (Jméno) a **LastName** (Příjmení).

Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Je třeba počítat s následujícím:

- V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md). V řešeních služby Data Factory můžete také použít [aktivity transformace dat](data-factory-data-transformation-activities.md).
- Vstup aktivity je nastavený na **AzureBlobInput** a výstup aktivity je nastavený na **AzureSqlOutput**. 
- V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. Úplný seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky najdete v tématu [podporovaných úložištích dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kliknutím na odkaz v tabulce se dozvíte, jak použít konkrétní podporovaná úložiště dat jako zdroj/jímku.  
 
Nahraďte hodnotu vlastnosti **start** aktuálním dnem a **end** následujícím dnem. Můžete zadat jenom část data a přeskočit část času. Například „2017-02-03“ je ekvivalentní hodnotě „2017-02-03T00:00:00Z“.
 
Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2016-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme. 
 
Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**.
 
V předchozím příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.

Popisy vlastností JSON použitých v definici kanálu najdete v článku [Vytvoření kanálů](data-factory-create-pipelines.md). Popisy vlastností JSON použitých v definici aktivity kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md). Popisy vlastností JSON podporovaných zdrojem BlobSource najdete v článku [Konektor Azure Blob](data-factory-azure-blob-connector.md). Popisy vlastností JSON podporovaných jímkou SqlSink najdete v článku [Konektor Azure SQL Database](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Nastavení globálních proměnných
V prostředí Azure PowerShell spusťte následující příkazy (po nahrazení ukázkových hodnot vašimi vlastními):

> [!IMPORTANT]
> Postup získání ID klienta, tajného klíče klienta, ID tenanta a ID předplatného naleznete v sekci [Požadavky](#prerequisites).   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Po dokončení aktualizace názvu datové továrny, kterou používáte, spusťte následující příkaz: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Ověření pomocí ADD
Spuštěním následujícího příkazu proveďte ověření pomocí služby Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku vytvoříte objekt služby Azure Data Factory s názvem **ADFCopyTutorialDF**. Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Například aktivita kopírování kopíruje data ze zdroje do cílového úložiště dat. Aktivita HDInsight Hive spustí skript Hive k transformaci vstupních dat na výstupní data produktu. Spuštěním následujícího příkazu vytvořte objekt pro vytváření dat: 

1. Přiřaďte příkaz k proměnné s názvem **cmd**. 
   
    > [!IMPORTANT]
    > Zkontrolujte, že název objektu pro vytváření dat zadaný zde (ADFCopyTutorialDF) odpovídá názvu zadanému v souboru **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byl objekt pro vytváření dat vytvořen úspěšně, v části **výsledky** se zobrazí JSON pro příslušný objekt pro vytváření dat. V opačném případě se zobrazí chybová zpráva.  
   
    ```
    Write-Host $results
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se ve výsledcích zobrazí chyba **Název objektu pro vytváření dat „ADFCopyTutorialDF“ není k dispozici**, proveďte následující kroky:  
  
  1. Změňte název (například yournameADFCopyTutorialDF) v souboru **datafactory.json**.
  2. V prvním příkazu, kde je proměnné **$cmd** přiřazena hodnota, nahraďte ADFCopyTutorialDF novým názvem a spusťte příkaz. 
  3. Spuštěním následujících dvou příkazů vyvoláte rozhraní REST API za účelem vytvoření objektu pro vytváření dat a tisku výsledků operace. 
     
     V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
* Instance služby Data Factory můžete vytvářet jenom tehdy, když jste přispěvatelem/správcem předplatného Azure.
* Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.
* Pokud se zobrazí chyba „**Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory**“, proveďte některý z těchto kroků a znovu zkuste název publikovat: 
  
  * Spuštěním následujícího příkazu v prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory: 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Spuštěním následujícího příkazu si můžete ověřit, jestli je zprostředkovatel služby Data Factory zaregistrovaný. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Přihlaste se na web [Azure Portal ](https://portal.azure.com) pomocí předplatného Azure a přejděte do okna Objekt pro vytváření dat nebo na webu Azure Portal vytvořte objekt pro vytváření dat. Zprostředkovatel se při takovém postupu zaregistruje automaticky.

Před vytvořením kanálu je nejdřív potřeba vytvořit několik entit služby Data Factory. Nejprve vytvoříte propojené služby propojující zdrojové a cílové úložiště dat do vašeho úložiště dat. Poté definujte vstupní a výstupní datové sady pro reprezentaci dat v propojených úložištích dat. Nakonec vytvořte kanál s aktivitou, která používá tyto datové sady.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu nebudete používat žádnou výpočetní službu jako je Azure HDInsight nebo Azure Data Lake Analytics. Budete používat dvě úložiště dat typu Azure Storage (zdroj) a Azure SQL Database (cíl). Vytvoříte tedy dvě propojené služby s názvem AzureStorageLinkedService a AzureSqlLinkedService typu: AzureStorage a AzureSqlDatabase.  

Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. Tento účet úložiště je ten, ve kterém jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili kontejner a nahráli do něj data.   

Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp.  

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku propojíte se svou datovou továrnou účet úložiště Azure. V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service).  

1. Přiřaďte příkaz k proměnné s názvem **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Příkaz spusťte pomocí **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zkontrolujte výsledky. Pokud byla propojená služba vytvořena úspěšně, v části **výsledky** se zobrazí JSON pro tuto propojenou službu. V opačném případě se zobrazí chybová zpráva.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Vytvoření propojené služby Azure SQL
V tomto kroku se svým objektem pro vytváření dat propojíte svou databázi SQL Azure. V tomto oddílu zadáte název serveru Azure SQL, název databáze, uživatelské jméno a heslo. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure SQL najdete v oddílu [Propojená služba Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

1. Přiřaďte příkaz k proměnné s názvem **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
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
V předchozím kroku jste vytvořili propojené služby, abyste propojili účet úložiště Azure a Azure SQL Database s datovou továrnou. V tomto kroku nadefinujete dvě datové sady s názvem AzureBlobInput a AzureSqlOutput, které představují vstupní a výstupní data uložená v úložištích dat, na která odkazují služby AzureStorageLinkedService a AzureSqlLinkedService.

Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob (AzureBlobInput) určuje kontejner a složku obsahující vstupní data.  

Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL (OutputDataset) určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob. 

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
V tomto kroku vytvoříte datovou sadu s názvem AzureBlobInput, která odkazuje na soubor blob (emp.txt) v kořenové složce kontejneru objektů blob (adftutorial), který se nachází ve službě Azure Storage reprezentované propojenou službou AzureStorageLinkedService. Pokud neurčíte hodnotu fileName (nebo ji přeskočíte), data ze všech objektů blob ve vstupní složce se zkopírují do cíle. V tomto kurzu určíte hodnotu fileName. 

1. Přiřaďte příkaz k proměnné s názvem **cmd**. 

    ```PowerSHell   
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
Propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. Výstupní datová sada tabulky SQL (OutputDataset), kterou v tomto kroku vytvoříte, určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.

1. Přiřaďte příkaz k proměnné s názvem **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
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
V tomto kroku vytvoříte kanál s **aktivitou kopírování**, která používá **AzureBlobInput** jako vstup a **AzureSqlOutput** jako výstup.

Výstupní datové sady v současné době řídí plán. V tomto kurzu je výstupní datová sada nakonfigurovaná tak, aby vytvářela řez jednou za hodinu. Kanál má čas spuštění a čas ukončení nastavený jeden den od sebe, což je 24 hodin. Proto kanál vytvoří 24 řezů výstupní datové sady. 

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

**Blahopřejeme!** Úspěšně jste vytvořili objekt pro vytváření dat Azure s kanálem, který kopíruje data ze služby Azure Blob Storage do databáze Azure SQL.

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku použijete rozhraní REST API služby Data Factory k monitorování řezů vytvářených kanálem.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Ujistěte se, že počáteční i koncový čas zadaný v následujícím příkazu se shoduje s počátečním a koncovým časem kanálu. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Spouštějte Invoke-Command a další příkaz, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. Pokud je řez ve stavu Připraveno, zkontrolujte výstupní data v tabulce **emp** ve vaší službě Azure SQL Database. 

Pro každý řez se ze zdrojového souboru do tabulky emp ve službě Azure SQL Database zkopírují dva řádky. Proto po úspěšném zpracování všech řezů (stav Připraveno) uvidíte v tabulce emp 24 nových záznamů. 

## <a name="summary"></a>Souhrn
V tomto kurzu jste pomocí rozhraní REST API vytvořili objekt pro vytváření dat Azure pro kopírování dat z objektu blob Azure do skužby Azure SQL Database. Zde jsou základní kroky, které jste v tomto kurzu provedli:  

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste **propojené služby**:
   1. Propojená služba Azure Storage připojující účet úložiště Azure, který obsahuje vstupní data.     
   2. Propojená služba Azure SQL připojující službu Azure SQL Database, která obsahuje výstupní data. 
3. Vytvořili jste **datové sady**, které popisují vstupní data a výstupní data pro kanály.
4. Vytvořili jste **kanál** s aktivitou kopírování, která má jako zdroj BlobSource a jako jímku SqlSink. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat.
