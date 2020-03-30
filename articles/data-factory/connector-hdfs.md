---
title: Kopírování dat z HDFS pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z cloudu nebo místního zdroje HDFS do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2cd76afa9412e89c57cfb6c357eb164ce5d3d1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830424"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopírování dat z HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-hdfs-connector.md)
> * [Aktuální verze](connector-hdfs.md)

Tento článek popisuje, jak kopírovat data ze serveru HDFS. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor HDFS je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Konkrétně tento konektor HDFS podporuje:

- Kopírování souborů pomocí **systému Windows** (Kerberos) nebo **anonymního** ověřování.
- Kopírování souborů pomocí protokolu **webhdfs** nebo integrované podpory **DistCp.**
- Kopírování souborů tak, jak je nebo analýza / generování souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Ujistěte se, že prostředí Integration Runtime má přístup k **all** [name node server]:[name node port] a [data node servers]:[data node port] clusteru Hadoop. Výchozí [port uzlu názvu] je 50070 a výchozí [datový uzel] je 50075.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro HDFS.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu HDFS jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Hdfs**. | Ano |
| url |Adresa URL systému HDFS |Ano |
| authenticationType | Povolené hodnoty jsou: **Anonymní**nebo **Windows**. <br><br> Chcete-li použít **ověřování protokolem Kerberos** pro konektor HDFS, v [této části](#use-kerberos-authentication-for-hdfs-connector) nastavte místní prostředí odpovídajícím způsobem. |Ano |
| userName |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem `<username>@<domain>.com`Kerberos zadejte . |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

**Příklad: použití anonymního ověřování**

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

**Příklad: použití ověřování systému Windows**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `location` pro HDFS v nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` pod v datové sadě musí být nastavena na **HdfsLocation**. | Ano      |
| folderPath | Cesta ke složce. Chcete-li k filtrování složky použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod danou složkouPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a určete v nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro HDFS v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **HdfsReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod danou složkouPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř.  Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| distcpSettings | Skupina vlastností při použití HDFS DistCp. | Ne |
| resourceManagerEndpoint | Koncový bod Správce zdrojů příze | Ano, pokud používáte DistCp |
| tempScriptPath | Cesta ke složce používaná k uložení příkazového skriptu temp DistCp. Soubor skriptu je generován factory a bude odebrán po dokončení úlohy kopírování. | Ano, pokud používáte DistCp |
| možnosti distcpOptions | Další možnosti poskytované příkazu DistCp. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| folderPath | fileName             | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory **tučně** jsou načteny) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (prázdný, použijte výchozí) | false (nepravda)     | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*`  | (prázdný, použijte výchozí) | true      | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*`  | `*.csv`              | false (nepravda)     | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*`  | `*.csv`              | true      | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopírování dat z HDFS pomocí funkce DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je hadoopský nativní nástroj příkazového řádku pro distribuovanou kopii v clusteru Hadoop. Při spuštění příkazu Distcp nejprve zobrazí seznam všech souborů, které mají být zkopírovány, vytvoří několik úloh map do clusteru Hadoop a každá úloha mapy provede binární kopii ze zdroje do jímky.

Podpora aktivit kopírování pomocí DistCp ke kopírování souborů tak, jak je do objektu Blob Azure (včetně [fázované kopie)](copy-activity-performance.md)nebo Azure Data Lake Store, v takovém případě může plně využít výkon vašeho clusteru namísto spuštění v prostředí Runtime integrace s vlastním hostitelem. Bude poskytovat lepší propustnost kopírování, zejména v případě, že váš cluster je velmi silný. Na základě vaší konfigurace v Azure Data Factory, Kopírování aktivity automaticky vytvořit příkaz distcp, odeslat do clusteru Hadoop a sledovat stav kopírování.

### <a name="prerequisites"></a>Požadavky

Pokud chcete pomocí DistCp kopírovat soubory tak, jak jsou z HDFS do objektu Blob Azure (včetně fázované kopie) nebo Azure Data Lake Store, ujistěte se, že cluster Hadoop splňuje níže uvedené požadavky:

1. Služby MapReduce a Yarn jsou povoleny.
2. Verze příze je 2,5 nebo vyšší.
3. HDFS server je integrovaný s cílovým úložištěm dat – Azure Blob nebo Azure Data Lake Store:

    - Azure Blob FileSystem je nativně podporována od Hadoop 2.7. Stačí zadat jar cestu v Hadoop env config.
    - Azure Data Lake Store FileSystem je zabalen od Hadoop 3.0.0-alpha1. Pokud je váš cluster Hadoop nižší než tato verze, je třeba ručně importovat balíčky jar související s ADLS (azure-datalake-store.jar) do [clusteru odtud](https://hadoop.apache.org/releases.html)a zadat jar cestu v konfiguračním programu Hadoop env.

4. Připravte dočasnou složku v HDFS. Tato dočasná složka se používá k ukládání skriptu prostředí DistCp, takže bude zabírat místo na úrovni KB.
5. Ujistěte se, že uživatelský účet poskytnutý v HDFS Linked Service mají oprávnění k a) podat žádost v Příze; b) mít oprávnění k vytvoření podsložky, číst / zapisovat soubory pod výše temp složky.

### <a name="configurations"></a>Konfigurace

Viz DistCp související konfigurace a příklady v [HDFS jako zdroj ové](#hdfs-as-source) části.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Použití ověřování protokolem Kerberos pro konektor HDFS

Existují dvě možnosti nastavení místního prostředí tak, aby bylo použití ověřování protokolem Kerberos v konektoru HDFS. Můžete si vybrat ten, který lépe vyhovuje vašemu případu.
* Možnost 1: [Připojte se k vlastnímu hostovanému integračnímu runtime počítači ve sféře Protokolu Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolení vzájemné důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Možnost 1: Připojte se k vlastnímu hostovanému integračnímu runtime počítači ve sféře Protokolu Kerberos

#### <a name="requirements"></a>Požadavky

* Počítač Integrovaný integrační běh stroj s vlastním hostitelem se musí připojit k sféře Protokolu Kerberos a nemůže se připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

**Na vlastním integračním runtime stroji:**

1.  Spusťte nástroj **Ksetup** a nakonfigurujte server a sféru protokolu Kerberos KDC.

    Počítač musí být nakonfigurován jako člen pracovní skupiny, protože sféra protokolu Kerberos se liší od domény systému Windows. Toho lze dosáhnout nastavením sféry Protokolu Kerberos a přidáním serveru KDC následujícím způsobem. Podle potřeby *REALM.COM* nahraďte vlastní sférou.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítač po provedení těchto 2 příkazů.

2.  Ověřte konfiguraci pomocí příkazu **Ksetup.** Výstup by měl být jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Ve Službě Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** spolu s hlavním jménem a heslem protokolu Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [vlastnosti propojené služby HDFS.](#linked-service-properties)

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Možnost 2: Povolení vzájemné důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos

#### <a name="requirements"></a>Požadavky

*   Počítač Integrovaný integrační běhový stroj s vlastním hostitelem se musí připojit k doméně systému Windows.
*   K aktualizaci nastavení řadiče domény potřebujete oprávnění.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

> [!NOTE]
> Podle potřeby nahraďte REALM.COM a AD.COM v následujícím kurzu vlastní sférou a řadičem domény.

**Na serveru KDC:**

1. Upravte konfiguraci KDC v souboru **krb5.conf,** aby kdc důvěřoval doméně systému Windows odkazující na následující konfigurační šablonu. Ve výchozím nastavení je konfigurace umístěna na **adrese /etc/krb5.conf**.

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

   Po konfiguraci **restartujte** službu KDC.

2. Připravte objekt zabezpečení s názvem **krbtgt/REALM.COM\@AD.COM** na serveru KDC pomocí následujícího příkazu:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. V souboru konfigurace služby **hadoop.security.auth_to_local** HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na řadiči domény:**

1.  Spusťte následující příkazy **Ksetup** a přidejte položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Vytvořte vztah důvěryhodnosti z domény systému Windows do sféry protokolu Kerberos. [heslo] je heslo pro hlavní **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus používaný v protokolu Kerberos.

    1. Přejděte na Správce serveru > správa zásad skupiny > objekty zásad skupiny > domény > výchozí nebo aktivní zásady domény a upravit.

    2. V místním okně **Editor správy zásad skupiny** přejděte na položku Zásady konfigurace počítače > > Nastavení systému Windows > Nastavení zabezpečení > Místní zásady > možnosti zabezpečení a konfigurace **zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos**.

    3. Vyberte šifrovací algoritmus, který chcete použít při připojení k KDC. Obvykle můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro protokol Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Pomocí příkazu **Ksetup** určete šifrovací algoritmus, který se má použít v konkrétní skutečnosti REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvořte mapování mezi účtem domény a objektem zabezpečení protokolu Kerberos, abyste mohli použít objekt zabezpečení protokolu Kerberos v doméně systému Windows.

    1. Spusťte nástroje pro správu > **uživatelé a počítače služby Active Directory**.

    2. Nakonfigurujte pokročilé funkce klepnutím na tlačítko **Zobrazit** > **rozšířené funkce**.

    3. Vyhledejte účet, pro který chcete vytvořit mapování, a kliknutím pravým tlačítkem myši **zobrazte položku Mapování názvů** > klikněte na kartu **Názvy kerberos.**

    4. Přidejte objekt zabezpečení z říše.

        ![Identita zabezpečení mapy](media/connector-hdfs/map-security-identity.png)

**Na vlastním integračním runtime stroji:**

* Spusťte následující příkazy **Ksetup** a přidejte položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Ve Službě Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s vaším účtem domény nebo objektem Kerberos Pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [vlastnosti propojené služby HDFS.](#linked-service-properties)

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr se zástupnými znaky je `*` podporován, povolené zástupné `?` znaky jsou: (odpovídá nule nebo více znaků) a (odpovídá nule nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char uvnitř. <br/><br/>Příklady: kořenová složka/podsložka/, další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ano |
| fileName |  **Filtr názvu nebo zástupných symbolů** pro soubor (soubory) pod zadanou "folderPath". Pokud pro tuto vlastnost nezadáte hodnotu, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr jsou povoleny `*` zástupné znaky: (odpovídá nula nebo více znaků) a `?` (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Chcete-li analyzovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), Formát [Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Formát [orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden soubor s daným názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** s filtrem se zástupnými symboly.

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
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: HdfsSource.** |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka nebudou zkopírovány nebo vytvořeny na jímce.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| distcpSettings | Skupina vlastností při použití HDFS DistCp. | Ne |
| resourceManagerEndpoint | Koncový bod Správce zdrojů příze | Ano, pokud používáte DistCp |
| tempScriptPath | Cesta ke složce používaná k uložení příkazového skriptu temp DistCp. Soubor skriptu je generován factory a bude odebrán po dokončení úlohy kopírování. | Ano, pokud používáte DistCp |
| možnosti distcpOptions | Další možnosti poskytované příkazu DistCp. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad: Zdroj HDFS v aktivitě kopírování pomocí DistCp**

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

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
