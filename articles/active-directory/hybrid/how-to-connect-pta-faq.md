---
title: 'Služby Azure AD Connect: Předávací ověřování – nejčastější dotazy | Dokumentace Microsoftu'
description: Odpovědi na nejčastější dotazy týkající se předávací ověřování Azure Active Directory
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4f2ceae349c921ce0d83fb7401e3b18404722763
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362904"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory předávací ověřování: Nejčastější dotazy

Tento článek se zabývá nejčastější dotazy týkající se předávací ověřování služby Azure Active Directory (Azure AD). Kontrolovat zpět pro aktualizaci obsahu.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Které metody pro přihlášení k Azure AD, předávací ověřování hesla hash synchronizace a Active Directory Federation Services (AD FS), mám zvolit?

Kontrola [Tato příručka](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) porovnání různé služby Azure AD přihlášení metod a jak zvolit metodu přímo přihlášení pro vaši organizaci.

## <a name="is-pass-through-authentication-a-free-feature"></a>Předávací ověřování je bezplatná funkce?

Předávací ověřování je bezplatná funkce. Není nutné žádné placené edice Azure AD, aby ho použít.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Je k dispozici v předávací ověřování [cloudu Microsoft Azure Germany](http://www.microsoft.de/cloud-deutschland) a [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Ne. Předávací ověřování je k dispozici pouze v celosvětové instance služby Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Nemá [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) fungují s předávací ověřování?

Ano. Všechny možnosti podmíněného přístupu, včetně Azure Multi-Factor Authentication, pracovat s předávací ověřování.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Podporuje předávací ověřování "Alternativní ID" jako uživatelské jméno, namísto "userPrincipalName"?

Ano. Předávací ověřování podporuje `Alternate ID` jako uživatelské jméno při konfiguraci ve službě Azure AD Connect. Další informace najdete v tématu [vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md). Ne všechny aplikace Office 365 podporují `Alternate ID`. Odkazovat na konkrétní aplikaci dokumentace k podpoře.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synchronizaci hodnot hash hesel fungují jako záložní předávací ověřování?

Ne. Předávací ověřování _nemá_ automatické převzetí služeb při selhání pro synchronizaci hodnot hash hesel. Aby se zabránilo neúspěšných přihlášení uživatele, měli byste nakonfigurovat předávací ověřování pro [vysoké dostupnosti](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Je možné nainstalovat [Proxy aplikací Azure AD](../manage-apps/application-proxy.md) konektor na stejném serveru jako Agent předávací ověřování?

Ano. Přejmenované verze předávací ověřování agenta, verze 1.5.193.0 nebo později, tuto konfiguraci podporovat.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jaké verze služby Azure AD Connect a předávací ověřování agenta je potřeba?

Tato funkce fungovala, musíte verze 1.1.750.0 nebo později pro Azure AD Connect a 1.5.193.0 nebo novější pro předávací ověřování agenta. Veškerý software nainstalujte na servery s Windows serverem 2012 R2 nebo novější.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co se stane, pokud vypršela platnost hesla pro daného uživatele a zkuste se přihlásit pomocí předávacího ověřování?

Pokud jste nakonfigurovali [zpětný zápis hesla](../authentication/concept-sspr-writeback.md) pro konkrétního uživatele, a pokud se uživatel přihlásí pomocí předávacího ověřování, můžete změnit nebo resetování hesel. Hesel, zapíšou se zpět do místní služby Active Directory podle očekávání.

Pokud jste nenakonfigurovali zpětný zápis hesla pro konkrétního uživatele, nebo pokud uživatel nemá platnou Azure AD licenci přiřazenou, uživatel nemůže aktualizovat své heslo služby v cloudu. Nemohou aktualizovat heslo, i v případě, že vypršela platnost jejich hesla. Uživateli se místo toho zobrazí tato zpráva: "vaše organizace nepovoluje aktualizaci hesla na tomto webu. Aktualizovat způsobem doporučeným ve vaší organizaci, nebo požádejte správce, pokud potřebujete pomoc." Uživatel nebo správce musí resetovat své heslo v místní službě Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak předávací ověřování chrání vás před útoky na hesla hrubou silou?

[Přečtěte si informace o inteligentním uzamčením](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co předávací ověřování agenti začnou komunikovat přes porty 80 a 443?

- Agentů ověřování vytvářet požadavky HTTPS přes port 443 pro všechny operace funkce.
- Agentů ověřování vytvářet požadavky HTTP přes port 80 k stažení SSL seznamy odvolaných certifikátů (CRL).

     >[!NOTE]
     >Nejnovější aktualizace snížený počet porty, které vyžaduje funkci. Pokud máte starší verze služby Azure AD Connect nebo ověřovacího agenta, ponechte tyto porty otevřít také: 5671 8080, 9090, 9091, 9350, 9352 a 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Komunikovat přes odchozí webového proxy serveru se službou agentů předávací ověřování?

Ano. Pokud Proxy Auto-Discovery WPAD (Web) je povoleno v místním prostředí, agentů ověřování automaticky pokusí vyhledat a použít webový proxy server v síti.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Můžete nainstalovat dvě nebo více agentů předávací ověřování na stejný server?

Ne, můžete jenom nainstalovat jeden ověřovací Agent nebyl předávací na jednom serveru. Pokud chcete nakonfigurovat předávací ověřování pro zajištění vysoké dostupnosti, [postupujte podle zde uvedených pokynů](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak odstranit agenta předávací ověřování?

Dokud agenta předávací ověřování je spuštěno, zůstane aktivní a průběžně zpracovává požadavky přihlášení uživatele. Pokud chcete odinstalovat ověřovacího agenta, přejděte na **ovládací panely -> programy -> programy a funkce** a odinstalujte i **agenta služby Microsoft Azure AD Connect ověřování** a  **Microsoft Azure AD Connect agenta Updater** programy.

Pokud najdete v okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozího kroku, zobrazí se ověřovací Agent zobrazuje jako **neaktivní**. Toto je _očekává_. Ověřovací Agent se automaticky ukončí ze seznamu po několika dnech.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Služba AD FS už používám pro přihlášení k Azure AD. Jak se přepíná ho na předávací ověřování?

Pokud migrujete ze služby AD FS (nebo jiné technologie federation) na předávací ověřování, doporučujeme, abyste postupovali podle našeho podrobné nasazení Průvodce publikování [tady](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Můžete použít předávací ověřování v prostředí s více doménovými strukturami služby Active Directory?

Ano. Podporují se prostředí s více doménovými strukturami, pokud existují vztahy důvěryhodnosti doménové struktury mezi vaší doménové struktury služby Active Directory a v případě směrování přípon názvů je správně nakonfigurovaný.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Kolik agentů předávací ověřování je nutné nainstalovat?

Instalace více agentů předávací ověřování zajišťuje [vysoké dostupnosti](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Ale neposkytuje deterministické rozložení zátěže mezi agentů ověřování.

Vezměte v úvahu maximální a průměrné zatížení žádostí o přihlášení, které byste měli vidět ve svém tenantovi. Jako srovnávací test může zpracovávat jeden ověřovací Agent 300 až 400 ověření za sekundu na standardní 4jádrový procesor, 16 GB paměti RAM serveru.

Pokud chcete odhadnout síťový provoz, použijte následující pokyny velikosti:
- Velikost datové části je každý požadavek (0.5K + 1 tisíc * num_of_agents) bajtů. To znamená data ze služby Azure AD k ověřování agenta. Tady "num_of_agents" označuje, že počet agentů ověřování zaregistrovaný ve svém tenantovi.
- Každou odpověď má velikost datové části 1 kB; tj. data z agentů ověřování do služby Azure AD.

Pro většinu zákazníků dvě nebo tři agenti ověřování celkem jsou dostačující pro vysokou dostupnost a kapacitu. Měli byste nainstalovat agentů ověřování blízko řadičů domén tak zlepšit latenci přihlášení.

>[!NOTE]
>Platí omezení systému 12 agentů ověřování každého tenanta.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Můžete nainstalovat první předávací ověřování agenta na jiném serveru než ten, který spouští služby Azure AD Connect?

Ne, tento scénář je _není_ podporována.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Proč potřebuji výhradně cloudový účet globálního správce pro povolit předávací ověřování

Doporučuje povolení nebo zakázání předávacího ověřování s využitím výhradně cloudový účet globálního správce. Další informace o [přidání výhradně cloudový účet globálního správce](../active-directory-users-create-azure-portal.md). Teď už takto se zajistí, že nezůstanete zamknutí mimo vašeho tenanta.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak zakázat předávací ověřování?

Znovu spusťte Průvodce Azure AD Connect a změňte metodu přihlašování uživatele z předávací ověřování na jinou metodu. Tato změna zakáže předávací ověřování u klienta a odinstaluje ověřovací Agent ze serveru. Musíte ručně odinstalovat agenty ověřování z jiných serverů.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co se stane, když při odinstalaci agenta předávací ověřování?

Pokud odinstalujete agenta předávací ověřování ze serveru, způsobí, že server zastavit přijímání žádostí o přihlášení. Pokud chcete vyhnout přerušení funkce přihlašování uživatelů ve vašem tenantovi, ujistěte se, že máte jiného ověřovacího agenta spuštěna před odinstalováním agenta předávací ověřování.

## <a name="next-steps"></a>Další postup
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [Rychlý start](how-to-connect-pta-quick-start.md): uvedení do provozu na předávacího ověřování Azure AD.
- [Migrace ze služby AD FS na předávací ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – podrobné pokyny k migraci ze služby AD FS (nebo jiné technologie federation) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení ve svém tenantovi k ochraně uživatelské účty.
- [Podrobné technické informace](how-to-connect-pta-how-it-works.md): pochopit, jak funguje funkce předávací ověřování.
- [Řešení potíží s](tshoot-connect-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): získáte podrobné technické informace o funkci předávací ověřování.
- [Azure AD bezproblémového jednotného přihlašování k](how-to-connect-sso.md): Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fórum Azure Active Directory do souboru žádostí o nové funkce.

