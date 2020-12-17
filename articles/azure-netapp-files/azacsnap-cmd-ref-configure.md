---
title: Konfigurace nástroje pro vytváření snímků konzistentního pro aplikace Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje návod pro spuštění příkazu konfigurace nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632759"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Konfigurace nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure (Preview)

Tento článek poskytuje návod pro spuštění příkazu konfigurace nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Konfigurační soubor lze vytvořit nebo upravit pomocí `azacsnap -c configure` příkazu.

## <a name="command-options"></a>Možnosti příkazu

`-c configure`Příkaz má následující možnosti:

- `--configuration new` pro vytvoření nového konfiguračního souboru.

- `--configuration edit` Úprava stávajícího konfiguračního souboru.

- `[--configfile <config filename>]` je volitelný parametr, který umožňuje názvy vlastních konfiguračních souborů.

## <a name="configuration-file-for-snapshot-tools"></a>Konfigurační soubor pro nástroje Snapshot Tools

Konfigurační soubor lze vytvořit spuštěním `azacsnap -c configure --configuration new` .  Ve výchozím nastavení je název konfiguračního souboru `azacsnap.json` .  Vlastní název souboru se dá použít spolu s `--configfile=` parametrem (například), `--configfile=<customname>.json` který je pro konfiguraci rozsáhlých instancí Azure potřeba:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Podrobnosti o požadovaných hodnotách

Následující části obsahují podrobné pokyny k různým hodnotám vyžadovaným pro konfigurační soubor.

### <a name="sap-hana-values"></a>SAP HANA hodnoty

Při přidávání *databáze* do konfigurace jsou vyžadovány následující hodnoty:

- **Adresa serveru Hana** = název hostitele nebo IP adresa serveru SAP HANA.
- **Hana SID** = SAP HANA ID systému.
- **Číslo instance Hana** = SAP HANA číslo instance.
- **HDB úložiště pro uživatele Hana** = uživatelský SAP HANA nakonfigurovaný s oprávněními ke spouštění záloh databáze.

- Jeden uzel: IP a název hostitele uzlu
- HSR s STONITH: IP a název hostitele uzlu
- Horizontální navýšení kapacity (N + N, N + M): aktuální IP adresa hlavního uzlu a název hostitele
- HSR bez STONITH: IP a název hostitele uzlu
- Multi SID na jednom uzlu: název hostitele a IP uzel hostující tyto identifikátory SID

### <a name="azure-large-instance-hli-storage-values"></a>Hodnoty úložiště Azure large instance (HLI)

Při přidávání *úložiště HLI* do oddílu databáze jsou potřeba následující hodnoty:

- **Uživatelské jméno úložiště** = tato hodnota je uživatelské jméno, které slouží k navázání připojení SSH k úložišti.
- **IP adresa úložiště** = adresa systému úložiště.
- **Název svazku úložiště** = název svazku, který se má snímek.  Tuto hodnotu je možné určit několika způsoby, možná nejjednodušší je vyzkoušet následující příkaz prostředí:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp Files (ANF) hodnoty úložiště

Při přidávání *úložiště ANF* do oddílu databáze jsou potřeba následující hodnoty:

- **Název souboru ověření objektu služby** = Toto je `authfile.json` soubor vygenerovaný ve Cloud shell při konfiguraci komunikace s úložištěm Azure NetApp Files.
- **Úplný identifikátor prostředku úložiště ANF** = úplné ID prostředku pro svazek, který se má snímek.  Dá se načíst z: Azure Portal – > ANF – > Volume – > nastavení/vlastnosti – > ID prostředku.

## <a name="configuration-file-overview-azacsnapjson"></a>Přehled konfiguračního souboru ( `azacsnap.json` )

V následujícím příkladu `azacsnap.json` je nakonfigurován s jedním identifikátorem SID.

Hodnoty parametrů musí být nastavené na konkrétní SAP HANA prostředí zákazníka.
Pro systém **velkých instancí Azure** tyto informace poskytuje služba Microsoft Service Management během volání registrace/předají a zpřístupňuje se v excelovém souboru, který je k dispozici během předají. Pokud tyto informace potřebujete znovu zadat, otevřete žádost o služby.

Níže je příklad, jak aktualizovat všechny hodnoty odpovídajícím způsobem.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> V případě scénáře zotavení po havárii, kdy se mají na webu DR spustit zálohy, musí být název serveru HANA nakonfigurovaný v konfiguračním souboru DR (například `DR.json` ) na serveru Dr stejný jako název provozního serveru.

> [!NOTE]
> Pro velkou instanci Azure se vaše IP adresa úložiště musí nacházet ve stejné podsíti jako váš fond serverů. V tomto případě je například podsíť fondu serverů 172. let. 18.0/24 a naše IP adresa úložiště jsou 172.18.18.11.

## <a name="next-steps"></a>Další kroky

- [Test nástroje Snapshot konzistentní s aplikacemi Azure](azacsnap-cmd-ref-test.md)
