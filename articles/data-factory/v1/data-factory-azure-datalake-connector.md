---
title: Kopírování dat do a z Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do a z Data Lake Store pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3fec0952f4b164327942d5dee108f89b17613042
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015535"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopírování dat do a z Data Lake Storage Gen1 pomocí služby Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-datalake-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Data Lake Storage Gen1 ve V2](../connector-azure-data-lake-store.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a z Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store). Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, přehled o přesun dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **z Azure Data Lake Store** ukládá následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **do Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Vytvoření účtu Data Lake Store před vytvořením kanálu s aktivitou kopírování. Další informace najdete v tématu [Začínáme s Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typy podporované metody ověřování
Konektor Data Lake Store podporuje tyto typy ověřování:
* Ověřování instančních objektů
* Ověření uživatele přihlašovací údaje (OAuth) 

Doporučujeme používat ověřování instančních objektů, zejména pro plánovanou kopírování. Vypršení platnosti tokenu chování může dojít, s ověřením pověření uživatele. Podrobnosti o konfiguraci, najdete v článku [vlastnostem propojených služeb](#linked-service-properties) oddílu.

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesunuje data ze Azure Data Lake Store s použitím různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál ke zkopírování dat je použít **Průvodce kopírováním**. Kurz týkající se vytvoření kanálu pomocí Průvodce kopírováním, najdete v tématu [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud se kopírování dat z Azure blob storage do Azure Data Lake Store, vytvoříte dvě propojené služby pro propojení účtu služby Azure storage a Azure Data Lake store se svou datovou továrnou. Vlastnosti propojené služby, které jsou specifické pro Azure Data Lake Store, naleznete v tématu [vlastnostem propojených služeb](#linked-service-properties) oddílu. 
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte kontejner objektů blob a složku obsahující vstupní data. A vytvořte jinou datovou sadu k určení složky a cesta k souboru ve službě Data Lake store, obsahující data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, naleznete v tématu [vlastnosti datové sady](#dataset-properties) oddílu.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí BlobSource jako zdroj a AzureDataLakeStoreSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure Data Lake Store do Azure Blob Storage, můžete použít AzureDataLakeStoreSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure Data Lake Store, naleznete v tématu [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště.  

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a ze Azure Data Lake Store najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-data-lake-store) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat do služby data factory. Vytvoření propojené služby typu **AzureDataLakeStore** propojení vašich dat v Data Lake Store se svou datovou továrnou. Následující tabulka popisuje elementy JSON, které jsou specifické pro Data Lake Store propojené služby. Můžete zvolit instančního objektu a ověření přihlašovacích údajů uživatele.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **type** | Vlastnost type musí být nastavená na **AzureDataLakeStore**. | Ano |
| **dataLakeStoreUri** | Informace o účtu Azure Data Lake Store. Tyto informace má jednu z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| **ID předplatného** | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro jímku |
| **resourceGroupName** | Název skupiny prostředků Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro jímku |

### <a name="service-principal-authentication-recommended"></a>Ověřování instančních objektů (doporučeno)
Pokud chcete používat ověřování instančních objektů, entity aplikaci zaregistrovat ve službě Azure Active Directory (Azure AD) a jí udělit přístup k Data Lake Store. Podrobné pokyny najdete v článku [ověřování služba služba](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

> [!IMPORTANT]
> Ujistěte se, že udělíte hlavní správné oprávnění služby v Azure Data Lake Store:
>- **Použití Data Lake Store jako zdroj**, přidělit nejméně **číst + provést** oprávnění k seznamu a zkopírujte obsah složky, přístup k datům nebo **čtení** oprávnění zkopírovat jeden soubor. Žádné požadavky na řízení přístupu na úrovni účtu.
>- **Použití Data Lake Store jako jímku**, přidělit nejméně **zapisovat + provést** oprávnění pro vytváření podřízených položek ve složce pro přístup k datům. A pokud používáte prostředí Azure IR pro kopírování (zdroj a jímka mají v cloudu), aby mohl zjišťovat Data Lake Store oblasti služby Data Factory, přidělit nejméně **čtečky** role v účtu řízení přístupu (IAM). Pokud chcete se vyhnout této role IAM [zadat executionlocation, abyste](data-factory-data-movement-activities.md#global) s umístěním vaše Data Lake Store v aktivitě kopírování.
>- Pokud jste **k vytváření kanálů pomocí Průvodce kopírováním**, přidělit nejméně **čtečky** role v účtu řízení přístupu (IAM). Také poskytnout alespoň **číst + provést** oprávnění na kořenovém adresáři Data Lake Store ("/") a jeho podřízené položky. Jinak se může zobrazit zpráva "poskytnuté přihlašovací údaje jsou neplatné."

Použijte ověřování instančních objektů zadáním následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta vaší aplikace. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **tenanta** | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano |

**Příklad: Ověřování instančních objektů**
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

### <a name="user-credential-authentication"></a>Ověření přihlašovacích údajů uživatele
Alternativně můžete ověření přihlašovacích údajů uživatele ke kopírování z nebo do Data Lake Store tak, že zadáte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klikněte na tlačítko **Authorize** tlačítko v editoru služby Data Factory a zadejte svoje přihlašovací údaje, které přiřadí adresu URL pro autorizaci automaticky generované této vlastnosti. | Ano |
| **ID relace** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečný a může být použit pouze jednou. Toto nastavení není automaticky vygenerován při použití editoru služby Data Factory. | Ano |

> [!IMPORTANT]
> Ujistěte se, že udělíte správné oprávnění uživatele v Azure Data Lake Store:
>- **Použití Data Lake Store jako zdroj**, přidělit nejméně **číst + provést** oprávnění k seznamu a zkopírujte obsah složky, přístup k datům nebo **čtení** oprávnění zkopírovat jeden soubor. Žádné požadavky na řízení přístupu na úrovni účtu.
>- **Použití Data Lake Store jako jímku**, přidělit nejméně **zapisovat + provést** oprávnění pro vytváření podřízených položek ve složce pro přístup k datům. A pokud používáte prostředí Azure IR pro kopírování (zdroj a jímka mají v cloudu), aby mohl zjišťovat Data Lake Store oblasti služby Data Factory, přidělit nejméně **čtečky** role v účtu řízení přístupu (IAM). Pokud chcete se vyhnout této role IAM [zadat executionlocation, abyste](data-factory-data-movement-activities.md#global) s umístěním vaše Data Lake Store v aktivitě kopírování.
>- Pokud jste **k vytváření kanálů pomocí Průvodce kopírováním**, přidělit nejméně **čtečky** role v účtu řízení přístupu (IAM). Také poskytnout alespoň **číst + provést** oprávnění na kořenovém adresáři Data Lake Store ("/") a jeho podřízené položky. Jinak se může zobrazit zpráva "poskytnuté přihlašovací údaje jsou neplatné."

**Příklad: Ověření přihlašovacích údajů uživatele**
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
Autorizační kód, který vygenerujete pomocí **Authorize** tlačítko vyprší po určitou dobu. Tato zpráva znamená, že vypršela platnost ověřovacího tokenu:

Chyba operace přihlašovacích údajů: invalid_grant - AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS70008: Udělení přístupu zadaná vyprší nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 časové razítko: 2015-12-15 21-09-31Z.

Následující tabulka uvádí čas vypršení platnosti různé typy uživatelských účtů:

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty *není* spravované v Azure Active Directory (například @hotmail.com nebo @live.com) |12 hodin |
| Uživatelské účty, které jsou spravované v Azure Active Directory |Spusťte 14 dnů po poslední řezu <br/><br/>90 dnů, pokud řezu založeného na základě OAuth propojenou službu používá alespoň jednou za 14 dní |

Pokud změníte svoje heslo před časem vypršení platnosti tokenu, tokenu vyprší platnost okamžitě. Zobrazí se zpráva již bylo zmíněno dříve v této části.

Účet můžete autorizovat pomocí **Authorize** tlačítko, když vyprší platnost tokenu pro propojenou službu znovu nasaďte. Hodnoty můžete vygenerovat také **sessionId** a **autorizace** vlastnosti prostřednictvím kódu programu pomocí následujícího kódu:


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
Podrobnosti o třídách služby Data Factory používá v kódu, najdete v článku [AzureDataLakeStoreLinkedService třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), a [ Třída AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témata. Přidat odkaz na verzi `2.9.10826.1824` z `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` pro `WindowsFormsWebAuthenticationDialog` třída používaná v kódu.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznaky:** Při kopírování dat **do** Azure Data Lake Store, pokud vaše aktivita kopírování selže s následující chybou:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Hlavní příčina:** Existují 2 možné důvody:

1. `resourceGroupName` A/nebo `subscriptionId` zadané v Azure Data Lake Store propojené služby není správné.
2. Uživatel nebo instanční objekt služby nemá potřebná oprávnění.

**Řešení:**

1. Ujistěte se, že `subscriptionId` a `resourceGroupName` zadáte v propojené službě `typeProperties` ve skutečnosti jsou ty, které vašemu účtu data lake patří.

2. Ujistěte se, že udělíte alespoň **čtečky** role u uživatele nebo instančního objektu v účtu data lake. Tady je postup:

    1. Přejděte na web Azure Portal -> svůj účet Data Lake Store
    2. Klikněte na tlačítko **řízení přístupu (IAM)** v okně Data Lake Store
    3. Klikněte na tlačítko **přidat přiřazení role**
    4. Nastavte **Role** jako **čtečky**a vyberte uživatele nebo instanční objekt služby, které budete používat pro kopírování k udělení přístupu

3. Pokud nechcete, aby udělit **čtečky** role u uživatele nebo instanční objekt, alternativní [explicitně zadat umístění pro spuštění](data-factory-data-movement-activities.md#global) v kopírování activitywith umístění vaše Data Lake Store. Příklad:

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
K určení datové sady reprezentující vstupní data v Data Lake Store, můžete nastavit **typ** vlastnosti datové sady na **AzureDataLakeStore**. Nastavte **linkedServiceName** vlastnosti datové sady na název Data Lake Store propojenou službu. Úplný seznam části JSON a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Části datové sady ve formátu JSON, jako například **struktura**, **dostupnosti**, a **zásady**, jsou podobné jako u všech typů datovou sadu (Azure SQL database, Azure blob a tabulek v Azure, pro např.). **TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace, jako je umístění a formát dat v úložišti. 

**TypeProperties** části datové sady typu **AzureDataLakeStore** obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **folderPath** |Cesta k kontejner a složku v Data Lake Store. |Ano |
| **fileName** |Název souboru v Azure Data Lake Store. **FileName** vlastnost je volitelná a malá a velká písmena. <br/><br/>Pokud zadáte **fileName**, aktivity (včetně kopie) funguje na konkrétní soubor.<br/><br/>Když **fileName** není zadán, zahrnuje kopírování všech souborů v **folderPath** ve vstupní sadě.<br/><br/>Když **fileName** pro výstupní datovou sadu není zadána a **preserveHierarchy** není zadán v jímky aktivity, je název generovaného souboru ve formátu Data. _Identifikátor GUID_.txt ". Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Ne |
| **partitionedBy** |**PartitionedBy** vlastnost je volitelná. Slouží k určení dynamické cestu a název souboru pro data časových řad. Například **folderPath** může být parametrizován pro každou hodinu data. Podrobnosti a příklady najdete v tématu [vlastnost partitionedBy](#using-partitionedby-property). |Ne |
| **Formát** | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, a **ParquetFormat**. Nastavte **typ** vlastnosti v části **formátu** na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly v [formáty souborů a komprese podporovaných službou Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článku. <br><br> Pokud chcete zkopírovat soubory "jako-je" mezi souborové úložiště (binární kopie), přejděte `format` části v definicích oba vstupní a výstupní datové sady. |Ne |
| **Komprese** | Zadejte typ a úroveň komprese pro data. Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese podporovaných službou Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="the-partitionedby-property"></a>Vlastnost partitionedBy
Můžete zadat dynamické **folderPath** a **fileName** vlastnosti pro data časových řad s **partitionedBy** vlastnost, funkce Data Factory a systémové proměnné. Podrobnosti najdete v tématu [Azure Data Factory – funkce a systémové proměnné](data-factory-functions-variables.md) článku.


V následujícím příkladu `{Slice}` je nahrazena hodnotou proměnné systému služby Data Factory `SliceStart` ve formátu určeném (`yyyyMMddHH`). Název `SliceStart` odkazuje na počáteční čas řezu. `folderPath` Vlastnost se liší pro každý řez, jako v `wikidatagateway/wikisampledataout/2014100103` nebo `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V následujícím příkladu, rok, měsíc, den a čas `SliceStart` jsou extrahovány do samostatných proměnných, které jsou používány `folderPath` a `fileName` vlastnosti:
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
Podrobné informace o časových řad datové sady, plánování a řezů, najdete v článku [datové sady ve službě Azure Data Factory](data-factory-create-datasets.md) a [služby Data Factory plánování a provádění](data-factory-scheduling-and-execution.md) článků. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

K dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

**AzureDataLakeStoreSource** podporuje následující vlastnost **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **rekurzivní** |Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. |True, False (výchozí hodnota) |Ne |


**AzureDataLakeStoreSink** podporuje následující vlastnosti v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **copyBehavior** |Určuje chování kopírování. |<b>PreserveHierarchy</b>: Zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/><b>FlattenHierarchy</b>: Všechny soubory ze zdrojové složky vytvořené v první úroveň cílové složky. Cílové soubory se vytvoří s automaticky generované názvy.<br/><br/><b>MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud není zadán název souboru nebo objekt blob, je název souboru sloučeného se zadaným názvem. Název souboru, jinak se automaticky generuje. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + 5 souboru obsahu jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaného souboru. Automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článku.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Příklady JSON pro kopírování dat do a z Data Lake Store
Následující příklady popisují ukázková definice JSON. Tyto ukázkové definice můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data do a z Data Lake Store a Azure Blob storage. Nicméně je možné zkopírovat data _přímo_ z jakéhokoli zdroje na jakýkoli z podporovaných jímky. Další informace najdete v oddílu "podporovaná úložiště dat a formáty" v [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) článku.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Příklad: Kopírování dat z Azure Blob Storage do Azure Data Lake Store
Příklad kódu v této části ukazuje:

* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Propojené služby typu [AzureDataLakeStore](#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureDataLakeStoreSink](#copy-activity-properties).

Příklady ukazují, jak časových řad dat z Azure Blob Storage je zkopírován do Data Lake Store za hodinu. 

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
> Podrobnosti o konfiguraci, najdete v článku [vlastnostem propojených služeb](#linked-service-properties) oddílu.
>

**Vstupní datová sada Azure Blob**

V následujícím příkladu, data je převzata z nového objektu blob každou hodinu (`"frequency": "Hour", "interval": 1`). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den část čas spuštění. Název souboru používá část pro hodinu čas spuštění. `"external": true` Nastavení informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Výstupní datová sada Azure Data Lake Store**

Následující příklad zkopíruje data do Data Lake Store. Nová data zkopírována do Data Lake Store za hodinu.

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


**Aktivita kopírování v kanálu s blob zdroje a jímky Data Lake Store**

V následujícím příkladu kanálu obsahujícího aktivitu kopírování, která je nakonfigurovaná k použití vstupní a výstupní datové sady. Aktivita kopírování je naplánováno spuštění každou hodinu. V definici JSON kanálu `source` je typ nastaven na `BlobSource`a `sink` je typ nastaven na `AzureDataLakeStoreSink`.

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
Příklad kódu v této části ukazuje:

* Propojené služby typu [AzureDataLakeStore](#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureDataLakeStoreSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kód zkopíruje data časových řad z Data Lake Store do objektu blob Azure každou hodinu. 

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
> Podrobnosti o konfiguraci, najdete v článku [vlastnostem propojených služeb](#linked-service-properties) oddílu.
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
**Vstupní datová sada Azure Data Lake**

V tomto příkladu nastavení `"external"` k `true` informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

V následujícím příkladu, data se zapisují do nového objektu blob každou hodinu (`"frequency": "Hour", "interval": 1`). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a část hodiny, čas spuštění.

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

**Aktivita kopírování v kanálu pomocí Azure Data Lake Store zdroje a jímky objektu blob**

V následujícím příkladu kanálu obsahujícího aktivitu kopírování, která je nakonfigurovaná k použití vstupní a výstupní datové sady. Aktivita kopírování je naplánováno spuštění každou hodinu. V definici JSON kanálu `source` je typ nastaven na `AzureDataLakeStoreSource`a `sink` je typ nastaven na `BlobSink`.

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

V definici aktivity kopírování můžete také namapovat sloupce ze jako zdrojovou datovou sadu sloupců v datové sadě jímky. Podrobnosti najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Seznamte se s faktory, které ovlivňují výkonem aktivity kopírování a jak ji optimalizovat, najdete v článku [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) článku.
