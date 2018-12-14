---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporovaných úložišť dat do Azure Data Lake Store, nebo z Data Lake Store do jímky podporované úložišť pomocí služby Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: jingwang
ms.openlocfilehash: 411223c2ef7b1400f2019ebc6704b3c4a5d41235
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387025"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuální verze](connector-azure-data-lake-store.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat do a z Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store) ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do Azure Data Lake Store nebo kopírování dat z Azure Data Lake Store do jakékoli podporovaného úložiště dat jímky. Zobrazit [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure Data Lake Store podporuje:

- Kopírování souborů pomocí jedné z následujících metod ověřování: **instanční objekt služby** nebo **spravovaných identit pro prostředky Azure**.
- Kopírování souborů jako-je, nebo analýza kódu nebo generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Pokud zkopírujete data pomocí místního prostředí integration runtime, konfigurace podnikové brány firewall umožňuje odchozí přenosy na `<ADLS account name>.azuredatalakestore.net` a `login.microsoftonline.com/<tenant>/oauth2/token` na portu 443. Ten je Azure službu tokenů zabezpečení, které se prostředí integration runtime potřebuje ke komunikaci s získat přístupový token.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Návod k používání konektoru Azure Data Lake Store, naleznete v tématu [načtení dat do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro Azure Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu Azure Data Lake Store:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | `type` Musí být vlastnost nastavena na **AzureDataLakeStore**. | Ano |
| dataLakeStoreUri | Informace o účtu Azure Data Lake Store. Tyto informace má jednu z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro jímku |
| resourceGroupName | Název skupiny prostředků Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro jímku |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít prostředí Azure integration runtime nebo místním prostředí integration runtime (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není tato vlastnost určena, používá výchozí prostředí Azure integration runtime. |Ne |

### <a name="use-service-principal-authentication"></a>Použít ověřování instančních objektů

Pokud chcete používat ověřování instančních objektů, entity aplikaci zaregistrovat ve službě Azure Active Directory a jí udělit přístup k Data Lake Store. Podrobné pokyny najdete v článku [ověřování služba služba](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

- ID aplikace
- Klíč aplikace
- ID tenanta

>[!IMPORTANT]
> Ujistěte se, že udělíte hlavní správné oprávnění služby v Data Lake Store:
>- **Jako zdroj**: V **Průzkumník dat** > **přístup**, přidělit nejméně **číst + provést** oprávnění k seznamu a zkopírujte soubory do složek a podsložek. Nebo můžete udělit **čtení** oprávnění zkopírovat jeden soubor. Můžete také přidat do **tato složka a všechny podřízené objekty** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položka oprávnění**. Neexistuje žádný požadavek na účet řízení přístupu (IAM).
>- **Jako jímku**: V **Průzkumník dat** > **přístup**, přidělit nejméně **zapisovat + provést** oprávnění pro vytváření podřízených položek ve složce. Můžete také přidat do **tato složka a všechny podřízené objekty** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položka oprávnění**. Pokud používáte prostředí Azure integration runtime ke kopírování (zdroj a jímka mají v cloudu), v IAM, udělit alespoň **čtečky** role, aby bylo možné nechat Data Factory rozpoznat oblast pro Data Lake Store. Pokud chcete se vyhnout této role IAM explicitně [vytvořit prostředí Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) umístěním systému Data Lake Store. Jako v následujícím příkladu je ve službě Data Lake Store propojená přidružení.

>[!NOTE]
>Do seznamu složek od kořene, je nutné nastavit oprávnění instančního objektu k **na kořenové úrovni s oprávněním "Spustit"**. To platí při použití:
>- **Nástroj pro kopírování dat** můžete vytvořit kanál kopírování.
>- **Uživatelské rozhraní služby Data Factory** otestovat připojení a procházení složek během vytváření obsahu.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano |

**Příklad:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Použití spravované identity pro ověřování prostředků Azure

Objekt pro vytváření dat můžou být spojené s [spravované identity pro prostředky Azure](data-factory-service-identity.md), která představuje této konkrétní datové továrně. Tuto identitu služby můžete použít přímo pro ověřování Data Lake Store, podobně jako u vlastních instančního objektu. Tento objekt pro vytváření určené umožňuje přístup a kopírování dat do nebo z Data Lake Store.

Použití spravované identity pro ověřování prostředků Azure:

1. [Načíst identita služeb datové továrny](data-factory-service-identity.md#retrieve-service-identity) tak, že zkopírujete hodnoty "ID aplikace Identity služby" generované spolu se svým objektem pro vytváření.
2. Udělte přístup identit služby Data Lake Store, stejným způsobem jako pro instanční objekt služby, za tyto poznámky.

>[!IMPORTANT]
> Ujistěte se, že udělíte data factory služby identity správné oprávnění v Data Lake Store:
>- **Jako zdroj**: V **Průzkumník dat** > **přístup**, přidělit nejméně **číst + provést** oprávnění k seznamu a zkopírujte soubory do složek a podsložek. Nebo můžete udělit **čtení** oprávnění zkopírovat jeden soubor. Můžete také přidat do **tato složka a všechny podřízené objekty** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položka oprávnění**. Neexistuje žádný požadavek na účet řízení přístupu (IAM).
>- **Jako jímku**: V **Průzkumník dat** > **přístup**, přidělit nejméně **zapisovat + provést** oprávnění pro vytváření podřízených položek ve složce. Můžete také přidat do **tato složka a všechny podřízené objekty** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položka oprávnění**. Pokud používáte prostředí Azure integration runtime ke kopírování (zdroj a jímka mají v cloudu), v IAM, udělit alespoň **čtečky** role, aby bylo možné nechat Data Factory rozpoznat oblast pro Data Lake Store. Pokud chcete se vyhnout této role IAM explicitně [vytvořit prostředí Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) umístěním systému Data Lake Store. Jako v následujícím příkladu je ve službě Data Lake Store propojená přidružení.

>[!NOTE]
>Do seznamu složek od kořene, je nutné nastavit oprávnění instančního objektu k **na kořenové úrovni s oprávněním "Spustit"**. To platí při použití:
>- **Nástroj pro kopírování dat** můžete vytvořit kanál kopírování.
>- **Uživatelské rozhraní služby Data Factory** otestovat připojení a procházení složek během vytváření obsahu.

Ve službě Azure Data Factory není nutné zadávat jakékoli vlastnosti kromě obecné informace o Data Lake Store v propojené službě.

**Příklad:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Chcete-li kopírovat data do a z Azure Data Lake Store, nastavte `type` vlastnosti datové sady na **AzureDataLakeStoreFile**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **AzureDataLakeStoreFile** |Ano |
| folderPath | Cesta ke složce v Data Lake Store. Filtr zástupných znaků není podporován. Pokud není zadán, odkazuje na kořen. Příklad: rootfolder/podsložka / |Ne |
| fileName | **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu a **preserveHierarchy** není uveden v aktivita jímky aktivity kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*Data. [id aktivity spustit GUID]. [Identifikátor GUID Pokud FlattenHierarchy]. [formátu, je-li nakonfigurovat]. [Pokud nakonfigurované komprese]* ". Například "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Pokud zkopírujete z tabulkové zdroje místo názvu tabulky dotazů, vzor názvů je "*[název tabulky]. [ formát]. [Pokud nakonfigurované komprese]* ". Například "MyTable.csv". |Ne |
| formát | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |


>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s konkrétní název, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků. 

**Příklad:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "myfile.csv.gz",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastnosti podporované službou Azure Data Lake Store zdroje a jímky.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako zdroj

Ke zkopírování dat z Data Lake Store, nastavte typ zdroje v aktivitě kopírování do **AzureDataLakeStoreSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | `type` Vlastnost zdroje aktivity kopírování musí být nastavena na: **AzureDataLakeStoreSource**. |Ano |
| rekurzivní | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že `recursive` je nastavena na hodnotu true a jímku je souborové úložiště, prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou: **true** (výchozí) a **false**. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako jímka

Ke zkopírování dat do Data Lake Store, nastavte typ jímky v aktivitě kopírování do **AzureDataLakeStoreSink**. Následující vlastnosti jsou podporovány v **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | `type` Vlastnost jímky aktivity kopírování musí být nastavena na: **AzureDataLakeStoreSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objekt blob, je název souboru sloučeného se zadaným názvem. Název souboru, jinak je automaticky generována. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="examples-of-behavior-of-the-copy-operation"></a>Příklady chování operace kopírování

Tato část popisuje výsledné chování operace kopírování pro různé kombinace `recursive` a `copyBehavior` hodnoty.

| rekurzivní | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + 5 souboru obsahu jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaného souboru. Automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky, aktivita kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
