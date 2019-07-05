---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do a z Azure Data Lake Storage Gen2 pomocí služby Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 9f60c6258da77c0aaa99d16e178f4b3531ce90d9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509253"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory

Azure Data Lake Storage Gen2 je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, které jsou součástí [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ho pro rozhraní s daty pomocí obou paradigmat souboru systému a objektu úložiště.

Tento článek popisuje, jak kopírovat data do a z Azure Data Lake Storage Gen2. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Data Lake Storage Gen2 je podporována pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [nepodporuje zdrojem nebo jímkou matice](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Pomocí tohoto konektoru můžete konkrétně:

- Kopírování dat pomocí klíče účtu, instanční objekt nebo spravovaných identit pro ověřování prostředků Azure.
- Kopírování souborů je nebo analyzovat a generovat soubory s [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Pokud povolíte hierarchického oboru názvů, aktuálně neexistuje žádná interoperability mezi objektem Blob a API služby Data Lake Storage Gen2 operací. Pokud dosáhnete chybu "ErrorCode = FilesystemNotFound" se zprávou "zadaný systému souborů neexistuje.", je způsobena systému souborů zadaný jímky, který byl vytvořen jinde přes rozhraní API objektu Blob namísto rozhraní API služby Data Lake Storage Gen2. Pokud chcete problém vyřešit, zadejte nový systém souborů s názvem, který neexistuje jako název kontejneru objektů Blob. Data Factory automaticky vytvoří tento systém souborů během kopírování dat.

>[!NOTE]
>Pokud povolíte **Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště** možnost v nastavení brány firewall služby Azure Storage, prostředí Azure integration runtime není připojení k Data Lake Storage Gen2 a ukazuje zakázaného přístupu. Zobrazí se chybová zpráva, protože objekt pro vytváření dat není považován za důvěryhodné služby Microsoftu. Místo toho připojit pomocí místní prostředí integration runtime.

## <a name="get-started"></a>Začínáme

>[!TIP]
>Návod, jak používat Data Lake Storage Gen2 konektoru, najdete v části [načtení dat do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnosti, které se používají k definování entit služby Data Factory, které jsou specifické pro Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Azure Data Lake Storage Gen2 konektor podporuje následující typy ověřování. Najdete v částech odpovídající podrobnosti:

- [Ověření pomocí klíče účtu](#account-key-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravovaných identit pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Při použití PolyBase k načtení dat do SQL Data Warehouse, pokud váš zdroj Data Lake Storage Gen2 je nakonfigurován se koncový bod virtuální sítě, je potřeba použít spravovanou identitu ověřování podle potřeby polybase. Zobrazit [spravovanou identitu ověřování](#managed-identity) části s požadavky na konfiguraci.

### <a name="account-key-authentication"></a>Ověření pomocí klíče účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 s vzor `https://<accountname>.dfs.core.windows.net`. | Ano |
| accountKey | Klíč účtu pro Data Lake Storage Gen2. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure integration runtime nebo místní prostředí integration runtime. Pokud není tato vlastnost určena, použije se výchozí prostředí Azure integration runtime. |Ne |

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Ověřování instančních objektů, postupujte podle těchto kroků.

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících kroků v [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělení oprávnění objektu zabezpečení správné služby. Další informace o tom, jak funguje oprávnění v Data Lake Storage Gen2 z [seznamy řízení přístupu na soubory a adresáře](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako zdroj**: V Průzkumníku služby Storage, přidělit nejméně **Execute** oprávnění od zdrojového systému souborů, spolu s **čtení** oprávnění pro soubory, které chcete kopírovat. Můžete taky v řízení přístupu (IAM), udělit alespoň **čtecí modul dat pro úložiště objektů Blob** role.
    - **Jako jímku**: V Průzkumníku služby Storage, přidělit nejméně **Execute** oprávnění od jímky systému souborů, spolu s **zápisu** oprávnění pro složku jímky. Můžete taky v řízení přístupu (IAM), udělit alespoň **Přispěvatel dat objektu Blob úložiště** role.

>[!NOTE]
>Do seznamu složek, počínaje na úrovni účtu, nebo chcete otestovat připojení, je nutné nastavit oprávnění instančního objektu k **účtu úložiště pomocí oprávnění "Čtenář dat objektu Blob úložiště" v IAM**. To platí při použití:
>- **Nástroj pro kopírování dat** můžete vytvořit kanál kopírování.
>- **Uživatelské rozhraní služby Data Factory** otestovat připojení a procházení složek během vytváření obsahu. 
>Pokud máte obavy o udělení oprávnění na úrovni účtu během vytváření obsahu, přeskočte testování připojení a vstup nadřazenou cestu s udělit oprávnění klikněte na tlačítko Procházet ze zadané cesty. Zkopírujte aktivitu funguje jako instanční objekt služby je poskytnuta s řádným oprávněním na soubory, které se mají zkopírovat.

Tyto vlastnosti jsou podporovány pro propojenou službu:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 s vzor `https://<accountname>.dfs.core.windows.net`. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` bezpečně uložit ve službě Data Factory. Nebo můžete [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure integration runtime nebo místní prostředí integration runtime. Pokud není zadán, je použít výchozí prostředí Azure integration runtime. |Ne |

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Spravovaných identit pro ověřování prostředků Azure

Objekt pro vytváření dat můžou být spojené s [spravované identity pro prostředky Azure](data-factory-service-identity.md), která představuje této konkrétní datové továrně. Tuto spravovanou identitu můžete použít přímo pro ověřování Data Lake Storage Gen2, podobně jako u vlastních instančního objektu. Tento objekt pro vytváření určené umožňuje přístup a kopírování dat do nebo z vaší Data Lake Storage Gen2.

Použití spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků.

1. [Získat informace o identitě spravované služby Data Factory](data-factory-service-identity.md#retrieve-managed-identity) tak, že zkopírujete hodnoty **ID aplikace identity služby** generované spolu se svým objektem pro vytváření.

2. Udělení oprávnění správné spravovaná identita. Další informace o tom, jak funguje oprávnění v Data Lake Storage Gen2 z [seznamy řízení přístupu na soubory a adresáře](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako zdroj**: V Průzkumníku služby Storage, přidělit nejméně **Execute** oprávnění od zdrojového systému souborů, spolu s **čtení** oprávnění pro soubory, které chcete kopírovat. Můžete taky v řízení přístupu (IAM), udělit alespoň **čtecí modul dat pro úložiště objektů Blob** role.
    - **Jako jímku**: V Průzkumníku služby Storage, přidělit nejméně **Execute** oprávnění od jímky systému souborů, spolu s **zápisu** oprávnění pro složku jímky. Můžete taky v řízení přístupu (IAM), udělit alespoň **Přispěvatel dat objektu Blob úložiště** role.

>[!NOTE]
>Do seznamu složek, počínaje na úrovni účtu, nebo chcete otestovat připojení, je nutné nastavit oprávnění spravovanou identitu udělované **účtu úložiště pomocí oprávnění "Čtenář dat objektu Blob úložiště" v IAM**. To platí při použití:
>- **Nástroj pro kopírování dat** můžete vytvořit kanál kopírování.
>- **Uživatelské rozhraní služby Data Factory** otestovat připojení a procházení složek během vytváření obsahu. 
>Pokud máte obavy o udělení oprávnění na úrovni účtu během vytváření obsahu, přeskočte testování připojení a vstup nadřazenou cestu s udělit oprávnění klikněte na tlačítko Procházet ze zadané cesty. Zkopírujte aktivitu funguje jako instanční objekt služby je poskytnuta s řádným oprávněním na soubory, které se mají zkopírovat.

>[!IMPORTANT]
>Pokud při použití spravované identity ověřování pro Data Lake Storage Gen2 načtení dat z Data Lake Storage Gen2 do SQL Data Warehouse pomocí PolyBase, ujistěte se, že je také postupovat podle kroků 1 a 2 v [návod](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) na 1) zaregistrujte SQL Databázový server se službou Azure Active Directory (Azure AD) a 2) přiřazení role Přispěvatel dat objektu Blob Storage do databáze SQL serveru. Zbývající provádí služba Data Factory. Pokud vaše Data Lake Storage Gen2 je nakonfigurovaný pomocí koncového bodu Azure Virtual Network, použití PolyBase k načtení dat z něj, je potřeba použít spravovanou identitu ověřování podle potřeby polybase.

Tyto vlastnosti jsou podporovány pro propojenou službu:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 s vzor `https://<accountname>.dfs.core.windows.net`. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure integration runtime nebo místní prostředí integration runtime. Pokud není zadán, je použít výchozí prostředí Azure integration runtime. |Ne |

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datových sad](concepts-datasets-linked-services.md).

- Parquet a formátu odděleného textu, naleznete v tématu [datovou sadu formátu Parquet a text s oddělovači](#parquet-and-delimited-text-format-dataset) oddílu.
- Dalších formátech, jako jsou ORC, Avro, JSON nebo binární formát, najdete v článku [jiné datové sady formátu](#other-format-dataset) oddílu.

### <a name="parquet-and-delimited-text-format-dataset"></a>Datová sada formátu parquet a text s oddělovači

Pro kopírování dat do a z Data Lake Storage Gen2 ve formátu odděleného textu nebo parquet, najdete v článku [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článků na datové sadě založené na formátu a podporovaných nastavení. Jsou podporovány následující vlastnosti pro Data Lake Storage Gen2 pod `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type v rámci `location` v datové sadě musí být nastaveno na **AzureBlobFSLocation**. | Ano      |
| fileSystem | Název systému souborů Data Lake Storage Gen2.                              | Ne       |
| folderPath | Cesta ke složce v daném systému souborů. Pokud chcete použít zástupný znak pro filtr složek, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v daném systému souborů + folderPath. Pokud chcete použít zástupný znak pro filtr souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

> [!NOTE]
> **AzureBlobFSFile** typ datové sady ve formátu parquet nebo text uvedených v následující části je stále podporovány, jako je kopírování, vyhledávání nebo aktivitě GetMetadata z důvodu zpětné kompatibility. Ale nefunguje s funkcí toku data mapování. Doporučujeme vám použít tento nový model do budoucna. Data Factory pro vytváření uživatelského rozhraní generuje tyto nové typy.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Další datové sady formátu

Pro kopírování dat do a z Data Lake Storage Gen2 ORC, Avro, JSON nebo binární formát, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **AzureBlobFSFile**. |Ano |
| folderPath | Cesta ke složce v Data Lake Storage Gen2. Pokud není zadán, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné znaky jsou `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak). Použití `^` řídicí, pokud vaše skutečná složka obsahuje zástupný znak nebo se nachází uvnitř této řídicí znak. <br/><br/>Příklady: systém souborů a složek /. Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). |Ne |
| fileName | Název nebo zástupný filtr pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, jsou povolené zástupné znaky `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` řídicí Pokud vašeho skutečného názvu souboru obsahuje zástupný znak, nebo tento znak escape uvnitř.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu a **preserveHierarchy** není uveden v aktivita jímky aktivity kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*Data. [spuštění aktivit Identifikátor GUID]. [Identifikátor GUID Pokud FlattenHierarchy]. [formátu, je-li nakonfigurovat]. [Pokud nakonfigurované komprese]* ", například"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Pokud zkopírujete z tabulkové zdroje místo názvu tabulky dotaz, vzor názvů je " *[název tabulky]. [ formát]. [Pokud nakonfigurované komprese]* ", například"MyTable.csv". |Ne |
| modifiedDatetimeStart | Soubory filtru na základě atributu naposledy upraveno. Soubory jsou vybrány, pokud je jejich čas poslední změny v období mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Povolením tohoto nastavení, pokud chcete soubor filtrů s obrovské množství souborů je ovlivněný celkový výkon pohybu dat. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že do datové sady není použit žádný filtr atribut souboru. Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná hodnotě data a času jsou vybrány. Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že jsou vybrané soubory, jejichž poslední upravené atribut je menší než hodnota data a času.| Ne |
| modifiedDatetimeEnd | Soubory filtru na základě atributu naposledy upraveno. Soubory jsou vybrány, pokud je jejich čas poslední změny v období mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Povolením tohoto nastavení, pokud chcete soubor filtrů s obrovské množství souborů je ovlivněný celkový výkon pohybu dat. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že do datové sady není použit žádný filtr atribut souboru. Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná hodnotě data a času jsou vybrány. Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že jsou vybrané soubory, jejichž poslední upravené atribut je menší než hodnota data a času.| Ne |
| format | Pokud chcete zkopírovat soubory, jako je mezi souborové úložiště (binární kopie), přejděte v části formát v definici vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, and **ParquetFormat**. Nastavte **typ** vlastnosti v části **formátu** na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format), [formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formát ORC](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků. 

**Příklad:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [konfigurace aktivity Kopírovat](copy-activity-overview.md#configuration) a [kanály a aktivity](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných službou Data Lake Storage Gen2 zdroje a jímky.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ zdroje

- Zkopírovat z parquet nebo formátu odděleného textu, najdete v článku [Parquet a zdroj obsahující text oddělený znaky formátu](#parquet-and-delimited-text-format-source) části.
- Zkopírujte z dalších formátech, jako jsou ORC, Avro, JSON nebo binární formát, najdete v článku [jiný formát zdroj](#other-format-source) oddílu.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet a zdroj formátu odděleného textu

Ke zkopírování dat z Data Lake Storage Gen2 ve formátu odděleného textu nebo parquet, najdete v článku [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek věnovaný tomu zdroj aktivity kopírování založená na formát a podporovaných nastavení. Jsou podporovány následující vlastnosti pro Data Lake Storage Gen2 pod `storeSettings` nastavení zdroje kopírování založená na formát:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type v rámci `storeSettings` musí být nastaveno na **AzureBlobFSReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Když rekurzivní nastavena na hodnotu true a jímku je souborové úložiště, prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v rámci systému daný soubor nakonfigurovaný v datové sadě do složky zdrojového filtru. <br>Povolené zástupné znaky jsou `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak). Použití `^` řídicí, pokud vaše skutečná složka obsahuje zástupný znak nebo tento znak escape uvnitř. <br>Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v daném systému souborů + folderPath/wildcardFolderPath ke zdrojovým souborům filtru. <br>Povolené zástupné znaky jsou `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak). Použití `^` řídicí, pokud vaše skutečná složka obsahuje zástupný znak nebo tento znak escape uvnitř. Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ano, pokud `fileName` není zadaný v datové sadě |
| modifiedDatetimeStart    | Soubory filtru na základě atributu naposledy upraveno. Soubory jsou vybrány, pokud je jejich čas poslední změny v období mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že není použit žádný filtr atributu souboru k datové sadě. Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že soubory, jejichž poslední změny atributů je větší než nebo rovná hodnotě data a času jsou vybrány. Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, jestli jsou vybrané soubory, jejichž poslední upravené atribut je menší než hodnota data a času. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti storage současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne                                            |

> [!NOTE]
> Pro parquet nebo formátu odděleného textu **AzureBlobFSSource** zdroj aktivity kopírování typů uvedených v následující části je stále podporovány, je z důvodu zpětné kompatibility. Doporučujeme vám použít tento nový model do budoucna. Data Factory pro vytváření uživatelského rozhraní generuje tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Jiný zdroj formátu

Ke zkopírování dat z Data Lake Storage Gen2 ORC, Avro, JSON nebo binární formát, jsou podporovány následující vlastnosti v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **AzureBlobFSSource**. |Ano |
| recursive | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Když rekurzivní nastavena na hodnotu true a jímku je souborové úložiště, prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 jako typ jímky

- Pokud chcete zkopírovat do formátu odděleného textu nebo parquet, najdete v článku [Parquet a text oddělený znaky formátu jímky](#parquet-and-delimited-text-format-sink) části.
- Ke zkopírování do jiných formátů, jako jsou třeba ORC, Avro, JSON nebo binární formát, najdete v článku [jiných formátu jímky](#other-format-sink) oddílu.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet a jímku formátu odděleného textu

Ke zkopírování dat do Data Lake Storage Gen2 ve formátu odděleného textu nebo parquet, najdete v článku [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článků na jímky aktivity kopírování založená na formát a podporovaných nastavení. Jsou podporovány následující vlastnosti pro Data Lake Storage Gen2 pod `storeSettings` nastavení jímky kopírování založená na formát:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type v rámci `storeSettings` musí být nastaveno na **AzureBlobFSWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>- FlattenHierarchy</b>: V první úroveň cílové složky jsou všechny soubory ze zdrojové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne       |

> [!NOTE]
> Pro parquet nebo formátu odděleného textu **AzureBlobFSSink** jímky aktivity kopírování typu uvedené v následující části je stále podporovány, jako je z důvodu zpětné kompatibility. Doporučujeme vám použít tento nový model do budoucna. Data Factory pro vytváření uživatelského rozhraní generuje tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Další formátu jímky

Ke zkopírování dat do Data Lake Storage Gen2 ORC, Avro, JSON nebo binární formát, jsou podporovány následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **AzureBlobFSSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>- FlattenHierarchy</b>: V první úroveň cílové složky jsou všechny soubory ze zdrojové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Složky a příklady filtr souborů

Tato část popisuje výsledné chování název složky a cesta k souboru s filtry zástupný znak.

| folderPath | fileName | recursive | Zdrojové složky struktury a filtrování výsledků (soubory v **tučné** načtením)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdný, použijte výchozí) | false (nepravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Prázdný, použijte výchozí) | true (pravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false (nepravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true (pravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true (pravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File5 |
| true (pravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. automaticky generovaným názvem File1<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat seznamy ACL v Data Lake Storage Gen1

>[!TIP]
>Ke zkopírování dat z Azure Data Lake Storage Gen1 na Gen2 obecně platí, naleznete v tématu [kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) návod, jak a osvědčené postupy.

Při kopírování souborů z Azure Data Lake Storage Gen1 na Gen2 je možné zachovat rámci specifikace POSIX seznamy řízení přístupu (ACL) spolu s daty. Další informace o řízení přístupu najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) a [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Následující typy seznamů ACL je možné zachovat pomocí aktivity kopírování Azure Data Factory. Můžete vybrat jeden nebo více typů:

- **ACL**: Zkopírujte a zachovat seznamy řízení přístupu POSIX souborů a adresářů. Úplné existující seznamy ACL zkopíruje ze zdroje do jímky. 
- **Vlastník**: Zkopírujte a zachovat vlastnícího uživatele u souborů a adresářů. Je vyžadován superuživatele přístup k Data Lake Storage Gen2 jímky.
- **Skupiny**: Zkopírujte a zachovat vlastnící skupina souborů a adresářů. Je vyžadován přístup superuživatele do jímky Data Lake Storage Gen2 nebo vlastnícím uživatelem (Pokud je vlastnící uživatel je také členem cílové skupiny).

Pokud chcete zadat kopírování ze složky, služby Data Factory replikuje se seznamy ACL pro tento dané složky a soubory a adresáře v něm, pokud `recursive` je nastavena na hodnotu true. Pokud zadáte kopírovat z jednoho souboru, zkopírují se seznamy řízení přístupu na tento soubor.

>[!IMPORTANT]
>Pokud budete chtít zachovat seznamy ACL, ujistěte se, že udělíte vyšší dostatečná oprávnění pro službu Data Factory k provozu proti účtu Data Lake Storage Gen2 pro jímku. Například účet ověřování pomocí klíče nebo přiřazení role vlastníka dat objektů Blob úložiště identita instančního objektu nebo spravované služby.

Při konfiguraci zdroje jako Data Lake Storage Gen1 možnost binární kopie nebo binární formát a jímku jako Data Lake Storage Gen2 s možností binární kopie nebo binární formát, můžete najít **zachovat** možnost **kopírování Nastavení nástroje data** stránku nebo **aktivity kopírování** > **nastavení** kartu pro vytváření aktivit.

![Data Lake Storage Gen1 na Gen2 zachovat seznamu ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Tady je příklad konfigurace JSON (viz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastnosti toku dat

Další informace o [zdroje transformace](data-flow-source.md) a [jímky transformace](data-flow-sink.md) ve funkci mapování datového toku.

## <a name="next-steps"></a>Další postup

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
