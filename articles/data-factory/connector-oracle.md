---
title: Kopírování dat do a z Oracle pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště pro databáze Oracle nebo Oracle do jímky podporovaných úložišť pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509232"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopírování dat z a do databáze Oracle pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
> * [Verze 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuální verze](connector-oracle.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z databáze Oracle ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze Oracle do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat k databázi Oracle. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Oracle podporuje:

- Následující verze databáze Oracle:
  - R1 Oracle 12c (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10,1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)
- Kopírování dat pomocí **základní** nebo **OID** ověření.
- Paralelní kopírování ze zdroje Oracle. Zobrazit [paralelní kopírování od Oraclu](#parallel-copy-from-oracle) část s podrobnostmi.

> [!Note]
> Nepodporuje proxy serveru Oracle.

## <a name="prerequisites"></a>Požadavky

Ke kopírování dat z databáze Oracle, který není veřejně přístupná, budete muset nastavit modul Integration Runtime. Další informace o modulu integration runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md). Prostředí integration runtime poskytuje integrované ovladače Oracle. Proto není nutné ručně nainstalovat ovladač při kopírování dat z a do databáze Oracle.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Oracle.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Oracle propojenou službu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **Oracle**. | Ano |
| connectionString | Určuje informace potřebné pro připojení k instanci databáze Oracle. <br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Heslo můžete také vložit do služby Azure Key Vault a o přijetí změn `password` konfigurace z připojovacího řetězce. Podívejte se na následující ukázky a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. <br><br>**Podporovaný typ připojení**: Můžete použít **Oracle SID** nebo **název služby Oracle** k identifikaci vaší databáze:<br>– Pokud používáte SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Pokud používáte název služby: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud dosáhnete o tom, že chyba "ORA-01025: UPI parametr je mimo rozsah"a systém Oracle je verze 8i, přidejte `WireProtocolMode=1` připojovací řetězec a zkuste to znovu.

**Chcete povolit šifrování na připojení Oracle**, máte dvě možnosti:

1.  Chcete-li použít **Triple-DES šifrování (3DES) a Advanced Encryption (Standard AES)** , na straně serveru Oracle, přejděte k Oracle rozšířené zabezpečení (OAS) a konfigurovat nastavení šifrování, přečtěte si podrobnosti o [tady](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Konektor ADF Oracle automatické způsob šifrování použít ten, který konfigurujete v OAS při navazování připojení k systému Oracle.

2.  Chcete-li použít **SSL**, postupujte podle následujících kroků:

    1.  Získejte informace o certifikátu SSL. Získejte informace o kódování DER certifikátu z vašeho certifikátu protokolu SSL a uložte výstup (---začít certifikát... Ukončit certifikát---) jako textový soubor.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Příklad:** extrahovat informace certifikátu z DERcert.cer; uložte výstup cert.txt

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
    
    2.  Vytvářejte úložiště klíčů nebo truststore. Následující příkaz vytvoří soubor truststore s nebo bez hesla ve formátu PKCS č. 12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Příklad:** vytvoří PKCS12 truststore soubor s názvem MyTrustStoreFile s heslem

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Umístěte soubor truststore na počítači místní prostředí IR, např. na C:\MyTrustStoreFile.
    4.  Ve službě ADF, nakonfigurujte připojovacím řetězci Oracle s `EncryptionMethod=1` a odpovídající `TrustStore` / `TrustStorePassword`hodnoty, třeba `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Příklad:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: ukládání hesel ve službě Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datové sady Oracle.

Pro kopírování dat z a do databáze Oracle, nastavte vlastnost typ datové sady na **OracleTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **OracleTable**. | Ano |
| tableName |Název tabulky v databázi Oracle, který odkazuje propojenou službu. | Ano |

**Příklad:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Oracle zdroje a jímky.

### <a name="oracle-as-a-source-type"></a>Oracle jako typ zdroje

> [!TIP]
>
> Další informace z [paralelní kopírování Oraclu](#parallel-copy-from-oracle) část o tom, jak načíst data z efektivně pomocí dělení dat Oracle.

Ke zkopírování dat z Oracle, nastavte typ zdroje v aktivitě kopírování do **OracleSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **OracleSource**. | Ano |
| oracleReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`.<br>Když povolíte dělené zatížení, budete muset připojit odpovídající integrované oddílu parametry v dotazu. Podívejte se na příklady v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu. | Ne |
| partitionOptions | Určuje možnosti používané k načtení dat z Oracle pro dělení dat. <br>Povolit hodnoty jsou: **Žádný** (výchozí), **PhysicalPartitionsOfTable** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (ne ' None'), také nakonfigurujte **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** nastavení aktivity kopírování, například jako 4, který určuje paralelní míru současně načíst data z Oraclu databáze. | Ne |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat. <br>Použít, když je možnost rozdělení `None`. | Ne |
| partitionNames | Seznam fyzických oddílů, které se musí zkopírovat. <br>Použít, když je možnost rozdělení `PhysicalPartitionsOfTable`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfTabularPartitionName` v klauzuli WHERE. Viz příklad v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu. | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu celé číslo** , který se použije rozdělením rozsah pro paralelní kopírování. Pokud není zadán, bude primární klíč tabulky automaticky zjistil a použít jako sloupec oddílu. <br>Použít, když je možnost rozdělení `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfRangePartitionColumnName` v klauzuli WHERE. Viz příklad v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu. | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu mohli zkopírovat data. <br>Použít, když je možnost rozdělení `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfRangePartitionUpbound` v klauzuli WHERE. Viz příklad v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu. | Ne |
| PartitionLowerBound | Minimální hodnota sloupce oddílu mohli zkopírovat data. <br>Použít, když je možnost rozdělení `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfRangePartitionLowbound` v klauzuli WHERE. Viz příklad v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu. | Ne |

**Příklad: kopírování dat pomocí základního dotazu bez oddílů**

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

Další příklady naleznete v [paralelní kopírování Oraclu](#parallel-copy-from-oracle) oddílu.

### <a name="oracle-as-a-sink-type"></a>Oracle jako typ jímky

Pro kopírování dat do databáze Oracle, nastavte typ jímky v aktivitě kopírování do **třídě OracleSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **třídě OracleSink**. | Ano |
| writeBatchSize | Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou Integer (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené jsou hodnoty Timespan. Příkladem je 00:30:00 (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisu dat do Oracle při každém spuštění. Tuto vlastnost můžete použít k vyčištění dat předem. | Ne |

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

## <a name="parallel-copy-from-oracle"></a>Paralelní kopírování z Oracle

Konektor Oracle data factory poskytuje předdefinované datové dělení ke zkopírování dat z Oracle paralelně s vynikajícím výkonem. Zjistíte, že možnosti dělení dat v aktivitě kopírování -> zdroje Oracle:

![Možnosti oddílu](./media/connector-oracle/connector-oracle-partition-options.png)

Když povolíte dělené kopírování, služby data factory spouští paralelní dotazy Oracle zdroj k načtení dat oddíly. Paralelní míra je nakonfigurovaná a ovládat **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** nastavení aktivity kopírování. Například pokud nastavíte `parallelCopies` jako čtyři, současně vytvoří objekt pro vytváření dat a čtyři spouští dotazy založené na zadaný oddíl možnosti a nastavení, všechny části načítání dat z databáze Oracle.

Byly navrženy povolit paralelní kopírování s daty rozdělení do oddílů, zejména v případě, že načtete velké množství dat z databáze Oracle. Tady jsou navrhované konfigurace pro různé scénáře:

| Scénář                                                     | Doporučené nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly          | **Možnost Rozdělit**: Fyzické oddíly tabulky. <br><br/>Během provádění data Factory automaticky zjistit fyzické oddíly a zkopírovat data oddíly. |
| Úplné načtení z velké tabulky bez fyzických oddílů, zatímco s sloupec celých čísel pro dělení dat | **Možnosti oddílu**: Dynamický rozsah oddílu.<br>**Sloupec oddílu**: Zadejte sloupec použitý k dělení dat. Pokud není zadaný, primární klíčový sloupec se používá. |
| Načtení velkého objemu dat pomocí vlastního dotazu pod s fyzickými oddíly | **Možnost Rozdělit**: Fyzické oddíly tabulky.<br>**Dotaz**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Název oddílu**: Zadejte názvy oddílů pro kopírování dat z. Pokud není zadán, ADF automaticky zjistí fyzické oddíly pro tabulku, kterou jste zadali v datové sadě Oracle.<br><br>Během provádění, nahraďte objekt pro vytváření dat `?AdfTabularPartitionName` s názvem skutečné oddílu a odeslat Oracle. |
| Načtení velkého objemu dat pomocí vlastního dotazu pod bez fyzických oddílů při s sloupec celých čísel pro dělení dat | **Možnosti oddílu**: Dynamický rozsah oddílu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec použitý k dělení dat. Můžete dělit na sloupec s datovým typem celé číslo.<br>**Oddíl horní mez** a **oddílu dolní mez**: Určete, jestli chcete filtrovat proti sloupec oddílu pouze načíst data mezi horní a dolní rozsahu.<br><br>Během provádění, nahraďte objekt pro vytváření dat `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, a `?AdfRangePartitionLowbound` skutečný název sloupce a hodnotu rozsahy pro každou oddílů a odeslat do databáze Oracle. <br>Například pokud vaše sloupce oddílu "ID" nastavit s dolní mez jako 1 a horní mez jako 80 sadou paralelní kopie jako 4, ADF načíst data 4 oddíly s ID mezi [1,20], [21, 40], [41, 60] a [61, 80]. |

**Příklad: dotaz s fyzický oddíl**

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

**Příklad: dotaz s dynamický rozsah oddílu**

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

## <a name="data-type-mapping-for-oracle"></a>Mapování pro Oracle datového typu

Při kopírování dat z a do databáze Oracle, se používají následující mapování z datových typů Oracle do služby Data Factory dočasné datových typů. Další informace o jak aktivita kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Oracle datového typu | Data Factory dočasné datový typ |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(podporováno pouze pro Oracle 10g a vyšší) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (Pokud přesnost > 28) |
| INTEGER |Decimal, String (Pokud přesnost > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (Pokud přesnost > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Za druhé datové typy na INTERVALU roku a měsíce a dne do INTERVALU nejsou podporovány.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
