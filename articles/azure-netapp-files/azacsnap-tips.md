---
title: Tipy a triky pro používání nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje tipy a triky pro používání nástroje pro snímky konzistentních vzhledem k aplikacím Azure, které můžete použít s Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 08edd86fd19e7698a791e411f42a2a89084a91f7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737129"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Tipy a triky pro používání nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure (Preview)

Tento článek poskytuje tipy a triky, které mohou být užitečné při používání AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Omezení oprávnění objektu služby

Může být nutné omezit rozsah instančního objektu služby AzAcSnap.  Další podrobnosti o řízení přístupu k prostředkům Azure najdete v [dokumentaci k Azure RBAC](../role-based-access-control/index.yml) .  

Následuje příklad definice role s minimálními požadovanými akcemi, které je potřeba, aby AzAcSnap fungovalo.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

## <a name="take-snapshots-manually"></a>Pořizovat snímky ručně

Před spuštěním příkazů zálohování ( `azacsnap -c backup` ) Zkontrolujte konfiguraci spuštěním testovacích příkazů a ověřte, jestli se úspěšně spustily.  Správné spuštění těchto testů `azacsnap` se může komunikovat s nainstalovanou SAP HANA databází a podkladovým systémem úložiště SAP HANA ve **velké instanci** nebo **Azure NetApp Files** systému Azure.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Pak pokud chcete provést ruční zálohování snímků databáze, spusťte následující příkaz:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Nastavit automatické zálohování snímků

Pro `cron` automatizaci spouštění příkazů v systému je běžné použití systémů UNIX/Linux. Standardním postupem pro nástroje Snapshot Tools je nastavení uživatele `crontab` .

Příklad `crontab` pro uživatele pro `azacsnap` automatizaci snímků je uveden níže.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Vysvětlení výše uvedeného crontab

- `MAILTO=""`: když má prázdná hodnota, brání se tak cron v automatickém pokusu o odeslání e-mailu uživateli při spuštění položky crontab, která by mohla skončit v e-mailovém souboru místního uživatele.
- Zkratky pro zkrácení verzí časování pro položky crontab jsou vysvětlivekné:
  - `@monthly` = Spustit jednou za měsíc, tj. "0 0 1 * *".
  - `@weekly`  = Spustit jednou týdně, tj. "0 0 * * 0".
  - `@daily`   = Spustí se jednou denně, tj. "0 0 * * *".
  - `@hourly`  = Spustit jednou hodinu, tj. "0 * * * *".
- Prvních pět sloupců slouží k označení časů, viz následující příklady sloupců:
  - `0,15,30,45`: Každých 15 minut
  - `0-23`: Každou hodinu
  - `*` : Každý den
  - `*` : Každý měsíc
  - `*` : Každý den v týdnu
- Příkazový řádek, který se má provést v závorkách "()"
  - `. /home/azacsnap/.profile` = načíst v profilu uživatele profil pro nastavení prostředí, včetně $PATH atd.
  - `cd /home/azacsnap/bin` = změnit adresář spouštění na umístění "/Home/azacsnap/bin", kde jsou konfigurační soubory.
  - `azacsnap -c .....` = úplný příkaz azacsnap, který se má spustit, včetně všech možností.

Další vysvětlení cron a formátu souboru crontab najdete tady: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Uživatelé zodpovídají za monitorování úloh cron, aby se zajistilo, že se snímky generují úspěšně.

## <a name="monitor-the-snapshots"></a>Monitorování snímků

Následující podmínky by se měly monitorovat, aby se zajistil dobrý systém:

1. Dostupné místo na disku. Snímky pomaleji spotřebují místo na disku, protože zůstanou ve snímku zachované starší bloky disku.
    1. K automatizaci správy místa na disku použijte `--retention` Možnosti a `--trim` k automatickému vyčištění starých snímků a souborů protokolů databáze.
1. Úspěšné provedení nástrojů snímků
    1. Ověřte `*.result` soubor pro úspěch nebo neúspěch posledního spuštění `azacsnap` .
    1. Vyhledat `/var/log/messages` výstup z `azacsnap` příkazu
1. Konzistence snímků je pravidelně obnovovaná jiným systémem.

> [!NOTE]
> Chcete-li zobrazit podrobnosti o snímku, spusťte příkaz `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Odstranění snímku

Chcete-li odstranit snímek, spusťte příkaz `azacsnap -c delete` . Snímky z úrovně operačního systému není možné odstranit. Chcete-li odstranit snímky úložiště, je nutné použít správný příkaz ( `azacsnap -c delete` ).

> [!IMPORTANT]
> Být ostražití při odstranění snímku. Po odstranění není **možné** obnovit odstraněné snímky.

## <a name="restore-a-snapshot"></a>Obnovení snímku

Snímek svazku úložiště se dá obnovit na nový svazek ( `-c restore --restore snaptovol` ).  U rozsáhlých instancí Azure může být svazek vrácen do snímku ( `-c restore --restore revertvolume` ).

> [!NOTE]
> Není zadaný **žádný** příkaz pro obnovení databáze.

Snímek se dá zkopírovat zpátky do oblasti SAP HANA dat, ale SAP HANA nesmí být spuštěný, když je vytvořená kopie ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

U rozsáhlých instancí Azure se můžete obrátit na provozní tým Microsoftu tak, že otevřete žádost o služby pro obnovení požadovaného snímku ze stávajících dostupných snímků. Můžete otevřít žádost o službu z Azure Portal: <https://portal.azure.com>

Pokud se rozhodnete provést převzetí služeb při selhání pro zotavení po havárii, `azacsnap -c restore --restore revertvolume` příkaz na webu Dr automaticky zpřístupní nejnovější ( `/hana/data` a `/hana/logbackups` ) snímky svazku, aby bylo možné SAP HANA obnovení. Tento příkaz používejte opatrně, protože přerušuje replikaci mezi provozními a webovými servery DR.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Nastavte snímky jenom pro spouštěcí svazky.

> [!IMPORTANT]
> Tato operace se vztahuje jenom na velkou instanci Azure.

V některých případech už zákazníci mají nástroje pro ochranu SAP HANA a chtějí konfigurovat jenom snímky spouštěcích svazků.  V tomto případě je tato úloha zjednodušená a je třeba provést následující kroky.

1. Proveďte kroky 1-4 z požadavků pro instalaci.
1. Povolte komunikaci s úložištěm.
1. Stáhněte si instalační program a nainstalujte nástroje snímků.
1. Dokončete nastavení nástrojů Snapshot Tools.
1. Následujícím způsobem vytvořte nový konfigurační soubor. Podrobnosti o spouštěcím svazku musí být v OtherVolume Stanza (položky uživatele <span style="color:red">červeně</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Podívejte se na následující příklad konfiguračního souboru:

    Pomocí `cat` příkazu zobrazte obsah konfiguračního souboru:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
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

1. Test zálohování spouštěcího svazku

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Zkontrolujte, zda je uvedena v seznamu, poznamenejte si přidání `--snapshotfilter` možnosti omezení vráceného seznamu snímků.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Výstup příkazu:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Nastavte automatické zálohování snímků.

> [!NOTE]
> Nastavení komunikace s SAP HANA se nevyžaduje.

## <a name="restore-a-boot-snapshot"></a>Obnovení snímku "boot"

> [!IMPORTANT]
> Tato operace je určena pro Azure large instance ony.
> Server bude obnoven do bodu, kdy byl snímek proveden.

Snímek spouštěcího souboru lze obnovit následujícím způsobem:

1. Zákazník bude muset vypnout server.
1. Po vypnutí serveru bude muset zákazník otevřít žádost o služby, která obsahuje ID a snímek počítače, který chcete obnovit.
    > Zákazníci mohou z Azure Portal otevřít žádost o služby: <https://portal.azure.com>
1. Microsoft obnoví logickou jednotku operačního systému pomocí zadaného ID počítače a snímku a pak spustí server.
1. Zákazník pak bude muset potvrdit, že server je spuštěný a v pořádku.

Po obnovení už nemusíte provádět žádné další kroky.

## <a name="key-facts-to-know-about-snapshots"></a>Důležité fakta o snímcích

Klíčové atributy snímků svazku úložiště:

- **Umístění snímků**: snímky se dají najít ve virtuálním adresáři ( `.snapshot` ) v rámci svazku.  Podívejte se na následující příklady pro **velkou instanci Azure**:
  - Databáze `/hana/data/<SID>/mnt00001/.snapshot`
  - Sdíleného `/hana/shared/<SID>/.snapshot`
  - Protokolování `/hana/logbackups/<SID>/.snapshot`
  - Boot: spouštěcí snímky pro HLI se **nedají zobrazit** na úrovni operačního systému, ale dají se uvést pomocí `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` je skrytá *virtuální* složka jen pro čtení, která poskytuje přístup k snímkům jen pro čtení.

- **Maximální počet snímků:** Hardware může udržovat až 250 snímků na jeden svazek. Příkaz Snapshot zachová maximální počet snímků pro předponu na základě sady uchování na příkazovém řádku a odstraní nejstarší snímek, pokud přesahuje maximální počet, který se má zachovat.
- **Název snímku:** Název snímku obsahuje popisek předpony poskytnutý zákazníkem.
- **Velikost snímku:** Závisí na velikosti a změnách na úrovni databáze.
- **Umístění souboru protokolu:** Soubory protokolu vygenerované příkazy jsou výstupem do složek, jak jsou definovány v konfiguračním souboru JSON, který je ve výchozím nastavení podsložkou, kde se příkaz spouští (například `./logs` ).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](azacsnap-troubleshoot.md)