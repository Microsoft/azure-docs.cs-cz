---
title: Kopírování dat do nebo z úložiště objektů BLOB v Azure pomocí Data Factory | Microsoft Docs
description: Přečtěte si, jak kopírovat data z podporovaných zdrojových úložišť dat do služby Azure Blob Storage nebo z úložiště objektů blob do podporovaných úložišť dat jímky pomocí Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: da8b4ebd5cf1e7a57842a116e5d9e21e3c3f7874
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387303"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopírování dat do nebo z úložiště objektů BLOB v Azure pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuální verze](connector-azure-blob-storage.md)

Tento článek popisuje, jak kopírovat data z a do úložiště objektů BLOB v Azure. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Blob Connector se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor BLOB Storage podporuje:

- Kopírování objektů blob do a z účtu Azure Storage pro obecné účely a horké nebo studené služby Blob Storage. 
- Kopírování objektů BLOB pomocí klíče účtu, sdíleného přístupového podpisu služby, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Kopírování objektů BLOB z bloků, přidávání nebo objektů blob stránky a kopírování dat pouze do objektů blob bloku.
- Kopírování objektů blob, jako je nebo analýza nebo generování objektů BLOB s [podporovanými formáty souborů a kodeky komprese](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Pokud povolíte možnost _"Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště"_ na Azure Storage nastavení brány firewall, při použití Azure Integration runtime pro připojení k úložišti objektů BLOB dojde k chybě zakázané, protože ADF není považována za důvěryhodnou. Služba společnosti Microsoft. Místo toho se prosím připojte přes Integration Runtime v místním prostředí.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factorych entit specifických pro úložiště objektů BLOB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor objektů blob Azure podporuje následující typy ověřování. Další informace najdete v příslušných oddílech:

- [Ověřování klíčů účtu](#account-key-authentication)
- [Ověřování sdíleného přístupového podpisu](#shared-access-signature-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Když použijete základnu k načtení dat do SQL Data Warehouse, pokud je zdrojové nebo pracovní úložiště objektů BLOB nakonfigurované pomocí Virtual Networkho koncového bodu, musíte použít spravované ověřování identity podle požadavků v rámci základny a používat místně hostované Integration Runtime s verzí. 3,18 nebo vyšší. Viz část [spravované ověřování identity](#managed-identity) s dalšími požadavky na konfiguraci.

>[!NOTE]
>HDInsights a Azure Machine Learning aktivity podporují jenom ověřování klíčů účtu Azure Blob Storage.

### <a name="account-key-authentication"></a>Ověřování klíčů účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, podporují se tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobStorage** (navrhovaný) nebo **AzureStorage** (viz poznámky níže). |Ano |
| Vlastnosti | Zadejte informace potřebné pro připojení k úložišti pro vlastnost connectionString. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Klíč účtu můžete také vložit do Azure Key Vault a načíst z připojovacího řetězce konfiguraci `accountKey`. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je vaše úložiště dat v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Sekundární koncový bod služby BLOB Service není podporován při použití ověřování klíčů účtu. Můžete použít jiné typy ověřování.

>[!NOTE]
>Pokud jste použili propojenou službu typu "AzureStorage", je stále podporovaná tak, jak je, a když jste se rozhodli použít tento nový typ propojené služby "AzureBlobStorage", který se bude přesměrovat.

**Příklad:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
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

Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Pomocí sdíleného přístupového podpisu můžete pro určitý čas udělit klientovi omezená oprávnění k objektům ve vašem účtu úložiště. Nemusíte sdílet přístupové klíče k účtu. Sdílený přístupový podpis je identifikátor URI, který v parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště se sdíleným přístupovým podpisem, klient musí předat sdílený přístupový podpis pouze příslušnému konstruktoru nebo metodě. Další informace o sdílených přístupových podpisech najdete v tématu [signatury sdíleného přístupu: Principy modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory teď podporuje jak **signatury sdíleného přístupu služby** , tak **signatury sdíleného přístupu účtu**. Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).
>- V novější konfiguraci datové sady je cesta ke složce absolutní cesta od úrovně kontejneru. Je nutné nakonfigurovat jednu zarovnaná s cestou v identifikátoru URI SAS.

> [!TIP]
> Pokud chcete pro svůj účet úložiště vygenerovat sdílený přístupový podpis služby, můžete spustit následující příkazy PowerShellu. Nahraďte zástupné symboly a udělte potřebné oprávnění.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Chcete-li použít ověřování pomocí sdíleného přístupového podpisu, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobStorage** (navrhovaný) nebo **AzureStorage** (viz poznámky níže). |Ano |
| sasUri | Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům úložiště, jako je například objekt BLOB nebo kontejner. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Do Azure Key Vault můžete také vložit token SAS a využít tak automatické otočení a odebrat část tokenu. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo v místním prostředí Integration Runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste použili propojenou službu typu "AzureStorage", je stále podporovaná tak, jak je, a když jste se rozhodli použít tento nový typ propojené služby "AzureBlobStorage", který se bude přesměrovat.

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

Při vytváření identifikátoru URI sdíleného přístupového podpisu Vezměte v úvahu následující body:

- Nastavte vhodná oprávnění ke čtení a zápisu pro objekty na základě způsobu, jakým se ve vaší datové továrně používá propojená služba (čtení, zápis, čtení a zápis).
- Nastavte odpovídající **čas vypršení platnosti** . Ujistěte se, že přístup k objektům úložiště nevyprší v aktivním období kanálu.
- Identifikátor URI by měl být vytvořen na správném kontejneru nebo objektu BLOB na základě potřeby. Identifikátor URI sdíleného přístupového podpisu k objektu BLOB umožňuje Data Factory přistupovat k tomuto konkrétnímu objektu BLOB. Identifikátor URI sdíleného přístupového podpisu kontejneru úložiště objektů BLOB umožňuje Data Factory iterovat objekty BLOB v tomto kontejneru. Pokud chcete později poskytnout přístup k více nebo méně objektům nebo aktualizovat identifikátor URI sdíleného přístupového podpisu, nezapomeňte aktualizovat propojenou službu pomocí nového identifikátoru URI.

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Azure Storage obecně ověřování instančních objektů najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md).

Chcete-li použít ověřování instančního objektu, postupujte takto:

1. Pomocí [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění ve službě Azure Blob Storage. Další podrobnosti o rolích najdete v tématu [Správa přístupových práv k Azure Storage dat pomocí RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako zdroj**v řízení přístupu (IAM) udělte aspoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**udělte v řízení přístupu (IAM) aspoň roli **Přispěvatel dat objektu BLOB úložiště** .

Tyto vlastnosti jsou podporované pro propojenou službu Azure Blob Storage:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobStorage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob Storage se vzorem `https://<accountName>.blob.core.windows.net/`. |Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** , abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je vaše úložiště dat v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Ověřování instančního objektu je podporováno pouze propojenou službou typu "AzureBlobStorage", ale ne předchozí propojenou službou typu "AzureStorage".

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

### <a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě prostředků Azure](data-factory-service-identity.md), která představuje tento konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete přímo použít pro ověřování BLOB Storage podobně jako při použití vlastního instančního objektu. Umožňuje této vyhrazené továrně přistupovat k úložišti objektů BLOB a kopírovat z nich data.

Informace o ověřování [přístupu k Azure Storage pomocí Azure Active Directory](../storage/common/storage-auth-aad.md) pro Azure Storage ověřování obecně. Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků:

1. [Načtěte informace o identitě spravované identity Data Factory](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty "ID aplikace identity služby" vygenerované společně s vaší továrnou.

2. Udělte spravované identitě správné oprávnění ve službě Azure Blob Storage. Další podrobnosti o rolích najdete v tématu [Správa přístupových práv k Azure Storage dat pomocí RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako zdroj**v řízení přístupu (IAM) udělte aspoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**udělte v řízení přístupu (IAM) aspoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!IMPORTANT]
>Pokud použijete základnu k načtení dat z objektu BLOB (jako zdroj nebo jako fázování) do SQL Data Warehouse, při použití spravovaného ověřování identity pro objekt BLOB se ujistěte, že jste provedli kroky 1 a 2 v [těchto pokynech](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) : 1) zaregistrovat SQL Database Server s Azure Active Directory (Azure AD) a 2) přiřaďte roli Přispěvatel dat objektů BLOB úložiště k vašemu SQL Database serveru; zbytek se zpracovává pomocí Data Factory. Pokud je úložiště objektů BLOB nakonfigurované pomocí koncového bodu Azure Virtual Network, musíte použít základ ověřování identity, jak je požadováno v rámci základny.

Tyto vlastnosti jsou podporované pro propojenou službu Azure Blob Storage:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobStorage**. |Ano |
| serviceEndpoint | Zadejte koncový bod služby Azure Blob Storage se vzorem `https://<accountName>.blob.core.windows.net/`. |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je vaše úložiště dat v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

> [!NOTE]
> Spravované identity pro ověřování prostředků Azure podporuje pouze propojená služba typu "AzureBlobStorage", ale ne předchozí propojenou službu typu "AzureStorage". 

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

- Pro **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [Parquet, oddělený text, JSON, Avro a binární formát binárního formátu](#format-based-dataset) .
- Pro jiné formáty, jako je **Formát ORC/JSON**, se podívejte na [jiný oddíl formátu DataSet](#other-format-dataset) .

### <a name="format-based-dataset"></a>Datová sada Parquet, oddělený text, JSON, Avro a binární formát

Chcete-li kopírovat data do úložiště objektů BLOB a z něj v Parquet, oddělený text, Avro nebo binární formát, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. Následující vlastnosti jsou podporovány pro Azure BLOB v nastavení `location` v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type umístění v datové sadě musí být nastavená na **AzureBlobStorageLocation**. | Ano      |
| kontejner  | Kontejner objektů BLOB.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným kontejnerem + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

> [!NOTE]
>
> Datová sada typu **azureblobu** s Parquet/textovým formátem, která je uvedená v následující části, je stále podporovaná tak, jak je pro aktivitu kopírování/vyhledávání nebo GetMetadata pro zpětnou kompatibilitu, ale nefunguje s tokem dat mapování. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

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

### <a name="other-format-dataset"></a>Jiná Formátová datová sada

Chcete-li kopírovat data z a do úložiště objektů BLOB ve formátu ORC/JSON, nastavte vlastnost Type datové sady na **azureblobu**. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **azureblobu**. |Ano |
| folderPath | Cesta ke kontejneru a složce v úložišti objektů BLOB. <br/><br/>Pro cestu s názvem kontejneru je podporován filtr zástupných znaků. Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br/><br/>Příklady: myblobcontainer/myblobfolder/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano pro aktivitu kopírování/vyhledávání, ne pro aktivitu GetMetadata |
| fileName | **Název nebo zástupný filtr** pro objekty BLOB v rámci zadaného "FolderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny objekty blob ve složce. <br/><br/>V případě filtru jsou povoleny zástupné znaky: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Pokud skutečný název souboru obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu a v jímky aktivity není zadán parametr **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název objektu BLOB s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky místo dotazu, vzor názvu je " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime.| Ne |
| formátovat | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát jak v definicích vstupní, tak i ve výstupní datové sadě.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny objekty blob ve složce, zadejte jenom **FolderPath** .<br>Pokud chcete zkopírovat jeden objekt BLOB se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Pokud chcete zkopírovat podmnožinu objektů BLOB ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou úložiště objektů BLOB.

### <a name="blob-storage-as-a-source-type"></a>Úložiště objektů BLOB jako typ zdroje

- Chcete-li kopírovat z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [zdrojové části Parquet, oddělovače text, JSON, Avro a Binary Format](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je například **Formát ORC**, přečtěte si část [Další zdrojový formát](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, oddělený text, JSON, Avro a binární formát formátu

Chcete-li kopírovat data do úložiště objektů BLOB a z něj v **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet Format](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), [Formát Avro](format-avro.md) a [binární formát](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. Následující vlastnosti jsou podporovány pro Azure BLOB v nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavena na hodnotu **AzureBlobStorageReadSetting**. | Ano                                           |
| zahrnout                | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného kontejneru + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v datové sadě není zadáno `fileName` |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime. | Ne                                            |
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
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

Pokud chcete kopírovat data z úložiště objektů BLOB ve **formátu ORC**, nastavte typ zdroje v aktivitě kopírování na **BlobSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **BlobSource**. |Ano |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
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

### <a name="blob-storage-as-a-sink-type"></a>Úložiště objektů BLOB jako typ jímky

- Chcete-li kopírovat z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [zdrojové části Parquet, oddělovače text, JSON, Avro a Binary Format](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je například **Formát ORC**, přečtěte si část [Další zdrojový formát](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, oddělený text, JSON, Avro a binární formát formátu

Chcete-li kopírovat data z úložiště objektů BLOB v **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro zdroj aktivity kopírování na základě formátu. a podporovaná nastavení. Následující vlastnosti jsou podporovány pro Azure BLOB v nastavení `storeSettings` v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavena na hodnotu **AzureBlobStorageWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, sloučený název souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

> [!NOTE]
> V případě textového formátu Parquet/Unlimited je jímka aktivity kopírování typu **BlobSink** uvedená v další části stále podporovaná tak, jak je kvůli zpětné kompatibilitě. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Další jímka formátu

Pokud chcete kopírovat data do úložiště objektů BLOB ve **formátu ORC**, nastavte typ jímky v aktivitě kopírování na **BlobSink**. V oddílu **jímky** jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **BlobSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektu blob, sloučený název souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | Ne |
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

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (prázdné, použít výchozí) | false | kontejner<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | (prázdné, použít výchozí) | true | kontejner<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**file3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**file4. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | kontejner<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | kontejner<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**Soubor1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**file3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivních a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| zahrnout | copyBehavior | Struktura zdrojové složky | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílová složka Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 |
| true |flattenHierarchy | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro Soubor1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro soubor2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro file3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro file4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro File5 |
| true |mergeFiles | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + soubor2 + file3 + file4 + File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false |preserveHierarchy | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílová složka Složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false |flattenHierarchy | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílová složka Složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro Soubor1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false |mergeFiles | Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + soubor2 Contents jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
