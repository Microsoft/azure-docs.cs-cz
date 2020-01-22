---
title: Nejčastější dotazy týkající se Azure Active Directory B2C
description: Odpovědi na nejčastější dotazy týkající se Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 803c82c9926c3b75545b42a9a382fd5c327b7151
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289080"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: nejčastější dotazy (FAQ)

Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se Azure Active Directory B2C (Azure AD B2C). Udržujte kontrolu aktualizací.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Proč nemůžu získat přístup k rozšíření Azure AD B2C v Azure Portal?

Existují dva běžné důvody, proč rozšíření Azure AD nefunguje za vás. Azure AD B2C vyžaduje, aby vaše role uživatele v adresáři byla globální správce. Pokud se domníváte, že byste měli mít přístup, obraťte se prosím na správce. Máte-li oprávnění globálního správce, ujistěte se, že jste v adresáři Azure AD B2C a nikoli v adresáři Azure Active Directory. Můžete si prohlédnout pokyny k [Vytvoření klienta Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Můžu ve svém existujícím tenantovi Azure AD s využitím zaměstnanců použít funkce Azure AD B2C?

Azure AD a Azure AD B2C jsou samostatné nabídky produktů a nemůžou koexistovat ve stejném tenantovi. Tenant Azure AD představuje organizaci. Tenant Azure AD B2C představuje kolekci identit, které se mají používat s aplikacemi předávající strany. Přidáním **nového poskytovatele OpenID Connect** v rámci **Azure AD B2C > zprostředkovatelů identity** nebo s vlastními zásadami Azure AD B2C může federovat do Azure AD umožnit ověřování zaměstnanců v organizaci.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Můžu použít Azure AD B2C k poskytování sociálního přihlášení (Facebook a Google +) do sady Office 365?

Azure AD B2C nelze použít k ověřování uživatelů pro systém Microsoft Office 365. Azure AD je řešení Microsoftu pro správu přístupu zaměstnanců k aplikacím SaaS a obsahuje funkce navržené pro tento účel, jako je licencování a podmíněný přístup. Azure AD B2C poskytuje platformu pro správu identit a přístupu pro vytváření webových a mobilních aplikací. Když je Azure AD B2C nakonfigurovaná tak, aby federovat na tenanta Azure AD, tenant Azure AD spravuje přístup zaměstnanců k aplikacím, které spoléhají na Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co jsou místní účty v Azure AD B2C? Jak se liší od pracovních nebo školních účtů ve službě Azure AD?

V tenantovi Azure AD uživatelé patřící do přihlášení klienta pomocí e-mailové adresy formuláře `<xyz>@<tenant domain>`. `<tenant domain>` je jedna z ověřených domén v tenantovi nebo počáteční `<...>.onmicrosoft.com` doména. Tento typ účtu je pracovní nebo školní účet.

V tenantovi Azure AD B2C se většina aplikací chce přihlašovat pomocí libovolné e-mailové adresy (například joe@comcast.net, bob@gmail.com, sarah@contoso.comnebo jim@live.com). Tento typ účtu je místní účet. Také podporujeme libovolná uživatelská jména jako místní účty (například Jana, Bob, Sarah nebo jim). Při konfiguraci zprostředkovatelů identity pro Azure AD B2C v Azure Portal můžete zvolit jeden z těchto dvou místních typů účtů. Ve vašem tenantovi Azure AD B2C vyberte **Zprostředkovatelé identity**, vyberte **místní účet**a pak vyberte **uživatelské jméno**.

Uživatelské účty pro aplikace se musí vždy vytvořit prostřednictvím uživatelského toku registrace, registrace nebo přihlašování uživatelů nebo pomocí Graph API Azure AD. Uživatelské účty vytvořené v Azure Portal se používají jenom pro správu tenanta.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Kteří zprostředkovatelé sociálních identit teď podporujete? Které z nich plánujete v budoucnu podporovat?

V současné době podporujeme několik poskytovatelů sociálních identit, včetně Amazon, Facebook, GitHubu (Preview), Google, LinkedInu, účtu Microsoft (MSA), QQ (Preview), Twitteru, WeChat (Preview) a Weibo (Preview). Vyhodnocujeme přidání podpory pro další oblíbené poskytovatele sociálních identit na základě zákaznických požadavků.

Azure AD B2C také podporuje [vlastní zásady](active-directory-b2c-overview-custom.md). Vlastní zásady umožňují vytvořit vlastní zásady pro libovolného poskytovatele identity, který podporuje [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) nebo SAML. Začněte s vlastními zásadami a podívejte se na náš [úvodní balíček vlastních zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Můžu nakonfigurovat obory pro shromažďování dalších informací o uživatelích z různých poskytovatelů sociálních identit?

Ne. Výchozí obory používané pro naši podporovanou sadu zprostředkovatelů sociálních identit jsou:

* Facebook: e-mail
* Google +: e-mail
* Účet Microsoft: e-mailový profil OpenID
* Amazon: profile
* LinkedIn: r_emailaddress r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Musí být moje aplikace spuštěná v Azure pro práci s Azure AD B2C?

Ne, aplikaci můžete hostovat kdekoli (v cloudu nebo místně). To jediné, co pro interakci s Azure AD B2C potřebuje, je možnost odesílat a přijímat požadavky HTTP na veřejně přístupných koncových bodech.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mám více Azure AD B2C tenantů. Jak je můžu spravovat na Azure Portal?

Před otevřením ' Azure AD B2C ' v nabídce na levé straně Azure Portal je nutné přepnout do adresáře, který chcete spravovat. Přepněte adresáře kliknutím na vaši identitu v pravém horním rohu Azure Portal a pak zvolte adresář v rozevíracím seznamu, který se zobrazí.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Návody přizpůsobení ověřovacích e-mailů (obsah a pole od:) odesílaných Azure AD B2C?

K přizpůsobení obsahu ověřovacích e-mailů můžete použít [funkci Branding společnosti](../active-directory/fundamentals/customize-branding.md) . Konkrétně je možné přizpůsobit tyto dva prvky e-mailu:

* **Logo banner**: zobrazuje se vpravo dole.
* **Barva pozadí**: zobrazuje se v horní části.

    ![Snímek obrazovky přizpůsobeného ověřovacího e-mailu](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Podpis e-mailu obsahuje název tenanta Azure AD B2C, který jste zadali při prvním vytvoření klienta Azure AD B2C. Název můžete změnit pomocí těchto pokynů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce.
1. Otevřete okno **Azure Active Directory**.
1. Klikněte na tlačítko **vlastnosti** kartu.
1. Změňte pole **název** .
1. Klikněte na **Uložit** v horní části stránky.

V současné době neexistuje žádný způsob, jak změnit pole "od:" v e-mailu.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak můžu migrovat existující uživatelská jména, hesla a profily z databáze do Azure AD B2C?

Pomocí Graph API Azure AD můžete napsat nástroj pro migraci. Podrobnosti najdete v [Průvodci migrací uživatelů](active-directory-b2c-user-migration.md) .

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jaký tok uživatelského hesla se používá pro místní účty v Azure AD B2C?

Tok uživatele Azure AD B2C hesla pro místní účty je založený na zásadách pro Azure AD. Služby Azure AD B2C's pro registraci, registraci nebo přihlášení a resetování hesel uživatelské toky používají sílu silného hesla a neprošli platnost hesla. Další podrobnosti najdete v tématu [zásady hesel služby Azure AD](/previous-versions/azure/jj943764(v=azure.100)) . Informace o uzamčení a heslech účtů najdete v tématu [Správa hrozeb pro prostředky a data v Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Můžu použít Azure AD Connect k migraci identit zákazníků, které jsou uložené v místní službě Active Directory, do Azure AD B2C?

Ne, Azure AD Connect není navržená tak, aby spolupracovala s Azure AD B2C. Pro migraci uživatelů zvažte použití [Graph API Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) . Podrobnosti najdete v [Průvodci migrací uživatelů](active-directory-b2c-user-migration.md) .

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Může moje aplikace otevřít Azure AD B2C stránky v rámci prvku iFrame?

Ne, z důvodů zabezpečení nelze v rámci prvku iFrame otevřít Azure AD B2C stránky. Naše služba komunikuje s prohlížečem a znemožňuje prvky iFrame. Bezpečnostní komunita obecně a specifikace OAUTH2 doporučují pro používání prvků iFrame pro prostředí identity z důvodu rizika při kliknutí na zásuvky.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Funguje Azure AD B2C se systémy CRM, jako je Microsoft Dynamics?

K dispozici je integrace s portálem Microsoft Dynamics 365. Viz téma [Konfigurace portálu Dynamics 365 pro použití Azure AD B2C pro ověřování](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Pracuje Azure AD B2C s místní službou SharePoint 2016 nebo starší?

Azure AD B2C není určeno pro scénář sdílení pro externí partnery SharePointu; místo toho se podívejte na [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Mám použít Azure AD B2C nebo B2B ke správě externích identit?

Přečtěte si tento článek o [externích identitách](../active-directory/active-directory-b2b-compare-external-identities.md) , kde najdete další informace o použití vhodných funkcí pro vaše externí scénáře identity.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jaké funkce pro vytváření sestav a auditování Azure AD B2C poskytují? Jsou stejné jako v Azure AD Premium?

Ne, Azure AD B2C nepodporuje stejnou sadu sestav jako Azure AD Premium. Existuje však mnoho commonalities:

* **Sestavy přihlášení** poskytují záznam o každém přihlášení s omezenými podrobnostmi.
* **Sestavy auditu** zahrnují jak aktivitu správce, tak i aktivitu aplikace.
* **Sestavy o využití** zahrnují počet uživatelů, počet přihlášení a objem MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Můžu lokalizovat uživatelské rozhraní stránek obsluhované Azure AD B2C? Jaké jsou podporované jazyky?

Ano, podívejte se na téma [přizpůsobení jazyka](active-directory-b2c-reference-language-customization.md). Poskytujeme překlady pro jazyky 36 a můžete přepsat libovolný řetězec tak, aby vyhovoval vašim potřebám.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Můžu vlastní adresy URL používat na přihlašovacích a přihlašovacích stránkách, které obsluhuje Azure AD B2C? Například je možné změnit adresu URL z contoso.b2clogin.com na login.contoso.com?

Momentálně ne. Tato funkce je v našem plánu. Ověření domény na kartě **domény** v Azure Portal nedosahuje tohoto cíle. V b2clogin.com ale nabízíme [neutrální doménu nejvyšší úrovně](b2clogin.md), takže externí vzhled se dá implementovat bez zmínky o Microsoftu.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Návody odstranit můj tenant Azure AD B2C?

Chcete-li odstranit svého tenanta Azure AD B2C, postupujte podle těchto kroků.

Můžete použít aktuální prostředí **aplikací** nebo naše nové sjednocené **Registrace aplikací (ve verzi Preview)** . [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako *Správce předplatného*. Použijte stejný pracovní nebo školní účet nebo stejnou účet Microsoft, jakou jste použili k registraci do Azure.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Odstraňte všechny **toky uživatelů (zásady)** ve vašem tenantovi Azure AD B2C.
1. Odstraňte všechny **aplikace** , které jste zaregistrovali ve vašem tenantovi Azure AD B2C.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V části **Spravovat** vyberte **Uživatele**.
1. Vyberte možnost jednotliví uživatelé zase (vyloučit *Správce předplatného* , kterého jste právě přihlášeni jako uživatel). V dolní části stránky vyberte **Odstranit** a po zobrazení výzvy vyberte **Ano** .
1. V části **Spravovat**vyberte **Registrace aplikací** (nebo **Registrace aplikací (starší verze)** ).
1. Vybrat **Zobrazit všechny aplikace**
1. Vyberte aplikaci s názvem **B2C-Extensions-App**, vyberte **Odstranit**a po zobrazení výzvy vyberte **Ano** .
1. V části **Spravovat**vyberte **uživatelská nastavení**.
1. Pokud je k dispozici, vyberte v části **připojení k účtu LinkedIn**možnost **ne**a pak vyberte **Uložit**.
1. V části **Spravovat**vyberte **vlastnosti** .
1. V části **Správa přístupu pro prostředky Azure** vyberte **Ano** a pak vyberte **Uložit**.
1. Odhlaste se ze Azure Portal a pak se znovu přihlaste, abyste mohli aktualizovat svůj přístup.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. Na stránce **Přehled** vyberte **odstranit adresář**. Dokončete proces podle pokynů na obrazovce.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako *Správce předplatného*. Použijte stejný pracovní nebo školní účet nebo stejnou účet Microsoft, jakou jste použili k registraci do Azure.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Odstraní všechny **toky uživatelů (zásady)** ve vašem tenantovi Azure AD B2C.
1. Vyberte **Registrace aplikací (Preview)** a pak vyberte kartu **všechny aplikace** .
1. Odstraňte všechny aplikace, které jste zaregistrovali.
1. Odstraňte **B2C-Extensions-App**.
1. V části **Spravovat** vyberte **Uživatele**.
1. Vyberte možnost jednotliví uživatelé zase (vyloučit *Správce předplatného* , kterého jste právě přihlášeni jako uživatel). V dolní části stránky vyberte **Odstranit** a po zobrazení výzvy vyberte **Ano** .
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V části **Spravovat**vyberte **uživatelská nastavení**.
1. Pokud je k dispozici, vyberte v části **připojení k účtu LinkedIn**možnost **ne**a pak vyberte **Uložit**.
1. V části **Spravovat**vyberte **vlastnosti** .
1. V části **Správa přístupu pro prostředky Azure** vyberte **Ano** a pak vyberte **Uložit**.
1. Odhlaste se ze Azure Portal a pak se znovu přihlaste, abyste mohli aktualizovat svůj přístup.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. Na stránce **Přehled** vyberte **odstranit adresář**. Dokončete proces podle pokynů na obrazovce.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Můžu Azure AD B2C jako součást sady Enterprise Mobility Suite?

Ne, Azure AD B2C je služba Azure s průběžnými platbami, která není součástí sady Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Návody nahlásit problémy s Azure AD B2C?

Viz [žádosti o podporu souborů pro Azure Active Directory B2C](active-directory-b2c-support.md).
