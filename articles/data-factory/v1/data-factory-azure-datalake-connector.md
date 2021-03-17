---
title: Kopírování dat do a z Azure Data Lake Storage Gen1
description: Naučte se, jak kopírovat data do Data Lake Store a z nich pomocí Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379399"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopírování dat do a z Data Lake Storage Gen1 pomocí Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-datalake-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Data Lake Storage Gen1 v v2](../connector-azure-data-lake-store.md).

Tento článek vysvětluje, jak používat aktivitu kopírování v Azure Data Factory k přesunu dat do a z Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store). Vychází z článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , což je přehled přesunu dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z Azure Data Lake Store** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Před vytvořením kanálu s aktivitou kopírování vytvořte účet Data Lake Store. Další informace najdete v tématu [Začínáme s Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Podporované typy ověřování
Konektor Data Lake Store podporuje tyto typy ověřování:
* Ověřování instančních objektů
* Ověřování přihlašovacích údajů uživatele (OAuth)

Doporučujeme používat ověřování instančního objektu, zejména při plánovaném kopírování dat. K chování vypršení platnosti tokenu může dojít při ověřování přihlašovacích údajů uživatele. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby](#linked-service-properties) .

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Azure Data Lake Store pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál pro kopírování dat, je použít **Průvodce kopírováním**. Kurz týkající se vytvoření kanálu pomocí Průvodce kopírováním najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z úložiště objektů BLOB v Azure do Azure Data Lake Store, vytvoříte dvě propojené služby, které propojí váš účet služby Azure Storage a Azure Data Lake úložiště do vaší datové továrny. Vlastnosti propojené služby, které jsou specifické pro Azure Data Lake Store, najdete v části [Vlastnosti propojené služby](#linked-service-properties) .
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje vstupní data. A vytvoříte další datovou sadu pro určení složky a cesty k souboru v úložišti Data Lake, která obsahuje data zkopírovaná z úložiště objektů BLOB. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, najdete v části [Vlastnosti datové sady](#dataset-properties) .
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování BlobSource jako zdroj a AzureDataLakeStoreSink. Podobně pokud kopírujete z Azure Data Lake Store do Azure Blob Storage, v aktivitě kopírování použijete AzureDataLakeStoreSource a BlobSink. Vlastnosti aktivity kopírování, které jsou specifické pro Azure Data Lake Store, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory používané ke kopírování dat do a z Azure Data Lake Store najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které slouží k definování Data Factory entit specifických pro Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat s datovou továrnou. Vytvoříte propojenou službu typu **AzureDataLakeStore** , která propojí data data Lake Store s datovou továrnou. Následující tabulka obsahuje popis prvků JSON specifických pro Data Lake Store propojených služeb. Můžete si vybrat mezi instančním objektem a ověřováním přihlašovacích údajů uživatele.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **textový** | Vlastnost Type musí být nastavená na **AzureDataLakeStore**. | Yes |
| **dataLakeStoreUri** | Informace o účtu Azure Data Lake Store. Tyto informace přebírají jeden z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/` . | Yes |
| **subscriptionId** | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžadováno pro jímku |
| **resourceGroupName** | Název skupiny prostředků Azure, ke které patří účet Data Lake Store. | Vyžadováno pro jímku |

### <a name="service-principal-authentication-recommended"></a>Ověřování instančního objektu (doporučeno)
Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k Data Lake Store. Podrobný postup najdete v tématu [ověřování služba-služba](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:
* ID aplikace
* Klíč aplikace
* ID tenanta

> [!IMPORTANT]
> Ujistěte se, že jste instančnímu objektu udělili správné oprávnění v Azure Data Lake Store:
>- **Chcete-li použít data Lake Store as source**, udělte k seznamu a zkopírování obsahu složky nebo oprávnění ke **čtení** pro kopírování jednoho souboru oprávnění k přístupu k datům alespoň **pro čtení + Execute** . Nepožaduje se řízení přístupu na úrovni účtu.
>- Pokud **chcete použít data Lake Store jako jímku**, udělte aspoň oprávnění **zapisovat + provést** přístup k datům k vytváření podřízených položek ve složce. A pokud použijete Azure IR k tomu, aby se daly kopírovat (zdroj i jímka jsou v cloudu), aby Data Factory Data Lake Store zjistilo, že je v oblasti řízení přístupu (IAM) k dispozici alespoň role **Čtenář** . Pokud se chcete této roli IAM vyhnout, [Zadejte executionLocation](data-factory-data-movement-activities.md#global) s umístěním Data Lake Store v aktivitě kopírování.
>- Pokud **k vytváření kanálů použijete Průvodce kopírováním**, udělte aspoň roli **Čtenář** v řízení přístupu k účtu (IAM). Udělte taky oprávnění ke **čtení a spouštění** pro váš Data Lake Store root ("/") a jeho podřízené položky. V opačném případě se může zobrazit zpráva "poskytnuté přihlašovací údaje jsou neplatné."

Použijte ověřování instančního objektu zadáním následujících vlastností:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta aplikace. | Yes |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Yes |
| **tenant** | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Yes |

**Příklad: ověřování instančního objektu**
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

### <a name="user-credential-authentication"></a>Ověřování přihlašovacích údajů uživatele
Alternativně můžete pomocí ověření přihlašovacích údajů uživatele zkopírovat z nebo do Data Lake Store zadáním následujících vlastností:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **udělován** | V editoru Data Factory klikněte na tlačítko **autorizovat** a zadejte přihlašovací údaje, které přiřadí automaticky vygenerované autorizační URL k této vlastnosti. | Yes |
| **sessionId** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a dá se použít jenom jednou. Toto nastavení se generuje automaticky, když použijete Editor Data Factory. | Yes |

> [!IMPORTANT]
> Ujistěte se, že udělíte uživateli správné oprávnění v Azure Data Lake Store:
>- **Chcete-li použít data Lake Store as source**, udělte k seznamu a zkopírování obsahu složky nebo oprávnění ke **čtení** pro kopírování jednoho souboru oprávnění k přístupu k datům alespoň **pro čtení + Execute** . Nepožaduje se řízení přístupu na úrovni účtu.
>- Pokud **chcete použít data Lake Store jako jímku**, udělte aspoň oprávnění **zapisovat + provést** přístup k datům k vytváření podřízených položek ve složce. A pokud použijete Azure IR k tomu, aby se daly kopírovat (zdroj i jímka jsou v cloudu), aby Data Factory Data Lake Store zjistilo, že je v oblasti řízení přístupu (IAM) k dispozici alespoň role **Čtenář** . Pokud se chcete této roli IAM vyhnout, [Zadejte executionLocation](data-factory-data-movement-activities.md#global) s umístěním Data Lake Store v aktivitě kopírování.
>- Pokud **k vytváření kanálů použijete Průvodce kopírováním**, udělte aspoň roli **Čtenář** v řízení přístupu k účtu (IAM). Udělte taky oprávnění ke **čtení a spouštění** pro váš Data Lake Store root ("/") a jeho podřízené položky. V opačném případě se může zobrazit zpráva "poskytnuté přihlašovací údaje jsou neplatné."

**Příklad: ověření přihlašovacích údajů uživatele**
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
Autorizační kód, který vygenerujete pomocí tlačítka **autorizovat** , vyprší po uplynutí určité doby. Následující zpráva znamená, že platnost ověřovacího tokenu vypršela:

Chyba operace Credential: invalid_grant-AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: poskytnutý nebo odvolaný udělený přístup vypršel. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8D69-a776d2ffefd7 časové razítko: 2015-12-15 21 – 09 – 31Z.

V následující tabulce jsou uvedena doba platnosti různých typů uživatelských účtů:

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské účty  nespravované pomocí Azure Active Directory (například @hotmail.com nebo @live.com ) |12 hodin |
| Uživatelské účty spravované pomocí Azure Active Directory |14 dní po posledním spuštění řezu <br/><br/>90 dnů, pokud se řez založený na propojené službě založené na protokolu OAuth spouští aspoň každých 14 dní |

Pokud změníte heslo před časem vypršení platnosti tokenu, vyprší platnost tokenu okamžitě. V této části se zobrazí zpráva uvedená výše.

Účet můžete znovu autorizovat pomocí tlačítka **autorizovat** , když platnost tokenu vyprší a znovu nasadíte propojenou službu. Hodnoty pro **identifikátor SessionID** a vlastnosti **autorizace** můžete také generovat programově pomocí následujícího kódu:


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
Podrobnosti o třídách Data Factory používaných v kódu naleznete v tématech [Třída AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [Třída AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)a [AuthorizationSessionGetResponse třídy](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) . Přidejte odkaz na verzi `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` pro `WindowsFormsWebAuthenticationDialog` třídu použitou v kódu.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznak:** Pokud při kopírování dat **do** Azure Data Lake Store dojde k selhání aktivity kopírování s následující chybou:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Hlavní příčina:** K dispozici jsou dvě možné příčiny:

1. `resourceGroupName`A/nebo `subscriptionId` zadané v Azure Data Lake Store propojená služba není správná.
2. Uživatel nebo instanční objekt nemá potřebná oprávnění.

**Rozhodnutí**

1. Ujistěte se, že jsou `subscriptionId` a `resourceGroupName` zadané v propojené službě `typeProperties` jsou vlastně ty, ke kterým váš účet Data Lake patří.

2. Ujistěte se, že jste uživateli nebo instančnímu objektu v účtu Data Lake udělili aspoň roli **Čtenář** . Tady je postup, jak to udělat:

    1. Přejít na Azure Portal > účet Data Lake Store
    2. V okně Data Lake Store klikněte na **řízení přístupu (IAM)** .
    3. Klikněte na **Přidat přiřazení role** .
    4. Nastavte **role** jako **čtecí modul** a vyberte uživatele nebo instanční objekt, který používáte pro kopírování k udělení přístupu.

3. Pokud nechcete, aby se uživateli nebo instančnímu objektu udělila role **čtecího modulu** , je alternativou [explicitně zadat umístění spuštění](data-factory-data-movement-activities.md#global) v aktivitě kopírování s umístěním vašeho Data Lake Store. Příklad:

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
Chcete-li určit datovou sadu reprezentující vstupní data v Data Lake Store, nastavte vlastnost **Type** datové sady na **AzureDataLakeStore**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Data Lake Store. Úplný seznam oddílů a vlastností JSON dostupných pro definování datových sad najdete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly datové sady ve formátu JSON, jako je například **Struktura**, **dostupnost** a **zásada**, jsou podobné pro všechny typy datových sad (například Azure SQL Database, Azure Blob a Azure Table). Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace, jako je například umístění a formát dat v úložišti dat.

Oddíl **typeProperties** pro datovou sadu typu **AzureDataLakeStore** obsahuje následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| **folderPath** |Cesta ke kontejneru a složce v Data Lake Store. |Yes |
| **fileName** |Název souboru v Azure Data Lake Store. Vlastnost **filename** je volitelná a rozlišuje velká a malá písmena. <br/><br/>Pokud zadáte **název souboru**, bude aktivita (včetně kopie) fungovat na konkrétním souboru.<br/><br/>Pokud není zadán **název souboru** , příkaz Kopírovat zahrnuje všechny soubory v **FolderPath** ve vstupní datové sadě.<br/><br/>Pokud není zadán **název souboru** pro výstupní datovou sadu a v jímky aktivity není zadán parametr **preserveHierarchy** , je název generovaného souboru ve formátu `Data._Guid_.txt` . Například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |No |
| **partitionedBy** |Vlastnost **partitionedBy** je nepovinná. Můžete ji použít k zadání dynamické cesty a názvu souboru pro data časové řady. Například **FolderPath** může být Parametrizovaná za každou hodinu dat. Podrobnosti a příklady najdete v tématu vlastnost partitionedBy. |No |
| **formátovat** | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), formát [ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) v [souborech a kompresních formátech podporovaných](data-factory-supported-file-and-compression-formats.md) v článku Azure Data Factory. <br><br> Pokud chcete kopírovat soubory "tak jak jsou" mezi úložišti na základě souborů (binární kopie), přeskočte tento `format` oddíl jak v definici vstupní, tak výstupní datové sady. |No |
| **komprese** | Zadejte typ a úroveň komprese dat. Podporované typy jsou **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně jsou **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese podporované nástrojem Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="the-partitionedby-property"></a>Vlastnost partitionedBy
Můžete zadat dynamické **FolderPath** a vlastnosti **filename** pro data časových řad pomocí vlastnosti **partitionedBy** , data Factorych funkcí a systémových proměnných. Podrobnosti najdete v článku o [Azure Data Factory funkce a systémových proměnných](data-factory-functions-variables.md) .


V následujícím příkladu `{Slice}` je nahrazen hodnotou proměnné systému Data Factory `SliceStart` v zadaném formátu ( `yyyyMMddHH` ). Název `SliceStart` odkazuje na počáteční čas řezu. `folderPath`Vlastnost je pro každý řez odlišná, jako v `wikidatagateway/wikisampledataout/2014100103` nebo `wikidatagateway/wikisampledataout/2014100104` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V následujícím příkladu je rok, měsíc, den a čas `SliceStart` extrahován do samostatných proměnných, které jsou používány `folderPath` `fileName` vlastnostmi a:
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
Další podrobnosti o datových sadách časových řad, plánování a řezech najdete v článku [datové sady v tématu Azure Data Factory](data-factory-create-datasets.md) a [Data Factory plánování a spouštění](data-factory-scheduling-and-execution.md) .


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

Vlastnosti, které jsou k dispozici v části **typeProperties** v aktivitě, se liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

**AzureDataLakeStoreSource** podporuje následující vlastnost v části **typeProperties** :

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| **zahrnout** |Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. |True (výchozí hodnota), false |No |

**AzureDataLakeStoreSink** podporuje následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| **copyBehavior** |Určuje chování při kopírování. |<b>PreserveHierarchy</b>: zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvořeny pomocí automaticky generovaných názvů.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, sloučený název souboru je zadaný název. V opačném případě se název souboru automaticky vygeneruje. |No |

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivních a copyBehavior
Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| zahrnout | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří se stejnou strukturou jako zdroj.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro File5 |
| true |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 + file3 + file4 + soubor 5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |
| false (nepravda) |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/><br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článku.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Příklady JSON pro kopírování dat do a z Data Lake Store
V následujících příkladech jsou uvedeny ukázkové definice JSON. Tyto ukázkové definice můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data z Data Lake Store a do úložiště objektů BLOB v Azure. Data je však možné zkopírovat _přímo_ ze všech zdrojů do kterékoli z podporovaných umyvadel. Další informace najdete v části "podporované úložiště dat a formáty" v článku [přesunutí dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) .

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Příklad: zkopírování dat z Azure Blob Storage do Azure Data Lake Store
Vzorový kód v této části ukazuje:

* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Propojená služba typu [AzureDataLakeStore](#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureDataLakeStoreSink](#copy-activity-properties).

Příklady ukazují, jak se data časových řad z Azure Blob Storage zkopírují do Data Lake Store každou hodinu.

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
> Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby](#linked-service-properties) .
>

**Vstupní datová sada Azure Blob**

V následujícím příkladu se data vybírají z nového objektu BLOB každou hodinu ( `"frequency": "Hour", "interval": 1` ). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce a dne počátečního času. Název souboru používá hodinovou část času spuštění. Toto `"external": true` nastavení informuje službu Data Factory o tom, že je tabulka externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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

**Aktivita kopírování v kanálu se zdrojem objektu BLOB a jímky Data Lake Store**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady. Aktivita kopírování je naplánována ke spuštění každou hodinu. V definici JSON kanálu `source` je typ nastaven na hodnotu `BlobSource` a `sink` typ je nastaven na `AzureDataLakeStoreSink` .

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Příklad: kopírování dat z Azure Data Lake Store do objektu blob Azure
Vzorový kód v této části ukazuje:

* Propojená služba typu [AzureDataLakeStore](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureDataLakeStoreSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kód kopíruje data časových řad z Data Lake Store do objektu blob Azure každou hodinu.

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
> Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby](#linked-service-properties) .
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

V tomto příkladu se nastaví `"external"` na `true` informování služby Data Factory, že je tabulka externí pro objekt pro vytváření dat, a není vytvořená aktivitou v datové továrně.

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

V následujícím příkladu se data zapisují do nového objektu BLOB každou hodinu ( `"frequency": "Hour", "interval": 1` ). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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

**Aktivita kopírování v kanálu se zdrojem Azure Data Lake Store a jímkou objektů BLOB**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady. Aktivita kopírování je naplánována ke spuštění každou hodinu. V definici JSON kanálu `source` je typ nastaven na hodnotu `AzureDataLakeStoreSource` a `sink` typ je nastaven na `BlobSink` .

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

V definici aktivity kopírování můžete také namapovat sloupce ze zdrojové datové sady na sloupce v datové sadě jímky. Podrobnosti najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o faktorech, které ovlivňují výkon aktivity kopírování a jejich optimalizaci, najdete v článku [Průvodce výkonem aktivity kopírování a ladění](data-factory-copy-activity-performance.md) .