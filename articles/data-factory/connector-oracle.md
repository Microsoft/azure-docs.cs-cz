---
title: Kopírování dat do a z Oracle pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z podporovaných zdrojových úložišť do databáze Oracle nebo z databáze Oracle do podporovaných úložišť jímek pomocí data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 874c685491774e2a318ae0a8b7394945a51b2f7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244508"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopírování dat z a do společnosti Oracle pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuální verze](connector-oracle.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze Oracle a do databáze Oracle. Vychází z [přehledu aktivity kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Oracle je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z databáze Oracle můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Můžete také zkopírovat data z libovolného podporovaného zdrojového úložiště dat do databáze Oracle. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Oracle podporuje:

- Následující verze databáze Oracle:
    - Oracle 18c R1 (18.1) a vyšší
    - Oracle 12c R1 (12.1) a vyšší
    - Oracle 11g R1 (11.1) a vyšší
    - Oracle 10g R1 (10.1) a vyšší
    - Oracle 9i R2 (9.2) a vyšší
    - Oracle 8i R3 (8.1.7) a vyšší
    - Služba Exadata Oracle Database Cloud
- Paralelní kopírování ze zdroje Oracle. Podrobnosti najdete v části [Paralelní kopie z oracle.](#parallel-copy-from-oracle)

> [!Note]
> Proxy server Oracle není podporován.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integrační runtime poskytuje integrovaný ovladač Oracle. Proto není nutné ručně nainstalovat ovladač při kopírování dat z a do Oracle.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Oracle.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Propojená služba Oracle podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **oracle**. | Ano |
| připojovací řetězec | Určuje informace potřebné k připojení k instanci databáze Oracle Database. <br/>Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v úložišti klíčů Azure.](store-credentials-in-key-vault.md) <br><br>**Podporovaný typ připojení**: K identifikaci databáze můžete použít aplikaci **Oracle SID** nebo **Název služby Oracle:**<br>- Pokud používáte SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Pokud používáte název služby:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Pro pokročilé možnosti nativního připojení Oracle můžete přidat položku v [TNSNAMES. Ora](http://www.orafaq.com/wiki/Tnsnames.ora) na serveru Oracle a ve službě ADF Oracle linked service se rozhodnete použít typ připojení Název služby Oracle a nakonfigurovat odpovídající název služby. | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud se zobrazí chyba "ORA-01025: UPI parametr mimo rozsah" a vaše `WireProtocolMode=1` verze Oracle je 8i, přidejte do připojovacího řetězce. Opakujte.

Další vlastnosti připojení, které můžete nastavit v připojovacím řetězci podle případu:

| Vlastnost | Popis | Povolené hodnoty |
|:--- |:--- |:--- |
| Velikost pole |Počet bajtů, které může konektor načíst v jedné síti. `ArraySize=‭10485760‬`Např.<br/><br/>Větší hodnoty zvyšují propustnost snížením počtu načítání dat v síti. Menší hodnoty zvyšují dobu odezvy, protože je menší zpoždění čekání na server k přenosu dat. | Celé číslo od 1 do 4294967296 (4 GB). Výchozí hodnota `60000`je . Hodnota 1 nedefinuje počet bajtů, ale označuje přidělení místa přesně pro jeden řádek dat. |

Chcete-li povolit šifrování v připojení Oracle, máte dvě možnosti:

-   Chcete-li použít **triple-DES encryption (3DES) a Advanced Encryption Standard (AES) ( AES)** na straně serveru Oracle, přejděte na oracle advanced security (OAS) a nakonfigurujte nastavení šifrování. Podrobnosti naleznete v této [dokumentaci k řešení Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Konektor Oracle Application Development Framework (ADF) automaticky vyjedná metodu šifrování tak, aby při navazování připojení k řešení Oracle používala metodu konfigurace v aplikaci OAS.

-   Použití **ssl**:

    1.  Získejte informace o certifikátu SSL. Získejte informace o certifikátu s kódováním (DER) kódovaná odlišenými písmeny (DER) a uložte výstup (----- Begin Certificate ... Koncový certifikát -----) jako textový soubor.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Příklad:** Extrahujte informace o certifikátu z souboru DERcert.cer a potom uložte výstup na soubor cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Sestavení `keystore` nebo `truststore`. Následující příkaz vytvoří `truststore` soubor, s nebo bez hesla, ve formátu PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Příklad:** Vytvořte soubor PKCS12 `truststore` s názvem MyTrustStoreFile s heslem.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Umístěte `truststore` soubor na samoobslužný infračervený počítač. Umístěte například soubor na c:\MyTrustStoreFile.
    4.  V Azure Data Factory nakonfigurujte `TrustStore` / `TrustStorePassword`připojovací řetězec Oracle s `EncryptionMethod=1` odpovídající hodnotou. Například, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Příklad:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: ukládání hesla v trezoru klíčů Azure**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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
## <a name="dataset-properties"></a>Vlastnosti datové sady

Tato část obsahuje seznam vlastností podporovaných datovou sadou Oracle. Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Datové sady](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z a do oracle, `OracleTable`nastavte vlastnost typu datové sady na . Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být `OracleTable`nastavena na . | Ano |
| Schématu | Název schématu. |Ne pro zdroj, Ano pro umyvadlo  |
| tabulka | Název tabulky/zobrazení. |Ne pro zdroj, Ano pro umyvadlo  |
| tableName | Název tabulky/zobrazení se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne pro zdroj, Ano pro umyvadlo |

**Příklad:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností podporovaných zdrojem oracle a jímkou. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle jako zdroj

>[!TIP]
>Chcete-li efektivně načítat data z oracle pomocí dělení dat, další informace z [paralelní kopie od oracle](#parallel-copy-from-oracle).

Chcete-li zkopírovat data z oracle, nastavte `OracleSource`typ zdroje v aktivitě kopírování na . Následující vlastnosti jsou podporovány v části **zdroje aktivity** kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí `OracleSource`být nastavena na . | Ano |
| oracleReaderQuery | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`.<br>Když povolíte rozdělené zatížení, je třeba připojit všechny odpovídající vestavěné parametry oddílu v dotazu. Příklady naleznete [paralelní kopie z Oracle](#parallel-copy-from-oracle) části. | Ne |
| partitionOptions | Určuje možnosti dělení dat použité k načtení dat z oracle. <br>Povolené hodnoty jsou: **None** (výchozí), **PhysicalPartitionsOfTable** , a **DynamicRange**.<br>Pokud je povolena možnost oddílu `None`(to znamená, že ne ), stupeň paralelismu souběžně načítat data z databáze Oracle je řízen [`parallelCopies`](copy-activity-performance.md#parallel-copy) nastavení na aktivitu kopírování. | Ne |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat. <br>Použít, pokud možnost oddílu není `None`. | Ne |
| partitionNames | Seznam fyzických oddílů, které je třeba zkopírovat. <br>Použít, pokud je `PhysicalPartitionsOfTable`možnost oddílu . Pokud použijete dotaz k načtení `?AdfTabularPartitionName` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z oracle.](#parallel-copy-from-oracle) | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu celé číslo,** který bude použit dělením rozsahu pro paralelní kopírování. Pokud není zadán, primární klíč tabulky je automaticky rozpoznán a použit jako sloupec oddílu. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionColumnName` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z oracle.](#parallel-copy-from-oracle) | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionUpbound` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z oracle.](#parallel-copy-from-oracle) | Ne |
| oddíl LowerBound | Minimální hodnota sloupce oddílu zkopírovat data. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionLowbound` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z oracle.](#parallel-copy-from-oracle) | Ne |

**Příklad: kopírování dat pomocí základního dotazu bez oddílu**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle jako umyvadlo

Chcete-li zkopírovat data do společnosti Oracle, `OracleSink`nastavte typ jímky v aktivitě kopírování na . Následující vlastnosti jsou podporovány v části **jímky** aktivity kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování `OracleSink`musí být nastavena na . | Ano |
| writeBatchSize | Vloží data do tabulky SQL, když `writeBatchSize`velikost vyrovnávací paměti dosáhne .<br/>Povolené hodnoty jsou Integer (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Čekací doba pro operaci dávkové vložení k dokončení před časovým oběhem.<br/>Povolené hodnoty jsou Timespan. Příkladem je 00:30:00 (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která má být spuštěna před zápisem dat do oracle v každém spuštění. Tuto vlastnost můžete použít k vyčištění přednačtených dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Paralelní kopie od společnosti Oracle

Konektor Oracle data factory poskytuje integrované dělení dat pro paralelní kopírování dat z oracle. Možnosti dělení dat najdete na kartě **Zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílu](./media/connector-oracle/connector-oracle-partition-options.png)

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy proti zdroji Oracle pro načtení dat pomocí oddílů. Paralelní stupeň je [`parallelCopies`](copy-activity-performance.md#parallel-copy) řízen nastavením aktivity kopírování. Pokud například nastavíte `parallelCopies` na čtyři, data factory současně generuje a spouští čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z databáze Oracle.

Doporučujeme povolit paralelní kopírování s dělením dat, zejména při načítání velkého množství dat z databáze Oracle. Níže jsou navrženy konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je připonuto zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Plné zatížení z velké tabulky s fyzickými oddíly.          | **Možnost oddílu**: Fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a zkopíruje data podle oddílů. |
| Úplné zatížení z velké tabulky, bez fyzických oddílů, zatímco s celočíselným sloupcem pro dělení dat. | **Možnosti oddílu**: Oddíl dynamického rozsahu.<br>**Sloupec oddílu**: Zadejte sloupec použitý k rozdělení dat. Pokud není zadán, použije se sloupec primárního klíče. |
| Načtěte velké množství dat pomocí vlastního dotazu s fyzickými oddíly. | **Možnost oddílu**: Fyzické oddíly tabulky.<br>**Dotaz** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**Název oddílu**: Zadejte názvy oddílů, ze kterých chcete kopírovat data. Pokud není zadán, data factory automaticky detekuje fyzické oddíly v tabulce, kterou jste zadali v datové sadě Oracle.<br><br>Během provádění data factory `?AdfTabularPartitionName` nahradí skutečný název oddílu a odešle společnosti Oracle. |
| Načtěte velké množství dat pomocí vlastního dotazu bez fyzických oddílů, zatímco s celým sloupcem pro dělení dat. | **Možnosti oddílu**: Oddíl dynamického rozsahu.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Sloupec oddílu**: Zadejte sloupec použitý k rozdělení dat. Můžete oddíl proti sloupci s celýdatový typ.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, zda chcete filtrovat proti sloupci oddílu, chcete-li načíst data pouze mezi dolní a horní oblastí.<br><br>Během provádění data factory `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`nahradí `?AdfRangePartitionLowbound` , a skutečné názvy sloupců a rozsahy hodnot pro každý oddíl a odešle společnosti Oracle. <br>Například pokud je sloupec oddílu "ID" nastaven s dolní mezí jako 1 a horní mez jako 80, s paralelní kopií nastavenou jako 4, Data Factory načte data o 4 oddíly. Jejich ID jsou mezi [1,20], [21, 40], [41, 60], a [61, 80]. |

**Příklad: dotaz s fyzickým oddílem**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Příklad: dotaz s oddílem dynamického rozsahu**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapování datových typů pro oracle

Při kopírování dat z a do oracle platí následující mapování. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Oracle | Dočasný datový typ datové továrny |
|:--- |:--- |
| BFILE |Bajt[] |
| Blob |Bajt[]<br/>(podporováno pouze u oracle 10g a vyšší) |
| Char |Řetězec |
| Clob |Řetězec |
| DATE (Datum) |DateTime |
| Float |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| CELÉ ČÍSLO |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| Dlouhé |Řetězec |
| DLOUHÉ RAW |Bajt[] |
| Nchar |Řetězec |
| Nclob |Řetězec |
| Číslo |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| NVARCHAR2 |Řetězec |
| Syrové |Bajt[] |
| ROWID |Řetězec |
| Časové razítko |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÝM PÁSMEM |Řetězec |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |Řetězec |
| NEPODEPSANÉ CELÉ ČÍSLO |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datové typy INTERVAL INTERVAL OD MĚSÍCE a INTERVAL OD DNE DO DRUHÉHO nejsou podporovány.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
