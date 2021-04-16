---
title: Řešení potíží s chybami zálohování SAP HANAových databází
description: Popisuje, jak řešit běžné chyby, ke kterým může dojít při použití Azure Backup k zálohování databází SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517228"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Řešení potíží se zálohováním databází SAP HANA v Azure

Tento článek poskytuje informace o řešení potíží při zálohování SAP HANA databází na virtuálních počítačích Azure. Další informace o scénářích SAP HANA Backup, které momentálně podporujeme, najdete v tématu [Podpora scénářů](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Předpoklady a oprávnění

Informace o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript před registrací zahrnuje](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , najdete v částech před konfigurací záloh.

## <a name="common-user-errors"></a>Běžné chyby uživatelů

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Chybová zpráva**      | <span style="font-weight:normal">Azure Backup nemá požadovaná oprávnění role k provedení zálohování.</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Možné příčiny**    | Tato role mohla být přepsána.                          |
| **Doporučená akce** | Pokud chcete problém vyřešit, spusťte skript z podokna **Vyhledat databázi** nebo si ho stáhněte [tady](https://aka.ms/scriptforpermsonhana). Případně přidejte roli ' SAP_INTERNAL_HANA_SUPPORT ' do uživatele pro zálohování úloh (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Chybová zpráva      | <span style="font-weight:normal">Nepovedlo se připojit k systému HANA.</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Instance SAP HANA může být mimo provoz.<br/>Požadovaná oprávnění pro Azure Backup interakci s databází HANA nejsou nastavena. |
| **Doporučená akce** | Ověřte, zda je databáze SAP HANA. Pokud je databáze spuštěná a spuštěná, ověřte, jestli jsou nastavená všechna požadovaná oprávnění. Pokud některá z oprávnění chybí, spusťte [předregistrační skript](https://aka.ms/scriptforpermsonhana) pro přidání chybějících oprávnění. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Chybová zpráva      | <span style="font-weight:normal">Zadaná instance SAP HANA buď není platná, nebo ji nejde najít.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Víc instancí SAP HANA na jednom virtuálním počítači Azure se nedá zálohovat. |
| **Doporučená akce** | Spusťte [skript Předregistrace](https://aka.ms/scriptforpermsonhana) na instanci SAP HANA, kterou chcete zálohovat. Pokud potíže přetrvávají, obraťte se na podporu Microsoftu. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Chybová zpráva      | <span style="font-weight:normal">Zadaná operace SAP HANA není podporovaná.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Azure Backup pro SAP HANA nepodporuje přírůstkové zálohování a akce prováděné na SAP HANA nativních klientech (Studio/řídicí panel/DBA). |
| **Doporučená akce** | Další informace najdete [tady](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Chybová zpráva      | <span style="font-weight:normal">Řetěz záložního protokolu je porušený.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Cíl zálohování protokolu mohl být aktualizován z backint na systém souborů nebo může být změněn spustitelný soubor backint. |
| **Doporučená akce** | Pokud chcete problém vyřešit, spusťte úplnou zálohu.                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Chybová zpráva      | <span style="font-weight:normal">Zjistil se upgrade SDC na MDC.</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Instance SAP HANA byla upgradována z SDC na MDC. Po aktualizaci se zálohování nezdaří. |
| **Doporučená akce** | Pokud chcete tento problém vyřešit, postupujte podle kroků uvedených v [SDC a MDC upgrade](#sdc-to-mdc-upgrade-with-a-change-in-sid) . |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Chybová zpráva      | <span style="font-weight:normal">Zjistila se neplatná konfigurace backint.</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Možné příčiny**    | Parametry zálohování jsou pro Azure Backup nesprávně zadané. |
| **Doporučená akce** | Ověřte, zda jsou nastaveny následující parametry (backint):<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Pokud jsou na hostiteli přítomné parametry založené na backint, odeberte je. Pokud parametry nejsou k dispozici na úrovni hostitele, ale byly ručně upraveny na úrovni databáze, vraťte je na příslušné hodnoty, jak je popsáno výše. Případně můžete spustit možnost [Zastavit ochranu a zachovat data zálohy](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z Azure Portal a pak vybrat **obnovit zálohování**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Chybová zpráva  |Zdrojový a cílový systém pro obnovení nejsou kompatibilní.  |
|---------|---------|
|Možné příčiny   | Zdrojové a cílové systémy vybrané k obnovení jsou nekompatibilní.        |
|Doporučená akce   |   Ujistěte se, že váš scénář obnovení není v následujícím seznamu možných nekompatibilních obnovení: <br><br>   **Případ 1:** SYSTEMDB se během obnovování nedá přejmenovat.  <br><br> **Případ 2:** Source-SDC a target-MDC: zdrojová databáze nemůže být v cíli obnovena jako databáze SYSTEMDB nebo tenanta. <br><br> **Případ 3:** Source-MDC a target-SDC: zdrojová databáze (SYSTEMDB nebo tenant DB) se nedá obnovit do cíle. <br><br>  Další informace najdete v části Poznámka **1642148** v [hlavní části podpory SAP](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Obnovit kontroly

### <a name="single-container-database-sdc-restore"></a>Obnovení jediné databáze kontejnerů (SDC)

Při obnovování jedné databáze kontejnerů (SDC) pro HANA na jiný počítač SDC se ujistěte, že zaberete vstupy. Název databáze by měl být uveden malými písmeny a musí obsahovat "sdc" připojený v závorkách. Instance HANA se zobrazí velkými písmeny.

Předpokládat, že je instance SDC HANA "H21" zálohovaná. Na stránce zálohované položky se zobrazí název zálohované položky jako **H21 (SDC)**. Pokud se pokusíte obnovit tuto databázi do jiného cílového SDCu, řekněme h11, že je potřeba zadat následující vstupy.

![Název databáze SDC se obnovil.](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Je třeba počítat s následujícím:

- Ve výchozím nastavení se název obnovené databáze naplní názvem zálohované položky. V tomto případě H21 (SDC).
- Výběr cíle jako h11 nemění automaticky obnovený název databáze. **Měla by být upravena na H11 (SDC)**. V souvislosti s SDCem bude obnovený název databáze ID cílové instance s malými písmeny a "sdc" přidanými v závorkách.
- Vzhledem k tomu, že SDC může mít pouze jednu databázi, je nutné zaškrtnout políčko, aby bylo možné přepsat existující databázová data s daty bodu obnovení.
- Linux rozlišuje velká a malá písmena. Buďte proto opatrní při zachování případu.

### <a name="multiple-container-database-mdc-restore"></a>Vícenásobné obnovení databáze kontejnerů (MDC)

Ve více databázích kontejnerů pro HANA je standardní konfigurace SYSTEMDB + 1 nebo více tenantů databáze. Obnovení celé instance SAP HANA znamená obnovení obou SYSTEMDB i tenantů databáze. Nejdříve obnoví SYSTEMDB a pak pokračuje pro databázi tenanta. System DB v podstatě znamená přepsat systémové informace ve vybraném cíli. Toto obnovení také přepíše informace související s BackInt v cílové instanci. Takže po obnovení systémové databáze do cílové instance znovu spusťte skript před registrací. Jenom potom se obnoví i následné obnovení databáze tenanta.

## <a name="back-up-a-replicated-vm"></a>Zálohování replikovaného virtuálního počítače

### <a name="scenario-1"></a>Scénář 1

Původní virtuální počítač se replikoval pomocí Azure Site Recovery nebo zálohování virtuálního počítače Azure. Nový virtuální počítač byl sestaven pro simulaci starého virtuálního počítače. To znamená, že nastavení jsou přesně stejná. (Důvodem je skutečnost, že původní virtuální počítač byl odstraněn a obnovení bylo provedeno ze zálohy virtuálního počítače nebo Azure Site Recovery).

Tento scénář může zahrnovat dva možné případy. Přečtěte si, jak zálohovat replikovaný virtuální počítač v obou těchto případech:

1. Nově vytvořený virtuální počítač má stejný název a je ve stejné skupině prostředků a předplatném jako odstraněný virtuální počítač.

    - Rozšíření již na virtuálním počítači existuje, ale není viditelné pro žádné služby.
    - Spusťte skript před registrací
    - Znovu zaregistrujte rozšíření pro stejný počítač v Azure Portal (  ->  **Podrobnosti o zobrazení** zálohy – > vyberte příslušný virtuální počítač Azure – > znovu zaregistrovat).
    - Již existující databáze zálohovaných (z odstraněného virtuálního počítače) by se pak měly úspěšně zálohovat.

2. Nově vytvořený virtuální počítač má jednu z těchto akcí:

    - jiný název než odstraněný virtuální počítač
    - stejný název jako odstraněný virtuální počítač, ale je v jiné skupině prostředků nebo předplatném (ve srovnání s odstraněným virtuálním počítačem)

    Pokud se jedná o tento případ, proveďte následující kroky:

    - Rozšíření již na virtuálním počítači existuje, ale není viditelné pro žádné služby.
    - Spusťte skript před registrací
    - Pokud zjistíte a chráníte nové databáze, začnete na portálu zobrazovat duplicitní aktivní databáze. Pokud se tomu chcete vyhnout, [Zastavte ochranu při zachování dat](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pro staré databáze. Pak pokračujte zbývajícími kroky.
    - Vyhledat databáze pro povolení zálohování
    - Povolit zálohování těchto databází
    - Už existující zálohované databáze (z odstraněného virtuálního počítače) se budou dál ukládat do trezoru (s uloženými zálohami podle zásad).

### <a name="scenario-2"></a>Scénář 2

Původní virtuální počítač se replikoval pomocí Azure Site Recovery nebo zálohování virtuálního počítače Azure. Nový virtuální počítač byl vytvořen z obsahu, který se má použít jako šablona. Toto je nový virtuální počítač s novým identifikátorem SID.

Pokud chcete povolit zálohování na novém virtuálním počítači, postupujte podle těchto kroků:

- Rozšíření již na virtuálním počítači existuje, ale není viditelné pro žádné služby.
- Spusťte skript před registrací. Na základě identifikátoru SID nového virtuálního počítače může dojít k dvěma scénářům:
  - Původní virtuální počítač a nový virtuální počítač mají stejný identifikátor SID. Skript před registrací se úspěšně spustí.
  - Původní virtuální počítač a nový virtuální počítač mají různé identifikátory SID. Skript před registrací se nezdaří. Pokud potřebujete pomoc v tomto scénáři, obraťte se na podporu.
- Vyhledat databáze, které chcete zálohovat
- Povolit zálohování těchto databází

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade verze SDC nebo upgrade verze MDC na stejném virtuálním počítači

Upgrady na operační systém, změnu verze SDC nebo změny verze MDC, které nezpůsobí změnu SID, mohou být zpracovány následujícím způsobem:

- Ujistěte se, že [Azure Backup aktuálně podporuje](sap-hana-backup-support-matrix.md#scenario-support) nová verze operačního systému, verze SDC nebo MDC.
- [Zastavení ochrany s uchováním dat](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pro databázi
- Provedení upgradu nebo aktualizace
- Znovu spusťte skript před registrací. Proces upgradu často může odebrat [potřebné role](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Spuštění předregistračního skriptu vám pomůže ověřit všechny požadované role.
- Znovu obnovit ochranu pro databázi

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC upgrade na MDC bez změny v SID

Upgrady z SDC na MDC, které nezpůsobují změnu SID, mohou být zpracovány následujícím způsobem:

- Zajistěte, aby byla nová verze MDC aktuálně [podporována nástrojem Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Zastavení ochrany s uchováním dat](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pro starou databázi SDC
- Proveďte upgrade. Po dokončení se systém HANA teď MDC se systémovou databází a databáze tenanta.
- Znovu spustit [skript před registrací](https://aka.ms/scriptforpermsonhana)
- Znovu zaregistrujte rozšíření pro stejný počítač v Azure Portal (  ->  **Podrobnosti o zobrazení** zálohy – > vyberte příslušný virtuální počítač Azure – > znovu zaregistrovat).
- Vyberte možnost znovu **zjistit databáze** pro stejný virtuální počítač. Tato akce by měla zobrazit nové databáze v kroku 3 jako SYSTEMDB a databázi tenanta, ne SDC
- Starší databáze SDC bude i nadále existovat v trezoru a budou mít v souladu se zásadami zachována stará zálohovaná data.
- Konfigurace zálohování pro tyto databáze

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC na MDC upgrade se změnou v SID

Upgrady z SDC na MDC, které způsobují změnu SID, mohou být zpracovány následujícím způsobem:

- Zajistěte, aby byla nová verze MDC aktuálně [podporována nástrojem Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Zastavení ochrany s uchováním dat** pro starou databázi SDC
- Proveďte upgrade. Po dokončení se systém HANA teď MDC se systémovou databází a databáze tenanta.
- Znovu spusťte [skript před registrací](https://aka.ms/scriptforpermsonhana) se správnými podrobnostmi (nový identifikátor SID a MDC). Kvůli změně v identifikátoru SID může při úspěšném spuštění skriptu dorazit k problémům. Pokud čelíte problémům, kontaktujte podporu Azure Backup.
- Znovu zaregistrujte rozšíření pro stejný počítač v Azure Portal (  ->  **Podrobnosti o zobrazení** zálohy – > vyberte příslušný virtuální počítač Azure – > znovu zaregistrovat).
- Vyberte možnost znovu **zjistit databáze** pro stejný virtuální počítač. Tato akce by měla zobrazit nové databáze v kroku 3 jako SYSTEMDB a databázi tenanta, ne SDC
- Starší databáze SDC bude i nadále existovat v trezoru a budou mít stará zálohovaná data uchovávaná podle zásad.
- Konfigurace zálohování pro tyto databáze

## <a name="re-registration-failures"></a>Selhání opětovné registrace

Než zahájíte operaci opětovného zápisu, proveďte kontrolu jednoho nebo více následujících příznaků:

- Všechny operace (například zálohování, obnovení a konfigurace zálohování) selžou na virtuálním počítači s jedním z následujících kódů chyb: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Pokud je v oblasti **stavu zálohování** pro zálohovanou položku **nedostupná**, vyfiltrujte všechny ostatní příčiny, které by mohly mít za následek stejný stav:

  - Nedostatečná oprávnění k provádění operací souvisejících se zálohováním virtuálního počítače
  - Virtuální počítač je vypnutý, takže zálohy nejdou uskutečnit.
  - Problémy se sítí

K těmto potížím může dojít z některého z následujících důvodů:

- Rozšíření bylo na portálu odstraněno nebo odinstalováno.
- Virtuální počítač se znovu obnovil v čase prostřednictvím místního obnovení disku.
- Po delší dobu byl virtuální počítač vypnutý, takže platnost konfigurace rozšíření na něm vypršela.
- Virtuální počítač se odstranil a vytvořil se jiný virtuální počítač se stejným názvem a ve stejné skupině prostředků jako odstraněný virtuální počítač.

V předchozích scénářích doporučujeme, abyste na virtuálním počítači aktivovali operaci opětovného zápisu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Nejčastější dotazy](./sap-hana-faq-backup-azure-vm.yml) týkající se zálohování SAP HANA databází na virtuálních počítačích Azure.
