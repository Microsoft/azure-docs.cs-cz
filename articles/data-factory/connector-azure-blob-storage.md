---
title: Kopírování a transformace dat v úložišti objektů BLOB v Azure
description: Naučte se, jak kopírovat data do a z úložiště objektů BLOB a transformovat data v úložišti objektů BLOB pomocí Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2020
ms.openlocfilehash: f245254c85cbf9223ab5dda60799c6b7ce72f11c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120209"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopírování a transformace dat v úložišti objektů BLOB v Azure pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuální verze](connector-azure-blob-storage.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do úložiště objektů BLOB v Azure a jak transformovat data v úložišti objektů BLOB v Azure. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Blob Connector se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Pro aktivitu kopírování podporuje tento konektor úložiště objektů BLOB:

- Kopírování objektů BLOB mezi účty Azure storage pro obecné účely a vrstvami hot a cool blob storage. 
- Kopírování objektů BLOB s použitím klíče účtu služby sdíleného přístupového podpisu, identity objektu zabezpečení nebo spravované služby pro ověřování prostředků Azure.
- Kopírování objektů blob z bloku, doplňovací nebo objekty BLOB stránky a kopírování dat do jenom objekty BLOB bloku.
- Kopírování objektů BLOB, jako je analýza kódu nebo generování objektů BLOB s [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).
- [Při kopírování zachovat metadata souboru](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** při Azure Storage nastavení brány firewall a chcete použít prostředí Azure Integration runtime pro připojení k BLOB Storage, musíte použít [spravované ověřování identity](#managed-identity).

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní do úložiště objektů Blob.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor Azure Blob podporuje následující typy ověřování, přečtěte si odpovídající část na podrobnosti:

- [Ověření pomocí klíče účtu](#account-key-authentication)
- [Ověřování podpisu sdíleného přístupu](#shared-access-signature-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravovaných identit pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Když použijete základnu k načtení dat do SQL Data Warehouse, pokud je zdrojové nebo pracovní úložiště objektů BLOB nakonfigurované pomocí Virtual Networkho koncového bodu, musíte použít spravované ověřování identity podle požadavků v rámci základny a používat místně hostované Integration Runtime s verzí. 3,18 nebo vyšší. Viz část [spravované ověřování identity](#managed-identity) s dalšími požadavky na konfiguraci.

>[!NOTE]
>HDInsights a Azure Machine Learning aktivity podporují jenom ověřování klíčů účtu Azure Blob Storage.

### <a name="account-key-authentication"></a>Ověření pomocí klíče účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **službě Azure BLOB Storage** (doporučeno) nebo **AzureStorage** (viz poznámky níže). |Ano |
| connectionString | Zadejte informace potřebné pro připojení k úložišti pro vlastnost připojovací řetězec. <br/> Můžete také umístit klíč účtu do Azure Key Vault a načíst `accountKey` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Sekundární koncový bod služby BLOB Service není podporován při použití ověřování klíčů účtu. Můžete použít jiné typy ověřování.

>[!NOTE]
>Pokud jste používali "AzureStorage" typu propojené služby, je stále podporovány jako-se, když se doporučují používat tento nový "službě Azure BLOB Storage" propojené služby typu od dané chvíle.

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: klíč účtu úložiště v Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Ověřování podpisu sdíleného přístupu

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Sdílený přístupový podpis můžete použít k udělení, že klient omezená oprávnění k objektům v účtu úložiště pro určitou dobu. Nemusíte sdílet přístupové klíče vašeho účtu. Sdílený přístupový podpis je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, musí klient pouze a zajistěte tak předání sdílený přístupový podpis odpovídajícího konstruktoru nebo metody. Další informace o sdílených přístupových podpisů najdete v tématu [sdílené přístupové podpisy: vysvětlení modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory teď podporuje obě **služby sdílené přístupové podpisy** a **účet sdílené přístupové podpisy**. Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).
>- Cesta ke složce v konfiguraci vyšší datovou sadu, je absolutní cesta od úrovni kontejneru. Musíte nakonfigurovat jednu zarovnaná s cestou v váš identifikátor URI SAS.

> [!TIP]
> Vygenerovat sdílený přístupový podpis služby pro účet úložiště, spusťte následující příkazy Powershellu. Nahraďte zástupné symboly a udělit příslušná oprávnění.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Použít sdílený přístupový podpis ověřování, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **službě Azure BLOB Storage** (doporučeno) nebo **AzureStorage** (viz poznámky níže). |Ano |
| sasUri | Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům úložiště, jako je například objekt BLOB nebo kontejner. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Do Azure Key Vault můžete také vložit token SAS a využít tak automatické otočení a odebrat část tokenu. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali "AzureStorage" typu propojené služby, je stále podporovány jako-se, když se doporučují používat tento nový "službě Azure BLOB Storage" propojené služby typu od dané chvíle.

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: klíč účtu úložiště v Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Při vytváření identifikátor URI sdíleného přístupového podpisu, zvažte následující body:

- Nastavit oprávnění odpovídající čtení a zápisu u objektů podle použití propojené služby (čtení, zápisu, čtení a zápis) ve službě data factory.
- Nastavte **čas vypršení platnosti** odpovídajícím způsobem. Ujistěte se, že přístup k objektům úložiště platnost pasu nevyprší do aktivního období kanálu.
- Identifikátor URI by měl být vytvořen na správném kontejneru nebo objektu BLOB na základě potřeby. Identifikátor URI sdíleného přístupového podpisu do objektu blob umožňuje služby Data Factory pro přístup k této konkrétní objekt blob. Identifikátor URI sdíleného přístupového podpisu kontejneru úložiště objektů BLOB umožňuje Data Factory iterovat objekty BLOB v tomto kontejneru. Poskytnout přístup k více nebo méně objektů později nebo aktualizujte URI sdíleného přístupového podpisu, nezapomeňte aktualizovat propojenou službu s nový identifikátor URI.

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Ověřování instančních objektů Azure Storage, najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md).

Pokud chcete používat ověřování instančních objektů, postupujte takto:

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte hlavní správné oprávnění služby ve službě Azure Blob storage. Odkazovat na [Správa přístupových práv k datům služby Azure Storage pomocí RBAC](../storage/common/storage-auth-aad-rbac.md) s dalšími podrobnostmi na rolích.

    - **Jako zdroj**, do Access control (IAM), udělit alespoň **čtecí modul dat pro úložiště objektů Blob** role.
    - **Jako jímku**, do Access control (IAM), udělit alespoň **Přispěvatel dat objektu Blob úložiště** role.

Tyto vlastnosti jsou podporovány pro Azure Blob propojené služby storage:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **službě Azure BLOB Storage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob storage s vzor `https://<accountName>.blob.core.windows.net/`. |Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Služba ověřování instančních objektů je podporována pouze ve "službě Azure BLOB Storage" typu propojené služby, ale ne předchozí "AzureStorage" typu propojené služby.

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

Objekt pro vytváření dat můžou být spojené s [spravované identity pro prostředky Azure](data-factory-service-identity.md), která představuje této konkrétní datové továrně. Tuto spravovanou identitu můžete přímo použít pro ověřování BLOB Storage podobně jako při použití vlastního instančního objektu. To umožňuje tento určený objekt pro vytváření pro přístup a kopírování dat z/do úložiště objektů Blob.

Informace o ověřování [přístupu k Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md) pro Azure Storage ověřování obecně. Použití spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků:

1. [Načtěte informace o identitě spravované identity Data Factory](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty **ID spravovaného objektu identity** generovaného společně s vaší továrnou.

2. Udělení oprávnění správné spravovanou identitu ve službě Azure Blob storage. Odkazovat na [Správa přístupových práv k datům služby Azure Storage pomocí RBAC](../storage/common/storage-auth-aad-rbac.md) s dalšími podrobnostmi na rolích.

    - **Jako zdroj**, do Access control (IAM), udělit alespoň **čtecí modul dat pro úložiště objektů Blob** role.
    - **Jako jímku**, do Access control (IAM), udělit alespoň **Přispěvatel dat objektu Blob úložiště** role.

>[!IMPORTANT]
>Pokud použijete základnu k načtení dat z objektu BLOB (jako zdroj nebo jako fázování) do SQL Data Warehouse, při použití spravovaného ověřování identity pro objekt BLOB se ujistěte, že jste provedli kroky 1 a 2 v [těchto pokynech](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) : 1) zaregistrovat SQL Database server pomocí Azure Active Directory (Azure AD) a 2) přiřaďte k vašemu serveru SQL Database roli Přispěvatel dat objektu BLOB služby Storage. zbytek se zpracovává pomocí Data Factory. Pokud je úložiště objektů BLOB nakonfigurované pomocí koncového bodu Azure Virtual Network, musíte použít základ ověřování identity, jak je požadováno v rámci základny.

Tyto vlastnosti jsou podporovány pro Azure Blob propojené služby storage:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **službě Azure BLOB Storage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob storage s vzor `https://<accountName>.blob.core.windows.net/`. |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

> [!NOTE]
> Spravované identity pro prostředky Azure, které ověřování je podporován pouze ve službě Azure BLOB "Storage" typu propojené služby, ale ne předchozí "AzureStorage" typ propojené služby. 

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro Azure BLOB v části nastavení `location` v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type umístění v datové sadě musí být nastavená na **AzureBlobStorageLocation**. | Ano      |
| kontejner  | Kontejner objektů BLOB.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným kontejnerem + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností objektu Blob úložiště zdroje a jímky podporované.

### <a name="blob-storage-as-a-source-type"></a>Úložiště objektů BLOB jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro Azure BLOB v části nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureBlobStorageReadSettings**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného kontejneru + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v sadě dat není zadaný `fileName` |
| modifiedDatetimeStart    | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro formát textu Parquet/s oddělovači, který je uveden v další části, je zdroj aktivity kopírování typu **BlobSource** stále podporován tak, jak je z důvodu zpětné kompatibility. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Úložiště objektů BLOB jako typ jímky

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro Azure BLOB v části nastavení `storeSettings` v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureBlobStorageWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud není zadán název souboru nebo objekt blob, je název souboru sloučeného se zadaným názvem. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (empty, use default) | false | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (empty, use default) | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | false | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File5 |
| true |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. |
| false |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | cílové složky složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | cílové složky složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. automaticky generovaným názvem File1<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Při kopírování souborů z Amazon S3/Azure blob/Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2/Azure Blob se můžete rozhodnout zachovat metadata souboru spolu s daty. Další informace o [zachování metadat](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat soubory z Azure Blob Storage ve formátu JSON, Avro, text s oddělovači nebo Parquet. Další informace naleznete v tématu [transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v funkci toku dat mapování.

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v Azure Blob Storage. Na kartě **Možnosti zdrojového kódu** můžete spravovat, jak se soubory čtou. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesta zástupného znaku:** Pomocí vzoru se zástupnými znaky nastavíte ADF, aby prochází každou shodnou složku a soubor v jediné zdrojové transformaci. Toto je efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Přidejte více vzorů pro porovnávání se zástupnými znaky s symbolem +, který se zobrazí při najetí myší na stávající zástupný vzor.

Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

Příklady zástupných znaků:

* ```*``` představuje libovolnou sadu znaků.
* ```**``` představuje rekurzivní vnořování adresářů.
* ```?``` nahrazuje jeden znak.
* ```[]``` odpovídá jednomu nebo více znakům v závorkách.

* ```/data/sales/**/*.csv``` získá všechny soubory CSV pod/data/Sales
* ```/data/sales/20??/**``` získá všechny soubory ve dvacátém století.
* ```/data/sales/2004/*/12/[XY]1?.csv``` získá všechny soubory CSV v 2004 v prosinci začínající znakem X nebo Y a číslem se dvěma číslicemi.

**Kořenová cesta oddílu:** Pokud jste ve zdroji souborů nastavili dělené složky s formátem ```key=value``` (například Year = 2019), můžete přiřadit nejvyšší úroveň stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

Nejdřív nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělené do oddílů, a soubory listů, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Nastavení kořenové cesty oddílu použijte k definování toho, co je nejvyšší úroveň struktury složek. Když zobrazíte obsah vašich dat prostřednictvím náhledu dat, uvidíte, že tento ADF bude přidávat vyřešené oddíly, které se nacházejí v jednotlivých úrovních vaší složky.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Zobrazit kořenovou cestu oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cest ke zpracování. Najeďte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat vyberte, že nechcete nic dělat se zdrojovým souborem, odstraňte zdrojový soubor nebo přesuňte zdrojový soubor. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do následujícího následného zpracování, vyberte nejprve možnost přesunout pro operaci soubor. Potom nastavte adresář "z". Pokud pro cestu nepoužíváte žádné zástupné znaky, pak bude mít nastavení "od" stejnou složku jako vaše zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, vaše syntaxe bude vypadat následovně:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "to" jako

```/backup/priorSales```

V tomto případě se všechny soubory, které se nacházely v/data/Sales, přesunuly do/backup/priorSales.

> [!NOTE]
> Operace se soubory běží jenom při spuštění toku dat ze spuštění kanálu (ladění kanálu nebo spuštění spuštění), které používá aktivitu spustit tok dat v kanálu. Operace *se* soubory neběží v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Můžete filtrovat, které soubory se mají zpracovat, zadáním rozsahu data při jejich poslední úpravě. Všechna data jsou v čase UTC. 

### <a name="sink-properties"></a>Vlastnosti jímky

V transformaci jímky můžete zapisovat do kontejneru nebo složky v Azure Blob Storage. Karta **Nastavení** vám umožní spravovat způsob zápisu souborů.

![možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymažte složku:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou:
   * **Výchozí**: umožňuje Sparku pojmenovat soubory založené na výchozím nastavení části.
   * **Vzor**: zadejte vzor, který vytvoří výčet výstupních souborů na oddíl. Například **výpůjčky [n]. csv** vytvoří loans1. csv, loans2. csv a tak dále.
   * **Na oddíl**: zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem k kontejneru DataSet, nikoli k cílové složce.
   * **Výstup do jednoho souboru**: sloučí výstupní soubory rozdělené do jednoho pojmenovaného souboru. Cesta je relativní vzhledem ke složce DataSet. Počítejte s tím, že operace te Merge může být na základě velikosti uzlu neúspěšná. Tato možnost se pro velké datové sady nedoporučuje.

**Všechna citace:** Určuje, jestli se mají všechny hodnoty uzavřít do uvozovek.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Navrhnete použití nového modelu uvedeného výše v předchozích částech a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **AzureBlob**. |Ano |
| folderPath | Cesta k kontejner a složku v úložišti objektů blob. <br/><br/>Pro cestu s názvem kontejneru je podporován filtr zástupných znaků. Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br/><br/>Příklady: myblobcontainer/myblobfolder/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano pro aktivitu kopírování a vyhledávání, ne pro aktivitě GetMetadata |
| fileName | **Název nebo zástupný filtr** pro objekty BLOB v rámci zadaného "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny objekty BLOB ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu a v jímky aktivity není zadán parametr **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název objektu BLOB s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky místo dotazu, vzor názvu je " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| formát | Pokud chcete zkopírovat soubory, jako je mezi souborové úložiště (binární kopie), přejděte v části formát v definici vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, a **ParquetFormat**. Nastavte **typ** vlastnosti v části **formátu** na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formátu JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formát Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format), a [formát Parquet ](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny objekty BLOB ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden objekt blob se zadaným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu objektů BLOB ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků. 

**Příklad:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **BlobSource**. |Ano |
| recursive | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **BlobSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud není zadán název souboru nebo objekt blob, je název souboru sloučeného se zadaným názvem. V opačném případě je automaticky generovaným názvem souboru. | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
