---
title: Kopírování dat do a z Azure Data Lake Store | Microsoft Docs
description: Zjistěte, jak zkopírovat data do a z Data Lake Store pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d83e71efa89746d7744893281ffe0ac1169fa08b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621468"
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Kopírování dat do a z Data Lake Store pomocí objektu pro vytváření dat
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-azure-datalake-connector.md)
> * [Verze 2 – Preview](../connector-azure-data-lake-store.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor Azure Data Lake Store v V2](../connector-azure-data-lake-store.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat do a z Azure Data Lake Store. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článku přehled o přesun dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z Azure Data Lake Store** ukládá do následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **do Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Před vytvořením kanálu s aktivitou kopírování, vytvoření účtu Data Lake Store. Další informace najdete v tématu [Začínáme s Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typy podporované ověřování
Konektor služby Data Lake Store podporuje tyto typy ověřování:
* Ověřování instančních objektů
* Ověření přihlašovacích údajů (OAuth) uživatele 

Doporučujeme použít objekt zabezpečení ověřování služby, zejména pro naplánované dat kopie. Vypršení platnosti tokenu chování mohou nastat u ověření přihlašovacích údajů uživatele. Podrobnosti konfigurace najdete v tématu [propojené vlastnosti služby](#linked-service-properties) části.

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure Data Lake Store pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál ke zkopírování dat je použití **Průvodce kopírováním**. Kurz týkající se vytváření kanálu pomocí Průvodce kopírováním, najdete v části [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z Azure blob storage do Azure Data Lake Store, například vytvoříte dvě propojené služby k propojení pro vytváření dat svůj účet úložiště Azure a Azure Data Lake store. Vlastnosti propojené služby, které jsou specifické pro Azure Data Lake Store, naleznete v části [propojené vlastnosti služby](#linked-service-properties) části. 
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a zadat kontejner objektů blob a složky, která obsahuje vstupní data. A vytvořte jinou datovou sadu, která určete složku a cesta k souboru v úložišti Data Lake, který obsahuje data zkopírovat z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, naleznete v části [vlastnosti datové sady](#dataset-properties) části.
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete BlobSource jako zdroj a AzureDataLakeStoreSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure Data Lake Store do úložiště objektů Blob Azure, můžete použít AzureDataLakeStoreSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure Data Lake Store, naleznete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store.  

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo ze Azure Data Lake Store naleznete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-data-lake-store) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k definování entit služby Data Factory, které jsou specifické pro Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba odkazuje na objekt pro vytváření dat úložiště dat. Vytvoření propojené služby typu **AzureDataLakeStore** propojení Data Lake Store dat do data factory. Následující tabulka popisuje elementy JSON, které jsou specifické pro Data Lake Store propojené služby. Můžete zvolit instanční objekt a ověření přihlašovacích údajů uživatele.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **type** | Vlastnost typu musí být nastavená na **AzureDataLakeStore**. | Ano |
| **dataLakeStoreUri** | Informace o účtu Azure Data Lake Store. Tyto informace má jednu z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| **ID předplatného** | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro sink |
| **resourceGroupName** | Název skupiny prostředků Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro sink |

### <a name="service-principal-authentication-recommended"></a>Objekt zabezpečení ověřování služby (doporučeno)
Pokud chcete použít ověřování hlavní služby, zaregistrujte entitu aplikace v Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Store. Podrobné pokyny najdete v tématu [Service-to-service ověřování](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

> [!IMPORTANT]
> Ujistěte se, že udělíte hlavní správné oprávnění služby v Azure Data Lake Store:
>- **Jako zdroj použít Data Lake Store**, udělit alespoň **čtení + Execute** oprávnění k seznamu a zkopírujte obsah složky, přístup k datům nebo **čtení** oprávnění zkopírovat jeden soubor. Žádný požadavek na řízení úrovně přístupu účtu.
>- **Použití Data Lake Store jako jímku**, udělte alespoň **zápisu + provést** oprávnění k vytváření podřízených položek ve složce přístup k datům. A pokud používáte Azure Reakcí na základě kterých kopírování (zdroj a jímka mají v cloudu), aby mohli zjistit Data Lake Store oblasti služby Data Factory, udělte alespoň **čtečky** role v účtu řízení přístupu (IAM). Pokud chcete, aby se zabránilo této role IAM [zadejte executionLocation](data-factory-data-movement-activities.md#global) s umístěním Data Lake Store v aktivitě kopírování.
>- Pokud jste **vytvářet kanály pomocí Průvodce kopírováním**, udělte alespoň **čtečky** role v účtu řízení přístupu (IAM). Navíc udělit alespoň **čtení + Execute** oprávnění kořenového adresáře Data Lake Store ("/") a její podřízené položky. V opačném případě může zobrazí se zpráva "zadané přihlašovací údaje jsou neplatné."

Použijte objekt zabezpečení ověřování služby tak, že zadáte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID aplikace klienta. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **Klienta** | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano |

**Příkladu: Ověření objektu službu**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Ověření pověření uživatele
Alternativně můžete ověření přihlašovacích údajů uživatele ke kopírování z nebo do Data Lake Store tak, že zadáte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klikněte **Autorizovat** tlačítko v editoru služby Data Factory a zadejte svoje přihlašovací údaje, který přiřazuje URL pro autorizaci automaticky generovaný této vlastnosti. | Ano |
| **ID relace** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a může být použit pouze jednou. Toto nastavení se automaticky generuje při pomocí editoru služby Data Factory. | Ano |

> [!IMPORTANT]
> Ujistěte se, že udělíte správné oprávnění uživatele v Azure Data Lake Store:
>- **Jako zdroj použít Data Lake Store**, udělit alespoň **čtení + Execute** oprávnění k seznamu a zkopírujte obsah složky, přístup k datům nebo **čtení** oprávnění zkopírovat jeden soubor. Žádný požadavek na řízení úrovně přístupu účtu.
>- **Použití Data Lake Store jako jímku**, udělte alespoň **zápisu + provést** oprávnění k vytváření podřízených položek ve složce přístup k datům. A pokud používáte Azure Reakcí na základě kterých kopírování (zdroj a jímka mají v cloudu), aby mohli zjistit Data Lake Store oblasti služby Data Factory, udělte alespoň **čtečky** role v účtu řízení přístupu (IAM). Pokud chcete, aby se zabránilo této role IAM [zadejte executionLocation](data-factory-data-movement-activities.md#global) s umístěním Data Lake Store v aktivitě kopírování.
>- Pokud jste **vytvářet kanály pomocí Průvodce kopírováním**, udělte alespoň **čtečky** role v účtu řízení přístupu (IAM). Navíc udělit alespoň **čtení + Execute** oprávnění kořenového adresáře Data Lake Store ("/") a její podřízené položky. V opačném případě může zobrazí se zpráva "zadané přihlašovací údaje jsou neplatné."

**Příklad: Ověření pověření uživatele**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Vypršení platnosti tokenu
Autorizační kód, který můžete vygenerovat pomocí **Autorizovat** tlačítko vyprší po určité době. Tato zpráva znamená, že vypršela platnost tokenu ověřování:

Přihlašovací údaje chyby operace: invalid_grant - AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: Předloženému udělení přístupu je prošlý nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: časové razítko fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21-09-31Z.

V následující tabulce jsou uvedeny doby vypršení platnosti, různé typy uživatelských účtů:

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty *není* spravované službou Azure Active Directory (například @hotmail.com nebo @live.com) |12 hodin |
| Účty uživatelů spravované službou Azure Active Directory |14 dnů po poslední řez spustit <br/><br/>90 dnů, pokud řezu založeného na základě OAuth propojené služby používá alespoň jednou za 14 dní |

Pokud změníte heslo před časem vypršení platnosti tokenu, platnost tokenu vyprší okamžitě. Zobrazí se zpráva již bylo zmíněno dříve v této části.

Můžete opětovné pověření k účtu pomocí **Autorizovat** tlačítko když vyprší platnost tokenu pro propojenou službu znovu nasaďte. Můžete také vygenerovat hodnoty **sessionId** a **autorizace** vlastnosti programově pomocí následujícího kódu:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Podrobnosti o třídách objekt pro vytváření dat používá v kódu najdete v tématu [azuredatalakestorelinkedservice třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), a [AuthorizationSessionGetResponse třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témata. Přidat odkaz na verzi `2.9.10826.1824` z `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` pro `WindowsFormsWebAuthenticationDialog` třída používaná v kódu.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznak:** při kopírování dat **do** Azure Data Lake Store, pokud vaše aktivity kopírování došlo k následující chybě:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Hlavní příčina:** 2 příčin:

1. `resourceGroupName` Nebo `subscriptionId` zadaný v Azure Data Lake Store propojené služby není správné.
2. Uživatel nebo objekt služby nemá potřebná oprávnění.

**Řešení:**

1. Zajistěte, aby `subscriptionId` a `resourceGroupName` zadáte v propojené službě `typeProperties` skutečně jsou ty, které váš účet data lake patří do.

2. Zajistěte, aby aspoň udělíte "**čtečky**" role pro uživatele nebo instančního objektu v účtu data lake. Chcete-li nastavit jej:

    1. Přejděte na portálu Azure -> váš účet Data Lake Store
    2. V okně Data Lake Store klikněte na tlačítko "řízení přístupu (IAM)"
    3. Klikněte na tlačítko "Přidat" v okně z "přístup k řízení (IAM)"
    4. Nastavte "Role" jako "Čtečky" a vyberte uživatele nebo instanční objekt, který použijete pro kopírování k udělení přístupu

3. Pokud nechcete, aby udělit "Čtečky" role pro uživatele nebo instančního objektu, alernative je [explicitně zadat umístění provádění](data-factory-data-movement-activities.md#global) v kopie activitywith umístění Data Lake Store. Příklad:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Pokud chcete zadat datové sady představují vstupní data v Data Lake Store, nastavíte **typ** vlastnosti datové sady, která **AzureDataLakeStore**. Nastavte **linkedServiceName** vlastnosti datové sady, která název Data Lake Store propojené služby. Úplný seznam části JSON a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Části datové sady ve formátu JSON, jako například **struktura**, **dostupnosti**, a **zásad**, jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, databáze a tabulky Azure, např.). **Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace, jako je například umístění a formát dat v úložišti dat. 

**Rámci typeProperties** části datové sady typu **AzureDataLakeStore** obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **folderPath** |Cesta ke kontejneru a složce v Data Lake Store. |Ano |
| **fileName** |Název souboru v Azure Data Lake Store. **FileName** vlastnost je volitelná a velká a malá písmena. <br/><br/>Pokud zadáte **fileName**, aktivitu (včetně kopie) funguje na konkrétní soubor.<br/><br/>Když **fileName** není zadán, zahrnuje kopírování všech souborů v **folderPath** ve vstupní datové sady.<br/><br/>Když **fileName** pro datovou sadu výstupů není zadána a **preserveHierarchy** není zadané v podřízený aktivity, je název vygenerovaný soubor ve formátu Data. _Identifikátor GUID_.txt'. Například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Ne |
| **partitionedBy** |**PartitionedBy** vlastnost je nepovinná. Můžete ji zadat dynamické cestu a název souboru pro data časové řady. Například **folderPath** lze nastavit parametry pro každou hodinu data. Podrobnosti a příklady naleznete v tématu [vlastnost partitionedBy](#using-partitionedby-property). |Ne |
| **Formát** | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, a **ParquetFormat**. Nastavte **typ** vlastnost pod **formát** na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formátu](data-factory-supported-file-and-compression-formats.md#parquet-format) v částech [formáty souborů a komprese podporovaných službou Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článku. <br><br> Pokud chcete zkopírovat soubory "jako-je" mezi souborové úložiště (binární kopie), přejděte `format` část v obou definice vstupní a výstupní datové sady. |Ne |
| **Komprese** | Zadejte typ a úroveň komprese pro data. Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese podporovaných službou Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="the-partitionedby-property"></a>Vlastnost partitionedBy
Můžete zadat dynamické **folderPath** a **fileName** vlastnosti pro data časové řady s **partitionedBy** vlastnost, funkce pro vytváření dat a systémové proměnné. Podrobnosti najdete v tématu [Azure Data Factory – funkce a systémové proměnné](data-factory-functions-variables.md) článku.


V následujícím příkladu `{Slice}` se nahradí hodnota proměnné objektu pro vytváření dat systému `SliceStart` ve formátu určeném (`yyyyMMddHH`). Název `SliceStart` odkazuje na čas spuštění řezu. `folderPath` Vlastnost je jiný pro každý řez, jako v `wikidatagateway/wikisampledataout/2014100103` nebo `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V následujícím příkladu, rok, měsíc, den a čas `SliceStart` extrahují do samostatné proměnné, které jsou používány `folderPath` a `fileName` vlastnosti:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Další informace o datových sad časové řady, plánování a řezů, najdete v článku [datové sady v Azure Data Factory](data-factory-create-datasets.md) a [Data Factory plánování a provádění](data-factory-scheduling-and-execution.md) články. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

**AzureDataLakeStoreSource** podporuje následující vlastnost v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **Rekurzivní** |Označuje, zda je data načíst rekurzivně z podsložky nebo pouze do zadané složky. |True (výchozí hodnota), False. |Ne |


**AzureDataLakeStoreSink** podporuje následující vlastnosti v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **copyBehavior** |Určuje chování kopírování. |<b>PreserveHierarchy</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou vytvořené v první úroveň cílové složky. Cílové soubory se vytvoří s automaticky vygenerovanou názvy.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadaný název souboru nebo objekt blob, název souboru sloučené je zadaný název. Název souboru, jinak je generován automaticky. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivní a copyBehavior
Tato část popisuje jejich výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| Rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 je vytvořena s stejná struktura jako zdroj<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + soubor3 + File4 + soubor 5 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článku.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Příklady JSON pro kopírování dat do a z Data Lake Store
Následující příklady poskytují definice JSON ukázka. Tyto ukázkové definice můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak ke zkopírování dat z Data Lake Store a Azure Blob storage a do. Nicméně je možné zkopírovat data _přímo_ ze všech zdrojů do jakéhokoli z podporovaném jímky. Další informace najdete v části "podporované úložiště dat a formáty" v [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) článku.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Příklad: Kopírování dat z Azure Blob Storage do Azure Data Lake Store
Ukázkový kód v této části uvádí:

* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Propojené služby typu [AzureDataLakeStore](#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureDataLakeStoreSink](#copy-activity-properties).

Příklady ukazují, jak časové řady dat z Azure Blob Storage je zkopírován do Data Lake Store každou hodinu. 

**Propojená služba Azure Storage**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Propojená služba Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Podrobnosti konfigurace najdete v tématu [propojené vlastnosti služby](#linked-service-properties) části.
>

**Vstupní datová sada Azure Blob**

V následujícím příkladu dat je převzata z nového objektu blob každou hodinu (`"frequency": "Hour", "interval": 1`). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den část čas spuštění. Název souboru používá hodinu část čas spuštění. `"external": true` Nastavení služba Data Factory informuje, že v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store výstupní datovou sadu**

Následující příklad zkopíruje data do Data Lake Store. Nová data se zkopírují do Data Lake Store každou hodinu.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Aktivita kopírování v kanálu s blob zdroj a jímka Data Lake Store**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurována pro používání vstupní a výstupní datové sady. Aktivita kopírování je naplánována na každou hodinu. V definici JSON kanálu `source` je typ nastaven na `BlobSource`a `sink` je typ nastaven na `AzureDataLakeStoreSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Příklad: Kopírování dat z Azure Data Lake Store do objektu blob Azure
Ukázkový kód v této části uvádí:

* Propojené služby typu [AzureDataLakeStore](#linked-service-properties).
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureDataLakeStoreSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kód kopíruje data časové řady z Data Lake Store do objektu blob Azure každou hodinu. 

**Propojená služba Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Podrobnosti konfigurace najdete v tématu [propojené vlastnosti služby](#linked-service-properties) části.
>

**Propojená služba Azure Storage**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datové sady Azure Data Lake**

V tomto příkladu nastavení `"external"` k `true` služba Data Factory informuje, že v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Výstupní datová sada Azure Blob**

V následujícím příkladu, data se zapisují do nového objektu blob každou hodinu (`"frequency": "Hour", "interval": 1`). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas část čas spuštění.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Aktivita kopírování v kanálu s na Azure Data Lake Store zdroj a jímka objektů blob**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurována pro používání vstupní a výstupní datové sady. Aktivita kopírování je naplánována na každou hodinu. V definici JSON kanálu `source` je typ nastaven na `AzureDataLakeStoreSource`a `sink` je typ nastaven na `BlobSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

V definici aktivity kopírování můžete namapovat sloupců z datové sady zdroje sloupců v datové sadě jímky. Podrobnosti najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o faktory, které ovlivňují výkon aktivity kopírování a jak ji optimalizovat najdete v tématu [výkonu kopie aktivity a vyladění průvodce](data-factory-copy-activity-performance.md) článku.
