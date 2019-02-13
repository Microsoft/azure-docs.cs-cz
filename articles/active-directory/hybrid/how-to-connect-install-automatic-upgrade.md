---
title: 'Azure AD Connect: Automatický upgrade | Dokumentace Microsoftu'
description: Toto téma popisuje integrované funkce automatického upgradu ve službě Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6083237426e86220f8a3cb7fbe0cdfc30f44f8cc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208822"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatický upgrade
Tato funkce byla zavedena v systému sestavení [1.1.105.0 (všeobecně dostupné. února 2016)](reference-connect-version-history.md#111050).  Tato funkce byla aktualizována v [sestavení 1.1.561](reference-connect-version-history.md#115610) a teď podporuje další scénáře, které dříve nebyly podporovány.

## <a name="overview"></a>Přehled
Ujistěte se, vaši instalaci služby Azure AD Connect je vždy aktuální nikdy nebylo jednodušší s **automatický upgrade** funkce. Tato funkce je povolena ve výchozím nastavení pro expresní instalace a upgrade nástroje DirSync. Po vydání nové verze, dojde k automatickému upgradu vaší instalaci.
Automatický upgrade je povoleno standardně pro následující:

* Expresní nastavení instalace a upgrade nástroje DirSync.
* Použití SQL Express LocalDB, což je, co se expresní nastavení vždy použít. DirSync s SQL Express také používat LocalDB.
* Účet AD je výchozí účet MSOL_ vytvořeného expresním nastavením a DirSync.
* Máte méně než 100 000 objektů v úložišti metaverse.

Aktuální stav automatického upgradu lze zobrazit pomocí rutiny Powershellu `Get-ADSyncAutoUpgrade`. Má následující stavy:

| Stav | Poznámka |
| --- | --- |
| Povoleno |Je povolen automatický upgrade. |
| Pozastaveno |Nastavte pouze v systému. Systém je **není aktuálně** nárok na automatické upgrady. |
| Zakázáno |Automatický upgrade je zakázaná. |

Můžete změnit mezi **povoleno** a **zakázané** s `Set-ADSyncAutoUpgrade`. Jen systém by měl nastavit stav **pozastaveno**.

Automatický upgrade je upgrade infrastruktury pomocí služby Azure AD Connect Health. Pro automatický upgrade na práci, ujistěte se, že jste otevřeli adresy URL vašeho proxy serveru pro **Azure AD Connect Health** podle postupu uvedeného v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Pokud **Synchronization Service Manager** uživatelského rozhraní je spuštěn na serveru a pak upgrade je pozastaveno, dokud zavření uživatelského rozhraní.

## <a name="troubleshooting"></a>Řešení potíží
Pokud vaše instalace Connect neupgraduje samotný podle očekávání, postupujte podle těchto kroků a zjistěte, co mohou být nesprávné.

Nejprve by neměli očekávat automatický upgrade na pokus o první den, kdy je vydána nová verze. Před pokusem upgrade tak nemělo překvapit Pokud instalaci není upgradována okamžitě je úmyslné náhodnost.

Pokud si myslíte, něco není v pořádku, nejprve spusťte `Get-ADSyncAutoUpgrade` k povolte automatický upgrade.

Ujistěte se, že jste otevřeli požadované adresy URL v proxy serveru nebo brány firewall. Automatická aktualizace je pomocí Azure AD Connect Health, jak je popsáno v [přehled](#overview). Pokud používáte proxy server, ujistěte se, že stav je nakonfigurovaná pro použití [proxy serveru](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Také otestovat [stavu připojení](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do služby Azure AD.

Připojení k Azure AD ověřit je čas podívat protokoly událostí. Spusťte Prohlížeč událostí a podívejte se **aplikace** protokolu událostí. Přidat filtr protokolu událostí pro zdroj **Azure AD Connect Upgrade** a rozsah id událostí **300-399**.  
![Filtr protokolu událostí pro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Nyní je vidět protokoly událostí související se stavem pro automatický upgrade.  
![Filtr protokolu událostí pro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kód výsledku má předponu přehled o stavu.

| Předpona kód výsledku | Popis |
| --- | --- |
| Úspěch |Instalace byla úspěšně upgradována. |
| UpgradeAborted |Dočasný zastavena upgradu. Bude akci opakovat a očekává se, že proběhne později. |
| UpgradeNotSupported |Systém má konfiguraci, která blokuje systém automaticky upgraduje. Pokud chcete zobrazit, pokud se mění stav, ale očekává se, že systém musí být upgradovány ručně provede. |

Tady je seznam nejběžnějších zprávy, které najdete. Neobsahuje všechny, ale zpráva výsledku by měl být vymazat jaký problém se.

| Zpráva výsledku | Popis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nelze zapsat do registru. |
| UpgradeAbortedInsufficientDatabasePermissions |Předdefinované skupiny administrators nemá oprávnění k databázi. Ručně upgradujte na nejnovější verzi služby Azure AD Connect a tento problém vyřešit. |
| UpgradeAbortedInsufficientDiskSpace |Není dostatek místa na disku pro podporu upgradu. |
| UpgradeAbortedSecurityGroupsNotPresent |Nelze vyhledat a odstranit všechny skupiny zabezpečení, které jsou použity modulem synchronizace. |
| UpgradeAbortedServiceCanNotBeStarted |Spuštění služby NT **Microsoft Azure AD Sync** se nepovedlo spustit. |
| UpgradeAbortedServiceCanNotBeStopped |Spuštění služby NT **Microsoft Azure AD Sync** se nepovedlo zastavit. |
| UpgradeAbortedServiceIsNotRunning |Spuštění služby NT **Microsoft Azure AD Sync** neběží. |
| UpgradeAbortedSyncCycleDisabled |Možnost SyncCycle v [Plánovač](how-to-connect-sync-feature-scheduler.md) byla zakázána. |
| UpgradeAbortedSyncExeInUse |[Synchronization service Manageru uživatelského rozhraní](how-to-connect-sync-service-manager-ui.md) na serveru je otevřený. |
| UpgradeAbortedSyncOrConfigurationInProgress |Průvodce instalací je spuštěna nebo k synchronizaci byla naplánována mimo plánovače. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Vybrali jste jako metoda pro přihlašování do služby AD FS. | 
| UpgradeNotSupportedCustomizedSyncRules |Vaše vlastní pravidla jste přidali do konfigurace. |
| UpgradeNotSupportedDeviceWritebackEnabled |Povolíte [zpětný zápis zařízení](how-to-connect-device-writeback.md) funkce. |
| UpgradeNotSupportedGroupWritebackEnabled |Povolíte [zpětný zápis skupin](how-to-connect-preview.md#group-writeback) funkce. |
| UpgradeNotSupportedInvalidPersistedState |Instalaci se nepovedlo Expresní nastavení nebo upgradu nástroje DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Máte více než 100 000 objektů v úložišti metaverse. |
| UpgradeNotSupportedMultiForestSetup |Připojujete se k více než jednu doménovou strukturu. Expresní instalace pouze připojí k jedné doménové struktuře. |
| UpgradeNotSupportedNonLocalDbInstall |Nepoužíváte databázi SQL Server Express LocalDB. |d
| UpgradeNotSupportedNonMsolAccount |[Účet AD DS konektoru](reference-connect-accounts-permissions.md#ad-ds-connector-account) MSOL_ výchozího účtu už není. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Při nastavování AAD Connect, zvolíte *nekonfigurujte* při výběru metody přihlašování. | 
| UpgradeNotSupportedPtaSignInMethod | Jako metodu přihlašování jste vybrali předávací ověřování. |
| UpgradeNotSupportedStagingModeEnabled |Server je nastaven v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Povolíte [zpětný zápis uživatelů](how-to-connect-preview.md#user-writeback) funkce. |

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
