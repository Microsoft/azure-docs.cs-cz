---
title: Matice podpory pro zálohování SAP HANA
description: V tomto článku se dozvíte o podporovaných scénářích a omezeních při zálohování SAP HANA databází na virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842627"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matice podpory pro zálohování databází SAP HANA na virtuálních počítačích Azure

Azure Backup podporuje zálohování databází SAP HANA do Azure. Tento článek shrnuje podporované scénáře a omezení k dispozici při použití Azure Backup k zálohování databází SAP HANA na virtuálních počítačích Azure.

## <a name="onboard-to-the-public-preview"></a>Připojit se k veřejné verzi Preview

Připojte se k veřejné verzi Preview následujícím způsobem:

* Na portálu Zaregistrujte ID předplatného pro poskytovatele služby Recovery Services podle [tohoto článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Spusťte tuto rutinu pro PowerShell. Měla by být dokončena jako zaregistrovaná.

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> Frekvence zálohování protokolu se teď dá nastavit na minimálně 15 minut. Zálohování protokolů začíná proudem až po úspěšném úplném zálohování databáze.

## <a name="scenario-support"></a>Podpora scénářů

| **Scénář**               | **Podporované konfigurace**                                | **Nepodporované konfigurace**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA spouštění jenom na virtuálních počítačích Azure Linux                    | Velké instance HANA (HLI)                                   |
| **Zeměpisných oblastech**                   | **GA**<br />**Evropa** – Západní Evropa, Severní Evropa, Francie – střed, Velká Británie – jih, Velká Británie – západ, Německo – sever, Německo – středozápad, Švýcarsko – sever, Švýcarsko – západ<br />**Asie a Tichomoří** – Austrálie Central, Austrálie – střed 2, Austrálie – východ, Austrálie – jihovýchod, Japonsko – východ, Japonsko – západ, Korea – střed, Jižní Korea<br /><br>**Tisk**<br />**Severní Amerika** – Střed USA, Východní USA 2, Východní USA, Střed USA – sever, Střed USA – jih, Západní USA 2, Středozápadní USA, západní USA, Kanada – střed, Kanada – východ <br />**Asie a Tichomoří** – východní Asie, jihovýchodní asie, střed Indie Jižní Indie | Čína – východ, Čína – sever, Čína – východ 2, Čína – sever 2, Západní Indie, Central Švýcarsko – sever, Jižní Afrika sever, Jižní Afrika – západ, Spojené arabské emiráty sever, Spojené arabské emiráty Central, Azure Government oblasti, Francie – jih, Brazílie – jih |
| **Verze operačního systému**            | SLES 12 s aktualizací SP2, SP3 nebo SP4                                | SLES 15, RHEL                                                |
| **Verze HANA**          | SDC v HANA 1. x, MDC na HANA 2. x < = SPS04 rev 44            | -                                                            |
| **Nasazení HANA**       | SAP HANA jenom na jednom virtuálním počítači Azure – jenom škálovat               | Škálování na víc systémů                                                    |
| **Instance HANA**         | Jedna instance SAP HANA na jednom virtuálním počítači Azure – pouze horizontální navýšení kapacity | Několik instancí SAP HANA na jednom virtuálním počítači                  |
| **Typy databází HANA**    | Kontejner Izolovaná databáze (SDC) na 1. x, kontejneru více databází (MDC) na 2. x | MDC v HANA 1. x                                              |
| **Velikost databáze HANA**     | 2 – TB úplné zálohy, jak oznamuje HANA)                   |                                                              |
| **Typy zálohování**           | Úplné a rozdílové zálohy a zálohování protokolů                          | Přírůstkové, snímky                                       |
| **Typy obnovení**          | Informace o podporovaných typech obnovení najdete v SAP HANA poznámky [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Omezení zálohování**          | Až 2 TB velikosti úplné zálohy na instanci SAP HANA         |                                                              |
| **Speciální konfigurace** |                                                              | SAP HANA + dynamické vrstvení <br>  Klonování prostřednictvím LaMa        |

------

> [!NOTE]
> Operace zálohování a obnovení z SAP HANA nativních klientů (řídicí panel SAP HANA studia/řídicí panel/DBA) nejsou aktuálně podporovány.



## <a name="next-steps"></a>Další kroky

* Naučte se [zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) .
