---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen1
description: Zjistěte, jak kopírovat data z podporovaných zdrojových úložišť dat do Azure Data Lake Store nebo z Úložiště datových jezer do podporovaných úložišť jímek pomocí Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: a8ba8b212a504a8f8e4e29fbd50126189998e81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065470"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuální verze](connector-azure-data-lake-store.md)

Tento článek popisuje, jak kopírovat data do a z Azure Data Lake Storage Gen1. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Data Lake Storage Gen1 je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md) 
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně s tímto konektorem můžete:

- Zkopírujte soubory pomocí jedné z následujících metod ověřování: instanční objekt nebo spravované identity pro prostředky Azure.
- Kopírujte soubory tak, jak jsou, nebo je analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Zachovat akl při](#preserve-acls-to-data-lake-storage-gen2) kopírování do Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Pokud kopírujete data pomocí runtime integrace s vlastním hostitelem, nakonfigurujte podnikovou bránu firewall tak, aby umožňovala odchozí provoz na `<ADLS account name>.azuredatalakestore.net` port 443 a `login.microsoftonline.com/<tenant>/oauth2/token` na jeho portu. Ten druhý je Azure Security Token Service, že integrační runtime potřebuje komunikovat s získat přístupový token.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Návod, jak používat konektor Azure Data Lake Store, najdete v tématu [načítání dat do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které se používají k definování entit Datové továrny specifické pro Azure Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Azure Data Lake Store jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost `type` musí být nastavena na **AzureDataLakeStore**. | Ano |
| dataLakeStoreUri | Informace o účtu Azure Data Lake Store. Tyto informace mají jeden z `https://[accountname].azuredatalakestore.net/webhdfs/v1` následujících formátů: nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | ID předplatného Azure, do kterého patří účet Úložiště datového jezera. | Vyžadováno pro umyvadlo |
| resourceGroupName | Název skupiny prostředků Azure, do kterého patří účet Úložiště datového jezera. | Vyžadováno pro umyvadlo |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud je vaše úložiště dat umístěné v privátní síti, můžete použít runtime integrace Azure nebo runtime integrace s vlastním hostitelem. Pokud tato vlastnost není zadána, použije se výchozí doba runtime integrace Azure. |Ne |

### <a name="use-service-principal-authentication"></a>Použití ověřování instančního objektu

Chcete-li použít ověřování instančního objektu, postupujte takto.

1. Zaregistrujte entitu aplikace ve službě Azure Active Directory a udělte jí přístup k úložišti Data Lake Store. Podrobné kroky naleznete v [tématu Ověřování mezi službami](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte příslušnému objektu služby správné oprávnění. Podívejte se na příklady fungování oprávnění v oblasti Data Lake Storage Gen1 z [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako zdroj:** V **průzkumníku** > dat**Access**udělte alespoň **oprávnění spustit** pro všechny upstream složky včetně kořenového adresáře, spolu s **oprávněním ke čtení** pro soubory ke kopírování. Můžete přidat do **této složky a všechny podřízené položky** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položku oprávnění**. Neexistuje žádný požadavek na řízení přístupu na úrovni účtu (IAM).
    - **Jako jímka**: V **průzkumníku** > dat**Access**udělte alespoň **oprávnění spustit** pro všechny upstream složky včetně kořenového adresáře, spolu s **oprávněním k zápisu** pro složku jímky. Můžete přidat do **této složky a všechny podřízené položky** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položku oprávnění**. Pokud ke kopírování používáte runtime integrace Azure (zdroj i jímky jsou v cloudu), v IAM udělte alespoň roli **Čtečka,** aby data Factory mohla rozpoznat oblast úložiště datových jezer. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte runtime integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním úložiště Data Lake Store. Pokud je například úložiště data lake store v západní Evropě, vytvořte runtime integrace Azure s umístěním nastaveným na "Západní Evropa". Přidružte je k propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako a `SecureString` bezpečně jej uložte v datové továrně nebo [odkazujte na tajný klíč uložený v úložišti klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| Nájemce | Zadejte informace o klientovi, jako je název domény nebo ID klienta, pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Použití spravovaných identit pro ověřování prostředků Azure

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md), která představuje tuto konkrétní datovou továrnu. Tuto spravovanou identitu můžete použít přímo pro ověřování úložiště datového jezera, podobně jako při použití vlastního instančního objektu. Umožňuje této určené továrně přístup a kopírování dat do nebo z úložiště Data Lake Store.

Pokud chcete pro ověřování prostředků Azure použít spravované identity, postupujte takto.

1. [Načtěte informace o identitě spravované z továrny](data-factory-service-identity.md#retrieve-managed-identity) pomocí datové houštiny zkopírováním hodnoty "ID aplikace identity služby" generovaného spolu s továrnou.

2. Udělte spravované identitě přístup k úložišti Data Lake Store. Podívejte se na příklady fungování oprávnění v oblasti Data Lake Storage Gen1 z [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako zdroj:** V **průzkumníku** > dat**Access**udělte alespoň **oprávnění spustit** pro všechny upstream složky včetně kořenového adresáře, spolu s **oprávněním ke čtení** pro soubory ke kopírování. Můžete přidat do **této složky a všechny podřízené položky** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položku oprávnění**. Neexistuje žádný požadavek na řízení přístupu na úrovni účtu (IAM).
    - **Jako jímka**: V **průzkumníku** > dat**Access**udělte alespoň **oprávnění spustit** pro všechny upstream složky včetně kořenového adresáře, spolu s **oprávněním k zápisu** pro složku jímky. Můžete přidat do **této složky a všechny podřízené položky** pro rekurzivní a přidat jako **přístupová oprávnění a výchozí položku oprávnění**. Pokud ke kopírování používáte runtime integrace Azure (zdroj i jímky jsou v cloudu), v IAM udělte alespoň roli **Čtečka,** aby data Factory mohla rozpoznat oblast úložiště datových jezer. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte runtime integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním úložiště Data Lake Store. Přidružte je k propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

V Azure Data Factory, nemusíte zadávat žádné vlastnosti kromě obecné informace data lake store v propojené službě.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure `location` Data Lake Store Gen1 v rámci nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` v části v datové sadě musí být nastavena na **AzureDataLakeStoreLocation**. | Ano      |
| folderPath | Cesta ke složce. Chcete-li k filtrování složek filtrovat zástupný znak, přeskočte toto nastavení a zadejte jej v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod danou složkouPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a zadejte jej v nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou úložiště Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure `storeSettings` Data Lake Store Gen1 v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **AzureDataLakeStoreReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod danou složkouPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je null, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako umyvadlo

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporované pro Azure `storeSettings` Data Lake Store Gen1 v rámci nastavení v jímce na základě formátu kopírování:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **AzureDataLakeStoreWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne       |
| expiryDateTime | Určuje dobu vypršení platnosti písemných souborů. Čas je aplikován na čas UTC ve formátu "2020-03-01T08:00:00Z". Ve výchozím nastavení je null, což znamená, že písemné soubory nikdy vypršela. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
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
| `Folder*` | (Prázdné, použít výchozí) | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | (Prázdné, použít výchozí) | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Příklady chování operace kopírování

Tato část popisuje výsledné chování operace kopírování pro různé `recursive` `copyBehavior` kombinace a hodnoty.

| Rekurzivní | copyBehavior | Struktura zdrojových složek | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5. |
| true |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru5 |
| true |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 obsah jsou sloučeny do jednoho souboru, s automaticky generovaným názvem souboru. |
| false (nepravda) |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachovat akly do úložiště datových jezer Gen2

>[!TIP]
>Pokud chcete zkopírovat data z Azure Data Lake Storage Gen1 do Gen2 obecně, najdete v [tématu Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pro návod a osvědčené postupy.

Pokud chcete replikovat seznamy řízení přístupu (ACL) spolu s datovými soubory při upgradu z Data Lake Storage Gen1 na Data Lake Storage Gen2, přečtěte si informace [o zachování seznamů ACL z úložiště datového jezera Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování můžete číst a zapisovat soubory z Azure Data Lake Storage Gen1 ve formátu JSON, Avro, Oddělený text nebo parkety. Další informace naleznete v [tématu transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v prvku tok dat mapování.

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v Azure Data Lake Storage Gen1. Karta **Možnosti zdroje** umožňuje spravovat způsob čtení souborů. 

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

V transformaci jímky můžete zapisovat do kontejneru nebo složky v Azure Data Lake Storage Gen1. Karta **Nastavení** umožňuje spravovat způsob zápisu souborů.

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
| type | Vlastnost type datové sady musí být nastavena na **AzureDataLakeStoreFile**. |Ano |
| folderPath | Cesta ke složce v úložišti Data Lake Store. Pokud není zadán, odkazuje na kořen. <br/><br/>Je podporován filtr se zástupnými symboly. Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br/><br/>Příklad: kořenová složka/podsložka/. Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Filtr názvu nebo zástupných symbolů pro soubory pod zadanou "ssložkouCesta". Pokud pro tuto vlastnost nezadáte hodnotu, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr jsou `*` povolené zástupné znaky (odpovídá `?` nula nebo více znaků) a (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char uvnitř.<br/><br/>Když fileName není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce aktivity, aktivita kopírování automaticky generuje název souboru s následujícím vzorem: "*Data.[ aktivita spustit Identifikátor GUID]. [GUID pokud sloučení hierarchie]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]*", například "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky místo dotazu, bude se vzor názvu "*[název tabulky].[ formátu]. [komprese, pokud je nakonfigurována]*", například "MyTable.csv". |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je null, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je null, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete kopírovat soubory tak, jak jsou mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupníi i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte vlastnost **type** ve **formátu** na jednu z těchto hodnot. Další informace naleznete v části [Formát Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formát orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Parkety.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden soubor s určitým názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** se zástupným filtrem. 

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
| type | Vlastnost `type` zdroje aktivity kopírování musí být nastavena na **AzureDataLakeStoreSource**. |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Když `recursive` je nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost `type` jímky aktivity kopírování musí být nastavena na **AzureDataLakeStoreSink**. |Ano |
| copyBehavior | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě je název souboru automaticky generován. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
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

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
