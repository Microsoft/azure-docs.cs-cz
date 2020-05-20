---
title: Matice podpory pro zálohování SAP HANA
description: V tomto článku se dozvíte o podporovaných scénářích a omezeních při zálohování SAP HANA databází na virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 20519562151a70c9fd8e8fe5d86ce912e615b460
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660134"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matice podpory pro zálohování databází SAP HANA na virtuálních počítačích Azure

Azure Backup podporuje zálohování databází SAP HANA do Azure. Tento článek shrnuje podporované scénáře a omezení k dispozici při použití Azure Backup k zálohování databází SAP HANA na virtuálních počítačích Azure.

> [!NOTE]
> Frekvence zálohování protokolu se teď dá nastavit na minimálně 15 minut. Zálohování protokolů začíná proudem až po úspěšném úplném zálohování databáze.

## <a name="scenario-support"></a>Podpora scénářů

| **Scénář**               | **Podporované konfigurace**                                | **Nepodporované konfigurace**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA spouštění jenom na virtuálních počítačích Azure Linux                    | Velké instance HANA (HLI)                                   |
| **Zeměpisných oblastech**                   | **GA**<br> **Severní Amerika** – Střed USA, Východní USA 2, Východní USA, Střed USA – sever, Střed USA – jih, Západní USA 2, Středozápadní USA, západní USA, Kanada – střed, Kanada – východ, Brazílie – jih <br> **Asie a Tichomoří** – Austrálie – střed, Austrálie – střed, Austrálie – východ, Austrálie – jihovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, Jižní korea, východní Asie, jihovýchodní asie, Střed Indie, Jižní Indie, Západní Indie, Čína – východ, Čína – sever, Čína – východ 2, Čína – Sever 2 <br> **Evropa** – Západní Evropa, Severní Evropa, Francie – střed, Velká Británie – jih, Velká Británie – západ, Německo – sever, Německo – středozápad, Švýcarsko – sever, Švýcarsko – západ, centrální Švýcarsko – sever <br> **Afrika/já** – Jižní Afrika sever, Jižní Afrika – západ, Spojené arabské emiráty sever, Spojené arabské emiráty střed  <BR>  **Oblasti Azure Government** | Francie – jih, Německo – střed, Německo – severovýchod, US Gov IOWA |
| **Verze operačního systému**            | SLES 12 s aktualizací SP2, SP3 nebo SP4; SLES 15 s aktualizací SP1                              | RHEL                                                |
| **Verze HANA**          | SDC v HANA 1. x, MDC na HANA 2. x <= SPS04 rev 46       | -                                                            |
| **Nasazení HANA**       | SAP HANA jenom na jednom virtuálním počítači Azure – jenom škálovat. <br><br> U nasazení s vysokou dostupností se oba uzly na obou různých počítačích považují za jednotlivé uzly s oddělenými datovými řetězy.               | Škálování na více instancí <br><br> V nasazeních s vysokou dostupností nebude zálohování automaticky převzetí služeb při selhání sekundárním uzlem. Konfigurace zálohování by se měla provádět samostatně pro každý uzel.                                           |
| **Instance HANA**         | Jedna instance SAP HANA na jednom virtuálním počítači Azure – pouze horizontální navýšení kapacity | Několik instancí SAP HANA na jednom virtuálním počítači                  |
| **Typy databází HANA**    | Kontejner Izolovaná databáze (SDC) na 1. x, kontejneru více databází (MDC) na 2. x | MDC v HANA 1. x                                              |
| **Velikost databáze HANA**     | 2 – TB úplné zálohy, jak oznamuje HANA)                   |                                                              |
| **Typy zálohování**           | Úplné a rozdílové zálohy a zálohování protokolů                          | Přírůstkové, snímky                                       |
| **Typy obnovení**          | Informace o podporovaných typech obnovení najdete v SAP HANA poznámky [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Omezení zálohování**          | Až 2 TB velikosti úplné zálohy na instanci SAP HANA         |                                                              |
| **Speciální konfigurace** |                                                              | SAP HANA + dynamické vrstvení <br>  Klonování prostřednictvím LaMa        |

------

> [!NOTE]
> Nyní můžete [monitorovat úlohy zálohování a obnovení](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) (do stejného počítače) aktivované z nativních klientů Hana (řídicí panel SAP HANA Studio/řídicí panel/DBA) v Azure Portal.

## <a name="next-steps"></a>Další kroky

* Naučte se [zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) .
