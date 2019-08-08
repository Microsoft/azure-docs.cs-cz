---
title: Kopírování dat z a do Oracle pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z podporovaných úložišť zdrojů do databáze Oracle nebo z Oracle na podporovaná úložiště jímky pomocí Data Factory.
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
ms.openlocfilehash: 0a71c7ffe9040c3002b1f5378ce298a047554b15
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640193"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopírování dat z a do Oracle pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuální verze](connector-oracle.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do databáze Oracle. Sestaví se na [Přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze Oracle můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do databáze Oracle. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Oracle podporuje:

- Následující verze databáze Oracle:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)
- Kopírování dat pomocí ověřování Basic nebo OID.
- Paralelní kopírování ze zdroje Oracle. Podrobnosti najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Proxy server Oracle se nepodporuje.

## <a name="prerequisites"></a>Požadavky

Chcete-li kopírovat data z a do databáze Oracle, která není veřejně přístupná, je nutné nastavit [prostředí Integration runtime](create-self-hosted-integration-runtime.md)v místním prostředí. Modul runtime integrace poskytuje integrovaný ovladač Oracle. Proto nemusíte při kopírování dat z a do Oracle ručně instalovat ovladač.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Oracle.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Propojená služba Oracle podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na hodnotu **Oracle**. | Ano |
| connectionString | Určuje informace potřebné pro připojení k instanci Oracle Database. <br/>Označte toto pole jako `SecureString` pro bezpečné uložení v Data Factory. Můžete také vložit heslo do Azure Key Vault a z připojovacího řetězce si `password` vyžádat konfiguraci. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje uložené v Azure Key Vault](store-credentials-in-key-vault.md) . <br><br>**Podporovaný typ připojení**: K identifikaci vaší databáze můžete použít název **Oracle SID** nebo **Oracle Service** :<br>– Pokud používáte identifikátor SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Pokud použijete název služby:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít místní prostředí Integration runtime nebo prostředí Azure Integration runtime (Pokud je úložiště dat veřejně přístupné). Pokud tento parametr nezadáte, použije tato vlastnost výchozí prostředí Azure Integration runtime. |Ne |

>[!TIP]
>Pokud se zobrazí chyba, "ORA-01025: Parametr UPI je mimo rozsah a vaše verze Oracle je 8i, přidejte `WireProtocolMode=1` ho do svého připojovacího řetězce. Pak to zkuste znovu.

Pokud chcete povolit šifrování u připojení Oracle, máte dvě možnosti:

-   Pokud chcete použít **šifrování 3DES (Triple-DES) a standard AES (Advanced Encryption Standard) (AES)** , na straně serveru Oracle přejít na server Oracle Advanced Security (OAS) a nakonfigurovat nastavení šifrování. Podrobnosti najdete v [dokumentaci k systému Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Konektor pro vývoj aplikací Oracle (ADF) automaticky vyjednává metodu šifrování, aby používala tu, kterou nakonfigurujete v OAS při navazování připojení k Oracle.

-   Použití **protokolu SSL**:

    1.  Získejte informace o certifikátu SSL. Získejte informace o certifikátu SSL s kódováním kódování DER (DER) a uložte výstup (-----zahájit certifikát... Ukončit certifikát-----) jako textový soubor.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Příklad:** Extrahujte informace o certifikátu z DERcert. cer a pak výstup uložte do souboru CERT. txt.

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
    
    2.  Sestavte `truststore`nebo. `keystore` Následující příkaz vytvoří `truststore` soubor s heslem ve formátu PKCS-12 nebo bez něj.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Příklad:** Vytvořte soubor PKCS12 `truststore` s názvem MyTrustStoreFile s heslem.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore` Umístěte soubor na místně hostovaný počítač IR. Soubor umístěte například na C:\MyTrustStoreFile.
    4.  V Azure Data Factory nakonfigurujte připojovací `EncryptionMethod=1` řetězec Oracle pomocí a odpovídající `TrustStore` / `TrustStorePassword`hodnoty. Například, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

**Příklad: uložení hesla v Azure Key Vault**

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

V této části najdete seznam vlastností podporovaných datovou sadou Oracle. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z a do Oracle, nastavte vlastnost typ datové sady na `OracleTable`. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavena na `OracleTable`hodnotu. | Ano |
| tableName |Název tabulky v databázi Oracle, na kterou odkazuje propojená služba. | Ano |

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

V této části najdete seznam vlastností podporovaných zdrojem a jímkou Oracle. Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="oracle-as-a-source-type"></a>Oracle jako typ zdroje

> [!TIP]
>
> Pokud chcete data z Oracle načítat efektivně pomocí dělení dat, přečtěte si téma [paralelní kopírování od Oracle](#parallel-copy-from-oracle).

Chcete-li kopírovat data z Oracle, nastavte typ zdroje v aktivitě kopírování `OracleSource`na. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavena na `OracleSource`hodnotu. | Ano |
| oracleReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`.<br>Pokud povolíte rozdělené zatížení, musíte v dotazu připojit všechny odpovídající předdefinované parametry oddílu. Příklady najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z Oracle. <br>Povolené hodnoty jsou: **Žádné** (výchozí), **PhysicalPartitionsOfTable** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (tj. ne `None`), [`parallelCopies`](copy-activity-performance.md#parallel-copy) nakonfigurujte také nastavení aktivity kopírování. To určuje souběžný stupeň pro souběžné načítání dat z databáze Oracle. Můžete například nastavit hodnotu 4. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud možnost partition není `None`. | Ne |
| partitionNames | Seznam fyzických oddílů, které je třeba zkopírovat. <br>Použijte, pokud je `PhysicalPartitionsOfTable`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfTabularPartitionName` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition. <br>Použijte, pokud je `DynamicRange`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionColumnName` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionUpbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionLowbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |

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

### <a name="oracle-as-a-sink-type"></a>Oracle jako typ jímky

Chcete-li kopírovat data do Oracle, nastavte typ jímky v aktivitě kopírování `OracleSink`na. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavena na `OracleSink`. | Ano |
| writeBatchSize | Při dosažení `writeBatchSize`velikosti vyrovnávací paměti vloží data do tabulky SQL.<br/>Povolené hodnoty jsou celé číslo (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou TimeSpan. Příklad je 00:30:00 (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do Oracle při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | Ne |

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

Konektor Data Factory Oracle nabízí integrované vytváření oddílů dat pro kopírování dat z Oracle paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-oracle/connector-oracle-partition-options.png)

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy na zdroj Oracle a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení. Každý dotaz načte část dat z databáze Oracle.

Je vhodné povolit paralelní kopírování pomocí dělení dat, zejména při načítání velkého množství dat z databáze Oracle. Následují Doporučené konfigurace pro různé scénáře:

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.          | **Možnost oddílu**: Fyzické oddíly tabulky <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. |
| Úplné načtení z velké tabulky bez fyzických oddílů spolu se sloupcem typu Integer pro dělení dat. | **Možnosti oddílu**: Dynamický oddíl rozsahu.<br>**Sloupec oddílu**: Zadejte sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec primárního klíče. |
| Pomocí vlastního dotazu s fyzickými oddíly načtěte velké množství dat. | **Možnost oddílu**: Fyzické oddíly tabulky<br>**Dotaz**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Název oddílu**: Zadejte názvy oddílů, ze kterých se mají kopírovat data. Pokud není zadaný, Data Factory automaticky detekuje fyzické oddíly v tabulce, kterou jste zadali v datové sadě Oracle.<br><br>Během provádění Data Factory nahradí `?AdfTabularPartitionName` skutečným názvem oddílu a pošle je do Oracle. |
| Načtěte velké množství dat pomocí vlastního dotazu bez fyzických oddílů a zároveň se sloupcem typu Integer pro dělení dat. | **Možnosti oddílu**: Dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílů, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` skutečný název sloupce a rozsahy hodnot pro každý oddíl a odesílá je do Oracle. <br>Pokud je například sloupec oddílu "ID" nastaven s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

**Příklad: dotazování pomocí fyzického oddílu**

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

**Příklad: dotaz s dynamickým oddílem rozsahu**

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

## <a name="data-type-mapping-for-oracle"></a>Mapování datových typů pro Oracle

Při kopírování dat z a do Oracle platí následující mapování. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Oracle | Data Factory dočasné datový typ |
|:--- |:--- |
| BFILE |Byte[] |
| PŘÍZNAKY |Byte[]<br/>(podporuje se jenom v Oracle 10g a vyšších verzích) |
| CHAR |Řetězec |
| CLOB |Řetězec |
| DATE |Datetime |
| FLOAT |Decimal, String (Pokud přesnost > 28) |
| INTEGER |Decimal, String (Pokud přesnost > 28) |
| DLOUHOU |Řetězec |
| LONG RAW |Byte[] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| NUMBER |Decimal, String (Pokud přesnost > 28) |
| NVARCHAR2 |Řetězec |
| ZÍSKÁNÍ |Byte[] |
| ROWID |Řetězec |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Řetězec |
| TIMESTAMP WITH TIME ZONE |Řetězec |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datový typ INTERVAL v roce do měsíce a druhý den v sekundách se nepodporuje.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
