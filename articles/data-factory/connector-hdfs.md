---
title: Kopírovat data z HDFS pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z HDFS zdroje – místních i cloudových úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: jingwang
ms.openlocfilehash: 161683b48ae66edc621981142c538f8bce44a2b6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352168"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopírovat data z HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-hdfs-connector.md)
> * [Aktuální verze](connector-hdfs.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z HDFS. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z HDFS do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor HDFS podporuje:

- Kopírování souborů pomocí **Windows** (Kerberos) nebo **anonymní** ověřování.
- Kopírování souborů pomocí **webhdfs** protokol nebo **integrované DistCp** podporovat.
- Kopírování souborů jako-je nebo analýze a generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete zkopírovat data z HDFS, který není veřejně přístupná, musíte nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](concepts-integration-runtime.md) článku se dozvíte podrobnosti.

> [!NOTE]
> Ujistěte se, že se prostředí Integration Runtime přístup k **všechny** [název uzlu serveru]: [název uzlu port] a [data uzlu servery]: [data uzlu port] clusteru Hadoop. Výchozí [název uzlu port] je 50070 a výchozí [data uzlu port] je 50075.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní HDFS.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

HDFS propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Hdfs**. | Ano |
| url |Adresa URL HDFS |Ano |
| authenticationType. | Povolené hodnoty jsou: **Anonymní**, nebo **Windows**. <br><br> Použití **ověřování protokolem Kerberos** konektor HDFS najdete v tématu [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| uživatelské jméno |Ověřování uživatelského jména pro Windows. Pro ověřování protokolem Kerberos, zadejte `<username>@<domain>.com`. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování Windows. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad: použití anonymní ověřování**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: použití ověřování Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje HDFS datové sady.

Ke zkopírování dat z HDFS, nastavte vlastnost typ datové sady na **sdílení souborů**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr zástupných znaků není podporován. Příklad: / podsložce složky / |Ano |
| fileName |  **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř. |Ne |
| formát | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků.

**Příklad:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

Ke zkopírování dat z HDFS, nastavte typ zdroje v aktivitě kopírování do **HdfsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **HdfsSource** |Ano |
| rekurzivní | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| distcpSettings | Vlastnosti skupiny při použití HDFS DistCp. | Ne |
| resourceManagerEndpoint | Koncový bod správce prostředků Yarn | Ano, pokud pomocí DistCp |
| tempScriptPath | Cestu ke složce pro ukládání dočasného DistCp příkazový skript. Soubor skriptu vygenerované službou Data Factory a po dokončení úlohy kopírování se odebere. | Ano, pokud pomocí DistCp |
| distcpOptions | Příkaz DistCp k dispozici další možnosti. | Ne |

**Příklad: HDFS zdroje v aktivitou kopírování pomocí uvolnění z paměti**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Další informace o tom, jak použití DistCp ke kopírování dat z HDFS efektivně v další části.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Použití DistCp ke kopírování dat z HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je Hadoop nativní nástroj příkazového řádku provést distribuované kopírování v clusteru Hadoop. Při spuštění příkaz Distcp, zobrazí se seznam nejprve všechny soubory chcete zkopírovat, vytvořit několik Map úloh na clusteru Hadoop a každá úloha mapa bude do binární kopie ze zdroje do jímky.

Použití DistCp ke kopírování souborů jako podpora činnosti zkopírujte-do objektů Blob v Azure (včetně [fázovaného kopírování](copy-activity-performance.md) nebo Azure Data Lake Store, v takovém případě ji můžete plně využít váš cluster power místo spouštění na modul Integration Runtime . Zejména v případě, že je velice mocný nástroj váš cluster bude poskytovat vyšší propustnost kopírování. Na základě vaší konfigurace v Azure Data Factory, aktivita kopírování automaticky vytvořit příkaz distcp, odeslat ke clusteru Hadoop a monitorovat stav kopírování.

### <a name="prerequsites"></a>Prerequsites

Použití DistCp ke kopírování souborů jako-je z HDFS do objektů Blob v Azure (včetně dvoufázové instalace kopie) nebo Azure Data Lake Store, ujistěte se, že Hadoop cluster splňuje následující požadavky:

1. Povolené služby MapReduce a Yarn.
2. Je yarn verze 2.5 nebo vyšší.
3. HDFS server je integrován s vaší cílové úložiště dat – objektů Blob v Azure nebo Azure Data Lake Store:

    - Systém souborů Azure Blob je nativně podporuje od verze Hadoop 2.7. Stačí zadat soubor jar cestu v konfiguraci env Hadoop.
    - Systém souborů Azure Data Lake Store je zabalený od Hadoop 3.0.0-alpha1. Pokud váš cluster Hadoop je nižší než verze, budete muset ručně importovat ADLS související jar balíčky (azure-datalake-store.jar) do clusteru ze [tady](https://hadoop.apache.org/releases.html)a zadejte cestu jar v konfiguraci env Hadoop.

4. Příprava dočasnou složku v HDFS. Tuto dočasnou složku slouží k uložení DistCp skript prostředí, tak bude zabírat úrovni KB místa.
5. Ujistěte se, že uživatelský účet k dispozici v propojené službě HDFS mít oprávnění k) odeslat aplikaci Yarn; (b) mají oprávnění k vytváření podsložek, čtení a zápis souborů v rámci nad dočasnou složku.

### <a name="configurations"></a>Konfigurace

Níže je příklad konfigurace aktivity kopírování kopíruje data z HDFS do objektů Blob v Azure pomocí DistCp:

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Používat ověřování protokolem Kerberos pro konektor HDFS

Existují dvě možnosti, jak nastavit v místním prostředí tak, aby používala ověřování protokolu Kerberos v konektoru HDFS. Můžete zvolit, že je lépe vyhovuje vašemu případu.
* Option 1: [Připojte se k počítači modul Integration Runtime ve sféře Kerberos](#kerberos-join-realm)
* Option 2: [Povolit vzájemné důvěry mezi Windows doménu a sféru protokolu Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: Připojte se k počítači modul Integration Runtime ve sféře Kerberos

#### <a name="requirements"></a>Požadavky

* Počítač modul Integration Runtime je potřeba připojit k sféry Kerberos a nejde připojit k libovolné doméně Windows.

#### <a name="how-to-configure"></a>Postup konfigurace

**Na počítači modul Integration Runtime:**

1.  Spustit **Ksetup** nástroj a nakonfigurujte server KDC protokolu Kerberos a sféry.

    Na počítači musí být nakonfigurován jako člen pracovní skupiny, protože Sféra Kerberos se liší od domény Windows. Toho lze dosáhnout nastavením sféry Kerberos a přidání serveru služby KDC následujícím způsobem. Nahraďte *REALM.COM* s vlastním příslušných sféra podle potřeby.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítač po spuštění těchto příkazů 2.

2.  Ověřte konfiguraci pomocí **Ksetup** příkazu. Výstup by měl být jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Ve službě Azure Data Factory:**

* Nakonfigurujte pomocí konektoru HDFS **ověřování Windows** společně s vaší hlavní název Kerberos a heslo pro připojení ke zdroji dat HDFS. Zkontrolujte [propojená služba HDFS vlastnosti](#linked-service-properties) části na podrobnosti o konfiguraci.

### <a name="kerberos-mutual-trust"></a>Option 2: Povolit vzájemné důvěry mezi Windows doménu a sféru protokolu Kerberos

#### <a name="requirements"></a>Požadavky

*   Na počítači modul Integration Runtime musí připojit k doméně Windows.
*   Budete potřebovat oprávnění k aktualizaci nastavení řadiče domény.

#### <a name="how-to-configure"></a>Postup konfigurace

> [!NOTE]
> Nahraďte REALM.COM a AD.COM v následujícím kurzu s příslušnými sféry a řadič domény, podle potřeby.

**Na serveru služby KDC:**

1.  Upravit konfiguraci služby KDC v **krb5.conf** souboru chcete, aby služba KDC důvěřovat odkazující na následující šablonu konfigurace domény Windows. Ve výchozím nastavení, se nachází v konfiguraci **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Restartujte** služba KDC po konfiguraci.

2.  Příprava objekt zabezpečení s názvem **krbtgt/REALM.COM@AD.COM** na serveru služby KDC pomocí následujícího příkazu:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  V **hadoop.security.auth_to_local**  konfigurace služby HDFS přidejte `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na řadiči domény:**

1.  Spusťte následující příkaz **Ksetup** příkazy, které chcete přidat položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Navázání vztahu důvěryhodnosti z domény Windows do sféry Kerberos. [heslo] je heslo pro objekt **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus použitý v protokolu Kerberos.

    1. Přejděte do správce serveru > Správa zásad skupiny > domény > objekty zásad skupiny > výchozí nebo zásady aktivní domény a upravit.

    2. V **Editor správy zásad skupiny** automaticky otevíraném okně, přejděte na konfigurace počítače > zásady > Nastavení Windows > Nastavení zabezpečení > Místní zásady > Možnosti zabezpečení a nakonfigurujte **sítě zabezpečení: Konfigurovat typy šifrování povolené pro protokol Kerberos**.

    3. Vyberte šifrovací algoritmus, který chcete použít při připojení k služby KDC. Běžně můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfiguračním pro protokol Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Použití **Ksetup** příkazu zadejte šifrovací algoritmus, který se má použít na konkrétní SFÉRY.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvoření mapování mezi účet domény a zabezpečení protokolu Kerberos, aby bylo možné používat instanční objekt pomocí protokolu Kerberos v doméně Windows.

    1. Spuštění nástroje pro správu > **Active Directory Users and Computers**.

    2. Konfigurace rozšířených funkcí kliknutím **zobrazení** > **pokročilé funkce**.

    3. Najděte účet, ke kterému chcete vytvořit mapování a klikněte pravým tlačítkem na zobrazení **mapování názvů** > klikněte na tlačítko **Kerberos názvy** kartu.

    4. Přidáte objekt zabezpečení ze sféry.

        ![Identita zabezpečení mapy](media/connector-hdfs/map-security-identity.png)

**Na počítači modul Integration Runtime:**

* Spusťte následující příkaz **Ksetup** příkazy, které chcete přidat položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Ve službě Azure Data Factory:**

* Nakonfigurujte pomocí konektoru HDFS **ověřování Windows** společně se váš účet domény nebo objekt zabezpečení protokolu Kerberos pro připojení ke zdroji dat HDFS. Zkontrolujte [propojená služba HDFS vlastnosti](#linked-service-properties) části na podrobnosti o konfiguraci.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
