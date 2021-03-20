---
title: Obnovení pomocí nástroje pro vytváření snímků konzistentního pro aplikace Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje průvodce pro spuštění příkazu RESTORE pro snímek konzistentní vzhledem k aplikacím Azure, který můžete použít v nástroji s Azure NetApp Files.
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
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632718"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Obnovení pomocí nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure (Preview)

V tomto článku najdete návod, jak spustit příkaz pro obnovení v rámci nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Obnovení svazku ze snímku se provádí pomocí `azacsnap -c restore` příkazu.

> [!IMPORTANT]
> Neprovádí obnovení databáze, pouze obnovení svazků, jak je popsáno pro každou z následujících možností.

## <a name="command-options"></a>Možnosti příkazu

`-c restore`Příkaz má následující možnosti:

- `--restore snaptovol` Vytvoří nový svazek na základě posledního snímku na cílovém svazku. Tento příkaz vytvoří nový svazek "Klonovaný" na základě nakonfigurovaného cílového svazku pomocí posledního snímku svazku jako základu pro vytvoření nového svazku.  Tento příkaz nepřerušil replikaci úložiště z primární na sekundární. Místo toho se na webu DR vytvoří klony posledního dostupného snímku a zobrazí se Doporučené mountpoints systému klonovaných svazků. Tento příkaz by měl být spuštěný v systému Azure large instance **v oblasti zotavení po havárii** (to znamená cílový systém pro převzetí služeb při selhání).

- `--restore revertvolume` Obnoví cílový svazek na předchozí stav na základě nejnovějšího snímku.  Použití tohoto příkazu jako součást převzetí služeb při selhání DR do spárované oblasti DR. Tento příkaz **zastaví** replikaci úložiště z primární lokality do sekundární lokality a obnoví cílové svazky Dr na všech dostupných snímcích na svazcích zotavení po havárii spolu s doporučeným systémem souborů mountpoints pro vrácené svazky zotavení po havárii. Tento příkaz by měl být spuštěný v systému Azure large instance **v oblasti zotavení po havárii** (to znamená cílový systém pro převzetí služeb při selhání).
    > [!NOTE]
    > Dílčí příkaz ( `--restore revertvolume` ) je k dispozici pouze pro velkou instanci Azure a není k dispozici pro Azure NetApp Files.
- `--hanasid <SAP HANA SID>` je SAP HANA identifikátor SID vybraný z konfiguračního souboru pro použití příkazů pro obnovení svazku.

- `[--configfile <config filename>]` je volitelný parametr, který umožňuje názvy vlastních konfiguračních souborů.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Provést test převzetí služeb při selhání pro zotavení po havárii `azacsnap -c restore --restore snaptovol`

Tento příkaz je podobný příkazu DR Failover s podporou převzetí služeb při selhání ( `--restore restorevolume` ), ale místo přerušení replikace mezi primární lokalitou a lokalitou pro zotavení po havárii se z svazků pro zotavení po havárii vytvoří Klonovaný svazek, který umožňuje obnovení nejnovějšího snímku na webu Dr. Tyto naklonované svazky pak zákazník používá k testování zotavení po havárii, aniž by museli spouštět úplné převzetí služeb při selhání v prostředí HANA, které přerušuje smlouvu o replikaci mezi primární lokalitou a lokalitou pro zotavení po havárii.

- Tímto způsobem lze otestovat více různých bodů obnovení, každý s vlastním bodem obnovení.
- Klon je určen časovým razítkem při spuštění příkazu a představuje nejaktuálnější data a další snímek, který je k dispozici při spuštění.

> [!IMPORTANT]
> Tato operace se vztahuje jenom na velkou instanci Azure.
>
> - Po spuštění tohoto příkazu vyžaduje kontaktní e-mail, aby se operace nacházely před odstraněním klonů po 4 týdnech.
> - Při každém spuštění tohoto příkazu se vytvoří nový klon, který musí po dokončení testu odstranit operace společnosti Microsoft.
> - Všechny vytvořené klonované svazky budou automaticky odstraněny po 4 týdnech.

Konfigurační soubor (například `DR.json` ) by měl obsahovat pouze svazky Dr, nikoli produkční svazky. v opačném případě by mohlo dojít k vytvoření klonů.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Výstup `azacsnap -c restore --restore snaptovol` příkazu (pro Single-Node scénář)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> Výstup "zobrazení přípojných bodů podle svazku" se liší v různých scénářích.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Úplné převzetí služeb při selhání po havárii `azacsnap -c restore --restore revertvolume`

Tento příkaz **zastaví** replikaci úložiště z primární lokality do sekundární lokality, obnoví poslední snímek na svazcích Dr a poskytne mountpoints pro svazky Dr.

Tento příkaz musí být spuštěn na serveru DR pomocí konfiguračního souboru (například `DR.json` ) pouze se svazky Dr.

Proveďte převzetí služeb při selhání na webu DR spuštěním příkazu `azacsnap -c restore --restore revertvolume` .  Tento příkaz vyžaduje, aby byl jako parametr přidán identifikátor SID. Toto je identifikátor SID instance HANA, který je potřeba obnovit na webu DR.

> [!IMPORTANT]
> Tento příkaz spusťte pouze v případě, že máte v plánu provést cvičení programu zotavení po havárii nebo test. Tento příkaz ukončí replikaci. Chcete-li znovu povolit replikaci, musíte kontaktovat operace společnosti Microsoft.

Na nejvyšší úrovni je zde postup pro provedení převzetí služeb při selhání po havárii:

- Instanci HANA musíte vypnout v **primární** lokalitě. Tato akce je nutná pouze v případě, že skutečně provádíte převzetí služeb při selhání na webu DR, aby nedocházelo k nekonzistenci dat.
- Vypněte instanci HANA v uzlu DR pro produkční identifikátor SID.
- Spusťte příkaz `azacsnap -c restore --restore revertvolume` na uzlu Dr s identifikátorem SID, který se má obnovit.
  - Příkaz ukončí odkaz replikace úložiště z primární lokality na lokalitu DR.
  - Příkaz obnoví nakonfigurované svazky "data" a "jiné".  Obvykle by tato operace byla pro svazky `/hana/data` `/hana/logbackups` systémů a.  `/hana/shared`Systém souborů není obnoven, ale používá stávající `/hana/shared` pro identifikátor SID v umístění systému Dr.
  - Připojit `/hana/data` svazky a `/hana/logbackups` – Ujistěte se, že se přidají do `/etc/fstab` souboru.
- Obnovte snímek HANA SYSTEMDB. HANA Studio zobrazí jenom nejnovější snímek HANA dostupný v rámci snímku úložiště, který se obnovil jako součást provádění příkazu Snapshot `azacsnap -c restore --restore revertvolume` .
- Obnovte databázi tenanta.
- Spusťte instanci HANA na webu DR pro produkční identifikátor SID (příklad: H80 v tomto případě).
- Proveďte jakékoli testování databáze.

## <a name="next-steps"></a>Další kroky

- [Vytvořit zálohu](azacsnap-cmd-ref-backup.md)
- [Získat podrobnosti o snímku](azacsnap-cmd-ref-details.md)
