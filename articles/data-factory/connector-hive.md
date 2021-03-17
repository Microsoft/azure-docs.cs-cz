---
title: Kopírování dat z podregistru pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z podregistru do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367023"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Kopírování a transformace dat z podregistru pomocí Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z podregistru. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor podregistru se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z podregistru můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor podregistru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu podregistru jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **podregistr** . | Yes |
| Hostitel | IP adresa nebo název hostitele serveru pro podregistr, oddělený znakem '; ' pro více hostitelů (pouze v případě, že je povoleno serviceDiscoveryMode).  | Yes |
| port | Port TCP, který server podregistru používá k naslouchání klientským připojením. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Yes |
| serverType | Typ serveru podregistru <br/>Povolené hodnoty jsou: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | No |
| thriftTransportProtocol | Transportní protokol, který se má použít ve vrstvě Thrift. <br/>Povolené hodnoty jsou: **Binary**, **SASL**, **http** . | No |
| authenticationType | Metoda ověřování pro přístup k serveru podregistru <br/>Povolené hodnoty jsou: **anonymní**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. Ověřování protokolem Kerberos se teď nepodporuje. | Yes |
| serviceDiscoveryMode | true pro indikaci použití služby ZooKeeper, NEPRAVDA.  | No |
| zooKeeperNameSpace | Obor názvů na ZooKeeper, do kterého se přidají uzly pro podregistr Server 2  | No |
| useNativeQuery | Určuje, zda ovladač používá nativní dotazy HiveQL, nebo je převede do ekvivalentního formátu v HiveQL.  | No |
| username | Uživatelské jméno, které používáte pro přístup k serveru podregistru.  | No |
| heslo | Heslo odpovídající uživateli Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| httpPath | Částečná adresa URL odpovídající serveru podregistru.  | No |
| enableSsl | Určuje, jestli se připojení k serveru šifrují pomocí protokolu TLS. Výchozí hodnota je False.  | No |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority pro ověření serveru při připojení přes protokol TLS. Tuto vlastnost lze nastavit pouze při použití protokolu TLS v místním prostředí IR. Výchozí hodnota je soubor cacerts. pem nainstalovaný s IR.  | No |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | No |
| allowHostNameCNMismatch | Určuje, jestli se má při připojování přes protokol TLS vyžadovat, aby název certifikátu TLS/SSL vydaný certifikační autoritou odpovídal názvu hostitele serveru. Výchozí hodnota je False.  | No |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |
| storageReference | Odkaz na propojenou službu účtu úložiště, který se používá pro pracovní data v toku mapování dat. To se vyžaduje jenom v případě, že se při mapování toku dat používá propojená služba podregistr. | No |

**Příklad:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou podregistru.

Chcete-li kopírovat data z podregistru, nastavte vlastnost Type datové sady na **HiveObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **HiveObject** . | Yes |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky včetně části schématu Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table` . | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem podregistru.

### <a name="hivesource-as-source"></a>HiveSource as source

Chcete-li kopírovat data z podregistru, nastavte typ zdroje v aktivitě kopírování na **HiveSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **HiveSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Konektor podregistru je v datových tocích mapování toků podporován jako [vložený zdroj datové sady](data-flow-source.md#inline-datasets) . Číst pomocí dotazu nebo přímo z tabulky podregistru v HDInsight. Data z podregistru se připravují v účtu úložiště jako soubory Parquet, než se transformují jako součást toku dat. 

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem podregistru. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Uložení | Úložiště musí být `hive` | ano |  `hive` | store | 
| Formát | Bez ohledu na to, zda čtete z tabulky nebo dotazu | ano | `table` nebo `query` | formát |
| Název schématu | Při čtení z tabulky se jedná o schéma zdrojové tabulky. |  Ano, pokud je formát `table` | Řetězec | schemaName |
| Název tabulky | Pokud je čtena z tabulky, název tabulky |   Ano, pokud je formát `table` | Řetězec | tableName |
| Dotaz | Pokud je formát `query` , zdrojový dotaz na propojené službě podregistru | Ano, pokud je formát `query` | Řetězec | query |
| Připravené | Tabulka podregistru bude vždycky připravená. | ano | `true` | připravené |
| Kontejner úložiště | Kontejner úložiště, který se používá k přípravě dat před čtením z podregistru nebo zápisu do podregistru. Cluster podregistru musí mít přístup k tomuto kontejneru. | ano | Řetězec | storageContainer |
| Pracovní databáze | Schéma nebo databáze, ke kterým má uživatelský účet zadaný v propojené službě přístup Slouží k vytvoření externích tabulek během přípravy a v následně vyřazení | ne | `true` nebo `false` | stagingDatabaseName |
| Skripty pre-SQL | Kód SQL, který se má spustit v tabulce podregistru před čtením dat | ne | Řetězec | preSQLs |

#### <a name="source-example"></a>Zdrojový příklad

Níže je uveden příklad konfigurace zdroje podregistru:

![Příklad zdroje podregistru](media/data-flow/hive-source.png "[Příklad zdroje podregistru")

Tato nastavení se převádějí do následujícího skriptu toku dat:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Známá omezení

* Komplexní typy, jako jsou pole, mapy, struktury a sjednocení, se pro čtení nepodporují. 
* Konektor pro podregistr podporuje jenom tabulky podregistru ve službě Azure HDInsight verze 4,0 nebo vyšší (Apache Hive 3.1.0).

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
