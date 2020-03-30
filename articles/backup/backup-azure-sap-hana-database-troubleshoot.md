---
title: Poradce při potížích s chybami zálohování databází SAP HANA
description: Popisuje, jak řešit běžné chyby, které mohou nastat při zálohování azure backup k zálohování databází SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459306"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Poradce při potížích se zálohováním databází SAP HANA v Azure

Tento článek obsahuje informace o řešení potíží pro zálohování databází SAP HANA na virtuálních počítačích Azure. Další informace o scénářích zálohování SAP HANA, které v současné době podporujeme, naleznete v [tématu Podpora scénářů](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Požadavky a oprávnění

Před [konfigurací](tutorial-backup-sap-hana-db.md#prerequisites) záloh se podívejte na požadavky a na oddíly, které [skript předběžné registrace provádí.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

## <a name="common-user-errors"></a>Běžné chyby uživatelů

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Chybová zpráva**      | <span style="font-weight:normal">Zálohování Azure nemá potřebná oprávnění k provádění zálohování</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Možné příčiny**    | Role mohla být přepsána.                          |
| **Doporučená akce** | Chcete-li problém vyřešit, spusťte skript z podokna **Zjistit db** nebo jej stáhněte [sem](https://aka.ms/scriptforpermsonhana). Případně přidejte roli "SAP_INTERNAL_HANA_SUPPORT" do uživatele zálohování úloh (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOtevřenípřipojení HanaOdbcConnection

| Chybová zpráva      | <span style="font-weight:normal">Připojení k systému HANA se nezdařilo.</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Instance SAP HANA může být vypnutá.<br/>Požadovaná oprávnění pro zálohování Azure pro interakci s databází HANA nejsou nastaveny. |
| **Doporučená akce** | Zkontrolujte, zda je databáze SAP HANA nahoru. Pokud je databáze v provozu, zkontrolujte, zda jsou nastavena všechna požadovaná oprávnění. Pokud některý z oprávnění chybí, spusťte [skript předběžné registrace](https://aka.ms/scriptforpermsonhana) a přidejte chybějící oprávnění. |

### <a name="usererrorhanainstancenameinvalid"></a>UživatelErrorHanaInstanceNameNeplatný

| Chybová zpráva      | <span style="font-weight:normal">Zadaná instance SAP HANA je neplatná nebo ji nelze najít.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Více instancí SAP HANA na jednom virtuálním počítači Azure nelze zálohovat. |
| **Doporučená akce** | Spusťte [skript předběžné registrace](https://aka.ms/scriptforpermsonhana) v instanci SAP HANA, kterou chcete zálohovat. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Chybová zpráva      | <span style="font-weight:normal">Zadaná operace SAP HANA není podporována.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Zálohování Azure pro SAP HANA nepodporuje přírůstkové zálohování a akce prováděné na nativních klientech SAP HANA (Studio/ Cockpit/ DBA Cockpit) |
| **Doporučená akce** | Další informace naleznete [zde](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Chybová zpráva      | <span style="font-weight:normal">Tato databáze SAP HANA nepodporuje požadovaný typ zálohování</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Zálohování Azure nepodporuje přírůstkové zálohování a zálohování pomocí snímků |
| **Doporučená akce** | Další informace naleznete [zde](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>Chyba uživatele HANHANALSNSelháním platnosti

| Chybová zpráva      | <span style="font-weight:normal">Řetězec protokolů zálohování je přerušený.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Cíl zálohování protokolu byl pravděpodobně aktualizován z backintu na systém souborů nebo byl změněn spustitelný soubor backint. |
| **Doporučená akce** | Spuštění úplné zálohy k vyřešení problému                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Chybová zpráva      | <span style="font-weight:normal">Zdrojové a cílové systémy pro obnovení jsou nekompatibilní</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Cílový systém pro obnovení není kompatibilní se zdrojem |
| **Doporučená akce** | Informace o typech obnovení podporovaných dnes naleznete v poznámce SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) |

### <a name="usererrorsdctomdcupgradedetected"></a>Byl zjištěn upgrade userErrorSDCtoMDC

| Chybová zpráva      | <span style="font-weight:normal">Byl zjištěn upgrade SDC na MDC.</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Instance SAP HANA byla upgradována z SDC na MDC. Zálohy se po aktualizaci nezdaří. |
| **Doporučená akce** | Chcete-li problém vyřešit, postupujte podle pokynů uvedených v [části Upgrade ze sap hana 1.0 na 2.0.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) |

### <a name="usererrorinvalidbackintconfiguration"></a>Uživatelská chybaNeplatná konfigurace

| Chybová zpráva      | <span style="font-weight:normal">Zjištěna neplatná konfigurace backintu</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Parametry zálohování jsou nesprávně zadány pro zálohování Azure |
| **Doporučená akce** | Zkontrolujte, zda jsou nastaveny následující (backint) parametry:<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:false]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Pokud backint založené parametry jsou k dispozici v HOST, odeberte je. Pokud parametry nejsou k dispozici na úrovni HOST, ale byly ručně změněny na úrovni databáze, vrátit je k příslušné hodnoty, jak je popsáno výše. Nebo spusťte [ochranu stop a uchovávejte záložní data](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) z webu Azure portal a pak vyberte **Obnovit zálohování**. |

## <a name="restore-checks"></a>Obnovit kontroly

### <a name="single-container-database-sdc-restore"></a>Obnovení databáze jednoho kontejneru (SDC)

Postarejte se o vstupy při obnovení databáze jednoho kontejneru (SDC) pro HANA do jiného počítače SDC. Název databáze by měl být uveden s malá písmena a "sdc" připojené v závorkách. Instance HANA se zobrazí velkými písmeny.

Předpokládejme, že je zálohována instance SDC HANA "H21". Na stránce položky zálohy se zobrazí název položky zálohy jako **"h21(sdc)"**. Pokud se pokusíte obnovit tuto databázi do jiného cíle SDC, řekněme H11, pak následující vstupy je třeba zadat.

![Obnovený název databáze SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Je třeba počítat s následujícím:

- Ve výchozím nastavení bude obnovený název db naplněn názvem položky zálohy. V tomto případě h21(sdc).
- Výběr cíle jako H11 nebude automaticky měnit obnovený název db. **To by mělo být upraveno na h11(sdc)**. Pokud jde o SDC, obnovený název db bude ID cílové instance s malá písmena a 'sdc' připojené v závorkách.
- Vzhledem k tomu, že SDC může mít pouze jednu databázi, je také nutné klepnout na zaškrtávací políčko, aby bylo možné přepsat existující data databáze s daty bodu obnovení.
- Linux rozlišuje malá a velká písmena. Takže buďte opatrní, abyste zachovali případ.

### <a name="multiple-container-database-mdc-restore"></a>Obnovení databáze více kontejnerů (MDC)

Ve více databázíkontejnerů pro HANA je standardní konfigurace SYSTEMDB + 1 nebo více tenantdbů. Obnovení celé instance SAP HANA znamená obnovení systému SYSTEMDB i tenanta DBs. Jeden obnoví SYSTEMDB první a potom pokračuje pro tenanta DB. SystémDB v podstatě znamená přepsat systémové informace o vybraném cíli. Toto obnovení také přepíše informace související s BackInt v cílové instanci. Takže po obnovení systémové databáze do cílové instance spusťte skript předběžné registrace znovu. Teprve potom následné obnovení db bude úspěšné.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade z SAP HANA 1.0 na 2.0

Pokud chráníte databáze SAP HANA 1.0 a chcete upgradovat na 2.0, proveďte následující kroky:

- [Zastavte ochranu](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) s uchováním dat pro starou databázi SDC.
- Proveďte upgrade. Po dokončení, HANA je nyní MDC se systémem DB a tenantdb (y)
- Znovu spusťte [skript předběžné registrace](https://aka.ms/scriptforpermsonhana) se správnými údaji (sid a mdc).
- Znovu zaregistrujte rozšíření pro stejný počítač na webu Azure Portal (podrobnosti o zobrazení zálohování -> -> Vyberte příslušný virtuální počítač Azure -> re-register).
- Klikněte na znovu objevit centrální podniky pro stejný virtuální virtuální hotel. Tato akce by měla zobrazit nové DBs v kroku 2 se správnými podrobnostmi (SYSTEMDB a tenant db, nikoli SDC).
- Nakonfigurujte zálohování pro tyto nové databáze.

## <a name="upgrading-without-an-sid-change"></a>Inovace bez změny SID

Upgrady na operační systém nebo SAP HANA, které nezpůsobují změnu SID, lze zpracovat tak, jak je uvedeno níže:

- [Zastavit ochranu](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) s uchováním dat pro databázi
- Proveďte upgrade.
- Znovu spusťte [skript předběžné registrace](https://aka.ms/scriptforpermsonhana). Obvykle jsme viděli proces upgradu odstraňuje potřebné role. Spuštění skriptu předběžné registrace pomůže ověřit všechny požadované role.
- [Obnovení ochrany](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) databáze znovu

## <a name="re-registration-failures"></a>Selhání opětovné registrace

Před spuštěním operace opětovné registrace zkontrolujte jeden nebo více následujících příznaků:

- Všechny operace (například zálohování, obnovení a konfigurace zálohování) selhávají na virtuálním počítači s jedním z následujících kódů chyb: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Pokud se v oblasti **Stav zálohování** pro položku zálohy zobrazuje **nedosažitelná**, vylučujte všechny ostatní příčiny, které mohou mít za následek stejný stav:

  - Nedostatek oprávnění k provádění operací souvisejících se zálohováním na virtuálním počítači
  - Virtuální počítače se vypíná, takže zálohování nemůže proběhní.
  - Problémy se sítí

Tyto příznaky mohou vzniknout z jednoho nebo více z následujících důvodů:

- Rozšíření bylo odstraněno nebo odinstalováno z portálu.
- Virtuální modul byl obnoven zpět v čase prostřednictvím obnovení disku na místě.
- Virtuální počítač byl vypnut na delší dobu, takže platnost konfigurace rozšíření na něm vypršela.
- Virtuální hotel byl odstraněn a jiný virtuální byl vytvořen se stejným názvem a ve stejné skupině prostředků jako odstraněný virtuální ms.

V předchozích scénářích doporučujeme aktivovat operaci opětovnéregistrace na virtuálním počítači.

## <a name="next-steps"></a>Další kroky

- Projděte si [nejčastější dotazy](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) týkající se zálohování databází SAP HANA na virtuálních počítačích Azure.
