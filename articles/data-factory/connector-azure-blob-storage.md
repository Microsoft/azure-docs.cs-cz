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
ms.date: 10/12/2020
ms.openlocfilehash: 7aef08f4ba1948c32fe83a2d0064a21459c003b4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148959"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopírování a transformace dat v úložišti objektů BLOB v Azure pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuální verze](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do úložiště objektů BLOB v Azure. Popisuje také způsob použití aktivity toku dat k transformaci dat v úložišti objektů BLOB v Azure. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

>[!TIP]
>Další informace o scénáři migrace pro data Lake nebo datový sklad najdete v tématu [použití Azure Data Factory k migraci dat z datového Lake nebo datového skladu do Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Blob Storage se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Pro aktivitu kopírování podporuje tento konektor úložiště objektů BLOB:

- Kopírování objektů blob do a z účtu Azure Storage pro obecné účely a horké nebo studené služby Blob Storage. 
- Kopírování objektů BLOB pomocí klíče účtu, sdíleného přístupového podpisu služby (SAS), instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Kopírování objektů BLOB z bloků, přidávání nebo objektů blob stránky a kopírování dat pouze do objektů blob bloku.
- Kopírování objektů blob, jako je, nebo analýza nebo generování objektů BLOB s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Při kopírování se zachovává metadata souboru](#preserving-metadata-during-copy).

>[!IMPORTANT]
>Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** v Azure Storage nastavení brány firewall a chcete použít prostředí Azure Integration runtime pro připojení k úložišti objektů blob, musíte použít [spravované ověřování identity](#managed-identity).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factorych entit specifických pro úložiště objektů BLOB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Tato konektorová služba BLOB Storage podporuje následující typy ověřování. Podrobnosti najdete v příslušných oddílech.

- [Ověřování klíčů účtu](#account-key-authentication)
- [Ověřování sdíleného přístupového podpisu](#shared-access-signature-authentication)
- [Ověřování instančního objektu](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Pokud k načtení dat do služby Azure synapse Analytics (dříve SQL Data Warehouse) používáte základnu, pokud je vaše zdrojové nebo pracovní úložiště objektů BLOB nakonfigurované s koncovým bodem Azure Virtual Network, musíte použít spravované ověřování identity podle požadavků základu. Je také nutné použít modul runtime integrace v místním prostředí s verzí 3,18 nebo novější. Další požadavky na konfiguraci najdete v části věnované [ověřování spravované identity](#managed-identity) .

>[!NOTE]
>Azure HDInsight a Azure Machine Learning aktivity podporují jenom ověřování, které používá klíče účtu úložiště Azure Blob.

### <a name="account-key-authentication"></a>Ověřování klíčů účtu

Data Factory podporuje následující vlastnosti pro ověřování klíčů účtu úložiště:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureBlobStorage** (navrhovaná) nebo **AzureStorage** (viz následující poznámky). |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost **ConnectionString** . <br/> Klíč účtu můžete také vložit do Azure Key Vault a získat `accountKey` konfiguraci z připojovacího řetězce. Další informace najdete v následujících ukázkách a [přihlašovací údaje úložiště v článku Azure Key Vault](store-credentials-in-key-vault.md) . |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud je vaše úložiště dat v privátní síti). Pokud tato vlastnost není zadaná, služba použije výchozí prostředí Azure Integration runtime. |Ne |

>[!NOTE]
>Sekundární koncový bod Blob service není podporován, pokud používáte ověřování klíčů účtu. Můžete použít jiné typy ověřování.

>[!NOTE]
>Pokud používáte propojenou službu typu "AzureStorage", je stále podporovaná, jak je to. Doporučujeme ale použít nový typ propojené služby "AzureBlobStorage".

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

**Příklad: Uložte klíč účtu do Azure Key Vault**

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

### <a name="shared-access-signature-authentication"></a>Ověřování sdíleného přístupového podpisu

Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Pomocí sdíleného přístupového podpisu můžete pro určitý čas udělit klientovi omezená oprávnění k objektům ve vašem účtu úložiště. 

Nemusíte sdílet přístupové klíče k účtu. Sdílený přístupový podpis je identifikátor URI, který v parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště se sdíleným přístupovým podpisem, klient musí předat sdílený přístupový podpis pouze příslušnému konstruktoru nebo metodě. 

Další informace o sdílených přístupových podpisech najdete v tématu [signatury sdíleného přístupu: Principy modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory teď podporuje jak *signatury sdíleného přístupu služby* , tak *signatury sdíleného přístupu účtu*. Další informace o sdílených přístupových podpisech najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů](../storage/common/storage-sas-overview.md).
>- V pozdějších konfiguracích datových sad je cesta ke složce absolutní cesta od úrovně kontejneru. Je nutné nakonfigurovat jednu zarovnaná s cestou v identifikátoru URI SAS.

Data Factory podporuje následující vlastnosti pro použití ověřování pomocí sdíleného přístupového podpisu:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureBlobStorage** (navrhovaný) nebo **AzureStorage** (viz následující poznámka). |Ano |
| sasUri | Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům úložiště, jako je například objekt BLOB nebo kontejner. <br/>Označte toto pole jako **SecureString** a bezpečně ho uložte do Data Factory. Můžete také zadat token SAS v Azure Key Vault, aby se použilo automatické otočení a odebrala se část tokenu. Další informace najdete v následujících ukázkách a [přihlašovací údaje uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud je vaše úložiště dat v privátní síti). Pokud tato vlastnost není zadaná, služba použije výchozí prostředí Azure Integration runtime. |Ne |

>[!NOTE]
>Pokud používáte propojenou službu typu "AzureStorage", je stále podporovaná, jak je to. Doporučujeme ale použít nový typ propojené služby "AzureBlobStorage".

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: Uložte klíč účtu do Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Při vytváření identifikátoru URI sdíleného přístupového podpisu Vezměte v úvahu následující body:

- Nastavte vhodná oprávnění ke čtení a zápisu pro objekty na základě způsobu, jakým se ve vaší datové továrně používá propojená služba (čtení, zápis, čtení a zápis).
- Nastavte odpovídající **čas vypršení platnosti** . Ujistěte se, že přístup k objektům úložiště nevyprší v aktivním období kanálu.
- Identifikátor URI by měl být vytvořen na správném kontejneru nebo objektu BLOB na základě potřeby. Identifikátor URI sdíleného přístupového podpisu k objektu BLOB umožňuje Data Factory přistupovat k tomuto konkrétnímu objektu BLOB. Identifikátor URI sdíleného přístupového podpisu kontejneru úložiště objektů BLOB umožňuje Data Factory iterovat objekty BLOB v tomto kontejneru. Pokud chcete později poskytnout přístup k více nebo méně objektům nebo aktualizovat identifikátor URI sdíleného přístupového podpisu, nezapomeňte aktualizovat propojenou službu pomocí nového identifikátoru URI.

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Obecné informace o Azure Storage ověřování instančního objektu najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md).

Chcete-li použít ověřování instančního objektu, postupujte takto:

1. Pomocí [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si tyto hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění ve službě Azure Blob Storage. Další informace o rolích najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Jako zdroj**v **řízení přístupu (IAM)** udělte aspoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**udělte v **řízení přístupu (IAM)** aspoň roli **Přispěvatel dat objektu BLOB úložiště** .

Tyto vlastnosti jsou podporované pro propojenou službu Azure Blob Storage:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureBlobStorage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob Storage se vzorem `https://<accountName>.blob.core.windows.net/` . |Ano |
| accountKind | Zadejte druh účtu úložiště. Povolené hodnoty jsou: **Storage** (pro obecné účely V1), **StorageV2** (obecné účely v2), **BlobStorage**nebo **BlockBlobStorage**. <br/> Pokud používáte propojenou službu Azure BLOB v toku dat, není podporována spravovaná identita nebo ověřování instančního objektu, pokud je typ účtu prázdný nebo "úložiště". Zadejte správný druh účtu, zvolte jiné ověřování nebo upgradujte svůj účet úložiště na hodnotu pro obecné účely v2. |Ne |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Data Factory nebo [odkazujte na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší do pravého horního rohu Azure Portal. | Ano |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, ve kterém je vaše aplikace Azure Active Directory zaregistrovaná. <br/> Povolené hodnoty jsou **AzurePublic**, **AzureChina**, **AzureUsGovernment**a **AzureGermany**. Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | Ne |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud je vaše úložiště dat v privátní síti). Pokud tato vlastnost není zadaná, služba použije výchozí prostředí Azure Integration runtime. |Ne |

>[!NOTE]
>Pokud váš účet BLOB povolí [obnovitelné odstranění](../storage/blobs/soft-delete-blob-overview.md), ověřování instančního objektu se v toku dat nepodporuje.

>[!NOTE]
>Ověřování instančního objektu je podporováno pouze propojenou službou typu "AzureBlobStorage", nikoli předchozím propojenou službou typu "AzureStorage".

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě prostředků Azure](data-factory-service-identity.md), která představuje tento konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete přímo použít pro ověřování úložiště objektů blob, které se podobá používání vlastního instančního objektu. Umožňuje této určené továrně přistupovat a kopírovat data z nebo do úložiště objektů BLOB.

Obecné informace o ověřování Azure Storage najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md). Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků:

1. [Načtěte data Factory informace o spravované identitě](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty ID spravovaného objektu identity generovaného společně s vaší továrnou.

2. Udělte oprávnění spravované identity v úložišti objektů BLOB v Azure. Další informace o rolích najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Jako zdroj**v **řízení přístupu (IAM)** udělte aspoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**udělte v **řízení přístupu (IAM)** aspoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!IMPORTANT]
>Pokud použijete základnu k načtení dat z úložiště objektů BLOB (jako zdroj nebo jako pracovní) do služby Azure synapse Analytics (dříve SQL Data Warehouse), při použití spravovaného ověřování identity pro úložiště objektů BLOB se ujistěte, že jste provedli kroky 1 a 2 v [těchto pokynech](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Tyto kroky zaregistrují váš server ve službě Azure AD a přiřadí roli Přispěvatel dat objektů BLOB úložiště k vašemu serveru. Data Factory zpracuje zbytek. Pokud jste nakonfigurovali úložiště objektů BLOB s koncovým bodem Azure Virtual Network a chcete k načtení dat z něj použít základnu, musíte použít spravované ověřování identity podle požadavků základu.

Tyto vlastnosti jsou podporované pro propojenou službu Azure Blob Storage:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureBlobStorage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob Storage se vzorem `https://<accountName>.blob.core.windows.net/` . |Ano |
| accountKind | Zadejte druh účtu úložiště. Povolené hodnoty jsou: **Storage** (pro obecné účely V1), **StorageV2** (obecné účely v2), **BlobStorage**nebo **BlockBlobStorage**. <br/> Pokud používáte propojenou službu Azure BLOB v toku dat, není podporována spravovaná identita nebo ověřování instančního objektu, pokud je typ účtu prázdný nebo "úložiště". Zadejte správný druh účtu, zvolte jiné ověřování nebo upgradujte svůj účet úložiště na hodnotu pro obecné účely v2. |Ne |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud je vaše úložiště dat v privátní síti). Pokud tato vlastnost není zadaná, služba použije výchozí prostředí Azure Integration runtime. |Ne |

> [!NOTE]
> Pokud váš účet BLOB povoluje [obnovitelné odstranění](../storage/blobs/soft-delete-blob-overview.md), spravované ověřování identity se v toku dat nepodporuje.

> [!NOTE]
> Spravované identity pro ověřování prostředků Azure jsou podporované jenom propojenou službou typu "AzureBlobStorage", a ne předchozím propojenou službou typu "AzureStorage".

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

V části `location` nastavení v datové sadě založené na formátu jsou podporovány následující vlastnosti služby Azure Blob Storage:

| Vlastnost   | Popis                                                  | Povinné |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Vlastnost **Type** umístění v datové sadě musí být nastavená na **AzureBlobStorageLocation**. | Ano      |
| kontejner  | Kontejner objektů BLOB.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru. Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a určete v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v daném kontejneru a cestě ke složce. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho v nastavení zdroje aktivity. | Ne       |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které podporuje zdroj a jímka úložiště objektů BLOB.

### <a name="blob-storage-as-a-source-type"></a>Úložiště objektů BLOB jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

V `storeSettings` nastaveních ve zdrojovém kopírování založeném na formátu jsou podporovány následující vlastnosti pro úložiště objektů BLOB v Azure:

| Vlastnost                 | Popis                                                  | Povinné                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Vlastnost **Type** v poli `storeSettings` musí být nastavená na **AzureBlobStorageReadSettings**. | Ano                                           |
| ***Vyhledejte soubory ke zkopírování:*** |  |  |
| MOŽNOST 1: statická cesta<br> | Kopírování ze zadaného kontejneru nebo složky/cesty k souboru v datové sadě. Pokud chcete kopírovat všechny objekty BLOB z kontejneru nebo složky, zadejte také `wildcardFileName` jako `*` . |  |
| MOŽNOST 2: Předpona objektu BLOB<br>-prefix | Předpona pro název objektu BLOB v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových objektů BLOB Jsou vybrány objekty blob, jejichž názvy začínají na `container_in_dataset/this_prefix` . Využívá filtr na straně služby pro úložiště objektů blob, což poskytuje lepší výkon než filtr zástupných znaků. | Ne                                                          |
| MOŽNOST 3: zástupný znak<br>- wildcardFolderPath | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` k Escape, jestli má název složky zástupný znak nebo tento řídicí znak uvnitř. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| MOŽNOST 3: zástupný znak<br>- wildcardFileName | Název souboru se zástupnými znaky v daném kontejneru a cestě ke složce (nebo cesta ke složce se zástupnými znaky) pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` k ukončení, jestli má název složky zástupný znak nebo tento řídicí znak uvnitř. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano |
| MOŽNOST 4: seznam souborů<br>- fileListPath | Určuje, že se má zkopírovat daná sada souborů. Najeďte na textový soubor, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek, což je relativní cesta k cestě nakonfigurované v datové sadě.<br/>Při použití této možnosti nezadávejte název souboru v datové sadě. Další příklady najdete v [příkladech seznamu souborů](#file-list-examples). |Ne |
| ***Další nastavení:*** |  | |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je **rekurzivní** nastavení nastaveno na **hodnotu true** a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. <br>Povolené hodnoty jsou **true** (výchozí) a **false**.<br>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . |Ne |
| deleteFilesAfterCompletion | Uvádí, zda budou binární soubory po úspěšném přesunutí do cílového úložiště odstraněny ze zdrojového úložiště. Odstranění souboru je vázané na soubor, takže když aktivita kopírování selže, uvidíte, že některé soubory se už zkopírovaly do cílového umístění a odstranily ze zdroje, zatímco ostatní jsou pořád ve zdrojovém úložišti. <br/>Tato vlastnost je platná pouze ve scénáři kopírování binárních souborů. Výchozí hodnota: false. |Ne |
| modifiedDatetimeStart    | Soubory jsou filtrovány na základě atributu: Naposledy změněno. <br>Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít **hodnotu null**, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je **null**, budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je **null**, soubory, jejichž atribut Last Modified je menší než hodnota DateTime, se vybere.<br/>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| enablePartitionDiscovery | U souborů, které jsou rozdělené na oddíly, určete, jestli se mají analyzovat oddíly z cesty k souboru, a přidejte je jako další zdrojové sloupce.<br/>Povolené hodnoty jsou **false** (výchozí) a **true**. | Ne                                            |
| partitionRootPath | Pokud je povoleno zjišťování oddílů, zadejte absolutní kořenovou cestu, aby bylo možné číst rozdělené složky jako sloupce dat.<br/><br/>Pokud není zadaný, ve výchozím nastavení<br/>– Pokud použijete cestu k souboru v datové sadě nebo v seznamu souborů na zdroji, je kořenová cesta oddílu cestou nakonfigurovanou v datové sadě.<br/>– Když použijete filtr složky se zástupnými znaky, kořenová cesta oddílu je dílčí cesta před prvním zástupným znakem.<br/>– Při použití předpony je kořenová cesta oddílu podcestou před poslední znak "/". <br/><br/>Předpokládejme například, že nakonfigurujete cestu v datové sadě jako kořen/složka/rok = 2020/měsíc = 08/Day = 27:<br/>– Pokud zadáte kořenovou cestu oddílu jako "root/složka/Year = 2020", aktivita kopírování vygeneruje další dva sloupce `month` a `day` hodnoty "08" a "27" společně se sloupci uvnitř souborů.<br/>-Pokud není zadána kořenová cesta oddílu, nebude vygenerován žádný sloupec navíc. | Ne                                            |
| maxConcurrentConnections | Počet souběžných připojení k úložišti. Určete pouze v případě, že chcete omezit souběžná připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> V případě textového formátu Parquet/s oddělovači je typ **BlobSource** pro zdroj aktivity kopírování, který je uveden v další části, stále podporován, jak je z důvodu zpětné kompatibility. Doporučujeme, abyste nový model používali, dokud uživatelské rozhraní pro vytváření Data Factory nepřešlo na vygenerování těchto nových typů.

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

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure Blob Storage v `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Povinné |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Vlastnost **Type** v poli `storeSettings` musí být nastavená na **AzureBlobStorageWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, sloučený název souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | Ne       |
| blockSizeInMB | Zadejte velikost bloku (v megabajtech), která se používá k zápisu dat pro objekty blob bloku. Přečtěte si další informace o objektech [blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Povolená hodnota je *mezi 4 MB a 100 MB*. <br/>Ve výchozím nastavení Data Factory automaticky určuje velikost bloku na základě typu zdrojového úložiště a dat. Pro nebinární kopírování do úložiště objektů BLOB je výchozí velikost bloku 100 MB, takže se může vejít do velikosti (nejvíce) 4,95 TB dat. Nemusí být optimální, pokud vaše data nejsou velká, zejména při použití modulu Integration runtime v místním prostředí s nedostatečnými síťovými připojeními, které mají za následek časový limit operace nebo problémy s výkonem. Velikost bloku můžete explicitně zadat, přičemž zajistěte, aby `blockSizeInMB*50000` byla data ukládána dostatečně veliké. V opačném případě se spuštění aktivity kopírování nezdaří. | Ne |
| maxConcurrentConnections | Počet souběžných připojení k úložišti. Určete pouze v případě, že chcete omezit souběžná připojení k úložišti dat. | Ne       |

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

| folderPath | fileName | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (prázdné, použít výchozí) | false (nepravda) | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (prázdné, použít výchozí) | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false (nepravda) | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Příklady seznamů souborů

Tato část popisuje výsledné chování při použití cesty seznamu souborů ve zdroji aktivity kopírování.

Předpokládejme, že máte následující strukturu zdrojové složky a chcete soubory zkopírovat tučně:

| Ukázka zdrojové struktury                                      | Obsah v FileListToCopy.txt                             | Konfigurace Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mezipaměť<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **V datové sadě:**<br>Vnitřního `container`<br>– Cesta ke složce: `FolderA`<br><br>**Ve zdroji aktivity kopírování:**<br>– Cesta k seznamu souborů: `container/Metadata/FileListToCopy.txt` <br><br>Cesta k seznamu souborů odkazuje na textový soubor ve stejném úložišti dat, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek s relativní cestou k cestě, která je nakonfigurovaná v datové sadě. |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivních a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace **rekurzivních** a **copyBehavior** hodnot.

| zahrnout | copyBehavior | Struktura zdrojové složky | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 je vytvořena s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro File5 |
| true |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 je vytvořena s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah souboru soubor1 + soubor2 + file3 + file4 + File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 je vytvořena s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 je vytvořena s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílová složka Složku1 je vytvořena s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="preserving-metadata-during-copy"></a>Zachování metadat během kopírování

Když kopírujete soubory z Amazon S3, Azure Blob Storage nebo Azure Data Lake Storage Gen2 do služby Azure Data Lake Storage Gen2 nebo úložiště objektů BLOB v Azure, můžete se rozhodnout zachovat metadata souboru spolu s daty. Další informace o [zachování metadat](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v datových tocích s mapováním můžete číst a zapisovat soubory z úložiště objektů BLOB v Azure v následujících formátech:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties)
* [Rozdíl](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Nastavení konkrétního formátu se nachází v dokumentaci pro tento formát. Další informace najdete v tématu [transformace zdroje v části mapování toku dat](data-flow-source.md) a [transformace jímky při mapování toku dat](data-flow-sink.md).

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v úložišti objektů BLOB v Azure. Pomocí karty **Možnosti zdroje** můžete spravovat způsob čtení souborů. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesty zástupných znaků:** Pomocí vzoru se zástupnými znaky dáte pokyn, aby Data Factory projít každou shodnou složku a soubor v jediné transformaci zdroje. Toto je efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Přidejte více vzorů pro porovnávání se zástupnými znaky s znaménkem plus, které se zobrazí při najetí myší na stávající zástupný vzor.

Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

Příklady zástupných znaků:

* ```*``` Představuje libovolnou sadu znaků.
* ```**``` Představuje rekurzivní vnořování adresářů.
* ```?``` Nahradí jeden znak.
* ```[]``` Odpovídá jednomu nebo více znakům v závorkách.

* ```/data/sales/**/*.csv``` Načte všechny soubory. csv pod/data/Sales.
* ```/data/sales/20??/**/``` Načte všechny soubory ve dvacátém století.
* ```/data/sales/*/*/*.csv``` Získá soubory. csv dvě úrovně v/data/Sales.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Načte všechny soubory. CSV v prosinci 2004 od X nebo Y s předponou a číslem se dvěma číslicemi.

**Kořenová cesta oddílu:** Pokud jste ve zdroji souborů nastavili dělené složky s ```key=value``` formátem (například `year=2019` ), můžete přiřadit nejvyšší úroveň stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

Nejdřív nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělené do oddílů, a soubory listů, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Nastavení **kořenové cesty oddílu** použijte k definování toho, co je nejvyšší úroveň struktury složek. Když zobrazíte obsah vašich dat pomocí náhledu dat, uvidíte, že Data Factory přidá vyřešené oddíly, které se nacházejí v jednotlivých úrovních vaší složky.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Zobrazit kořenovou cestu oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cest ke zpracování. Najeďte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat vyberte, že nechcete nic dělat se zdrojovým souborem, odstraňte zdrojový soubor nebo přesuňte zdrojový soubor. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do následujícího následného zpracování, vyberte nejprve možnost přesunout pro operaci soubor. Potom nastavte adresář "z". Pokud pro cestu nepoužíváte žádné zástupné znaky, pak bude mít nastavení "od" stejnou složku jako vaše zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, vaše syntaxe bude vypadat takto:

```/data/sales/20??/**/*.csv```

Můžete zadat "z" jako:

```/data/sales```

A můžete zadat "do" jako:

```/backup/priorSales```

V tomto případě se všechny soubory, které se nacházely v/data/Sales, přesunuly do/backup/priorSales.

> [!NOTE]
> Operace se soubory běží jenom při spuštění toku dat ze spuštění kanálu (ladění kanálu nebo spuštění spuštění), které používá aktivitu spustit tok dat v kanálu. Operace *se* soubory neběží v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Můžete filtrovat, které soubory se mají zpracovat, zadáním rozsahu data při jejich poslední úpravě. Všechny časové údaje jsou v UTC. 

### <a name="sink-properties"></a>Vlastnosti jímky

V transformaci jímky můžete zapisovat do kontejneru nebo složky v úložišti objektů BLOB v Azure. Pomocí karty **Nastavení** můžete spravovat způsob, jakým se soubory napíší.

![Možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymažte složku:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou:
   * **Výchozí**: umožňuje Sparku pojmenovat soubory založené na výchozím nastavení části.
   * **Vzor**: zadejte vzor, který vytvoří výčet výstupních souborů na oddíl. Například **úvěry [n]. csv** vytvoří loans1.csv, loans2.csv a tak dále.
   * **Na oddíl**: zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem k kontejneru DataSet, nikoli k cílové složce. Pokud máte v datové sadě cestu ke složce, bude přepsána.
   * **Výstup do jednoho souboru**: sloučí výstupní soubory rozdělené do jednoho pojmenovaného souboru. Cesta je relativní vzhledem ke složce DataSet. Počítejte s tím, že operace sloučení může být v závislosti na velikosti uzlu pravděpodobně neúspěšná. Tuto možnost nedoporučujeme pro velké datové sady.

**Všechna citace:** Určuje, zda mají být všechny hodnoty uzavřeny v uvozovkách.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li získat informace o vlastnostech, ověřte [aktivitu GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li získat informace o vlastnostech, ověřte [aktivitu odstranit](delete-activity.md).

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Doporučujeme použít nový model uvedený výše. Uživatelské rozhraní pro vytváření Data Factory bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **azureblobu**. |Ano |
| folderPath | Cesta ke kontejneru a složce v úložišti objektů BLOB <br/><br/>Pro cestu se podporuje filtr zástupných znaků s výjimkou názvu kontejneru. Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` k ukončení, jestli má název složky zástupný znak nebo tento řídicí znak uvnitř. <br/><br/>Příklad: myblobcontainer/myblobfolder/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano pro aktivitu kopírování nebo vyhledávání, ne pro aktivitu GetMetadata |
| fileName | Název nebo zástupný filtr pro objekty BLOB v zadané hodnotě **FolderPath** Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny objekty blob ve složce. <br/><br/>Pro filtr jsou povoleny zástupné znaky: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použijte `^` pro Escape, jestli název souboru obsahuje zástupný znak nebo tento řídicí znak uvnitř.<br/><br/>Pokud není zadán **název souboru** pro výstupní datovou sadu a v jímky aktivity není zadán parametr **PreserveHierarchy** , aktivita kopírování automaticky vygeneruje název objektu BLOB s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]*". Například: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Pokud kopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu "*[název tabulky]. [ formát]. [komprese, pokud je nakonfigurována]*". Například: "MyTable.csv". |Ne |
| modifiedDatetimeStart | Soubory jsou filtrovány na základě atributu: Naposledy změněno. Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Mějte na paměti, že povolení tohoto nastavení bude mít vliv na celkový výkon přesunu dat, pokud chcete filtrovat velké objemy souborů. <br/><br/> Vlastnosti mohou mít **hodnotu null**, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je **null**, budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je **null**, soubory, jejichž atribut Last Modified je menší než hodnota DateTime, se vybere.| Ne |
| modifiedDatetimeEnd | Soubory jsou filtrovány na základě atributu: Naposledy změněno. Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Mějte na paměti, že povolení tohoto nastavení bude mít vliv na celkový výkon přesunu dat, pokud chcete filtrovat velké objemy souborů. <br/><br/> Vlastnosti mohou mít **hodnotu null**, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je **null**, budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je **null**, soubory, jejichž atribut Last Modified je menší než hodnota DateTime, se vybere.| Ne |
| formát | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát jak v definicích vstupní, tak i ve výstupní datové sadě.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny objekty blob ve složce, zadejte jenom **FolderPath** .<br>Chcete-li zkopírovat jeden objekt BLOB se zadaným názvem, zadejte **FolderPath** pro část **složky a název souboru pro** název souboru.<br>Pokud chcete zkopírovat podmnožinu objektů BLOB ve složce, zadejte **FolderPath** pro část složky a **název souboru** se zástupným filtrem. 

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

### <a name="legacy-source-model-for-the-copy-activity"></a>Starší zdrojový model pro aktivitu kopírování

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **BlobSource**. |Ano |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je **rekurzivní** nastavení nastaveno na **hodnotu true** a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet souběžných připojení k úložišti. Určete pouze v případě, že chcete omezit souběžná připojení k úložišti dat. | Ne |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>Starší model jímky pro aktivitu kopírování

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **BlobSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, sloučený název souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | Ne |
| maxConcurrentConnections | Počet souběžných připojení k úložišti. Určete pouze v případě, že chcete omezit souběžná připojení k úložišti dat. | Ne |

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

Seznam úložišť dat, která aktivita kopírování v Data Factory podporuje jako zdroje a jímky, najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
