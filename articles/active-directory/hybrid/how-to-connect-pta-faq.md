---
title: 'Azure AD Connect: Předávací ověřování – nejčastější dotazy | Dokumenty společnosti Microsoft'
description: Odpovědi na nejčastější dotazy týkající se předávacího ověřování služby Azure Active Directory
services: active-directory
keywords: Předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59bf7ae5914f5cc886d95f25b36abccfdf09c4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331290"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Předávací ověřování služby Azure Active Directory: Nejčastější dotazy

Tento článek řeší nejčastější dotazy týkající se předávacího ověřování služby Azure Active Directory (Azure AD). Pokračujte v kontrole aktualizovaného obsahu.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Kterou metodu přihlášení k Azure AD, předávací ověřování, synchronizaci hodnot hash hesla a službu AD FS (Active Directory Federation Services) mám zvolit?

V [této příručce](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) najdete porovnání různých metod přihlášení služby Azure AD a jak zvolit správnou metodu přihlášení pro vaši organizaci.

## <a name="is-pass-through-authentication-a-free-feature"></a>Je předávací ověřování bezplatnou funkcí?

Předávací ověřování je bezplatná funkce. K jeho použití nepotřebujete žádné placené edice Azure AD.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>Je předávací ověřování dostupné v [cloudu Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) a [v cloudu Microsoft Azure Government?](https://azure.microsoft.com/features/gov/)

Ne. Předávací ověřování je k dispozici jenom v celosvětové instanci Azure AD.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Funguje [podmíněný přístup](../active-directory-conditional-access-azure-portal.md) s předávacím ověřováním?

Ano. Všechny funkce podmíněného přístupu, včetně azure vícefaktorového ověřování, fungují s předávacím ověřováním.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Podporuje předávací ověřování jako uživatelské jméno místo "userPrincipalName"?
Přihlaste se pomocí hodnoty bez hlavního jména, jako je například alternativní e-mail, je aktuálně testován v privátní verzi Preview pro předávací ověřování (PTA) a synchronizaci hash hesla (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Funguje synchronizace hash hesla jako záložní k předávacímu ověřování?

Ne. Předávací ověřování _automaticky nepřevezme_ službu při selhání na synchronizaci hodnot hash hesel. Chcete-li se vyhnout chybám přihlášení uživatele, měli byste nakonfigurovat předávací ověřování pro [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Co se stane, když přejdu ze synchronizace hash hesla na předávací ověřování?

Při použití Služby Azure AD Connect přepnout metodu přihlášení z synchronizace hash hesla na předávací ověřování, předávací ověřování se stane primární přihlašovací metodou pro uživatele ve spravovaných doménách. Upozorňujeme, že všechny hashy hesel uživatelů, které byly dříve synchronizovány synchronizací hodnot hash hesla, zůstávají uloženy ve službě Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Můžu nainstalovat konektor [proxy aplikace Azure AD](../manage-apps/application-proxy.md) na stejný server jako předávacího agenta ověřování?

Ano. Tuto konfiguraci podporují rebrandované verze předávacího agenta ověřování verze 1.5.193.0 nebo novějšího.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jaké verze Azure AD Connect a předávacího agenta ověřování potřebujete?

Aby tato funkce fungovala, potřebujete verzi 1.1.750.0 nebo novější pro Azure AD Connect a 1.5.193.0 nebo novější pro předávacího agenta ověřování. Nainstalujte veškerý software na servery se systémem Windows Server 2012 R2 nebo novějším.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co se stane, pokud vypršela platnost hesla uživatele a pokusí se přihlásit pomocí předávacího ověřování?

Pokud jste nakonfigurovali [zpětný zápis hesla](../authentication/concept-sspr-writeback.md) pro konkrétního uživatele a uživatel se přihlásí pomocí předávacího ověřování, může změnit nebo obnovit svá hesla. Hesla jsou zapsána zpět do místní služby Active Directory podle očekávání.

Pokud jste nenakonfigurovali zpětný zápis hesla pro konkrétního uživatele nebo pokud uživatel nemá přiřazenou platnou licenci Azure AD, uživatel nemůže aktualizovat své heslo v cloudu. Nemohou aktualizovat své heslo, a to ani v případě, že jejich heslo vypršelo. Uživatel místo toho vidí tuto zprávu: "Vaše organizace neumožňuje aktualizovat heslo na tomto webu. Aktualizujte ji podle metody doporučené vaší organizací nebo se zeptejte správce, pokud potřebujete pomoc." Uživatel nebo správce musí resetovat své heslo v místní službě Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak vás předávací ověřování chrání před útoky hrubou silou hesel?

[Přečtěte si informace o inteligentním uzamčení](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co předávací agenti ověřování komunikují přes porty 80 a 443?

- Agenti ověřování provádět požadavky HTTPS přes port 443 pro všechny operace s funkcemi.
- Agenti ověřování pořite požadavky HTTP přes port 80 ke stažení seznamů odvolaných certifikátů TLS/SSL (CRL).

     >[!NOTE]
     >Poslední aktualizace snížily počet portů, které tato funkce vyžaduje. Pokud máte starší verze Azure AD Connect nebo agenta ověřování, ponechte tyto porty otevřené také: 5671, 8080, 9090, 9091, 9350, 9352 a 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Mohou předávací agenti ověřování komunikovat přes odchozí webový proxy server?

Ano. Pokud je v místním prostředí povoleno automatické zjišťování webového proxy serveru (WPAD), agenti ověřování se automaticky pokusí vyhledat a použít webový proxy server v síti.

Pokud nemáte WPAD ve vašem prostředí, můžete přidat proxy informace (jak je znázorněno níže) povolit předávací ověřování agent komunikovat s Azure AD:
- Před instalací předávacího agenta ověřování na server nakonfigurujte informace o serveru serveru. To vám umožní dokončit instalaci agenta ověřování, ale na portálu pro správu se stále zobrazí jako **neaktivní.**
- Na serveru přejděte na "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Upravte konfigurační soubor "AzureADConnectAuthenticationAgentService" a\:přidejte následující řádky (nahraďte "http //contosoproxy.com:8080" skutečnou adresou proxy):

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

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Mohu na stejný server nainstalovat dva nebo více předávacích agentů ověřování?

Ne, můžete nainstalovat pouze jednoho předávacího agenta ověřování na jeden server. Chcete-li nakonfigurovat předávací ověřování pro vysokou dostupnost, [postupujte podle pokynů zde](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Musím ručně obnovovat certifikáty používané předávacími agenty ověřování?

Komunikace mezi jednotlivými předávacími agenty ověřování a službou Azure AD je zabezpečená pomocí ověřování na základě certifikátu. Tyto [certifikáty se automaticky obnovují každých několik měsíců službou Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Tyto certifikáty není nutné ručně obnovovat. Podle potřeby můžete vyčistit starší certifikáty s prošlou platností.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak odeberu předávacího agenta ověřování?

Dokud je spuštěn předávací agent ověřování, zůstává aktivní a neustále zpracovává požadavky na přihlášení uživatelů. Pokud chcete odinstalovat agenta ověřování, přejděte na **Ovládací panely -> programy -> programy a funkce** a odinstalujte **agenta microsoft azure ad connect ověřování** i aktualizační programy Microsoft Azure **AD Connect Agent Updater.**

Pokud po dokončení předchozího kroku zaškrtnete okno Předávací ověřování v [Centru pro správu služby Azure Active Directory,](https://aad.portal.azure.com) zobrazí se agent ověřování, který se zobrazuje jako **neaktivní**. To _se očekává_. Agent ověřování je automaticky vynechán ze seznamu po několika dnech.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Už používám službu AD FS k přihlášení ke službě Azure AD. Jak ji přepnu na předávací ověřování?

Pokud migrujete ze služby AD FS (nebo jiných federačních technologií) na předávací ověřování, důrazně doporučujeme, abyste se řídili naším podrobným průvodcem nasazením, který byl zveřejněn [zde](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Lze předávací ověřování používat v prostředí služby Active Directory s více doménovými strukturami?

Ano. Prostředí s více doménovými strukturami jsou podporována, pokud mezi doménovými strukturami služby Active Directory existují vztahy důvěryhodnosti doménové struktury a pokud je směrování přípony názvů správně nakonfigurováno.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Poskytuje předávací ověřování vyrovnávání zatížení mezi více agenty ověřování?

Ne, instalace více předávacích agentů ověřování zajišťuje pouze [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Neposkytuje deterministické vyrovnávání zatížení mezi agenty ověřování. Jakýkoli agent ověřování (náhodně) může zpracovat požadavek na přihlášení určitého uživatele.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Kolik předávacích agentů ověřování musím nainstalovat?

Instalace více předávacích agentů ověřování zajišťuje [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Ale neposkytuje deterministické vyrovnávání zatížení mezi agenty ověřování.

Zvažte špičku a průměrné zatížení žádostí o přihlášení, které očekáváte, že uvidíte na vašem tenantovi. Jako měřítko může jeden agent ověřování zpracovat 300 až 400 ověřování za sekundu na standardním 4jádrovém procesoru, 16 GB RAM serveru.

Chcete-li odhadnout síťový provoz, použijte následující pokyny pro velikost:
- Každý požadavek má velikost datové části (0,5 kB + 1 kB * num_of_agents) bajtů; tedy data z Azure AD do agenta ověřování. Zde "num_of_agents" označuje počet agentů ověřování registrovaných ve vašem tenantovi.
- Každá odpověď má velikost datové části 1 kB bajtů; tedy data z agenta ověřování do služby Azure AD.

Pro většinu zákazníků jsou pro vysokou dostupnost a kapacitu dostačující celkem dva nebo tři agenti ověřování. Agenti pro ověřování byste měli nainstalovat v blízkosti řadičů domény, abyste zlepšili latenci přihlášení.

>[!NOTE]
>Existuje systémový limit 40 agentů ověřování na klienta.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Můžu nainstalovat prvního agenta předávacího ověřování na jiný server, než je ten, který spouští Azure AD Connect?

Ne, tento scénář _není_ podporován.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Proč k povolení předávacího ověřování potřebuji účet globálního správce pouze pro cloud?

Doporučujeme povolit nebo zakázat předávací ověřování pomocí účtu globálního správce pouze pro cloud. Přečtěte si [o přidání účtu globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Tímto způsobem zajistíte, že se nedostanete z vašeho tenanta.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak lze zakázat předávací ověřování?

Znovu spusťte průvodce Azure AD Connect a změňte metodu přihlášení uživatele z předávacího ověřování na jinou metodu. Tato změna zakáže předávací ověřování v tenantovi a odinstaluje agenta ověřování ze serveru. Agenty ověřování je nutné ručně odinstalovat z ostatních serverů.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co se stane, když odinstaluji předávacího agenta ověřování?

Pokud odinstalujete předávacího agenta ověřování ze serveru, způsobí, že server přestane přijímat požadavky na přihlášení. Chcete-li zabránit přerušení možnosti přihlášení uživatele na vašem tenantovi, ujistěte se, že máte jiný agent ověřování spuštěna před odinstalací předávacího agenta ověřování.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Mám staršího klienta, který byl původně nastaven pomocí služby AD FS.  Nedávno jsme se migrovali na PTA, ale teď nevidíme naše změny UPN, které se synchronizují se službou Azure AD.  Proč nejsou naše změny upn synchronizovány?

A: Za následujících okolností vaše místní UPN změny nemusí synchronizovat, pokud:

- Váš klient Azure AD byl vytvořen před 15.
- Původně jste byli federováni s klientem Azure AD pomocí služby AD FS pro ověřování
- Přešli jste na spravované uživatele, kteří používají PTA jako ověřování

Důvodem je, že výchozí chování klientů vytvořené před 15 června 2015 bylo blokovat změny UPN.  Pokud potřebujete zrušit blokování změn hlavního upn, musíte spustit následující cmdlt prostředí PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Klienti vytvořená po 15 června 2015 mají výchozí chování synchronizace změn HLAVNÍHO standardu.   



## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které ne.
- [Rychlý start](how-to-connect-pta-quick-start.md): Zprovoznění při předávacím ověřování Azure AD.
- [Migrace ze služby AD FS do předávacího ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Přečtěte si, jak nakonfigurovat funkci Inteligentní uzamčení ve vašem tenantovi za účelem ochrany uživatelských účtů.
- [Technické podrobné informace](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md)služby : Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory můžete zasít nové požadavky na funkce.

