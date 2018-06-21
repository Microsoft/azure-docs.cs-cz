---
title: Kopírovat data z HDFS pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z cloudu nebo na místní zdroj HDFS do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 2c25bff60adc1f3d462cc6a437eab0d46f9fa413
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287867"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopírování dat z HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-hdfs-connector.md)
> * [Verze 2 – Preview](connector-hdfs.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z HDFS. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [HDFS konektoru V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z HDFS do úložiště dat žádné podporované jímky. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor HDFS podporuje:

- Kopírování souborů pomocí **Windows** (Kerberos) nebo **anonymní** ověřování.
- Kopírování souborů pomocí **webhdfs** protokolu nebo **předdefinované DistCp** podporovat.
- Kopírování souborů jako-je nebo analýza nebo generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

Ke kopírování dat ze HDFS, který není veřejně přístupný, musíte nastavit Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](concepts-integration-runtime.md) článku se dozvíte podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování entit služby Data Factory, které jsou specifické pro HDFS.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro HDFS propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **Hdfs**. | Ano |
| url |Adresa URL HDFS |Ano |
| authenticationType. | Povolené hodnoty jsou: **anonymní**, nebo **Windows**. <br><br> Použít **ověřování protokolem Kerberos** HDFS konektor, najdete v části [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| Uživatelské jméno |Ověřování uživatelského jména pro systém Windows. Pro ověřování pomocí protokolu Kerberos, zadejte `<username>@<domain>.com`. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad: pomocí anonymní ověřování**

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

**Příklad: pomocí ověřování systému Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu HDFS.

Chcete-li zkopírovat data z HDFS, nastavte vlastnost typu datové sady, která **sdílení souborů**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **sdílení souborů** |Ano |
| folderPath | Cesta ke složce. Zástupný filtr není podporován. Příklad: složku nebo podsložku / |Ano |
| fileName |  **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` abyste se vyhnuli, pokud jejich název zástupných znaků nebo tento řídicí znak uvnitř. |Ne |
| Formát | Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat soubory s konkrétním formátu, jsou podporovány následující typy souboru formátu: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátu](supported-file-formats-and-compression-codecs.md#orc-format), a [Parquet formát](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro scénář binární kopie) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s částí složky a **fileName** s názvem souboru.<br>Pokud chcete zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s částí složky a **fileName** s filtrem zástupný znak.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

Pokud chcete zkopírovat data z HDFS, nastavte typ zdroje v aktivitě kopírování do **HdfsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **HdfsSource** |Ano |
| Rekurzivní | Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. Poznámka: když rekurzivní nastavena na hodnotu true a jímka je na základě souborů úložiště, prázdné složky nebo dílčí-folder nebudou zkopírovat nebo vytvořit v jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| distcpSettings | Skupina vlastností při použití HDFS DistCp. | Ne |
| resourceManagerEndpoint | Koncový bod Yarn ResourceManager | Ano, při použití DistCp |
| tempScriptPath | Cesty používá k ukládání dočasného DistCp příkazu skriptu. Soubor skriptu je generován objekt pro vytváření dat a bude odebrána po dokončení úlohu kopírování. | Ano, při použití DistCp |
| distcpOptions | Další možnosti poskytnuté DistCp příkaz. | Ne |

**Příklad: HDFS zdroje v aktivitou kopírování pomocí uvolnění**

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

Další informace o tom, jak použití DistCp zkopírovat data z HDFS efektivně z v další části.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Použití DistCp zkopírovat data z HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je Hadoop nativní nástroj příkazového řádku udělat distribuované kopírování v clusteru Hadoop. Při spuštění příkazu Distcp, bude nejprve seznamu všechny soubory, které chcete kopírovat, vytvořte několik úloh mapy do clusteru Hadoop a každá úloha mapy bude do binární kopie ze zdroje na jímky.

Zkopírujte aktivity podpora použití DistCp kopírovat soubory jako-je do Azure Blob (včetně [připravený kopie](copy-activity-performance.md) nebo Azure Data Lake Store, v takovém případě ji můžete plně využít váš cluster power namísto spuštění na modulu Runtime Self-hosted integrace . Lepší propustnosti kopie poskytne hlavně v případě, že je velice mocný nástroj vašeho clusteru. V závislosti na vaší konfiguraci v Azure Data Factory aktivity kopírování automaticky vytvořit příkaz distcp, odeslání do clusteru Hadoop a sledovat stav kopírování.

### <a name="prerequsites"></a>Prerequsites

Použití DistCp ke kopírování souborů jako-je z HDFS (včetně dvoufázové instalace kopie) objektů Blob v Azure nebo Azure Data Lake Store, zajistěte, aby Hadoop cluster splňuje následující požadavky:

1. MapReduce a Yarn služby jsou povolené.
2. Je yarn verze 2.5 nebo novější.
3. HDFS server je integrován se data store cíl - objektů Blob v Azure nebo Azure Data Lake Store:

    - Systém souborů Azure Blob je nativně podporována od Hadoop 2.7. Stačí zadat cestu jar v konfiguračním env Hadoop.
    - Systém souborů Azure Data Lake Store je zabalené od Hadoop 3.0.0-alpha1. Pokud váš cluster Hadoop je nižší než verze, budete muset ručně importovat ADLS související jar balíčky (azure datalake store.jar) do clusteru z [zde](https://hadoop.apache.org/releases.html)a zadejte cestu jar v konfiguračním env Hadoop.

4. Připravte dočasnou složku v HDFS. Tato dočasná složka se používá k uložení DistCp skript prostředí, takže bude zabírat úrovni KB místa.
5. Ujistěte se, že uživatelský účet zadaný v propojené službě HDFS mít oprávnění k) v Yarn; podat žádost b) mají oprávnění k vytvoření podsložky, pro čtení a zápis souborů ve výše dočasnou složku.

### <a name="configurations"></a>Konfigurace

Dole je příklad konfigurace aktivity kopírování zkopírovat data z HDFS do objektu Blob Azure pomocí DistCp:

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Ověřování pomocí protokolu Kerberos pro HDFS konektor

Existují dvě možnosti nastavit v místním prostředí tak, aby používala ověřování protokolu Kerberos v konektoru HDFS. Je možné, že byl lépe vyhovuje váš případ.
* Možnost 1: [Runtime integrace Self-hosted připojení k počítači ve sféře Kerberos](#kerberos-join-realm)
* Možnost 2: [povolit vzájemné vztah důvěryhodnosti mezi doménou systému Windows a Sféra Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: Připojte počítač Self-hosted integrace Runtime ve sféře Kerberos

#### <a name="requirements"></a>Požadavky

* Počítač Self-hosted integrace Runtime potřebuje k připojení Sféra Kerberos a nemůže připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Postup konfigurace

**Na počítači Self-hosted integrace Runtime:**

1.  Spustit **Ksetup** nástroj Konfigurace serveru protokolu Kerberos služby KDC a sféru.

    Počítač musí být nakonfigurován jako člena pracovní skupiny, protože Sféra Kerberos se liší od domény systému Windows. Toho lze dosáhnout nastavením Sféra Kerberos a takto přidejte server služby KDC. Nahraďte *REALM.COM* s vlastními příslušných sféry podle potřeby.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítače po provedení těchto příkazů 2.

2.  Ověření konfigurace s **Ksetup** příkaz. Výstup by měl vypadat podobně jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**V Azure Data Factory:**

* Konfigurovat pomocí konektoru HDFS **ověřování systému Windows** společně s hlavní název protokolu Kerberos a heslo pro připojení ke zdroji dat HDFS. Zkontrolujte [vlastnosti propojené služby HDFS](#linked-service-properties) části na podrobnosti o konfiguraci.

### <a name="kerberos-mutual-trust"></a>Možnost 2: Povolení vzájemné vztah důvěryhodnosti mezi doménou systému Windows a Sféra Kerberos

#### <a name="requirements"></a>Požadavky

*   Počítač Self-hosted integrace Runtime musí připojit k doméně systému Windows.
*   Budete potřebovat oprávnění k aktualizaci nastavení řadiče domény.

#### <a name="how-to-configure"></a>Postup konfigurace

> [!NOTE]
> Nahraďte REALM.COM a AD.COM v následujícím kurzu vlastní příslušných sféry a řadič domény, podle potřeby.

**Na serveru služby KDC:**

1.  Upravit konfiguraci služby KDC v **krb5.conf** souboru chcete, aby služba KDC vztahu důvěryhodnosti domény systému Windows na následující konfigurace šablony. Ve výchozím nastavení nachází ve **/etc/krb5.conf**.

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

3.  V **hadoop.security.auth_to_local** konfigurace služby HDFS souboru, přidejte `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na řadiči domény:**

1.  Spusťte následující **Ksetup** přidejte položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Navázání vztahu důvěryhodnosti z domény systému Windows k Sféra Kerberos. [heslo] je heslo pro objekt **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus použitý v protokolu Kerberos.

    1. Přejděte do správce serveru > Správa zásad skupiny > domény > objekty zásad skupiny > výchozí nebo zásady služby Active Domain a upravit.

    2. V **Editor správy zásad skupiny** automaticky otevíraném okně, přejděte na konfigurace počítače > zásady > Nastavení systému Windows > Nastavení zabezpečení > Místní zásady > Možnosti zabezpečení a nakonfigurujte **zabezpečení sítě: typy konfigurace šifrování povolené pro protokol Kerberos**.

    3. Vyberte algoritmus šifrování, kterou chcete použít při připojení k služby KDC. Běžně můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro protokol Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Použití **Ksetup** příkazu zadejte šifrovací algoritmus, který se má použít na konkrétní SFÉRY.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvoření mapování mezi účet domény a objektu zabezpečení pomocí protokolu Kerberos, aby bylo možné používat objekt zabezpečení protokolu Kerberos v doméně systému Windows.

    1. Spuštění nástroje pro správu > **Active Directory Users and Computers**.

    2. Konfigurace rozšířených funkcí kliknutím **zobrazení** > **pokročilé funkce**.

    3. Najděte účet, ke které chcete vytvořit mapování a klikněte pravým tlačítkem myši zobrazíte **mapování názvů** > klikněte na tlačítko **Kerberos názvy** kartě.

    4. Přidáte objekt z sféru.

        ![Mapování Identity zabezpečení](media/connector-hdfs/map-security-identity.png)

**Na počítači Self-hosted integrace Runtime:**

* Spusťte následující **Ksetup** přidejte položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**V Azure Data Factory:**

* Konfigurovat pomocí konektoru HDFS **ověřování systému Windows** společně s buď váš účet domény nebo objekt zabezpečení protokolu Kerberos pro připojení ke zdroji dat HDFS. Zkontrolujte [vlastnosti propojené služby HDFS](#linked-service-properties) části na podrobnosti o konfiguraci.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
