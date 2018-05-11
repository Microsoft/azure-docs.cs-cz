---
title: 'Azure AD Connect: Předávací ověřování – nejčastější dotazy | Microsoft Docs'
description: Odpovědi na nejčastější dotazy k Azure Active Directory předávací ověřování
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: 7a817092581ef0be0b6be063282f7484c46ff094
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory předávací ověřování: Nejčastější dotazy

Tento článek řeší nejčastější dotazy k Azure Active Directory (Azure AD) předávací ověřování. Kontrolovat zpátky na aktualizovaný obsah.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Které metody, které se přihlásit k Azure AD, předávací ověřování hesla hash synchronizace a služby Active Directory Federation Services (AD FS), mám zvolit?

To závisí na místním prostředí a požadavků organizace. Zkontrolujte [možnosti přihlášení uživatele Azure AD Connect](active-directory-aadconnect-user-signin.md) článku porovnání metod Azure AD přihlášení.

## <a name="is-pass-through-authentication-a-free-feature"></a>Předávací ověřování je bezplatná funkce?

Předávací ověřování je bezplatná funkce. Nepotřebujete žádné placené edice Azure AD pro použití.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Je k dispozici v předávací ověřování [cloudu Microsoft Azure v Německu](http://www.microsoft.de/cloud-deutschland) a [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Ne. Předávací ověřování je k dispozici pouze v celosvětové instance služby Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Nemá [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) pracovní pomocí předávacího ověřování?

Ano. Všechny funkce podmíněného přístupu, včetně ověřování Azure Multi-Factor Authentication, pracovat s předávací ověřování.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Podporuje předávací ověřování "Alternativní ID" jako uživatelské jméno, místo "userPrincipalName"?

Ano. Předávací ověřování podporuje `Alternate ID` jako uživatelské jméno při konfiguraci ve službě Azure AD Connect. Další informace najdete v tématu [vlastní instalace Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Ne všechny aplikace Office 365 podporují `Alternate ID`. Naleznete v prohlášení o odborné pomoci dokumentace konkrétní aplikaci.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synchronizaci hodnoty hash hesla fungovat jako nouzové řešení předávací ověřování?

Ne. Předávací ověřování _nemá_ automaticky převzetí služeb při selhání pro synchronizaci hodnoty hash hesla. Pouze slouží jako záložní pro [scénáře, které předávací ověřování nepodporuje Dnes](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Aby se zabránilo neúspěšných přihlášení uživatele, měli byste nakonfigurovat předávací ověřování pro [vysokou dostupnost](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Je možné nainstalovat [Azure AD Application Proxy](../manage-apps/application-proxy.md) konektor na stejném serveru jako Agent předávací ověřování?

Ano. Přejmenované verzích předávací ověřování agenta, verze 1.5.193.0 nebo novější, tuto konfiguraci podporovat.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jaké verze Azure AD Connect a předávací ověřování agenta potřebujete?

Tato funkce fungovala, musíte verze 1.1.486.0 nebo novější pro Azure AD Connect a 1.5.58.0 nebo novější pro agenta předávací ověřování. Veškerý software nainstalujte na servery se systémem Windows Server 2012 R2 nebo novější.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co se stane, pokud vypršela platnost hesla pro daného uživatele a zkuste se přihlásit pomocí předávacího ověřování?

Pokud jste nakonfigurovali [zpětný zápis hesla](../active-directory-passwords-update-your-own-password.md) pro konkrétního uživatele, a pokud se uživatel přihlásí pomocí předávacího ověřování, můžou změnit a resetovat jejich hesla. Hesla jsou zapsány zpět na místní služby Active Directory podle očekávání.

Pokud jste nenakonfigurovali zpětný zápis hesla pro konkrétního uživatele nebo pokud uživatel nemá platný Azure AD licence přiřazen, uživatel nemůže aktualizovat heslo, v cloudu. Jejich nelze aktualizovat heslo, i v případě, že vypršela platnost jejich hesla. Uživateli se místo toho zobrazí tato zpráva: "vaše organizace vám neumožňuje aktualizujte své heslo na tomto webu. Aktualizovat způsobem doporučovaným vaší organizací, nebo požádejte správce, pokud potřebujete pomoc." Uživatel nebo správce musí obnovit své heslo v místní službě Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak předávací ověřování ochraně před útoky hrubou silou heslo?

Čtení [ověřování Azure Active Directory průchozí: inteligentní uzamčení](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) Další informace.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co předávací ověřování agenti komunikovat přes porty 80 a 443?

- Agenti ověřování provádět požadavky HTTPS přes port 443 pro všechny operace funkce.
- Agenti ověřování provádět požadavky HTTP přes port 80 ke stažení SSL seznamy odvolaných certifikátů (CRL).

     >[!NOTE]
     >Poslední aktualizace snižuje počet portů, které funkce vyžaduje. Pokud máte starší verze Azure AD Connect nebo ověřování agenta, nechat tyto porty otevřít také: 5671, 8080, 9090, 9091, 9350, 9352 a 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Předávací ověřování agenti komunikovat přes odchozí webového proxy serveru?

Ano. Pokud Proxy Auto-Discovery WPAD (Web) je povoleno v místním prostředí, ověřování agentů automaticky pokusí najít a používají webový proxy server v síti.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Můžete nainstalovat dvě nebo více agentů předávací ověřování na stejném serveru?

Ne, můžete nainstalovat pouze jeden Agent předávací ověřování na jednom serveru. Pokud chcete nakonfigurovat předávací ověřování pro zajištění vysoké dostupnosti, postupujte podle pokynů v [ověřování Azure Active Directory průchozí: rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Odebrání agenta předávací ověřování

Tak dlouho, dokud je spuštěn Agent předávací ověřování, zůstane aktivní a průběžně zpracovává požadavky přihlášení uživatele. Pokud chcete odinstalovat agenta ověřování, přejděte na **ovládací panely -> programy -> programy a funkce** a odinstalovat i **agenta služby Microsoft Azure AD Connect ověřování** a  **Microsoft Azure AD Connect agenta aktualizační** programy.

Pokud zaškrtnete okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozího kroku, zobrazí se Agent ověřování zobrazuje jako **neaktivní**. Toto je _očekává_. Agent ověřování se automaticky ukončí ze seznamu po několik dní.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>I služby AD FS už používáte k přihlášení do služby Azure AD. Jak lze přepnout ho předávací ověřování?

Pokud jste nakonfigurovali službu AD FS jako způsob se přihlásit prostřednictvím Průvodce službou Azure AD Connect, změňte metodu, která uživatel používá pro přihlášení k předávací ověřování. Tato změna umožňuje předávací ověřování u klienta a převede _všechny_ federované domény na spravované domény. Předávací ověřování zpracuje všechny následné žádosti pro přihlášení ke klientovi. V současné době není nijak podporované v rámci Azure AD Connect používat kombinaci služby AD FS a předávací ověřování v různých doménách.

Pokud byl služby AD FS nakonfigurovaný jako metodu pro přihlášení _mimo_ Průvodce Azure AD Connect, změnit přihlášení uživatele metodu předávací ověřování. Provedete tuto změnu z **nekonfigurujte** možnost. Tato změna umožňuje předávací ověřování u klienta, ale všechny federované domény bude dál používat službu AD FS pro přihlášení. Pomocí prostředí PowerShell ručně převést některé nebo všechny tyto federované domény spravované domény. Po provedení této změny *pouze* předávací ověřování zpracovává všechny požadavky pro přihlášení ke spravované domény.

>[!IMPORTANT]
>Předávací ověřování nemůže pracovat přihlášení ke cloudové službě Azure AD users.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Můžete použít předávací ověřování v prostředí s více doménovými strukturami služby Active Directory?

Ano. Prostředí s více doménovými strukturami jsou podporovány, pokud existují vztahy důvěryhodnosti doménové struktury mezi vaší doménové struktury služby Active Directory a v případě směrování přípon názvů je správně nakonfigurován.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Kolik předávací ověřování agentů je nutné nainstalovat?

Instalace více agentů předávací ověřování zajišťuje [vysokou dostupnost](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Ale neposkytuje deterministický vyrovnávání zátěže mezi agenty ověřování.

Vezměte v úvahu maximální a průměrné zatížení přihlášení požadavků, které byste měli vidět na klientovi. Jako srovnávací test může zpracovávat jednoho agenta ověřování 300 až 400 ověření za sekundu na standardní 4 jádra procesoru, 16 GB paměti RAM serveru.

Chcete-li odhadnout síťový provoz, použijte následující pokyny k dimenzování:
- Každý požadavek má velikost datové části (0.5K + 1 tisíc * num_of_agents) bajtů. například data z Azure AD k ověřování agenta. Zde "num_of_agents" označuje, že počet agentů ověřování registrovaných na vašeho klienta.
- Každá odpověď má velikost datové části 1 kB; například data od agenta ověřování do služby Azure AD.

Pro většinu zákazníků dvě nebo tři agenty ověřování celkem postačí pro vysokou dostupnost a kapacity. Musíte nainstalovat agenty ověřování blízko řadičích domény ke zlepšení latence přihlášení.

>[!NOTE]
>Existuje omezení systému 12 ověřování agentů podle jednotlivých klientů.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Můžete nainstalovat první Agent předávací ověřování na jiném serveru než ten, který spouští Azure AD Connect?

Ne, tento scénář je _není_ podporována.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak můžete zakázat předávací ověřování?

Spusťte znovu Průvodce službou Azure AD Connect a změňte metoda přihlašování uživatele z předávací ověřování na jinou metodu. Tato změna zakáže předávací ověřování u klienta a ze serveru odinstaluje agenta pro ověřování. Musíte ručně odinstalovat agenty ověřování z jiných serverů.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co se stane, když odinstalovat agenta předávací ověřování?

Pokud odinstalujete agenta předávací ověřování ze serveru, stáhne server přestane přijímat požadavky na přihlášení. Aby se zabránilo rozdělení funkce přihlašování uživatelů z vašeho klienta, zajistěte, abyste měli jiné spuštěn Agent ověřování, než odinstalujete agenta předávací ověřování.

## <a name="next-steps"></a>Další postup
- [Aktuální omezení](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [Rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md): zprovoznění na Azure AD předávací ověřování.
- [Inteligentní uzamčení](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení na vašeho klienta k ochraně uživatelské účty.
- [Podrobné technické informace](active-directory-aadconnect-pass-through-authentication-how-it-works.md): pochopit, jak funguje funkci předávací ověřování.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Přečtěte si o řešení běžných problémů s funkcí předávací ověřování.
- [Podrobné informace zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): podrobný technický informace o funkci předávací ověřování.
- [Azure AD bezproblémové SSO](active-directory-aadconnect-sso.md): Další informace o této funkci vzájemně doplňují.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fóru Azure Active Directory do souboru žádosti o nové funkce.

