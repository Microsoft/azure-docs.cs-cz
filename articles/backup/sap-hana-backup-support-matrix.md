---
title: Matice podpory pro zálohování SAP HANA
description: V tomto článku se dozvíte o podporovaných scénářích a omezeních při použití Azure Backup k zálohování databází SAP HANA na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: cbf910a0291e90965c9698a8b2a43c587cbfe0b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707230"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matice podpory pro zálohování databází SAP HANA na virtuálních počítačích Azure

Azure Backup podporuje zálohování databází SAP HANA do Azure. Tento článek shrnuje podporované scénáře a omezení k dispozici při použití Azure Backup k zálohování databází SAP HANA na virtuálních počítačích Azure.

> [!NOTE]
> Frekvence zálohování protokolu se teď dá nastavit na minimálně 15 minut. Zálohování protokolů začíná proudem až po úspěšném úplném zálohování databáze.

## <a name="scenario-support"></a>Podpora scénářů

| **Scénář**               | **Podporované konfigurace**                                | **Nepodporované konfigurace**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA spouštění jenom na virtuálních počítačích Azure Linux                    | Velké instance HANA (HLI)                                   |
| **Oblasti**                   | **GA**<br> **Severní Amerika** – Střed USA, Východní USA 2, Východní USA, Střed USA – sever, Střed USA – jih, Západní USA 2, Středozápadní USA, západní USA, Kanada – střed, Kanada – východ, Brazílie – jih <br> **Asie a Tichomoří** – Austrálie – střed, Austrálie – střed, Austrálie – východ, Austrálie – jihovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, Jižní korea, východní Asie, jihovýchodní asie, Střed Indie, Jižní Indie, Západní Indie, Čína – východ, Čína – sever, Čína – východ 2, Čína – Sever 2 <br> **Evropa** – Západní Evropa, Severní Evropa, Francie – střed, Velká Británie – jih, Velká Británie – západ, Německo – sever, Německo – středozápad, Švýcarsko – sever, Švýcarsko – západ, centrální Švýcarsko – sever, Norsko – východ, Norsko – západ <br> **Afrika/já** – Jižní Afrika sever, Jižní Afrika – západ, Spojené arabské emiráty sever, Spojené arabské emiráty střed  <BR>  **Oblasti Azure Government** | Francie – jih, Německo – střed, Německo – severovýchod, US Gov IOWA |
| **Verze operačního systému**            | SLES 12 s aktualizací SP2, SP3, SP4 a SP5; SLES 15 s SP0, SP1, SP2 <br><br>  RHEL 7,4, 7,6, 7,7, 8,1 & 8,2                |                                             |
| **Verze HANA**          | SDC v HANA 1. x, MDC na HANA 2. x SPS04, SPS05 rev <= 53 (ověřuje se i pro scénáře s povoleným šifrováním)      |                                                            |
| **Nasazení HANA**       | SAP HANA jenom na jednom virtuálním počítači Azure – jenom škálovat. <br><br> U nasazení s vysokou dostupností se oba uzly na obou různých počítačích považují za jednotlivé uzly s oddělenými datovými řetězy.               | Škálování na více instancí <br><br> V nasazeních s vysokou dostupností nebude zálohování automaticky převzetí služeb při selhání sekundárním uzlem. Konfigurace zálohování by se měla provádět samostatně pro každý uzel.                                           |
| **Instance HANA**         | Jedna instance SAP HANA na jednom virtuálním počítači Azure – pouze horizontální navýšení kapacity | Několik instancí SAP HANA na jednom virtuálním počítači. V jednom okamžiku můžete chránit jenom jednu z těchto více instancí.                  |
| **Typy databází HANA**    | Kontejner Izolovaná databáze (SDC) na 1. x, kontejneru více databází (MDC) na 2. x | MDC v HANA 1. x                                              |
| **Velikost databáze HANA**     | Databáze HANA velikosti <= 8 TB (nejedná se o velikost paměti systému HANA)               |                                                              |
| **Typy zálohování**           | Úplné, rozdílové a přírůstkové zálohování a zálohy protokolů                          |  Snímky                                       |
| **Typy obnovení**          | Informace o podporovaných typech obnovení najdete v SAP HANA poznámky [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Omezení zálohování**          | Až 8 TB velikosti úplné zálohy na instanci SAP HANA (částečný limit)         |                                                              |
| **Speciální konfigurace** |                                                              | SAP HANA + dynamické vrstvení <br>  Klonování prostřednictvím LaMa        |

------

>[!NOTE]
>Azure Backup se při zálohování databáze SAP HANA běžící na virtuálním počítači Azure automaticky neupraví na letní čas při ukládání.
>
>Zásadu podle potřeby upravte ručně.

> [!NOTE]
> Nyní můžete [sledovat úlohy zálohování a obnovení](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (na stejný počítač) aktivované z nativních klientů Hana (řídicí panel SAP HANA Studio/řídicí panel/DBA) v Azure Portal.

## <a name="next-steps"></a>Další kroky

* Naučte se [zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure](./backup-azure-sap-hana-database.md) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](./sap-hana-db-restore.md)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](./backup-azure-sap-hana-database-troubleshoot.md) .
