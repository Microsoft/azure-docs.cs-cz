---
title: 'Azure AD Connect: Automatický upgrade | Dokumenty společnosti Microsoft'
description: Toto téma popisuje integrovanou funkci automatického upgradu v synchronizaci Azure AD Connect.
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
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60349814"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatický upgrade
Tato funkce byla představena s sestavením [1.1.105.0 (vydáno v únoru 2016)](reference-connect-version-history.md#111050).  Tato funkce byla aktualizována v [sestavení 1.1.561](reference-connect-version-history.md#115610) a nyní podporuje další scénáře, které dříve nebyly podporovány.

## <a name="overview"></a>Přehled
Ujistěte se, že vaše instalace Azure AD Connect je vždy aktuální, nebylo nikdy jednodušší s funkcí **automatického upgradu.** Tato funkce je ve výchozím nastavení povolena pro expresní instalace a upgrady DirSync. Po vydání nové verze je instalace automaticky upgradována.
Automatický upgrade je ve výchozím nastavení povolen pro následující:

* Expresní instalace nastavení a inovace DirSync.
* Použití SQL Express LocalDB, což je to, co Express nastavení vždy používat. DirSync s SQL Express také použít LocalDB.
* Účet služby AD je výchozí účet MSOL_ vytvořený pomocí nastavení Express a DirSync.
* Mají méně než 100 000 objektů v metaverse.

Aktuální stav automatického upgradu lze zobrazit pomocí `Get-ADSyncAutoUpgrade`rutiny prostředí PowerShell . Má následující stavy:

| Stav | Poznámka |
| --- | --- |
| Povoleno |Automatický upgrade je povolen. |
| Dočasně blokován. |Nastaveno pouze systémem. Systém **není v současné době** způsobilý pro automatické inovace. |
| Zakázáno |Automatický upgrade je zakázán. |

Můžete přepínat mezi `Set-ADSyncAutoUpgrade` **povoleno** a **zakázáno** s . Pouze systém by měl nastavit stav **Pozastaveno**.  Před 1.1.750.0 by rutina Set-ADSyncAutoUpgrade zablokovala automatický upgrade, pokud by byl stav automatického upgradu nastaven na Pozastaveno. Tato funkce se nyní změnila, takže neblokuje automatický upgrade.

Automatický upgrade používá Azure AD Connect Health pro infrastrukturu upgradu. Chcete-li automatický upgrade fungovat, ujistěte se, že jste otevřeli adresy URL na serveru proxy pro **Azure AD Connect Health,** jak je zdokumentováno v [adresách URL Office 365 a rozsahech IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Pokud je na serveru spuštěno **uj.**

## <a name="troubleshooting"></a>Řešení potíží
Pokud se instalace připojení neupgraduje podle očekávání, postupujte podle následujících kroků a zjistěte, co může být špatně.

Nejprve byste neměli očekávat, že se o automatický upgrade pokusí první den, kdy bude vydána nová verze. Před pokusem o upgrade dochází k záměrné náhodnosti, takže se nemusíte znepokojovat, pokud vaše instalace není upgradována okamžitě.

Pokud si myslíte, že něco `Get-ADSyncAutoUpgrade` není v pořádku, pak nejprve spustit, aby bylo zajištěno automatické upgradování je povolena.

Poté se ujistěte, že jste otevřeli požadované adresy URL v proxy nebo firewallu. Automatická aktualizace používá Azure AD Connect Health, jak je popsáno v [přehledu](#overview). Pokud používáte proxy server, ujistěte se, že stav byl nakonfigurován pro použití [proxy serveru](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Otestujte [také připojení stavu](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ke službě Azure AD.

S připojením k Azure AD ověřena, je čas podívat se do protokolů událostí. Spusťte prohlížeč událostí a podívejte se do protokolu událostí **aplikace.** Přidejte filtr protokolů událostí pro zdrojový **upgrade služby Azure AD Connect** a rozsah id události **300-399**.  
![Filtr eventlogpro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Nyní můžete zobrazit protokoly událostí přidružené ke stavu automatického upgradu.  
![Filtr eventlogpro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kód výsledku má předponu s přehledem stavu.

| Předpona kódu výsledku | Popis |
| --- | --- |
| Úspěch |Instalace byla úspěšně upgradována. |
| Upgradebyl přerušen. |Dočasná podmínka zastavila upgrade. Bude znovu zopakován a očekává se, že se později podaří. |
| Upgrade není podporován. |Systém má konfiguraci, která blokuje automatické inovace systému. Bude opakován, aby zjistil, zda se stav mění, ale očekává se, že systém musí být upgradován ručně. |

Zde je seznam nejčastějších zpráv, které najdete. Neuvádí všechny, ale zpráva o výsledech by měla být jasná s tím, v čem je problém.

| Zpráva o výsledech | Popis |
| --- | --- |
| **Upgradebyl přerušen.** | |
| UpgradePřerušenoJetoRetoRetoUupgrademarkeru |Nelze zapisovat do registru. |
| UpgradePřerušenoNedostatečnáoprávněnídatabáze |Předdefinovaná skupina administrators nemá oprávnění k databázi. Ručně upgradujte na nejnovější verzi Služby Azure AD Connect, abyste tento problém vyřešili. |
| UpgradePřerušenoInsufficientDiskSpace |Pro podporu upgradu není dostatek místa na disku. |
| UpgradePřerušenoSecurityGroupsNotPresent |Nelze najít a vyřešit všechny skupiny zabezpečení používané synchronizačním strojem. |
| UpgradeAbortedServiceCanNotNotReStarted |Spuštění služby **Microsoft Azure AD Sync** se nezdařilo. |
| UpgradeAbortedServiceCanNotNotBeZastaven |Služba **NT Microsoft Azure AD Sync** se nepodařilo zastavit. |
| UpgradePřerušenoJenem není spuštěno. |Služba **NT Microsoft Azure AD Sync** není spuštěna. |
| UpgradePřerušenoSynchronIzační cyklus zakázán |Možnost SyncCycle v [plánovači](how-to-connect-sync-feature-scheduler.md) byla zakázána. |
| UpgradePřerušenoSynchronEInUse |Na serveru je otevřeno [umulovacího modulu správce služeb](how-to-connect-sync-service-manager-ui.md) synchronizace. |
| UpgradePřerušenoSynchronizaceVprůběhu |Průvodce instalací je spuštěn nebo byla před plánovačem naplánována synchronizace. |
| **Upgrade není podporován.** | |
| UpgradeNotSupportedAdfsSignInMethod | Jako metodu přihlášení jste vybrali adfs. |
| UpgradeNotSupportedCustomizedSyncRules |Do konfigurace jste přidali vlastní pravidla. |
| UpgradeNotSupportedDeviceWritebackEnabled |Povolili jste funkci [zpětného zápisu zařízení.](how-to-connect-device-writeback.md) |
| UpgradeNotSupportedGroupWritebackEnabled |Povolili jste funkci [zpětného zápisu skupiny.](how-to-connect-preview.md#group-writeback) |
| UpgradeNotSupportedNesutentičně neplatnéhoStavu |Instalace není expresní nastavení nebo upgrade DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeedededed |Máte více než 100 000 objektů v metaverse. |
| UpgradeNotSupportedMultiForestSetup |Připojujete se k více než jedné doménové struktuře. Expresní nastavení se připojuje pouze k jedné doménové struktuře. |
| UpgradeNotSupportedNonLocalDbInstall |Nepoužíváte databázi SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Účet konektoru ad ds](reference-connect-accounts-permissions.md#ad-ds-connector-account) již není výchozí MSOL_ účet. |
| UpgradeNotSupportedNotConfiguredSignInMetoda | Při nastavování služby AAD Connect jste při výběru metody přihlášení zvolili *možnost Nekonfigurovat.* |
| Metoda UpgradeNotSupportedPtaSignIn | Jako metodu přihlášení jste vybrali předávací ověřování. |
| UpgradenotSupportedStagingModeEnabled |Server je nastaven na [pracovní režim](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Povolili jste funkci [zpětného zápisu uživatele.](how-to-connect-preview.md#user-writeback) |

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
