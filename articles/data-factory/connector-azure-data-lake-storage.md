---
title: Kopírování a transformace dat v Azure Data Lake Storage Gen2
description: Naučte se, jak kopírovat data do a z Azure Data Lake Storage Gen2 a transformovat data v Azure Data Lake Storage Gen2 pomocí Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 7a501a86f979bb508052c8957627ebfa7950fd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597576"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Data Lake Storage Gen2 pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) je sada funkcí vyhrazených pro analýzy velkých objemů dat integrované do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít pro připojení k datům pomocí paradigmat systému souborů a úložiště objektů.

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory kopírovat data do a ze služby Azure Data Lake Storage Gen2 a jak pomocí Toku dat transformovat data ve službě Azure Data Lake Storage Gen2. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

>[!TIP]
>Pokud potřebujete scénář migrace Data Lake nebo Data Warehouse, přečtěte si další informace o [použití Azure Data Factory k migraci dat z datového Lake nebo datového skladu do Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Data Lake Storage Gen2 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Pro aktivitu kopírování můžete pomocí tohoto konektoru:

- Kopírování dat z/do Azure Data Lake Storage Gen2 pomocí klíče účtu, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Zkopírujte soubory tak, jak jsou, nebo Analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Při kopírování zachovat metadata souboru](#preserve-metadata-during-copy).
- Při kopírování z Azure Data Lake Storage Gen1/Gen2 [zachovat seznamy ACL](#preserve-acls) .

## <a name="get-started"></a>Začínáme

>[!TIP]
>Návod, jak používat konektor Data Lake Storage Gen2, najdete v tématu [načtení dat do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor Azure Data Lake Storage Gen2 podporuje následující typy ověřování. Podrobnosti najdete v odpovídajících částech:

- [Ověřování klíčů účtu](#account-key-authentication)
- [Ověřování instančního objektu](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>- Pokud chcete pomocí veřejného prostředí Azure Integration runtime připojit k Data Lake Storage Gen2 pomocí možnosti **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** , který je povolený v bráně firewall Azure Storage, musíte použít [spravované ověřování identity](#managed-identity).
>- Pokud k načtení dat do služby Azure synapse Analytics použijete příkaz Base nebo COPY, pokud je váš zdrojový nebo pracovní Data Lake Storage Gen2 nakonfigurovaný s koncovým bodem Azure Virtual Network, musíte použít spravované ověřování identity podle požadavků synapse. Viz část [spravované ověřování identity](#managed-identity) s dalšími požadavky na konfiguraci.

### <a name="account-key-authentication"></a>Ověřování klíčů účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, podporují se tyto vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Yes |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net` . | Yes |
| accountKey | Klíč účtu pro Data Lake Storage Gen2. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tato vlastnost není zadaná, použije se výchozí prostředí Azure Integration runtime. |No |

>[!NOTE]
>Sekundární koncový bod ADLS systému souborů není při ověřování pomocí klíče účtu podporován. Můžete použít jiné typy ověřování.

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

Chcete-li použít ověřování instančního objektu, postupujte podle těchto kroků.

1. Pomocí postupu v části [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění. Podívejte se na příklady, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) .

    - **Jako zdroj**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním **ke čtení** pro kopírování souborů. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Používáte-li k vytváření Data Factory uživatelské rozhraní a instanční objekt není nastaven s rolí "čtecí modul dat objektů BLOB a Přispěvatel" v nástroji IAM, při provádění testovacího připojení nebo procházení/procházení složek zvolte možnost "testovat připojení k cestě k souboru" nebo "Procházet ze zadané cesty" a zadejte cestu s oprávněním **číst + spustit** , aby bylo možné pokračovat.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Yes |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net` . | Yes |
| servicePrincipalId | Zadejte ID klienta aplikace. | Yes |
| servicePrincipalCredentialType | Typ přihlašovacích údajů, který se má použít pro ověřování instančního objektu. Povolené hodnoty jsou **ServicePrincipalKey** a **ServicePrincipalCert**. | Yes |
| servicePrincipalCredential | Přihlašovací údaje instančního objektu. <br/> Pokud jako typ přihlašovacích údajů použijete **ServicePrincipalKey** , zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Data Factory nebo [odkazujte na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). <br/> Pokud jako přihlašovací údaje použijete **ServicePrincipalCert** , odkazujte na certifikát v Azure Key Vault. | Yes |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Data Factory nebo [odkazujte na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). <br/> Tato vlastnost je stále podporovaná tak, jak je pro `servicePrincipalId`  +  `servicePrincipalKey` . Protože ADF přidává nové ověřování certifikátů instančního objektu, je nový model pro ověřování instančního objektu `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | No |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Yes |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, ve kterém je vaše aplikace Azure Active Directory zaregistrovaná. <br/> Povolené hodnoty jsou **AzurePublic**, **AzureChina**, **AzureUsGovernment** a **AzureGermany**. Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | No |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |No |

**Příklad: použití ověřování klíčů instančního objektu**

Klíč objektu služby můžete také uložit v Azure Key Vault.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**Příklad: použití ověření certifikátu instančního objektu**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě prostředků Azure](data-factory-service-identity.md), která představuje tento konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Storage Gen2, podobně jako při používání vlastního instančního objektu. Umožňuje této určené továrně přístup k datům a jejich kopírování z Data Lake Storage Gen2.

Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků.

1. [Načtěte data Factory informace o spravované identitě](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty **ID spravovaného objektu identity** generovaného společně s vaší továrnou.

2. Udělte spravované identitě správné oprávnění. Podívejte se na příklady, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako zdroj**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním **ke čtení** pro kopírování souborů. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Pokud použijete Data Factory uživatelské rozhraní k vytváření a spravovaná identita není nastavena pomocí role čtenář/Přispěvatel dat objektů BLOB úložiště v nástroji IAM, při provádění testovacího připojení nebo procházení/procházení složek zvolte možnost Test připojení k cestě k souboru nebo procházet ze zadané cesty a zadejte cestu s oprávněním **číst + spustit** , aby bylo možné pokračovat.

>[!IMPORTANT]
>Pokud k načtení dat z Data Lake Storage Gen2 do služby Azure synapse Analytics použijete příkaz Base nebo COPY, při použití spravovaného ověřování identity pro Data Lake Storage Gen2 se ujistěte, že provedete také kroky 1 až 3 v [těchto pokynech](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Tyto kroky zaregistrují váš server ve službě Azure AD a přiřadí roli Přispěvatel dat objektů BLOB úložiště k vašemu serveru. Data Factory zpracuje zbytek. Pokud nakonfigurujete úložiště objektů BLOB pomocí koncového bodu Azure Virtual Network, musíte taky **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** v části Azure Storage **brány firewall účtů a nastavení virtuálních sítí** , jak to vyžaduje synapse.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Yes |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net` . | Yes |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Povinné |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Vlastnost Type v rámci `location` datové sady musí být nastavená na **AzureBlobFSLocation**. | Yes      |
| Systému souborů | Název systému souborů Data Lake Storage Gen2.                              | No       |
| folderPath | Cesta ke složce v daném systému souborů. Pokud chcete použít zástupný znak pro filtrování složek, toto nastavení nechte a zadejte v nastavení zdroje aktivity. | No       |
| fileName   | Název souboru pod daným systémem souborů + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho do nastavení zdroje aktivity. | No       |

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [Konfigurace aktivit kopírování](copy-activity-overview.md#configuration) a [kanály a aktivity](concepts-pipelines-activities.md). V této části najdete seznam vlastností podporovaných zdrojem a jímkou Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

K kopírování dat z ADLS Gen2 máte několik možností:

- Kopírování z dané cesty zadané v datové sadě.
- Filtr zástupných znaků pro cestu ke složce nebo název souboru naleznete v tématech `wildcardFolderPath` a `wildcardFileName` .
- Zkopírování souborů definovaných v daném textovém souboru jako sada souborů naleznete v tématu `fileListPath` .

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Povinné                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureBlobFSReadSettings**. | Yes                                           |
| ***Vyhledejte soubory ke zkopírování:*** |  |  |
| MOŽNOST 1: statická cesta<br> | Kopírování ze zadané cesty systému souborů nebo složky/souboru zadaného v datové sadě. Pokud chcete zkopírovat všechny soubory ze systému souborů nebo složky, zadejte také `wildcardFileName` jako `*` . |  |
| MOŽNOST 2: zástupný znak<br>- wildcardFolderPath | Cesta ke složce se zástupnými znaky v rámci daného systému souborů nakonfigurovaného v sadě dat pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); `^` Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k Escape. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | No                                            |
| MOŽNOST 2: zástupný znak<br>- wildcardFileName | Název souboru se zástupnými znaky v daném systému souborů + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); použijte `^` k Escape, jestli váš skutečný název souboru obsahuje zástupný znak nebo tento řídicí znak v.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Yes |
| MOŽNOST 3: seznam souborů<br>- fileListPath | Určuje, že se má zkopírovat daná sada souborů. Najeďte na textový soubor, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek, což je relativní cesta k cestě nakonfigurované v datové sadě.<br/>Při použití této možnosti nezadávejte název souboru v datové sadě. Další příklady najdete v [příkladech seznamu souborů](#file-list-examples). |No |
| ***Další nastavení:*** |  | |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. <br>Povolené hodnoty jsou **true** (výchozí) a **false**.<br>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . |No |
| deleteFilesAfterCompletion | Uvádí, zda budou binární soubory po úspěšném přesunutí do cílového úložiště odstraněny ze zdrojového úložiště. Odstranění souboru je vázané na soubor, takže když aktivita kopírování selže, uvidíte, že některé soubory se už zkopírovaly do cílového umístění a odstranily ze zdroje, zatímco ostatní jsou pořád ve zdrojovém úložišti. <br/>Tato vlastnost je platná pouze ve scénáři kopírování binárních souborů. Výchozí hodnota: false. |No |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Naposledy změněno <br>Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Pokud `modifiedDatetimeStart` má hodnota DateTime `modifiedDatetimeEnd` , ale je null, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud `modifiedDatetimeEnd` má hodnota DateTime `modifiedDatetimeStart` , ale je null, znamená to, že jsou soubory, jejichž naposledy upravený atribut je menší než hodnota DateTime, bude vybrána.<br/>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Platí to samé jako výše.                                               | No                                            |
| enablePartitionDiscovery | U souborů, které jsou rozdělené na oddíly, určete, jestli se mají analyzovat oddíly z cesty k souboru, a přidejte je jako další zdrojové sloupce.<br/>Povolené hodnoty jsou **false** (výchozí) a **true**. | No                                            |
| partitionRootPath | Pokud je povoleno zjišťování oddílů, zadejte absolutní kořenovou cestu, aby bylo možné číst rozdělené složky jako sloupce dat.<br/><br/>Pokud není zadaný, ve výchozím nastavení<br/>– Pokud použijete cestu k souboru v datové sadě nebo v seznamu souborů na zdroji, je kořenová cesta oddílu cestou nakonfigurovanou v datové sadě.<br/>– Když použijete filtr složky se zástupnými znaky, kořenová cesta oddílu je dílčí cesta před prvním zástupným znakem.<br/><br/>Předpokládejme například, že nakonfigurujete cestu v datové sadě jako kořen/složka/rok = 2020/měsíc = 08/Day = 27:<br/>– Pokud zadáte kořenovou cestu oddílu jako "root/složka/Year = 2020", aktivita kopírování vygeneruje další dva sloupce `month` a `day` hodnoty "08" a "27" společně se sloupci uvnitř souborů.<br/>-Pokud není zadána kořenová cesta oddílu, nebude vygenerován žádný sloupec navíc. | No                                            |
| maxConcurrentConnections | Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 jako typ jímky

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Povinné |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureBlobFSWriteSettings**. | Yes      |
| copyBehavior             | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | No       |
| blockSizeInMB | Určete velikost bloku v MB používaného k zápisu dat do ADLS Gen2. Přečtěte si další informace o objektech [blob bloku](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Povolená hodnota je **mezi 4 MB a 100 MB**. <br/>Ve výchozím nastavení ADF automaticky určuje velikost bloku na základě typu zdrojového úložiště a dat. V případě nebinárních kopírování do ADLS Gen2 je výchozí velikost bloku 100 MB, aby se vešla do velikosti maximálně 4,95 TB dat. Nemusí být optimální, pokud vaše data nejsou velká, zejména pokud používáte Integration Runtime v místním prostředí s nízkou sítí, která má za následek časový limit operace nebo problémy s výkonem. Můžete explicitně zadat velikost bloku a zajistit tak, aby blockSizeInMB * 50000 bylo dostatečně velké pro ukládání dat, jinak se spuštění aktivity kopírování nezdaří. | No |
| maxConcurrentConnections | Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No       |

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
                    "type": "AzureBlobFSWriteSettings",
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
| `Folder*` | (Prázdné, použít výchozí) | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Prázdné, použít výchozí) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Příklady seznamů souborů

Tato část popisuje výsledné chování při použití cesty seznamu souborů ve zdroji aktivity kopírování.

Za předpokladu, že máte následující strukturu zdrojové složky a chcete soubory zkopírovat tučně:

| Ukázka zdrojové struktury                                      | Obsah v FileListToCopy.txt                             | Konfigurace ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| systému souborů<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mezipaměť<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **V datové sadě:**<br>– Systém souborů: `filesystem`<br>– Cesta ke složce: `FolderA`<br><br>**Ve zdroji aktivity kopírování:**<br>– Cesta k seznamu souborů: `filesystem/Metadata/FileListToCopy.txt` <br><br>Cesta k seznamu souborů odkazuje na textový soubor ve stejném úložišti dat, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek s relativní cestou k cestě, která je nakonfigurovaná v datové sadě. |


### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivních a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| zahrnout | copyBehavior | Struktura zdrojové složky | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro File5 |
| true |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah souboru soubor1 + soubor2 + file3 + file4 + File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Při kopírování souborů z Amazon S3/Azure blob/Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2/Azure Blob se můžete rozhodnout zachovat metadata souboru spolu s daty. Další informace o [zachování metadat](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Zachovat seznamy ACL z Data Lake Storage Gen1/Gen2

Když zkopírujete soubory z Azure Data Lake Storage Gen1/Gen2 do Gen2, můžete se rozhodnout zachovat seznamy řízení přístupu (ACL) POSIX společně s daty. Přečtěte si další informace [o zachování seznamů ACL z Data Lake Storage Gen1/Gen2 na Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Pokud chcete kopírovat data z Azure Data Lake Storage Gen1 do Gen2 obecně, přečtěte si téma [kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pro návod a osvědčené postupy.

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Když transformují data v datových tocích mapování, můžete číst a zapisovat soubory z Azure Data Lake Storage Gen2 v následujících formátech:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common data model (Preview)](format-common-data-model.md#mapping-data-flow-properties)
* [Text oddělený textem](format-delimited-text.md#mapping-data-flow-properties)
* [Rozdíl](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Nastavení konkrétního formátu se nachází v dokumentaci pro tento formát. Další informace najdete v tématu [transformace zdroje v části mapování toku dat](data-flow-source.md) a [transformace jímky při mapování toku dat](data-flow-sink.md).

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v Azure Data Lake Storage Gen2. Na kartě **Možnosti zdrojového kódu** můžete spravovat, jak se soubory čtou. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesta zástupného znaku:** Pomocí vzoru se zástupnými znaky nastavíte ADF, aby prochází každou shodnou složku a soubor v jediné zdrojové transformaci. Toto je efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Přidejte více vzorů pro porovnávání se zástupnými znaky s symbolem +, který se zobrazí při najetí myší na stávající zástupný vzor.

Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

Příklady zástupných znaků:

* ```*``` Představuje libovolnou sadu znaků.
* ```**``` Představuje rekurzivní vnořování adresářů.
* ```?``` Nahradí jeden znak.
* ```[]``` Odpovídá jednomu nebo více znakům v závorkách

* ```/data/sales/**/*.csv``` Načte všechny soubory CSV v rámci/data/Sales.
* ```/data/sales/20??/**/``` Načte všechny soubory ve dvacátém století.
* ```/data/sales/*/*/*.csv``` Získá na/data/Sales dva úrovně souborů CSV.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Načte všechny soubory CSV v 2004 v prosinci počínaje písmenem X nebo Y a číslem se dvěma číslicemi.

**Kořenová cesta oddílu:** Pokud jste ve zdroji souborů nastavili dělené složky s ```key=value``` formátem (například Year = 2019), můžete přiřadit nejvyšší úroveň stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

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

V transformaci jímky můžete zapisovat do kontejneru nebo složky v Azure Data Lake Storage Gen2. Karta **Nastavení** vám umožní spravovat způsob zápisu souborů.

![možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymažte složku:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou:
   * **Výchozí**: umožňuje Sparku pojmenovat soubory založené na výchozím nastavení části.
   * **Vzor**: zadejte vzor, který vytvoří výčet výstupních souborů na oddíl. Například **úvěry [n]. csv** vytvoří loans1.csv, loans2.csv a tak dále.
   * **Na oddíl**: zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem k kontejneru DataSet, nikoli k cílové složce. Pokud máte v datové sadě cestu ke složce, bude přepsána.
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

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na **AzureBlobFSFile**. |Yes |
| folderPath | Cesta ke složce v Data Lake Storage Gen2. Pokud není zadaný, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné znaky jsou `*` (Porovná žádný nebo více znaků) a `?` (Porovná žádný nebo jeden znak). `^`Pokud má váš vlastní název složky zástupný znak nebo je tento řídicí znak uvnitř, použijte k tomu řídicí znak. <br/><br/>Příklady: systém souborů/složka/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |No |
| fileName | Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny soubory ve složce. <br/><br/>V případě filtru je povolených zástupných znaků `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použijte `^` k ukončení, jestli má skutečný název souboru zástupný znak nebo že se tento řídicí znak nachází uvnitř.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadaný **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese je-li nakonfigurována]*", například" Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz ". Pokud zkopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu "*[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]*", například" MyTable.csv ". |No |
| modifiedDatetimeStart | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| No |
| modifiedDatetimeEnd | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| No |
| formát | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát jak v definicích vstupní, tak i ve výstupní datové sadě.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **gzip**, **Deflate**, **bzip2** a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |No |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze **FolderPath** .<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

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

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AzureBlobFSSource**. |Yes |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | No |
| maxConcurrentConnections | Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No |

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

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **AzureBlobFSSink**. |Yes |
| copyBehavior | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | No |
| maxConcurrentConnections | Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No |

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

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).