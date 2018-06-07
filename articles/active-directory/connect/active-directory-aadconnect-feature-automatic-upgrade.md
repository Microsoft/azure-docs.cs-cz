---
title: 'Azure AD Connect: Automatický upgrade | Microsoft Docs'
description: Toto téma popisuje předdefinované funkce Automatické aktualizace v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: adfce1195a6a322e0225ee09a45148001d0167c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592125"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatický upgrade
Tato funkce byla zavedená s sestavení 1.1.105.0 (vydané. února 2016).

## <a name="overview"></a>Přehled
Instalace služby Azure AD Connect je vždy aktuální má ještě nikdy nebylo snazší s **automatický upgrade** funkce. Tato funkce povolena ve výchozím nastavení pro expresní instalace a upgradu nástroje DirSync. Po vydání nové verze, instalace automaticky upgradován.
Automatický upgrade je povoleno ve výchozím nastavení pro následující:

* Expresní nastavení instalace a upgrade nástroje DirSync.
* Pomocí SQL Express LocalDB, což je, co se expresní nastavení vždy použít. DirSync s SQL Express také použít LocalDB.
* Účet AD je to výchozí účet MSOL_ vytvořené Expresní nastavení a DirSync.
* Máte méně než 100 000 objektů v úložišti metaverse.

Aktuální stav automatický upgrade lze zobrazit pomocí rutiny prostředí PowerShell `Get-ADSyncAutoUpgrade`. Má následující stavy:

| Stav | Poznámka |
| --- | --- |
| Povoleno |Je povolen automatický upgrade. |
| Pozastaveno |Nastavte pouze v systému. V systému už není vhodné na automatické aktualizace. |
| Zakázáno |Automatický upgrade je zakázána. |

Můžete volit mezi **povoleno** a **zakázané** s `Set-ADSyncAutoUpgrade`. Pouze v systému měli nastavit stav **pozastaveno**.

Automatický upgrade používá Azure AD Connect Health pro upgrade infrastruktury. Pro automatický upgrade fungovat, ujistěte se, máte otevřený adresy URL v vašeho proxy serveru pro **Azure AD Connect Health** podle postupu uvedeného v [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Pokud **Synchronization Service Manager** uživatelského rozhraní běží na serveru a pak upgrade je pozastaveno, dokud zavření uživatelského rozhraní.

## <a name="troubleshooting"></a>Řešení potíží
Pokud vaše instalace Connect není sám sebe aktualizovat podle očekávání, postupujte podle těchto kroků a zjistěte, co mohou být nesprávné.

První není pravděpodobné, že automatického upgradu se pokus o první den, kdy je vydání nové verze. Před pokusem o upgrade, nemusíte sleduje výstražné zařízení, pokud vaše instalace není upgradována okamžitě je úmyslné náhodnost.

Pokud si myslíte, něco není pravé, nejprve spusťte `Get-ADSyncAutoUpgrade` zajistit je povolen automatický upgrade.

Ujistěte se, že máte otevřený požadované adresy URL v proxy serveru nebo brány firewall. Automatické aktualizace se pomocí Azure AD Connect Health, jak je popsáno v [přehled](#overview). Pokud používáte proxy server, ujistěte se, stavu byl nakonfigurován pro použití [proxy serveru](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Taky testovat, [připojení stavu](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do služby Azure AD.

S připojením ke službě Azure AD ověřit je čas, aby viděl protokoly událostí. Spusťte Prohlížeč událostí a zkontrolujte **aplikace** protokolu událostí. Přidat filtr protokolu událostí pro zdroj **Azure AD Connect Upgrade** a rozsah id událostí **300-399**.  
![Filtr protokolu událostí pro automatický upgrade](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Nyní můžete vidět protokoly událostí související se stavem pro automatický upgrade.  
![Filtr protokolu událostí pro automatický upgrade](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Kód výsledku má předponu přehled stavu.

| Předpona kód výsledku | Popis |
| --- | --- |
| Úspěch |Instalace byla úspěšně upgradována. |
| UpgradeAborted |Dočasné podmínce zastavena upgradu. Bude akci opakovat a očekává se, že úspěšné později. |
| UpgradeNotSupported |Systém má konfigurace, která blokuje v systému z automaticky upgraduje. Se bude opakovat, pokud chcete zobrazit, pokud je změna stavu, ale očekává se, že systém musí být upgradovány ručně. |

Tady je seznam nejčastějších zprávy, které můžete najít. Neobsahuje všechny seznam, ale zpráva výsledku by měla být zrušte s co problém je.

| Zpráva výsledku | Popis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nelze zapsat do registru. |
| UpgradeAbortedInsufficientDatabasePermissions |Předdefinované skupiny administrators nemá oprávnění k databázi. Ručně upgradujte na nejnovější verzi služby Azure AD Connect a tento problém vyřešit. |
| UpgradeAbortedInsufficientDiskSpace |Není dostatek místa na disku pro podporu upgradu. |
| UpgradeAbortedSecurityGroupsNotPresent |Nelze najít a vyřešte všechny skupiny zabezpečení používané synchronizační modul. |
| UpgradeAbortedServiceCanNotBeStarted |Služba NT **Microsoft Azure AD Sync** se nepodařilo spustit. |
| UpgradeAbortedServiceCanNotBeStopped |Služba NT **Microsoft Azure AD Sync** se nepovedlo zastavit. |
| UpgradeAbortedServiceIsNotRunning |Služba NT **Microsoft Azure AD Sync** neběží. |
| UpgradeAbortedSyncCycleDisabled |Možnost SyncCycle v [Plánovač](active-directory-aadconnectsync-feature-scheduler.md) byla zakázána. |
| UpgradeAbortedSyncExeInUse |[Uživatelské rozhraní Správce služby synchronizace](active-directory-aadconnectsync-service-manager-ui.md) na serveru je otevřený. |
| UpgradeAbortedSyncOrConfigurationInProgress |Průvodce instalací běží nebo synchronizace byla naplánována mimo plánovače. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Vybrali jste jako metoda přihlašování do služby AD FS. | 
| UpgradeNotSupportedCustomizedSyncRules |Vaše vlastní pravidla jste přidali do konfigurace. |
| UpgradeNotSupportedDeviceWritebackEnabled |Povolili jste [zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md) funkce. |
| UpgradeNotSupportedGroupWritebackEnabled |Povolili jste [zpětný zápis skupin](active-directory-aadconnect-feature-preview.md#group-writeback) funkce. |
| UpgradeNotSupportedInvalidPersistedState |Instalace není Expresní nastavení nebo upgradu nástroje DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Máte více než 100 000 objektů v úložišti metaverse. |
| UpgradeNotSupportedMultiForestSetup |Se připojujete k více než jedné doménové struktuře. Expresní instalace připojuje pouze k jedné doménové struktuře. |
| UpgradeNotSupportedNonLocalDbInstall |Nepoužíváte databázi SQL serveru Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Účet konektoru AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) není výchozí účet MSOL_ už. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Při nastavování AAD Connect, jste zvolili *nekonfigurujte* při výběru metody přihlašování. | 
| UpgradeNotSupportedPtaSignInMethod | Předávací ověřování vybrány jako metoda přihlašování. |
| UpgradeNotSupportedStagingModeEnabled |Server je nastaven v [pracovním režimu](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Povolili jste [zpětný zápis uživatelů](active-directory-aadconnect-feature-preview.md#user-writeback) funkce. |

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
