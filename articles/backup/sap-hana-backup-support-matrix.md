---
title: Matice podpory pro zálohování SAP HANA
description: V tomto článku se dozvíte o podporovaných scénářích a omezeních při zálohování SAP HANA databází na virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 0d847d2131a9a9c21fde14cae40a184de4195223
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288227"
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
| **Zeměpisných oblastech**                   | Austrálie – jihovýchod, východní Austrálie Brazílie – jih – střed, Kanada – jih Východní Asie, Východní Asie Východní USA, Východní USA 2, Středozápadní USA, Západní USA, Západní USA 2, Střed USA – sever, Střed USA, Střed USA – jih Indie – střed, Indie – jih – Japonsko – východ, Japonsko Svatý Korea – střed, Korea – jih Severní Evropa, Západní Evropa Velká Británie – jih, Velká Británie – západ | Austrálie – střed, Austrálie – střed 2 Čína – východ, Čína – sever, Čína – východ 2, Čína – sever 2 Západní Indie Francie – střed, Francie – jih Německo – sever, Německo – středozápad Švýcarsko – sever, Švýcarsko – západ Jižní Afrika – sever, Jižní Afrika – západ, Spojené arabské emiráty sever, Spojené arabské emiráty centra l Azure Government oblasti |
| **Verze operačního systému**            | SLES 12 s aktualizací SP2, SP3 nebo SP4           | SLES 15, RHEL                                                |
| **Verze HANA**          | SDC v HANA 1. x, MDC na HANA 2. x < = SPS04 rev 44           | -                                                            |
| **Nasazení HANA**       | SAP HANA jenom na jednom virtuálním počítači Azure – jenom škálovat               | Škálování na víc systémů                                                    |
| **Instance HANA**         | Jedna instance SAP HANA na jednom virtuálním počítači Azure – pouze horizontální navýšení kapacity | Několik instancí SAP HANA na jednom virtuálním počítači                  |
| **Typy databází HANA**    | Kontejner Izolovaná databáze (SDC) na 1. x, kontejneru více databází (MDC) na 2. x | MDC v HANA 1. x                                              |
| **Velikost databáze HANA**     | 2 – 1TB velikost úplné zálohy po kompresi (M Series 2 TB, 4 TB RAM) |                                                              |
| **Typy zálohování**           | Úplné a rozdílové zálohy a zálohování protokolů                           | Přírůstkové, snímky                                       |
| **Typy obnovení**          | Informace o podporovaných typech obnovení najdete v SAP HANA poznámky [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |                                                              |
| **Omezení zálohování**          | Až 2 TB velikosti úplné zálohy na instanci SAP HANA  |                                                              |
| **Speciální konfigurace** |                                                              | SAP HANA + dynamické vrstvení <br>  Klonování prostřednictvím LaMa            |

------

> [!NOTE]
> Operace zálohování a obnovení z SAP HANA nativních klientů (řídicí panel SAP HANA studia/řídicí panel/DBA) nejsou aktuálně podporovány.



## <a name="next-steps"></a>Další kroky

* Naučte se [zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) .
