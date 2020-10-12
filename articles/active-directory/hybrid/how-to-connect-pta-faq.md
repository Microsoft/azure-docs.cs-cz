---
title: 'Azure AD Connect: předávací ověřování – Nejčastější dotazy | Microsoft Docs'
description: Odpovědi na nejčastější dotazy týkající se Azure Active Directory předávacího ověřování
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6db477a288ef41c2946cd550af6ff87999eff2f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461270"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory předávací ověřování: nejčastější dotazy

Tento článek se zabývá nejčastějšími dotazy týkajícími se předávacího ověřování Azure Active Directory (Azure AD). Nechte kontrolu nad aktualizovaným obsahem.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Kterou z metod pro přihlášení ke službě Azure AD, předávacímu ověřování, synchronizaci hodnot hash hesel a Active Directory Federation Services (AD FS) (AD FS) mám zvolit?

Přečtěte si [tuto příručku](./choose-ad-authn.md) , kde najdete porovnání různých metod přihlášení do služby Azure AD a jak zvolit správnou metodu přihlašování pro vaši organizaci.

## <a name="is-pass-through-authentication-a-free-feature"></a>Používá předávací ověřování bezplatnou funkci?

Předávací ověřování je bezplatná funkce. Nepotřebujete žádné placené edice Azure AD, abyste ho mohli používat.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Pracuje [podmíněný přístup](../conditional-access/overview.md) s předávacím ověřováním?

Ano. Všechny funkce podmíněného přístupu, včetně Azure Multi-Factor Authentication, fungují s předávacím ověřováním.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Podporuje předávací ověřování "alternativní ID" jako uživatelské jméno místo "userPrincipalName"?
Ano, přihlaste se pomocí nestandardní hodnoty (UPN), jako je například alternativní e-mail, se podporuje jak předávací ověřování (PTA), tak i synchronizace hodnot hash hesel (KOSMETICE). Další informace o [alternativním přihlašovacím ID](../authentication/howto-authentication-use-email-signin.md)

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Funguje synchronizace hodnot hash hesel jako záložní pro předávací ověřování?

Ne. Předávací _ověřování neprovádí automatické_ převzetí služeb při synchronizaci hodnoty hash hesel. Aby nedocházelo k chybám při přihlašování uživatelů, měli byste nakonfigurovat předávací ověřování pro [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Co se stane, když přepnu z synchronizace hodnot hash hesel do předávacího ověřování?

Když pomocí Azure AD Connect přepnete metodu přihlašování z synchronizace hodnot hash hesel do předávacího ověřování, předávací ověřování se bude primární metodou přihlášení pro uživatele ve spravovaných doménách. Všimněte si, že hodnoty hash hesel všech uživatelů, které byly dříve synchronizovány pomocí synchronizace hodnot hash hesel, zůstávají uloženy ve službě Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Můžu nainstalovat konektor [Azure proxy aplikací služby AD](../manage-apps/application-proxy.md) na stejný server jako předávací ověřovací Agent?

Ano. Verze předaného agenta předávacího ověřování, verze 1.5.193.0 nebo novější, podporují tuto konfiguraci.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jaké verze Azure AD Connect a předávacího agenta pro ověřování potřebujete?

Aby tato funkce fungovala, potřebujete verzi 1.1.750.0 nebo novější pro Azure AD Connect a 1.5.193.0 nebo novější pro předávacího agenta ověřování. Nainstalujte veškerý software na servery se systémem Windows Server 2012 R2 nebo novějším.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co se stane, když platnost hesla k mému uživateli vyprší a pokusí se přihlásit pomocí předávacího ověřování?

Pokud jste nakonfigurovali [zpětný zápis hesla](../authentication/concept-sspr-writeback.md) pro konkrétního uživatele a pokud se uživatel přihlásí pomocí předávacího ověřování, může změnit nebo resetovat hesla. Hesla se zapisují zpátky do místní služby Active Directory podle očekávání.

Pokud jste nenakonfigurovali zpětný zápis hesla pro konkrétního uživatele nebo pokud uživatel nemá přiřazenou platnou licenci služby Azure AD, nemůže uživatel aktualizovat heslo v cloudu. Nemůžou aktualizovat svoje heslo, i když platnost hesla vypršela. Uživateli se zobrazí tato zpráva: "vaše organizace neumožňuje aktualizovat heslo na tomto webu. Aktualizujte ji podle metody doporučené vaší organizací, nebo požádejte správce, pokud potřebujete pomoc. Uživatel nebo správce musí heslo resetovat v místní službě Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak se předávací ověřování chrání před útoky hrubou silou nebo heslem?

[Přečtěte si informace o inteligentním uzamčení](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Jak předávací agenti ověřování komunikuje přes porty 80 a 443?

- Ověřovací agenti na všech operacích funkcí vytvářejí požadavky HTTPS přes port 443.
- Agenti ověřování provedou požadavky HTTP přes port 80 ke stažení seznamů odvolaných certifikátů TLS/SSL (CRL).

     >[!NOTE]
     >Poslední aktualizace snížily počet portů, které funkce vyžaduje. Pokud máte starší verze Azure AD Connect nebo ověřovacího agenta, nechte tyto porty otevřené také: 5671, 8080, 9090, 9091, 9350, 9352 a 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Můžou předávací ověřovací agenti komunikovat přes odchozí web proxy server?

Ano. Pokud je v místním prostředí povoleno automatické zjišťování webového proxy serveru (WPAD), ověřovací agenti se automaticky pokusí vyhledat a použít webový proxy server v síti.

Pokud ve svém prostředí nemáte WPAD, můžete přidat informace o proxy serveru (jak je vidět níže) a povolit agentovi předávacího ověřování komunikovat s Azure AD:
- Před instalací agenta předávacího ověřování na server nakonfigurujte v aplikaci Internet Explorer informace o proxy serveru. To vám umožní dokončit instalaci ověřovacího agenta, ale na portálu pro správu se pořád zobrazí jako **neaktivní** .
- Na serveru přejděte do složky C:\Program Files\Microsoft Azure AD Connect Authentication agent.
- Upravte konfigurační soubor "AzureADConnectAuthenticationAgentService" a přidejte následující řádky (nahraďte http \: //contosoproxy.com:8080 "skutečnou adresou proxy serveru):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Můžu na stejný server nainstalovat dva nebo více předávacích agentů ověřování?

Ne, na jeden server můžete nainstalovat jenom jednoho předávacího agenta pro ověřování. Pokud chcete pro vysokou dostupnost nakonfigurovat předávací ověřování, [postupujte podle pokynů uvedených tady](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Musím ručně obnovovat certifikáty používané agenty předávacího ověřování?

Komunikace mezi každým předávacím agentem ověřování a službou Azure AD je zabezpečená pomocí ověřování založeného na certifikátech. Tyto [certifikáty se v Azure AD automaticky Obnovují každých několik měsíců](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Tyto certifikáty není nutné ručně obnovovat. V případě potřeby můžete vyčistit starší certifikáty s vypršenou platností.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Návody odebrat předávacího agenta ověřování?

Pokud je spuštěn předávací agent ověřování, zůstane aktivní a nepřetržitě zpracovává požadavky na přihlášení uživatele. Chcete-li odinstalovat ověřovacího agenta, klepněte na **Ovládací panely – > programy – > programy a funkce** a odinstalujte **ověřovacího agenta Microsoft Azure AD Connect** a Microsoft Azure AD programy pro aktualizace **agenta Connect** .

Pokud zaškrtnete okno předávací ověřování v [centru pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozího kroku, zobrazí se agent ověřování zobrazený jako **neaktivní**. Toto je _očekávané_. Agent ověřování se automaticky vynechává ze seznamu po 10 dnech.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Už používám AD FS k přihlášení do služby Azure AD. Návody přepnout na předávací ověřování?

Pokud provádíte migraci z AD FS (nebo jiných federačních technologií) na předávací ověřování, důrazně doporučujeme, abyste provedli náš podrobný průvodce nasazením, který jste publikovali [tady](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Je možné používat předávací ověřování v prostředí Active Directory s více doménovými strukturami?

Ano. Prostředí s více doménovými strukturami jsou podporovaná, pokud existují vztahy důvěryhodnosti mezi doménovými strukturami služby Active Directory (obousměrně) a pokud je Směrování přípon názvů správně nakonfigurované.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Poskytuje předávací ověřování vyrovnávání zatížení mezi více agenty ověřování?

Ne, instalace více agentů předávacího ověřování zajišťuje jenom [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Neposkytuje deterministické vyrovnávání zatížení mezi ověřovacími agenty. Libovolný ověřovací Agent (náhodně) může zpracovat žádost o přihlášení konkrétního uživatele.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Kolik předávacích agentů ověřování Potřebuji nainstalovat?

Instalace více agentů předávacího ověřování zajišťuje [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Ale neposkytuje deterministické vyrovnávání zatížení mezi ověřovacími agenty.

Vezměte v úvahu špičku a průměrnou zátěž žádostí o přihlášení, které očekáváte ve svém tenantovi. Jako srovnávací test může jeden agent ověřování zpracovat 300 až 400 ověřování za sekundu na standardním procesoru se 4 jádry, 16 GB paměti RAM.

K odhadu provozu v síti použijte následující pokyny k určení velikosti:
- Každá žádost má velikost datové části (0,5 KB + 1 tisíc × num_of_agents) bajtů; To znamená, že data z Azure AD do ověřovacího agenta. Zde "num_of_agents" označuje počet agentů ověřování registrovaných ve vašem tenantovi.
- Každá odpověď má velikost datové části 1 tisíc bajtů; To znamená, že data z ověřovacího agenta do Azure AD.

U většiny zákazníků jsou celkem dva nebo tři ověřovací agenti dostačující pro vysokou dostupnost a kapacitu. Měli byste nainstalovat agenty ověřování blízko k řadičům domény, aby se zlepšila latence přihlášení.

>[!NOTE]
>Omezení počtu 40 ověřovacích agentů na každého tenanta je systému.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Je možné nainstalovat prvního předávacího agenta na jiný server, než je ten, který spouští Azure AD Connect?

Ne, tento scénář není _podporován._

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Proč potřebuji k povolení předávacího ověřování zadat jenom cloudový účet globálního správce?

Doporučuje se povolit nebo zakázat předávací ověřování pomocí účtu globálního správce jenom pro Cloud. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../fundamentals/add-users-azure-active-directory.md). Tím se zajistí, že se nezamknete z vašeho tenanta.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak můžu zakázat předávací ověřování?

Spusťte Průvodce Azure AD Connect a změňte metodu přihlašování uživatele z předávacího ověřování na jinou metodu. Tato změna zakazuje předávací ověřování v tenantovi a odinstaluje agenta ověřování ze serveru. Agenty ověřování musíte ručně odinstalovat z jiných serverů.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co se stane, když odinstalujem předávacího ověřovacího agenta?

Pokud odinstalujete průchozího ověřovacího agenta ze serveru, způsobí to, že server přestane přijímat žádosti o přihlášení. Abyste se vyhnuli narušení schopnosti přihlašování uživatelů v tenantovi, ujistěte se, že máte spuštěný jiný ověřovací Agent před tím, než odinstalujete předávacího agenta ověřování.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Mám staršího tenanta, který byl původně nastaven pomocí AD FS.  Nedávno jsme migrovali na PTA, ale teď nevidíme naše změny hlavního názvu uživatele, které se synchronizují do Azure AD.  Proč se naše hlavní názvy UPN nesynchronizují?

Odpověď: v následujících případech se vaše místní změny hlavního názvu uživatele (UPN) nemusí synchronizovat, pokud:

- Tenant Azure AD se vytvořil před 15. června 2015.
- Původně jste federované s vaším klientem Azure AD pomocí AD FS pro ověřování.
- Přepnuli jste se na spravované uživatele pomocí PTA jako ověřování.

Důvodem je, že výchozí chování klientů vytvořených před 15. června 2015 bylo zablokování změn hlavního názvu uživatele.  Pokud potřebujete zrušit blokování změn hlavního názvu uživatele, musíte spustit následující rutinu prostředí PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Klienti vytvoření po 15. června 2015 mají výchozí chování při synchronizaci změn hlavního názvu uživatele (UPN).   



## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které nejsou.
- [Rychlý Start](how-to-connect-pta-quick-start.md): zprovoznění při předávacím ověřování Azure AD.
- [Migrace z AD FS na předávací ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Naučte se konfigurovat funkci inteligentního uzamknutí pro vašeho tenanta pro ochranu uživatelských účtů.
- [Technický hluboký podrobně](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Řešení potíží](tshoot-connect-pass-through-authentication.md): Naučte se řešit běžné problémy s funkcí předávacího ověřování.
- [Hloubkové podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Přečtěte si další informace o této doplňkové funkci.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte Fórum Azure Active Directory k započetí nových požadavků na funkce.
