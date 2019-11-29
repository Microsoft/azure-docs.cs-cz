---
title: Řešení potíží s chybami zálohování SAP HANAových databází
description: Popisuje, jak řešit běžné chyby, ke kterým může dojít při použití Azure Backup k zálohování databází SAP HANA.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: b4c39c631963a358dcdc9d1eafe954a85a9499ad
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554863"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Řešení potíží se zálohováním databází SAP HANA v Azure

Tento článek poskytuje informace o řešení potíží při zálohování SAP HANA databází na virtuálních počítačích Azure. Další informace o scénářích SAP HANA Backup, které momentálně podporujeme, najdete v tématu [Podpora scénářů](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Předpoklady a oprávnění

Před konfigurací záloh najdete v oddílech [požadavky](tutorial-backup-sap-hana-db.md#prerequisites) a [Nastavení oprávnění](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

## <a name="common-user-errors"></a>Běžné chyby uživatelů

###  <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection 

| Chybová zpráva      | Nepovedlo se připojit k systému HANA.                             |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Instance SAP HANA může být mimo provoz.<br/>Požadovaná oprávnění pro práci s databází HANA nejsou nastavená pro Azure Backup. |
| Doporučená akce | Ověřte, zda je databáze SAP HANA. Pokud je databáze spuštěná a spuštěná, ověřte, jestli jsou nastavená všechna požadovaná oprávnění. Pokud některá z oprávnění chybí, spusťte [předregistrační skript](https://aka.ms/scriptforpermsonhana) pro přidání chybějících oprávnění. |

###  <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid 

| Chybová zpráva      | Zadaná instance SAP HANA buď není platná, nebo ji nejde najít. |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Víc instancí SAP HANA na jednom virtuálním počítači Azure se nedá zálohovat. |
| Doporučená akce | Spusťte [skript Předregistrace](https://aka.ms/scriptforpermsonhana) na instanci SAP HANA, kterou chcete zálohovat. Pokud potíže přetrvávají, obraťte se na podporu Microsoftu. |

###  <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation 

| Chybová zpráva      | Zadaná operace SAP HANA není podporovaná.             |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Azure Backup pro SAP HANA nepodporuje přírůstkové zálohování a akce prováděné v SAP HANA nativních klientech (Studio/řídicí panel/DBA). |
| Doporučená akce | Další informace najdete [tady](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

###  <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType 

| Chybová zpráva      | Tato databáze SAP HANA nepodporuje požadovaný typ zálohování. |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Azure Backup nepodporuje přírůstkové zálohování a zálohování pomocí snímků. |
| Doporučená akce | Další informace najdete [tady](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

###  <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure 

| Chybová zpráva      | Řetěz záložního protokolu je porušený.                                   |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Cíl zálohování protokolu mohl být aktualizován z backint na systém souborů nebo může být změněn spustitelný soubor backint. |
| Doporučená akce | Pokud chcete problém vyřešit, spusťte úplnou zálohu.                   |

###  <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore 

| Chybová zpráva      | Zdrojový a cílový systém pro obnovení nejsou kompatibilní.   |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Cílový systém pro obnovení není kompatibilní se zdrojem. |
| Doporučená akce | Další informace o typech obnovení, které jsou v současnosti podporované, najdete v tématu SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) . |

###  <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected 

| Chybová zpráva      | Zjistil se upgrade SDC na MDC.                                  |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Instance SAP HANA byla upgradována z SDC na MDC. Po aktualizaci se zálohování nezdaří. |
| Doporučená akce | Pokud chcete tento problém vyřešit, postupujte podle kroků uvedených v [části Upgrade z SAP HANA 1,0 na 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) . |

###  <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration 

| Chybová zpráva      | Zjistila se neplatná konfigurace backint.                       |
| ------------------ | ------------------------------------------------------------ |
| Možné příčiny    | Parametry zálohování jsou pro Azure Backup nesprávně zadané. |
| Doporučená akce | Ověřte, zda jsou nastaveny následující parametry (backint):<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Pokud jsou na hostiteli přítomné parametry založené na backint, odeberte je. Pokud parametry nejsou k dispozici na úrovni hostitele, ale byly ručně upraveny na úrovni databáze, vraťte je na příslušné hodnoty, jak je popsáno výše. Případně můžete spustit možnost [Zastavit ochranu a zachovat data zálohy](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) z Azure Portal a pak vybrat **obnovit zálohování**. |

## <a name="restore-checks"></a>Obnovit kontroly

### <a name="single-container-database-sdc-restore"></a>Obnovení jediné databáze kontejnerů (SDC)

Při obnovování jedné databáze kontejnerů (SDC) pro HANA na jiný počítač SDC se ujistěte, že zaberete vstupy. Název databáze by měl být uveden malými písmeny a musí obsahovat "sdc" připojený v závorkách. Instance HANA se zobrazí velkými písmeny.

Předpokládat, že je instance SDC HANA "H21" zálohovaná. Na stránce zálohované položky se zobrazí název zálohované položky jako **H21 (SDC)** . Pokud se pokusíte obnovit tuto databázi do jiného cílového SDCu, řekněme h11, že je potřeba zadat následující vstupy.

![Vstupy pro obnovení SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Je třeba počítat s následujícím:

- Ve výchozím nastavení se obnovený název databáze naplní názvem zálohované položky, tj. H21 (SDC).
- Když vyberete cíl jako h11, nemění se automaticky název obnovené databáze. **Měla by být upravena na H11 (SDC)** . V souvislosti s SDCem bude obnovený název databáze ID cílové instance s malými písmeny a "sdc" přidanými v závorkách.
- Vzhledem k tomu, že SDC může mít pouze jednu databázi, je nutné kliknout na zaškrtávací políčko, aby bylo možné přepsat existující databázová data s daty bodu obnovení.
- Linux rozlišuje velká a malá písmena. Proto buďte opatrní, abyste zachovali případ.

### <a name="multiple-container-database-mdc-restore"></a>Vícenásobné obnovení databáze kontejnerů (MDC)

Ve více databázích kontejnerů pro HANA je standardní konfigurace SYSTEMDB + 1 nebo více tenantů databáze. Obnovení celé instance SAP HANA znamená obnovení obou SYSTEMDB i tenantů databáze. Nejdříve obnoví SYSTEMDB a pak pokračuje pro databázi tenanta. System DB v podstatě znamená přepsat systémové informace ve vybraném cíli. Toto obnovení také přepíše informace související s BackInt v cílové instanci. Proto po obnovení systémové databáze do cílové instance jedna musí znovu spustit skript před registrací. Jenom potom se obnoví i následné obnovení databáze tenanta.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade z SAP HANA 1,0 na 2,0

Pokud chráníte databáze SAP HANA 1,0 a chcete upgradovat na 2,0, postupujte podle následujících kroků:

- [Zastavte ochranu](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) s uchováním dat pro starou databázi SDC.
- Znovu spusťte [skript před registrací](https://aka.ms/scriptforpermsonhana) se správnými podrobnostmi k (SID a MDC).
- Opětovné zaregistrování rozšíření (zálohování-> zobrazení podrobností – > Výběr relevantního virtuálního počítače Azure – > opětovné registraci).
- Klikněte na znovu zjistit databáze pro stejný virtuální počítač. Tato akce by měla zobrazit nové databáze v kroku 2 se správnými podrobnostmi (SYSTEMDB and tenant DB, ne SDC).
- Chraňte tyto nové databáze.

## <a name="upgrading-without-an-sid-change"></a>Upgrade bez změny identifikátoru SID

Upgrady na operační systém nebo SAP HANA, které nezpůsobí změnu SID, mohou být zpracovány následujícím způsobem:

- [Zastavení ochrany](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) s uchováním dat pro databázi
- Znovu spustit [skript před registrací](https://aka.ms/scriptforpermsonhana)
- Znovu [Obnovit ochranu](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) pro databázi

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Nejčastější dotazy](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) týkající se zálohování SAP HANA databází na virtuálních počítačích Azure]
