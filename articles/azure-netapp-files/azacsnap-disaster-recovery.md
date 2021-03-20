---
title: Zotavení po havárii pomocí nástroje pro vytváření snímků konzistentního pro aplikace Azure pro Azure NetApp Files | Microsoft Docs
description: Vysvětluje, jak provést zotavení po havárii při použití nástroje snímek konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.
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
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730866"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Zotavení po havárii pomocí nástroje Snapshot konzistentního pro aplikace Azure (Preview)

Tento článek vysvětluje, jak provést zotavení po havárii při použití nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.

> [!IMPORTANT]
> Tato operace se vztahuje jenom na **velkou instanci Azure** .

## <a name="introduction"></a>Úvod

Platforma pro velké instance Azure může mít taky nakonfigurovanou lokalitu pro zotavení po havárii, do které se dají replikovat snímky svazků úložiště.  Pokud byly pro takovou instalaci správně nakonfigurovány snímky, je možné v této lokalitě provést zotavení po havárii.  Tento dokument je určený jako průvodce pro zotavení po havárii pro tuto instalaci.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Předpoklady pro nastavení zotavení po havárii

Před naplánováním převzetí služeb při selhání pro zotavení po havárii musí být splněné následující předpoklady.

- Máte uzel DR zřízený na webu DR. Pro zotavení po havárii existují dvě možnosti. Jedna je normální DR a druhá je Víceúčelová DR.
- Funguje replikace úložiště. Provozní tým Microsoftu provede nastavení replikace úložiště v době automatického zřizování DR. Replikaci úložiště můžete monitorovat pomocí příkazu `azacsnap -c details --details replication` na webu Dr.
- Nastavili jste a nakonfigurovali snímky úložiště v primárním umístění.
- Máte instanci HANA nainstalovanou na webu DR pro primární server se stejným identifikátorem SID, jako má primární instance.
- Přečtěte si a pochopte postup převzetí služeb při selhání zotavení po havárii popsané v [velké instance SAP HANA vysoké dostupnosti a zotavení po havárii v Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Nastavili jste a nakonfigurovali snímky úložiště v umístění pro zotavení po havárii.
- Konfigurační soubor (například `DR.json` ) byl vytvořen pomocí svazků úložiště Dr a přidružených informací na serveru Dr.
- Dokončili jste kroky na webu DR pro:
  - Povolte komunikaci s úložištěm.
  - Povolte komunikaci s SAP HANA.

## <a name="set-up-disaster-recovery"></a>Nastavení zotavení po havárii

Microsoft podporuje replikaci na úrovni úložiště pro zotavení po havárii. Existují dva způsoby, jak nastavit zotavení po havárii.

Jedna je **normální** a druhá je **Víceúčelová**. V **normálním** programu zotavení po havárii máte vyhrazenou instanci v umístění Dr pro převzetí služeb při selhání. Ve scénáři **víceúčelové** zotavení je na serveru Dr spuštěná jiná instance QA nebo vývoj Hana, která běží na velké jednotce instance Hana. Ale nainstalovali jste také předinstalovanou instanci HANA, která je neaktivní a má stejné SID jako instance HANA, u které chcete převzít služby velkých instancí HANA. Operace s Microsoftem nastavují prostředí pro vás, včetně replikace úložiště na základě vstupu ve formuláři žádosti o služby (SRF) v době připojování.

> [!IMPORTANT]
> Zajistěte, aby byly splněny všechny požadavky pro instalaci programu zotavení po havárii.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Monitorování replikace dat z primární lokality na lokalitu DR

Microsoft Operations Team již spravuje a monitoruje připojení DR z primární lokality na lokalitu DR.
Replikaci dat z primárního serveru můžete monitorovat na server DR pomocí příkazu Snapshot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Provedení převzetí služeb při selhání na webu DR

Spusťte příkaz převzetí služeb při selhání na webu DR ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume`Příkaz ukončí replikaci úložiště z produkční lokality do lokality Dr. Chcete-li znovu nastavit replikaci, musíte se obrátit na operace společnosti Microsoft. Po opětovném povolení replikace budou inicializována všechna data v úložišti DR pro tento identifikátor SID. Příkaz, který provede převzetí služeb při selhání, zpřístupňuje poslední snímek úložiště, který je k dispozici. Pokud se potřebujete vrátit zpátky do staršího snímku, otevřete žádost o podporu, aby operace pomohly zajistit předchozí snímek obnovený v lokalitě DR.

V horní části najdete postup pro převzetí služeb při selhání pomocí zotavení po havárii:

- Instanci HANA musíte vypnout v **primární** lokalitě. Tato akce je nutná jenom v případě, že nebudete mít k dispozici nekonzistence dat pro převzetí služeb při selhání v lokalitě DR.
- Vypněte instanci HANA v uzlu DR pro produkční identifikátor SID.
- Spusťte příkaz `azacsnap -c restore --restore revertvolume` na uzlu Dr s identifikátorem SID, který se má obnovit.
  - Příkaz ukončí odkaz replikace úložiště z primární lokality na lokalitu DR.
  - Příkaz obnoví pouze svazek/data a/logbackups, svazek/Shared není obnoven, ale místo toho použije existující/Shared pro identifikátor SID v umístění systému DR.
  - Připojte svazek/data a/logbackups – zajistěte, aby se přidal do souboru fstab.
- Obnovte snímek HANA SYSTEMDB. HANA Studio zobrazí jenom nejnovější snímek HANA dostupný v rámci snímku úložiště, který se obnovil jako součást provádění příkazu `azacsnap -c restore --restore revertvolume` .
- Obnovte databázi tenanta.
- Spusťte instanci HANA na webu DR pro produkční identifikátor SID (příklad: H80 v tomto případě).
- Proveďte testování.

### <a name="example-performing-disaster-recovery"></a>Příklad provádění zotavení po havárii

Tato dílčí část popisuje podrobné kroky pro převzetí služeb při selhání na lokalitu pro zotavení po havárii.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Krok 1: Získání podrobností o svazku uzlu DR

Spusťte příkaz `df –h` , který zobrazí seznam systémů souborů a přidružených svazků, na které se odkazuje po převzetí služeb při selhání.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Krok 2: vypnutí HANA v primární lokalitě

Pokud provedete úplné převzetí služeb při selhání produkčních úloh a je možné se připojit k primárnímu provoznímu webu, vypínání instancí SAP HANA, u kterých došlo k převzetí služeb při selhání, na zotavení po havárii.

Například pokud se přihlásíte jako root, následující příklad ukazuje, jak lze SAP HANA vypnout.  Nahraďte <sid> svým SAP HANAm SID.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Krok 3: vypnutí HANA na webu DR

Před obnovením svazků je důležité vypnout SAP HANA na serveru DR.

Například pokud se přihlásíte jako root, následující příklad ukazuje, jak lze SAP HANA vypnout.  Nahraďte <sid> svým SAP HANAm SID.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Před obnovením svazků se ujistěte, že instance HANA na webu DR jsou offline.

#### <a name="step-4-restore-the-volumes"></a>Krok 4: obnovení svazků

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_Výstup příkazu zotavení po selhání_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Aby se dokončila příprava úložiště pro převzetí služeb při selhání pro zotavení po havárii, je nutné provést kroky na konci zobrazení konzoly.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Krok 5: odpojení zbytečných systémů souborů

Spuštěním příkazu `umount` odpojte systémy souborů nebo svazky, které nejsou potřebné.

```bash
umount <Mount point>
```

Odpojte data a mountpoints zálohy protokolu. Ve scénáři horizontálního navýšení kapacity může být více přípojný bod dat.

#### <a name="step-6-configure-the-mount-points"></a>Krok 6: Konfigurace přípojných bodů

Upravte soubor `/etc/fstab` tak, aby odkazoval na položky zálohování dat a protokolů pro primární identifikátor SID (v tomto příkladu SID = H80) a přidejte nové položky přípojného bodu vytvořené z primární lokality svazky Dr. Nové položky přípojných bodů jsou k dispozici ve výstupu příkazu.

- Odkomentujte stávající přípojné body běžící na webu DR pomocí `#` znaku:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Přidejte následující řádky do `/etc/fstab`
  > mělo by se jednat o stejný výstup z příkazu

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Krok 7: připojení svazků pro obnovení

Spusťte příkaz `mount –a` pro připojení všech přípojných bodů.

```bash
mount -a
```

Když teď spustíte, `df –h` měli byste vidět `*_dp` připojené svazky.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Krok 8: obnovení SYSTEMDB

Z aplikace HANA Studio klikněte pravým tlačítkem myši na instance SYSTEMDB a zvolte možnost zálohování a obnovení a pak obnovte systémovou databázi.

Podívejte se na Průvodce obnovením databáze ze snímku, konkrétně do SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Krok 9: obnovení databáze tenanta

V HANA studiu klikněte pravým tlačítkem myši na instance SYSTEMDB a zvolte možnost zálohování a obnovení a pak obnovte databázi tenanta.

Přečtěte si průvodce pro obnovení databáze ze snímku, konkrétně pro databáze TENANTŮ.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Spustit `azacsnap -c backup` na webu Dr

Pokud používáte zálohy na základě snímků na webu DR, název serveru HANA nakonfigurovaný v `azacsnap` konfiguračním souboru na serveru Dr by měl být stejný jako název provozního serveru.

> [!IMPORTANT]
> Spuštění nástroje `azacsnap -c backup` může vytvářet snímky úložiště na webu Dr, nejsou automaticky replikovány do jiné lokality.  Pracujte s operacemi Microsoftu, abyste lépe pochopili vrácení jakýchkoli souborů nebo dat zpátky do původní provozní lokality.

## <a name="next-steps"></a>Další kroky

- [Získat podrobnosti o snímku](azacsnap-cmd-ref-details.md)
- [Vytvořit zálohu](azacsnap-cmd-ref-backup.md)
