---
title: Nejčastější dotazy (nejčastější dotazy) pro Azure Active Directory B2C
description: Odpovědi na nejčastější dotazy týkající se služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264398"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Nejčastější dotazy (NEJČASTĚJŠÍ DOTAZY)

Tato stránka odpovídá na nejčastější dotazy týkající se služby Azure Active Directory B2C (Azure AD B2C). Mějte kontrolu zpět pro aktualizace.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Proč nemůžu získat přístup k rozšíření Azure AD B2C na webu Azure Portal?

Existují dva běžné důvody, proč rozšíření Azure AD nefunguje pro vás. Azure AD B2C vyžaduje, aby vaše role uživatele v adresáři byla globálním správcem. Pokud se domníváte, že byste k ní měli mít přístup, obraťte se na správce. Pokud máte oprávnění globálního správce, ujistěte se, že jste v adresáři Azure AD B2C a ne v adresáři Azure Active Directory. Můžete zobrazit pokyny pro [vytvoření klienta Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Můžu používat funkce Azure AD B2C ve svém stávajícím tenantovi Azure AD založeném na zaměstnancích?

Azure AD a Azure AD B2C jsou samostatné nabídky produktů a nelze existovat společně ve stejném tenantovi. Klient Azure AD představuje organizaci. Tenant Azure AD B2C představuje kolekci identit, které se mají používat s aplikacemi předávající strany. Přidáním **nového poskytovatele OpenID Connect** v rámci **Azure AD B2C > identity nebo** s vlastními zásadami, Azure AD B2C můžete federate do Azure AD umožňující ověřování zaměstnanců v organizaci.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Můžu používat Azure AD B2C k poskytování přihlášení na sociálních sítích (Facebook a Google+) do Office 365?

Azure AD B2C nelze použít k ověření uživatelů pro Microsoft Office 365. Azure AD je řešení microsoftu pro správu přístupu zaměstnanců k aplikacím SaaS a má funkce navržené pro tento účel, jako jsou licencování a podmíněný přístup. Azure AD B2C poskytuje platformu pro správu identit a přístupu pro vytváření webových a mobilních aplikací. Když se Azure AD B2C nakonfiguruje pro federate do klienta Azure AD, tenant Azure AD spravuje přístup zaměstnanců k aplikacím, které jsou závislé na Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co jsou místní účty v Azure AD B2C? Jak se liší od pracovních nebo školních účtů ve službě Azure AD?

V tenantovi Azure AD uživatelé, kteří patří do klienta `<xyz>@<tenant domain>`přihlášení s e-mailovou adresou formuláře . Jedná `<tenant domain>` se o jednu z ověřených `<...>.onmicrosoft.com` domén v klientovi nebo počáteční doméně. Tento typ účtu je pracovní nebo školní účet.

V tenantovi Azure AD B2C většina aplikací chce, aby se uživatel přihlašoval pomocí libovolné e-mailové adresy (například joe@comcast.net bob@gmail.com, , sarah@contoso.com, nebo jim@live.com). Tento typ účtu je místní účet. Podporujeme také libovolná uživatelská jména jako místní účty (například joe, bob, sarah nebo jim). Jeden z těchto dvou typů místních účtů můžete zvolit při konfiguraci poskytovatelů identit pro Azure AD B2C na webu Azure Portal. Ve svém tenantovi Azure AD B2C vyberte **Zprostředkovatelé identity**, vyberte **Místní účet**a pak vyberte **Uživatelské jméno**.

Uživatelské účty pro aplikace lze vytvářet prostřednictvím toku uživatelů registrace, registrace nebo přihlášení uživatele toku, Microsoft Graph API nebo na webu Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Které poskytovatele sociální identity nyní podporujete? Které z nich plánujete v budoucnu podpořit?

V současné době podporujeme několik poskytovatelů sociální identity, včetně Amazon, Facebook, GitHub (náhled), Google, LinkedIn, Účet Microsoft (MSA), QQ (náhled), Twitter, WeChat (náhled) a Weibo (náhled). Vyhodnocujeme přidání podpory pro další populární poskytovatele sociální identity na základě poptávky zákazníků.

Azure AD B2C také podporuje [vlastní zásady](custom-policy-overview.md). Vlastní zásady umožňují vytvořit vlastní zásady pro všechny poskytovatele identity, který podporuje [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) nebo SAML. Můžete začít s vlastními zásadami tím, že se podíváme na náš [vlastní balíček pro spuštění zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Lze nakonfigurovat obory tak, aby shromažďovaly další informace o spotřebitelích od různých poskytovatelů sociální identity?

Ne. Výchozí obory používané pro naši podporovanou sadu poskytovatelů sociální identity jsou:

* Facebook: e-mail
* Google+: e-mail
* Účet Microsoft: e-mailový profil openid
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Musí se moje aplikace spouštět v Azure, protože funguje s Azure AD B2C?

Ne, aplikaci můžete hostovat kdekoli (v cloudu nebo místně). Vše, co potřebuje k interakci s Azure AD B2C je možnost odesílat a přijímat požadavky HTTP na veřejně přístupné koncové body.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mám několik klientů Azure AD B2C. Jak je můžu spravovat na webu Azure Portal?

Před otevřením "Azure AD B2C" v levé části nabídky portálu Azure, musíte přepnout do adresáře, který chcete spravovat. Přepněte adresáře kliknutím na vaši identitu v pravém horním části portálu Azure a pak zvolte adresář v rozevíracím seznamu, který se zobrazí.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Jak přizpůsobit ověřovací e-maily (obsah a pole "Od:") odeslané službou Azure AD B2C?

Pomocí funkce [firemníznačky](../active-directory/fundamentals/customize-branding.md) můžete přizpůsobit obsah ověřovacích e-mailů. Konkrétně tyto dva prvky e-mailu lze přizpůsobit:

* **Logo nápisu**: Zobrazeno vpravo dole.
* **Barva pozadí**: Zobrazeno nahoře.

    ![Snímek obrazovky s přizpůsobeným ověřovacím e-mailem](./media/faq/company-branded-verification-email.png)

Podpis e-mailu obsahuje název klienta Azure AD B2C, který jste zadali při prvním vytvoření klienta Azure AD B2C. Název můžete změnit podle těchto pokynů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako globální správce.
1. Otevřete okno **Azure Active Directory**.
1. Klikněte na kartu **Vlastnosti.**
1. Změňte pole **Název.**
1. Klikněte na **Uložit** v horní části stránky.

V současné době neexistuje žádný způsob, jak změnit pole "Od:" v e-mailu.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak můžu migrovat svá existující uživatelská jména, hesla a profily z databáze do Azure AD B2C?

K napsání nástroje pro migraci můžete použít rozhraní Microsoft Graph API. Podrobnosti najdete v [příručce pro migraci uživatelů.](user-migration.md)

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jaký tok uživatele hesla se používá pro místní účty v Azure AD B2C?

Tok uživatele hesla Azure AD B2C pro místní účty je založený na zásadách pro Azure AD. Azure AD B2C je registrace, registrace nebo přihlášení a obnovení hesla uživatelské toky použít "silné" sílu hesla a nevyprší žádná hesla. Další podrobnosti najdete [v tématu Zásady a omezení hesel ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

Informace o uzamčení účtů a heslech najdete [v tématu Správa hrozeb pro prostředky a data ve službě Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Můžu použít Azure AD Connect k migraci identit spotřebitelů, které jsou uložené v mém místním active ksadovit do Azure AD B2C?

Ne, Azure AD Connect není navržený pro práci s Azure AD B2C. Zvažte použití [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md) pro migraci uživatelů. Podrobnosti najdete v [příručce pro migraci uživatelů.](user-migration.md)

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Může moje aplikace otevřít stránky Azure AD B2C v rámci iFrame?

Ne, z bezpečnostních důvodů nelze otevřít stránky Azure AD B2C v rámci prvku iFrame. Naše služba komunikuje s prohlížečem zakázat iFrames. Komunita zabezpečení obecně a specifikace OAUTH2 doporučují nepoužívat iFrames pro prostředí identity kvůli riziku kliknutí.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Funguje Azure AD B2C se systémy CRM, jako je Microsoft Dynamics?

Integrace s portálem Microsoft Dynamics 365 Portal je k dispozici. Viz [Konfigurace portálu Dynamics 365 portal pro použití Azure AD B2C pro ověřování](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Funguje Azure AD B2C s SharePointem v místním prostředí 2016 nebo starším?

Azure AD B2C není určen pro sharepointové externí sdílení partnerů scénář; viz [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) místo.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Mám ke správě externích identit použít Azure AD B2C nebo B2B?

Přečtěte si [porovnat spolupráci B2B a B2C ve službě Azure AD,](../active-directory/b2b/compare-with-b2c.md) kde se dozvíte další informace o použití příslušných funkcí ve scénářích externí identity.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jaké funkce vytváření sestav a auditování poskytuje Azure AD B2C? Jsou stejné jako ve službě Azure AD Premium?

Ne, Azure AD B2C nepodporuje stejnou sadu sestav jako Azure AD Premium. Existuje však mnoho společných rysů:

* **Sestavy přihlášení** poskytují záznam o každém přihlášení se sníženými podrobnostmi.
* **Zprávy o auditu** zahrnují aktivitu správce i aktivitu aplikace.
* **Sestavy využití** zahrnují počet uživatelů, počet přihlášení a objem vícefaktorové analýzy.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Můžu lokalizovat ui stránek obsluhovaných Azure AD B2C? Jaké jazyky jsou podporovány?

Ano, viz [přizpůsobení jazyka](user-flow-language-customization.md). Zajišťujeme překlady pro 36 jazyků a můžete přepsat libovolný řetězec tak, aby vyhovoval vašim potřebám.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Můžu na svých přihlašovacích a přihlašovacích stránkách, které obsluhuje Azure AD B2C, používat vlastní adresy URL? Mohu například změnit adresu URL z contoso.b2clogin.com na login.contoso.com?

Momentálně ne. Tato funkce je v našem plánu. Ověření domény na kartě **Domény** na webu Azure Portal tohoto cíle nedosáhne. Nicméně, s b2clogin.com, nabízíme [neutrální nejvyšší úrovni domény](b2clogin.md), a tak externí vzhled může být realizován bez zmínky o Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak odstraním svého klienta Azure AD B2C?

Podle těchto kroků odstraňte klienta Azure AD B2C.

Můžete využít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako *správce předplatného*. Použijte stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k registraci do Azure.
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Odstraňte všechny **toky uživatelů (zásady)** v tenantovi Azure AD B2C.
1. Odstraňte všechny **aplikace, které** jste zaregistrovali v tenantovi Azure AD B2C.
1. V levé nabídce vyberte **službu Azure Active Directory.**
1. V části **Spravovat** vyberte **Uživatele**.
1. Vyberte každého uživatele podle pořadí (vyjádřete uživatele *správce předplatného,* ke které jste aktuálně přihlášeni). V dolní části stránky vyberte **Odstranit** a po zobrazení výzvy vyberte **ANO.**
1. V části **Správa**vyberte **Registrace aplikací** (nebo registrace **aplikací (starší verze).**
1. Vybrat **Zobrazit všechny aplikace**
1. Vyberte aplikaci s názvem **b2c-extensions-app**, vyberte **Odstranit**a po zobrazení výzvy vyberte **Ano.**
1. V části **Manage**vyberte **Uživatelská nastavení**.
1. Pokud je k dispozici, vyberte v části **Připojení účtů LinkedIn** **možnost Ne**a pak vyberte **Uložit**.
1. V části **Manage**vyberte **Vlastnosti.**
1. V části **Správa přístupu pro prostředky Azure** vyberte **Ano** a pak vyberte **Uložit**.
1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste a aktualizujte přístup.
1. V levé nabídce vyberte **službu Azure Active Directory.**
1. Na stránce **Přehled** vyberte **Odstranit adresář**. Proces dokončete podle pokynů na obrazovce.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako *správce předplatného*. Použijte stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k registraci do Azure.
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Odstraňte všechny **toky uživatelů (zásady)** v tenantovi Azure AD B2C.
1. Vyberte **Registrace aplikací (náhled)** a pak vyberte kartu **Všechny aplikace.**
1. Odstraňte všechny aplikace, které jste zaregistrovali.
1. Odstraňte **aplikaci b2c-extensions**.
1. V části **Spravovat** vyberte **Uživatele**.
1. Vyberte každého uživatele podle pořadí (vyjádřete uživatele *správce předplatného,* ke které jste aktuálně přihlášeni). V dolní části stránky vyberte **Odstranit** a po zobrazení výzvy vyberte **Ano.**
1. V levé nabídce vyberte **službu Azure Active Directory.**
1. V části **Manage**vyberte **Uživatelská nastavení**.
1. Pokud je k dispozici, vyberte v části **Připojení účtů LinkedIn** **možnost Ne**a pak vyberte **Uložit**.
1. V části **Manage**vyberte **Vlastnosti.**
1. V části **Správa přístupu pro prostředky Azure** vyberte **Ano** a pak vyberte **Uložit**.
1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste a aktualizujte přístup.
1. V levé nabídce vyberte **službu Azure Active Directory.**
1. Na stránce **Přehled** vyberte **Odstranit adresář**. Proces dokončete podle pokynů na obrazovce.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Můžu získat Azure AD B2C jako součást sady Enterprise Mobility Suite?

Ne, Azure AD B2C je služba Azure s průběžným platbou a není součástí sady Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak nahlásím problémy s Azure AD B2C?

Viz [Žádosti o podporu souborů pro Službu Azure Active Directory B2C](support-options.md).
