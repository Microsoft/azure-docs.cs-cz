---
title: Kopírování dat do a z Azure Data Lake Storage Gen1
description: Zjistěte, jak kopírovat data do a z Úložiště datových jezer pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281597"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopírování dat do a z Data Lake Storage Gen1 pomocí data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-datalake-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Azure Data Lake Storage Gen1 ve Verzi 2](../connector-azure-data-lake-store.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesunout data do a z Azure Data Lake Storage Gen1 (dříve známé jako Azure Data Lake Store). Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) přehled pohybu dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **Azure Data Lake Store** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do úložiště Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Před vytvořením kanálu s aktivitou kopírování vytvořte účet Úložiště datového jezera. Další informace najdete [v tématu Začínáme s Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Podporované typy ověřování
Konektor Úložiště datových jezer podporuje tyto typy ověřování:
* Ověřování instančních objektů
* Ověřování pověření uživatele (OAuth)

Doporučujeme použít ověřování instančního objektu, zejména pro naplánovanou kopii dat. Při ověřování pověření uživatele může dojít k chování vypršení platnosti tokenu. Podrobnosti o konfiguraci naleznete v části [Vlastnosti propojené služby.](#linked-service-properties)

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Úložiště dat Azure Lake Store pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál pro kopírování dat, je použít **Průvodce kopírováním**. Návod k vytvoření kanálu pomocí Průvodce kopírováním naleznete v [tématu Návod: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Například pokud kopírujete data z úložiště objektů blob Azure do úložiště Azure Data Lake Store, vytvoříte dvě propojené služby, které propojí váš účet úložiště Azure a úložiště Azure Data Lake s vaší továrně dat. Vlastnosti propojené služby, které jsou specifické pro Azure Data Lake Store, najdete v článku [vlastnosti propojené služby.](#linked-service-properties)
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu, která určí kontejner objektů blob a složku, která obsahuje vstupní data. A můžete vytvořit další datovou sadu k určení složky a cesty k souboru v úložišti Datové jezero, který obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, najdete v tématu [vlastnosti datové sady](#dataset-properties) části.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použijete BlobSource jako zdroj a AzureDataLakeStoreSink jako jímky pro aktivitu kopírování. Podobně pokud kopírujete z Azure Data Lake Store do Azure Blob Storage, použijete AzureDataLakeStoreSource a BlobSink v aktivitě kopírování. Informace o vlastnostech aktivity kopírování, které jsou specifické pro Azure Data Lake Store, najdete v tématu [kopírování vlastností aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do nebo z úložiště Azure Data Lake Store, najdete v části [příklady JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro úložiště dat.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Propojená služba propojuje úložiště dat s továrnou na data. Vytvoříte propojenou službu typu **AzureDataLakeStore** pro propojení dat úložiště datových jezer s vaší továrně dat. Následující tabulka popisuje prvky JSON specifické pro propojené služby Úložiště dat. Můžete si vybrat mezi instančním objektem a ověřováním pověření uživatele.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Typ** | Vlastnost type musí být nastavena na **AzureDataLakeStore**. | Ano |
| **dataLakeStoreUri** | Informace o účtu Azure Data Lake Store. Tyto informace mají jeden z `https://[accountname].azuredatalakestore.net/webhdfs/v1` následujících formátů: nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| **subscriptionId** | ID předplatného Azure, do kterého patří účet Úložiště datového jezera. | Vyžadováno pro umyvadlo |
| **resourceGroupName** | Název skupiny prostředků Azure, do kterého patří účet Úložiště datového jezera. | Vyžadováno pro umyvadlo |

### <a name="service-principal-authentication-recommended"></a>Ověření instančního objektu (doporučeno)
Pokud chcete použít ověřování o jistině služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí přístup k úložišti Data Lake Store. Podrobné kroky naleznete v [tématu Ověřování mezi službami](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:
* ID aplikace
* Klíč aplikace
* ID tenanta

> [!IMPORTANT]
> Ujistěte se, že jste v Azure Data Lake Store udělili správné oprávnění k instančnímu objektu:
>- **Chcete-li použít úložiště data lake jako zdroj**, udělte alespoň oprávnění ke čtení + **spuštění** přístupu k datům k vypáčit a zkopírovat obsah složky nebo **číst** oprávnění ke kopírování jednoho souboru. Žádný požadavek na řízení přístupu na úrovni účtu.
>- **Chcete-li použít úložiště datového jezera jako jímku**, udělte alespoň oprávnění k přístupu **k datům Zápis + Spouštění** k vytvoření podřízených položek ve složce. A pokud používáte Azure IR k posílení kopírování (zdroj i jímky jsou v cloudu), aby data factory zjistit data Lake Store oblasti, udělit alespoň **roli Reader** v řízení přístupu k účtu (IAM). Pokud se chcete vyhnout této roli IAM, [zadejte spuštěníUmístění](data-factory-data-movement-activities.md#global) s umístěním úložiště datového jezera v aktivitě kopírování.
>- Pokud **použijete Průvodce kopírováním k vytváření kanálů**, udělte alespoň roli **čtečky** v řízení přístupu k účtu (IAM). Také udělte alespoň **oprávnění Ke čtení + spuštění** kořenovému adresáři úložiště datového jezera ("/") a jeho podřízeným položkám. V opačném případě se může zobrazit zpráva "Zadaný pověření jsou neplatná."

Pomocí ověřování instančního objektu zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **servicePrincipalId** | Zadejte ID klienta aplikace. | Ano |
| **servicePrincipalKey** | Zadejte klíč aplikace. | Ano |
| **Nájemce** | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano |

**Příklad: Ověřování instančního objektu**
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

### <a name="user-credential-authentication"></a>Ověřování pověření uživatele
Případně můžete použít ověřování pověření uživatele ke kopírování z nebo do úložiště Data Lake Store zadáním následujících vlastností:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **Autorizace** | Klepněte na tlačítko **Autorizovat** v editoru datové továrny a zadejte pověření, které této vlastnosti přiřadí automaticky vygenerovanou autorizační adresu URL. | Ano |
| **Sessionid** | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a lze jej použít pouze jednou. Toto nastavení se automaticky vygeneruje při použití Editoru datových tocích. | Ano |

> [!IMPORTANT]
> Ujistěte se, že uživateli udělíte vlastní oprávnění v Azure Data Lake Store:
>- **Chcete-li použít úložiště data lake jako zdroj**, udělte alespoň oprávnění ke čtení + **spuštění** přístupu k datům k vypáčit a zkopírovat obsah složky nebo **číst** oprávnění ke kopírování jednoho souboru. Žádný požadavek na řízení přístupu na úrovni účtu.
>- **Chcete-li použít úložiště datového jezera jako jímku**, udělte alespoň oprávnění k přístupu **k datům Zápis + Spouštění** k vytvoření podřízených položek ve složce. A pokud používáte Azure IR k posílení kopírování (zdroj i jímky jsou v cloudu), aby data factory zjistit data Lake Store oblasti, udělit alespoň **roli Reader** v řízení přístupu k účtu (IAM). Pokud se chcete vyhnout této roli IAM, [zadejte spuštěníUmístění](data-factory-data-movement-activities.md#global) s umístěním úložiště datového jezera v aktivitě kopírování.
>- Pokud **použijete Průvodce kopírováním k vytváření kanálů**, udělte alespoň roli **čtečky** v řízení přístupu k účtu (IAM). Také udělte alespoň **oprávnění Ke čtení + spuštění** kořenovému adresáři úložiště datového jezera ("/") a jeho podřízeným položkám. V opačném případě se může zobrazit zpráva "Zadaný pověření jsou neplatná."

**Příklad: Ověřování pověření uživatele**
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
Autorizační kód, který vygenerujete pomocí tlačítka **Autorizovat,** vyprší po určité době. Následující zpráva znamená, že vypršela platnost ověřovacího tokenu:

Chyba operace pověření: invalid_grant - AADSTS70002: Chyba při ověřování pověření. AADSTS70008: Zadaný přístupový grant vypršel nebo byl odvolán. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korelační ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Časové razítko: 2015-12-15 21-09-31Z.

V následující tabulce jsou uvedeny doby vypršení platnosti různých typů uživatelských účtů:

| Typ uživatele | Platnost vyprší po |
|:--- |:--- |
| Uživatelské *účty,* které není spravováno @hotmail.com službou Azure Active Directory (například nebo) @live.com |12 hodin |
| Uživatelské účty spravované službou Azure Active Directory |14 dní po posledním spuštění řezu <br/><br/>90 dní, pokud řez založený na propojené službě založené na OAuth běží alespoň jednou za 14 dní |

Pokud změníte heslo před vypršením platnosti tokenu, platnost tokenu okamžitě vyprší. Zobrazí se zpráva uvedená výše v této části.

Účet můžete znovu autorizovat pomocí tlačítka **Autorizovat,** když vyprší platnost tokenu pro opětovné nasazení propojené služby. Můžete také generovat hodnoty pro **sessionId** a **autorizace** vlastnosti programově pomocí následujícího kódu:


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
Podrobnosti o třídách Data Factory použitých v kódu najdete v tématech [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)a [AuthorizationSessionGetResponse Class.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) Přidejte odkaz `2.9.10826.1824` na `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` verzi `WindowsFormsWebAuthenticationDialog` pro třídu použitou v kódu.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznak:** Při kopírování dat **do** Úložiště dat Azure, pokud vaše aktivita kopírování nezdaří s následující chybou:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Hlavní příčina:** Existují 2 možné důvody:

1. A/nebo `resourceGroupName` `subscriptionId` zadaná v propojené službě Azure Data Lake Store je nesprávná;
2. Uživatel nebo instanční objekt nemá potřebná oprávnění.

**Rozlišení:**

1. Ujistěte `subscriptionId` se, že a `resourceGroupName` `typeProperties` zadáte v propojené služby jsou skutečně ty, které váš účet datového jezera patří.

2. Ujistěte se, že udělíte alespoň **roli čtečky** uživateli nebo instančnímu objektu v účtu datového jezera. Zde je návod, jak to udělat:

    1. Přejděte na portál Azure -> účtu Úložiště dat.
    2. Klikněte na **řízení přístupu (IAM)** v okně úložiště datových jezer.
    3. Klikněte na **Přidat přiřazení role.**
    4. Nastavte **roli** jako **čtenáře**a vyberte uživatele nebo instanční objekt, který používáte pro kopírování, aby byl přístup udělován.

3. Pokud nechcete udělit roli **čtenáře** uživateli nebo instančnímu objektu služby, alternativou je [explicitně zadat umístění spuštění](data-factory-data-movement-activities.md#global) v aktivitě kopírování s umístěním úložiště datového jezera. Příklad:

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
Chcete-li zadat datovou sadu představující vstupní data v úložišti datových jezer, nastavte vlastnost **type** datové sady na **AzureDataLakeStore**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Data Lake Store. Úplný seznam oddílů JSON a vlastností dostupných pro definování datových sad naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Části datové sady v JSON, jako je **struktura**, **dostupnost**a **zásady**, jsou podobné pro všechny typy datových sad (azure SQL databáze, Azure blob a azure tabulka, například). Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace, jako je umístění a formát dat v úložišti dat.

Část **typeProperties** pro datovou sadu typu **AzureDataLakeStore** obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| **folderPath** |Cesta ke kontejneru a složce v úložišti Data Lake Store. |Ano |
| **fileName** |Název souboru v Úložišti Azure Data Lake Store. Vlastnost **fileName** je volitelná a rozlišuje malá a velká písmena. <br/><br/>Pokud zadáte **název_souboru**, aktivita (včetně kopírování) funguje na konkrétním souboru.<br/><br/>Pokud není zadán **název_souboru,** funkce Kopírovat zahrne všechny soubory ve **složceCesta** ve vstupní datové sadě.<br/><br/>Pokud **fileName** není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce `Data._Guid_.txt`aktivity, název generovaného souboru je ve formátu . Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Ne |
| **partitionedBy** |**PartitionedBy** Vlastnost je volitelné. Můžete ji použít k určení dynamické cesty a názvu souboru pro data časových řad. Například **folderPath** lze parametrizovat pro každou hodinu dat. Podrobnosti a příklady naleznete v tématu partitionedBy vlastnost. |Ne |
| **Formát** | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte vlastnost **type** ve **formátu** na jednu z těchto hodnot. Další informace najdete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [Formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket](data-factory-supported-file-and-compression-formats.md#parquet-format) v článku Soubor a komprese podporované v článku Azure [Data Factory.](data-factory-supported-file-and-compression-formats.md) <br><br> Pokud chcete kopírovat soubory "tak, jak jsou" mezi obchody založenými na souborech (binární kopie), přeskočte `format` oddíl v definicích vstupní i výstupní datové sady. |Ne |
| **Komprese** | Určete typ a úroveň komprese dat. Podporované typy jsou **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou **optimální** a **nejrychlejší**. Další informace najdete [v tématu Formáty souborů a komprese podporované službou Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="the-partitionedby-property"></a>PartitionedBy Vlastnost
Můžete určit dynamické **vlastnosti folderPath** a **fileName** pro data časových řad s **vlastností partitionedBy,** funkcemi Data Factory a systémovými proměnnými. Podrobnosti najdete v článku [Azure Data Factory - funkce a systémové proměnné.](data-factory-functions-variables.md)


V následujícím příkladu `{Slice}` je nahrazena hodnotou systémové `SliceStart` proměnné Data Factory`yyyyMMddHH`v zadaném formátu ( ). Název `SliceStart` odkazuje na čas zahájení řezu. Vlastnost `folderPath` se liší pro každý `wikidatagateway/wikisampledataout/2014100103` řez, jako v nebo `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V následujícím příkladu `SliceStart` jsou rok, měsíc, den a čas extrahovány do `folderPath` samostatných proměnných, které jsou používány vlastnostmi a: `fileName`
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
Další podrobnosti o datových sadách časových řad, plánování a řezech najdete [v článcích datasady v Azure Data Factory](data-factory-create-datasets.md) a Data Factory plánování a [provádění.](data-factory-scheduling-and-execution.md)


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti dostupné v části **typeProperties** aktivity se liší podle jednotlivých typů aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

**AzureDataLakeStoreSource** podporuje následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **Rekurzivní** |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |True (výchozí hodnota), False |Ne |

**AzureDataLakeStoreSink** podporuje následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **copyBehavior** |Určuje chování kopírování. |<b>Zachovat hierarchii</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>Sloučení</b>: Všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvářeny s automaticky generovanými názvy.<br/><br/><b>MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, je zadanýnázev sloučeného souboru. V opačném případě je název souboru automaticky generován. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování operace Copy pro různé kombinace rekurzivních a copyBehavior hodnot.

| Rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |zachovat hierarchii |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5. |
| true |sloučení hierarchie |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru5 |
| true |mergeFiles |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru |
| false (nepravda) |zachovat hierarchii |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |sloučení hierarchie |Pro zdrojovou složku Folder1 s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/><br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku Folder1 s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete [v článku Soubor a kompresní formáty v Azure Data Factory.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Příklady JSON pro kopírování dat do a z úložiště Data Lake Store
Následující příklady poskytují ukázkové definice JSON. Tyto ukázkové definice můžete použít k vytvoření kanálu pomocí [Visual Studia](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data do a z Úložiště datových jezer a úložiště objektů blob Azure. Data však lze zkopírovat _přímo_ z libovolného zdroje do některého z podporovaných jímky. Další informace naleznete v části Podporovaná úložiště a formáty dat v článku [Přesunout data.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Příklad: Kopírování dat z Azure Blob Storage do Azure Data Lake Store
Ukázkový kód v této části ukazuje:

* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Propojená služba typu [AzureDataLakeStore](#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [Objekt BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureDataLakeStoreSink](#copy-activity-properties).

Příklady ukazují, jak se data časových řad z Azure Blob Storage zkopírují do úložiště datových jezer každou hodinu.

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
> Podrobnosti o konfiguraci naleznete v části [Vlastnosti propojené služby.](#linked-service-properties)
>

**Vstupní datová sada Azure Blob**

V následujícím příkladu se data získanou`"frequency": "Hour", "interval": 1`z nového objektu blob každou hodinu ( ). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času. Název souboru používá hodinovou část počátečního času. Nastavení `"external": true` informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

**Výstupní datová sada Úložiště dat Azure**

Následující příklad zkopíruje data do úložiště Data Lake Store. Nová data se kopírují do úložiště datových jezer každou hodinu.

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

**Kopírování aktivity v kanálu se zdrojem objektů blob a jímkou úložiště data lake store**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad. Aktivita kopírování je naplánována na každou hodinu. V definici JSON `source` kanálu je `BlobSource`typ nastaven `sink` na . `AzureDataLakeStoreSink`a typ je nastaven na .

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
Ukázkový kód v této části ukazuje:

* Propojená služba typu [AzureDataLakeStore](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureDataLakeStoreSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kód každou hodinu kopíruje data časových řad z úložiště Data Lake Store do objektu blob Azure.

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
> Podrobnosti o konfiguraci naleznete v části [Vlastnosti propojené služby.](#linked-service-properties)
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

V tomto příkladu nastavení `"external"` `true` informuje službu Data Factory, že tabulka je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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
**Výstupní datová sada objektů blob Azure**

V následujícím příkladu se data zapisují`"frequency": "Hour", "interval": 1`do nového objektu blob každou hodinu ( ). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část počátečního času rok, měsíc, den a hodiny.

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

**Aktivita kopírování v kanálu se zdrojem úložiště Azure Data Lake Store a jímkou blob**

V následujícím příkladu kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad. Aktivita kopírování je naplánována na každou hodinu. V definici JSON `source` kanálu je `AzureDataLakeStoreSource`typ nastaven `sink` na . `BlobSink`a typ je nastaven na .

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

V definici aktivity kopírování můžete také mapovat sloupce ze zdrojové datové sady na sloupce v datové sadě jímky. Podrobnosti najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Informace o faktorech, které ovlivňují výkon aktivity kopírování a jak ji optimalizovat, naleznete v článku [Průvodce výkonem aktivity kopírování a laděním.](data-factory-copy-activity-performance.md)
