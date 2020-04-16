---
title: Kopírování a transformace dat v úložišti objektů Blob Azure
description: Zjistěte, jak kopírovat data do úložiště objektů Blob a z nich a transformovat data v úložišti objektů Blob pomocí Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415488"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopírování a transformace dat v úložišti objektů Blob Azure pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuální verze](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do úložiště objektů blob Azure a pomocí toku dat k transformaci dat v úložišti objektů Blob Azure. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

>[!TIP]
>V případě migrace datového jezera nebo datového skladu najdete další informace v [aplikaci Azure Data Factory k migraci dat z datového jezera nebo datového skladu do Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor objektů blob Azure je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Pro aktivitu kopírování tento konektor úložiště objektů blob podporuje:

- Kopírování objektů BLOB do a z účtů úložiště Azure pro obecné účely a úložiště objektů blob za tepla a cool. 
- Kopírování objektů BLOB pomocí klíče účtu, podpisu sdíleného přístupu služby, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Kopírování objektů BLOB z objektů BLOB bloku, připojení nebo stránky a kopírování dat pouze do objektů BLOB bloku.
- Kopírování objektů BLOB tak, jak jsou, nebo analýza nebo generování objektů BLOB s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Zachovat metadata souboru během kopírování](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu přístup k této možnosti účtu úložiště v** nastavení brány firewall Služby Azure Storage a chcete použít runtime integrace Azure pro připojení k úložišti objektů Blob, musíte použít [spravované ověřování identity](#managed-identity).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifické pro úložiště objektů Blob.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Konektor azure blob podporuje následující typy ověřování, naleznete v odpovídající části na podrobnosti:

- [Ověřování pomocí klíče účtu](#account-key-authentication)
- [Ověřování podpisu sdíleného přístupu](#shared-access-signature-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Při použití PolyBase k načtení dat do datového skladu SQL, pokud je zdrojové nebo pracovní úložiště objektů Blob nakonfigurováno s koncovým bodem virtuální sítě, musíte použít spravované ověřování identity podle požadavků PolyBase a použít modul Runtime integrace s vlastním hostitelem s verzí 3.18 nebo vyšší. Podívejte se na část [ověřování spravované identity](#managed-identity) s dalšími požadavky na konfiguraci.

>[!NOTE]
>HdInsights a Azure Machine Learning aktivity podporují jenom ověřování klíče účtu úložiště Azure Blob.

### <a name="account-key-authentication"></a>Ověřování pomocí klíče účtu

Chcete-li použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobStorage** (doporučeno) nebo **AzureStorage** (viz poznámky níže). |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost connectionString. <br/> Můžete také umístit klíč účtu v Azure `accountKey` Key Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. |Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat je v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Sekundární koncový bod služby objektů blob není podporován při použití ověřování pomocí klíče účtu. Můžete použít jiné typy ověřování.

>[!NOTE]
>Pokud jste používali propojené služby typu AzureStorage, je stále podporována tak, jak je, zatímco se vám doporučuje používat tento nový typ propojené služby AzureBlobStorage do budoucna.

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

**Příklad: klíč účtu úložiště v azure key vaultu**

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

Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům v účtu úložiště. Sdílený přístupový podpis můžete použít k udělení omezených oprávnění klienta objektům v účtu úložiště po určitou dobu. Nemusíte sdílet přístupové klíče k účtu. Sdílený přístupový podpis je identifikátor URI, který ve svých parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, klient musí pouze předat sdílený přístupový podpis příslušnému konstruktoru nebo metodě. Další informace o sdílených přístupových podpisech naleznete v [tématu Sdílené přístupové podpisy: Principy modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory nyní podporuje jak **služby sdílené přístupové podpisy** a **podpisy sdíleného přístupu účtu**. Další informace o sdílených přístupových podpisech najdete v tématu [Udělení omezeného přístupu k prostředkům úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../storage/common/storage-sas-overview.md)
>- V pozdější konfiguraci datové sady je cesta ke složce absolutní cestou počínaje úrovní kontejneru. Je třeba nakonfigurovat jeden zarovnán s cestou v identifikátoru URI SAS.

Chcete-li použít ověřování podpisu sdíleného přístupu, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobStorage** (doporučeno) nebo **AzureStorage** (viz poznámky níže). |Ano |
| sasUri | Zadejte identifikátor URI podpisu sdíleného přístupu k prostředkům úložiště, jako je objekt blob/kontejner. <br/>Označte toto pole jako securestring bezpečně ukládat v datové továrně. Token SAS můžete také umístit do trezoru klíčů Azure Key Vault, abyste využili automatické střídání a odebrali část tokenu. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. |Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Runtime integrace s vlastním hostitelem (pokud vaše úložiště dat je umístěnv privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali propojené služby typu AzureStorage, je stále podporována tak, jak je, zatímco se vám doporučuje používat tento nový typ propojené služby AzureBlobStorage do budoucna.

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

**Příklad: klíč účtu úložiště v azure key vaultu**

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

Při vytváření identifikátoru URI sdíleného přístupového podpisu zvažte následující body:

- Nastavte příslušná oprávnění pro čtení a zápis u objektů na základě toho, jak se propojená služba (čtení, zápis, čtení a zápis) používá ve vaší datové továrně.
- Nastavte **dobu expirace** odpovídajícím způsobem. Ujistěte se, že přístup k objektům úložiště nevyprší během aktivní období kanálu.
- Identifikátor URI by měl být vytvořen v pravém kontejneru/objektu blob na základě potřeby. Identifikátor URI podpisu sdíleného přístupu k objektu blob umožňuje datové továrně přístup k tomuto konkrétnímu objektu blob. Identifikátor URI sdíleného přístupu k kontejneru úložiště objektů Blob umožňuje aplikaci Data Factory iterate prostřednictvím objektů BLOB v tomto kontejneru. Chcete-li později poskytnout přístup k více nebo méně objektům nebo aktualizovat identifikátor URI sdíleného přístupového podpisu, nezapomeňte propojenou službu aktualizovat novým identifikátorem URI.

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Ověření na uskladnění služby Azure Storage obecně najdete v [najdete v případě ověření přístupu k Úložišti Azure pomocí služby Azure Active Directory](../storage/common/storage-auth-aad.md).

Chcete-li použít ověřování instančního objektu, postupujte takto:

1. Zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) podle [registrace aplikace u klienta Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte správné oprávnění hlavního povinného k obslužné službě v úložišti objektů blob Azure. Odkazovat na [Spravovat přístupová práva k datům Azure Storage s RBAC](../storage/common/storage-auth-aad-rbac.md) s další podrobnosti o rolích.

    - **Jako zdroj**, v řízení přístupu (IAM), udělit alespoň roli **čtečky dat objektů blob úložiště.**
    - **Jako jímky**, v řízení přístupu (IAM), udělit alespoň roli **přispěvatele dat objektů blob úložiště.**

Tyto vlastnosti jsou podporované pro propojené služby úložiště Azure blob:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobStorage**. |Ano |
| Serviceendpoint | Zadejte koncový bod služby úložiště objektů `https://<accountName>.blob.core.windows.net/`blob Azure se vzorem . |Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Načtěte ji najetím myší v pravém horním rohu portálu Azure. | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat je v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Ověřování instančního objektu je podporováno pouze službou propojenou typem AzureBlobStorage, ale ne předchozí linkovou službou typu AzureStorage.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md), která představuje tuto konkrétní datovou továrnu. Tuto spravovanou identitu můžete použít přímo pro ověřování úložiště objektů Blob podobně jako při použití vlastního instančního objektu. Umožňuje této určené továrně přístup a kopírování dat z nebo do úložiště objektů Blob.

Informace o [ověřování přístupu k Úložišti Azure se](../storage/common/storage-auth-aad.md) obecně vztahují k ověřování Azure Active Directory pro Azure Storage. Pokud chcete pro ověřování prostředků Azure použít spravované identity, postupujte takto:

1. [Načtěte informace o identitě spravované z továrny](data-factory-service-identity.md#retrieve-managed-identity) pomocí data zkopírováním hodnoty **ID spravovaného objektu identity** generovaného spolu s továrnou.

2. Udělte správná oprávnění spravované identity v úložišti objektů blob Azure. Odkazovat na [Spravovat přístupová práva k datům Azure Storage s RBAC](../storage/common/storage-auth-aad-rbac.md) s další podrobnosti o rolích.

    - **Jako zdroj**, v řízení přístupu (IAM), udělit alespoň roli **čtečky dat objektů blob úložiště.**
    - **Jako jímky**, v řízení přístupu (IAM), udělit alespoň roli **přispěvatele dat objektů blob úložiště.**

>[!IMPORTANT]
>Pokud používáte PolyBase k načtení dat z objektu Blob (jako zdroj nebo jako pracovní) do datového skladu SQL, při použití ověřování spravované identity pro objekt Blob, ujistěte se, že jste také postupujte podle kroků 1 a 2 v [tomto návodu](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) k 1) zaregistrovat sql databázový server s Azure Active Directory (Azure AD) a 2) přiřadit roli přispěvatele dat blob úložiště na váš server SQL Database; zbytek zpracovává Data Factory. Pokud je úložiště objektů blob nakonfigurované s koncovým bodem virtuální sítě Azure, chcete-li použít PolyBase k načtení dat z něj, musíte použít spravované ověřování identity podle požadavků PolyBase.

Tyto vlastnosti jsou podporované pro propojené služby úložiště Azure blob:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobStorage**. |Ano |
| Serviceendpoint | Zadejte koncový bod služby úložiště objektů `https://<accountName>.blob.core.windows.net/`blob Azure se vzorem . |Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat je v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

> [!NOTE]
> Spravované identity pro ověřování prostředků Azure je podporována jenom "AzureBlobStorage" typ propojené služby, ale ne předchozí "AzureStorage" typ propojené služby. 

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro `location` Azure Blob v rámci nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type umístění v datové sadě musí být nastavena na **AzureBlobStorageLocation**. | Ano      |
| kontejner  | Kontejner blob.                                          | Ano      |
| folderPath | Cesta ke složce pod daným kontejnerem. Chcete-li k filtrování složky použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným kontejnerem + složkaPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a určete v nastavení zdroje aktivity. | Ne       |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem úložiště objektů blob a jímkou.

### <a name="blob-storage-as-a-source-type"></a>Úložiště objektů blob jako zdrojový typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro `storeSettings` Objekt blob Azure v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **AzureBlobStorageReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| Předponu                   | Předpona názvu objektu blob pod daným kontejnerem nakonfigurovaným v datové sadě pro filtrování zdrojových objektů BLOB. Objekty BLOB, jejichž název začíná touto předponou, jsou vybrány. <br>Platí pouze `wildcardFolderPath` v `wildcardFileName` případě, že vlastnosti nejsou zadány. | Ne                                                          |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pod daným kontejnerem nakonfigurovaná v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod daným kontejnerem + složkaCesta/zástupný znakFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř.  Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro formát textu parkety/oddělovače je zdroj aktivity kopírování typu **BlobSource** uvedený v další části stále podporován jako-je pro zpětnou kompatibilitu. Doporučujeme použít tento nový model do budoucna a vývojové ui ADF přešel na generování těchto nových typů.

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

### <a name="blob-storage-as-a-sink-type"></a>Úložiště objektů blob jako typ jímky

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro `storeSettings` Objekt blob Azure v nastavení v jímce na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **AzureBlobStorageWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, je zadanýnázev sloučeného souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne       |
| blockSizeInMB | Zadejte velikost bloku v MB, která se používá k zápisu dat do bloků objektů BLOB. Další informace [o objektech BLOB bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Povolená hodnota je **mezi 4 a 100 MB**. <br/>Ve výchozím nastavení adf automaticky určit velikost bloku na základě typu úložiště zdrojového obchodu a data. Pro nebinární kopírování do objektu Blob je výchozí velikost bloku 100 MB, aby se vešla maximálně do dat 4.95 TB. Nemusí být optimální, pokud vaše data nejsou velká, zejména při použití prostředí Runtime integrace s vlastním hostitelem se špatnou sítí, což vede k výpadku operace nebo problému s výkonem. Můžete explicitně zadat velikost bloku, zatímco ujistěte se, že blockSizeInMB*50000 je dostatečně velký pro uložení dat, jinak se spustí operace kopírování. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

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

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| folderPath | fileName | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory **tučně** jsou načteny)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (prázdný, použijte výchozí) | false (nepravda) | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `container/Folder*` | (prázdný, použijte výchozí) | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `container/Folder*` | `*.csv` | false (nepravda) | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `container/Folder*` | `*.csv` | true | kontejner<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování operace Copy pro různé kombinace rekurzivních a copyBehavior hodnot.

| Rekurzivní | copyBehavior | Struktura zdrojových složek | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 |
| true |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru5 |
| true |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Když kopírujete soubory z Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2/Azure Blob, můžete se rozhodnout zachovat metadata souboru spolu s daty. Další informace najdete v [souboru Zachovat metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování můžete číst a zapisovat soubory z Azure Blob Storage ve formátu JSON, Avro, Oddělený text nebo parkety. Další informace naleznete v [tématu transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v prvku tok dat mapování.

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru ve službě Azure Blob Storage. Karta **Možnosti zdroje** umožňuje spravovat způsob čtení souborů. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesta se zástupnými symboly:** Pomocí zástupný vzor pokyn ADF smyčku přes každou odpovídající složku a soubor v jednom zdroj transformace. Jedná se o efektivní způsob zpracování více souborů v rámci jednoho toku. Přidejte více vzorů porovnávání se zástupnými symboly se znaménkem +, které se zobrazí při najetí nad existujícím vzorkem se zástupnými symboly.

Ze zdrojového kontejneru zvolte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta se zástupnými symboly proto musí obsahovat také cestu ke složce z kořenové složky.

Příklady zástupných symbolů:

* ```*```Představuje libovolnou sadu znaků.
* ```**```Představuje vnoření rekurzivního adresáře.
* ```?```Nahradí jeden znak
* ```[]```Shoduje se s jedním z více znaků v závorkách.

* ```/data/sales/**/*.csv```Získá všechny soubory csv pod /data/sales.
* ```/data/sales/20??/**/```Získá všechny soubory ve 20.
* ```/data/sales/*/*/*.csv```Získá soubory csv dvě úrovně pod /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Získá všechny soubory CSV v roce 2004 v prosinci počínaje X nebo Y předponou dvoumístné číslo

**Kořenová cesta oddílu:** Pokud máte rozdělené složky ve ```key=value``` zdroji souborů ve formátu (například year=2019), můžete přiřadit nejvyšší úroveň tohoto stromu složek oddílů názvu sloupce v datovém toku dat.

Nejprve nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělenými složkami a soubory listu, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Pomocí nastavení Kořenová cesta oddílu definujte, jaká je nejvyšší úroveň struktury složek. Když si obsah dat zobrazíte v náhledu dat, uvidíte, že adf přidá vyřešené oddíly nalezené v každé z úrovní složek.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Náhled kořenové cesty oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cesty ke zpracování. Přejděte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat zvolte, zda se zdrojovým souborem nedělat nic, odstraňte zdrojový soubor nebo zdrojový soubor přesuňte. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do jiného umístění po zpracování, nejprve vyberte možnost Přesunout pro operaci souboru. Potom nastavte adresář "od". Pokud pro cestu nepoužíváte žádné zástupné znaky, bude nastavení "od" stejné jako zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, syntaxe bude vypadat takto:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "do" jako

```/backup/priorSales```

V tomto případě jsou všechny soubory, které byly získány pod /data/sales jsou přesunuty do /backup/priorSales.

> [!NOTE]
> Operace se soubory spustit pouze při spuštění toku dat z kanálu spustit (kanál ladění nebo spuštění spustit), který používá spustit tok dat aktivity v kanálu. Operace se soubory *nespouštějí* v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Soubory, které zpracováváte, můžete filtrovat zadáním rozsahu dat, kdy byly naposledy změněny. Všechny časy data jsou v UTC. 

### <a name="sink-properties"></a>Vlastnosti jímky

V transformaci jímky můžete zapisovat do kontejneru nebo složky v azure blob storage. Karta **Nastavení** umožňuje spravovat způsob zápisu souborů.

![možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymazání složky:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou následující:
   * **Výchozí**: Povolit Spark pojmenovat soubory na základě výchozích hodnot ČÁSTI.
   * **Vzorek**: Zadejte vzorek, který vyjmenovává výstupní soubory na oddíl. Například **půjčky[n].csv** vytvoří loans1.csv, loans2.csv a tak dále.
   * **Na oddíl**: Zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem ke kontejneru datové sady, nikoli k cílové složce. Pokud máte cestu ke složce v datové sadě, bude přepsána.
   * **Výstup do jednoho souboru**: Zkombinujte rozdělené výstupní soubory do jednoho pojmenovaného souboru. Cesta je relativní vzhledem ke složce datové sady. Uvědomte si, že operace te sloučení může případně selhat na základě velikosti uzlu. Tato možnost se nedoporučuje pro velké datové sady.

**Citace vše:** Určuje, zda mají být všechny hodnoty uzavřeny v uvozovkách.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zaškrtněte [políčko Odstranit aktivitu.](delete-activity.md)

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **AzureBlob**. |Ano |
| folderPath | Cesta ke kontejneru a složce v úložišti objektů blob. <br/><br/>Pro cestu s výjimkou názvu kontejneru je podporován filtr se zástupnými symboly. Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br/><br/>Příklady: myblobcontainer/myblobfolder/, viz další příklady v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ano pro aktivitu kopírování/vyhledávání, Ne pro aktivitu GetMetadata |
| fileName | **Název nebo zástupný filtr** pro objekty blob pod zadanou "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny objekty BLOB ve složce. <br/><br/>Pro filtr jsou povoleny `*` zástupné znaky: (odpovídá nula nebo více znaků) a `?` (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char uvnitř.<br/><br/>Když fileName není zadán pro výstupní datové sady a **preserveHierarchy** není zadán v jímce aktivity, aktivita kopírování automaticky generuje název objektu blob s následujícím vzorem: "*Data.[ aktivita spustit Identifikátor GUID]. [GUID pokud sloučení hierarchie]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]*", například "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky namísto dotazu, bude se vzor názvu "*[název tabulky].[ formátu]. [komprese, pokud je nakonfigurována]*", například "MyTable.csv". |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete kopírovat soubory tak, jak jsou mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte vlastnost **type** ve **formátu** na jednu z těchto hodnot. Další informace naleznete v části [Formát Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formát orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Parkety.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny objekty BLOB pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden objekt blob s daným názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu objektů BLOB pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** se zástupným filtrem. 

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

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **objekt BlobSource**. |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **objekt BlobSink**. |Ano |
| copyBehavior | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, je zadanýnázev sloučeného souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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

Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
