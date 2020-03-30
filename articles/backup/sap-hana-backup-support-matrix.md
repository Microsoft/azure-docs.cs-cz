---
title: Matice podpory pro zálohování SAP HANA
description: V tomto článku se dozvíte o podporovaných scénářích a omezeních při zálohování Azure k zálohování databází SAP HANA na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252438"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matice podpory pro zálohování databází SAP HANA na virtuálních počítačích Azure

Azure Backup podporuje zálohování databází SAP HANA do Azure. Tento článek shrnuje podporované scénáře a omezení při zálohování Azure Backup k zálohování databází SAP HANA na virtuálních počítačích Azure.

> [!NOTE]
> Frekvenci zálohování protokolu lze nyní nastavit na minimálně 15 minut. Zálohy protokolů začnou tok až po úspěšné úplné zálohování pro databázi byla dokončena.

## <a name="scenario-support"></a>Podpora scénářů

| **Scénář**               | **Podporované konfigurace**                                | **Nepodporované konfigurace**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA spuštěné jenom v virtuálních počítačích Azure Linux                    | Velké instance HANA (HLI)                                   |
| **Geos**                   | **Ga:**<br> **Americas** – Střední USA, Východní USA 2, Východní USA, Severní Střed USA, Jižní Střed USA, Západní USA 2, Západ – střed USA, Západní USA, Kanada – střed, Kanada – východ, Brazílie – jih <br> **Asie a Tichomoří** – Austrálie – Střed, Austrálie – střed 2, Austrálie – východ, Austrálie – jihovýchod, Japonsko – východ, Japonsko – západ, Korea – střed, Korea – jih, východní Asie, jihovýchodní Asie, střední Indie, Jižní Indie, Západní Indie, Čína – východ, Čína – sever, Čína – východ2, Čína – sever 2. <br> **Evropa** – západní Evropa, severní Evropa, Francie – střed, Velká Británie – jih, Velká Británie – západ, Německo – sever, Německo – západ – střed, Švýcarsko – západ, střední Švýcarsko – sever <br> **Afrika / ME** - Jižní Afrika Sever, Jižní Afrika Západ, SAE Sever, SAE Central  <BR>  **Oblasti Azure Government** | Francie Jih, Německo – střed, Německo Severovýchod, US Gov IOWA |
| **Verze operačního systému**            | SLES 12 s sp2, SP3 nebo SP4; SLES 15 s SP1                              | RHEL                                                |
| **Verze HANA**          | SDC na HANA 1.x, MDC na HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **Nasazení HANA**       | SAP HANA na jednom virtuálním počítači Azure – škálovat pouze. <br><br> Pro nasazení s vysokou dostupností jsou oba uzly na dvou různých počítačích považovány za jednotlivé uzly se samostatnými datovými řetězci.               | Škálování na více instancí <br><br> V nasazení s vysokou dostupností záloha není převzetí služeb při selhání do sekundárního uzlu automaticky. Konfigurace zálohování by měla být provedena samostatně pro každý uzel.                                           |
| **Instance HANA**         | Jedna instance SAP HANA na jednom virtuálním počítači Azure – jenom navýšit kapacitu | Více instancí SAP HANA na jednom virtuálním počítači                  |
| **Typy databází HANA**    | Kontejner jedné databáze (SDC) na 1.x, vícedatabázový kontejner (MDC) na 2.x | MDC v HANA 1.x                                              |
| **Velikost databáze HANA**     | 2 TB plná velikost zálohy podle hana)                   |                                                              |
| **Typy zálohování**           | Úplné, rozdílové a protokolovací zálohy                          | Přírůstkové, snímky                                       |
| **Typy obnovení**          | Informace o podporovaných typech obnovení naleznete v poznámce SAP HANA [1642148.](https://launchpad.support.sap.com/#/notes/1642148) |                                                              |
| **Limity zálohování**          | Až 2 TB plné velikosti zálohy na instanci SAP HANA         |                                                              |
| **Speciální konfigurace** |                                                              | SAP HANA + Dynamické vrstvení <br>  Klonování přes LaMa        |

------

> [!NOTE]
> Operace zálohování a obnovení od nativních klientů SAP HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) nejsou v současné době podporovány.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zálohovat databáze SAP HANA spuštěné na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Zjistěte, jak [obnovit databáze SAP HANA spuštěné na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Zjistěte, jak [spravovat databáze SAP HANA, které jsou zálohované pomocí azure backupu](sap-hana-db-manage.md)
* Informace o [řešení běžných problémů při zálohování databází SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
