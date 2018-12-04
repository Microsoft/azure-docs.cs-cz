---
title: Nejčastější dotazy k Azure Active Directory B2C | Dokumentace Microsoftu
description: Nejčastější dotazy (FAQ) o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6957a18440dfc225c63a1b1843e2b47af8b841bf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842947"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Nejčastější dotazy (FAQ) 
Tato stránka odpovědi na nejčastější dotazy týkající se Azure Active Directory (Azure AD) B2C. Kontrolovat novinky.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Proč nemám přístup k rozšíření Azure AD B2C na webu Azure Portal?
Existují dva běžné důvody pro proč rozšíření Azure AD nefunguje pro vás.  Azure AD B2C vyžaduje vaše uživatelská role v adresář, který má být globální správce.  Pokud si myslíte, že byste měli mít přístup, kontaktujte prosím svého správce.  Pokud máte oprávnění globálního správce, ujistěte se, že jste v adresáři Azure AD B2C a není adresář služby Azure Active Directory.  Zobrazí se pokyny, jak [vytvoření tenanta Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Můžete použít funkce Azure AD B2C v tenantovi Azure AD existující, na základě zaměstnanců?
Azure AD a Azure AD B2C jsou samostatný produkt nabídek a nemůžou být společně ve stejném tenantovi.  Klient služby Azure AD představuje organizace.  Klient služby Azure AD B2C představuje kolekci identit pro použití s aplikacemi předávajících stran.  Pomocí vlastních zásad (ve verzi public preview) může provést federaci Azure AD B2C do služby Azure AD umožňuje ověřování zaměstnanců v organizaci.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Můžu použít Azure AD B2C k zajištění přihlašování sociálních sítích (Facebook nebo Google +.) do Office 365?
Azure AD B2C nelze použít k ověřování uživatelů pro Microsoft Office 365.  Azure AD je řešení Microsoftu pro správu přístup zaměstnanců k aplikacím SaaS a obsahuje funkce, které jsou navržené k tomuto účelu, jako je například licencování a podmíněný přístup.  Azure AD B2C poskytuje jako platforma správy identit a přístupu pro vytváření webových a mobilních aplikací.  Pokud Azure AD B2C je nakonfigurován pro federování s tenantem Azure AD, tenanta Azure AD spravuje přístup zaměstnanců k aplikacím, které závisí na Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co jsou místní účty v Azure AD B2C? Jak jsou odlišné od pracovních nebo školních účtů ve službě Azure AD?
V tenantovi Azure AD, uživatelé, kteří patří do tenanta Přihlaste se pomocí e-mailovou adresu ve formátu `<xyz>@<tenant domain>`.  `<tenant domain>` Je jedním z ověřených domén tenanta nebo počáteční `<...>.onmicrosoft.com` domény. Tento typ účtu je pracovní nebo školní účet.

V tenantovi Azure AD B2C, většina aplikací, aby uživatel musel přihlásit pomocí libovolné libovolné e-mailovou adresu (například joe@comcast.net, bob@gmail.com, sarah@contoso.com, nebo jim@live.com). Tento typ účtu je místní účet.  Také podporujeme libovolného uživatelská jména jako místní účty (například joe, bob, sarah nebo jim). Při konfiguraci zprostředkovatelů identity pro Azure AD B2C na webu Azure Portal můžete s jedním z těchto dvou typů místní účet. Ve vašem tenantovi Azure AD B2C, klikněte na tlačítko **zprostředkovatelé Identity** a pak vyberte **uživatelské jméno** pod místní účty. 

Uživatelské účty pro aplikace je nutné vytvořit vždy prostřednictvím registrace uživatele tok, tok registrace / přihlášení uživatele, nebo pomocí Azure AD Graph API. Uživatelské účty vytvořené na webu Azure Portal slouží pouze ke správě tenanta.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Které zprostředkovatele sociální identity podporujete nyní? Ty, které plánujete podporovat v budoucnu?
Aktuálně podporujeme sítě Facebook, Google +, LinkedIn, Amazon, Twitteru (preview), WeChat (preview), Weibo (preview) a QQ (Preview). Přidáme podporou dalších zprostředkovatelů oblíbených sociálních identit, podle potřeb zákazníků.

Azure AD B2C má také přidanou podporu pro [vlastní zásady](active-directory-b2c-overview-custom.md).  Tyto [vlastní zásady](active-directory-b2c-overview-custom.md) povolit pro vývojáře k vytvoření vlastní zásady, které s všechny zprostředkovatele identity, který podporuje [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) nebo SAML. 

Začínáme s vlastními zásadami můžete najít naše [vlastní zásady starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Můžete nakonfigurovat obory, které chcete získat další informace o příjemci od různých zprostředkovatelů sociálních identit
Ne. Výchozí obory pro naše Sada podporovaných zprostředkovatelů sociálních identit jsou:

* Facebook: e-mailu
* Google +: e-mailu
* Účet Microsoft: openid e-mailový profil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Má ke spuštění v Azure pro práci s Azure AD B2C mé aplikace?
Ne, můžete hostovat aplikace kdekoli (v cloudu nebo místně). Všechno, co je potřeba pracovat s Azure AD B2C je schopnost odesílat a přijímat požadavky HTTP na veřejně přístupnými koncovými body.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mám několik tenantů Azure AD B2C. Jak můžu spravovat je na portálu Azure portal?
Před otevřením "Azure AD B2C' v nabídce vlevo na webu Azure portal, je nutné přepnout do adresáře, který chcete spravovat.  Adresář změnit kliknutím svou identitu v pravém horním rohu webu Azure portal a potom vyberte adresář, v rozevíracím seznamu, který se zobrazí.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Jak mohu přizpůsobit ověření e-mailů (obsah a "z:" pole) odeslaných službou Azure AD B2C?
Můžete použít [firemní branding funkce](../active-directory/fundamentals/customize-branding.md) přizpůsobit obsah ověřovací e-mailů. Konkrétně je možné přizpůsobit tyto dva prvky e-mailu:

* **Logo banneru**: zobrazí vpravo dole.
* **Barva pozadí**: zobrazí v horní části.

    ![Snímek obrazovky přizpůsobené ověřovací e-mail](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Podpis e-mailu obsahuje název tenanta Azure AD B2C, který jste zadali při prvním vytvoření tenanta Azure AD B2C. Můžete změnit název, podle těchto pokynů:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce.
1. Otevřít **Azure Active Directory** okno.
1. Klikněte na tlačítko **vlastnosti** kartu.
1. Změnit **název** pole.
1. Klikněte na **Uložit** v horní části stránky.

V současné době neexistuje žádný způsob, jak změnit "z:" na e-mailu.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak můžu migrovat své existující uživatelská jména, hesla a profily z databáze do Azure AD B2C?
Azure AD Graph API můžete použít k zápisu vašeho nástroje pro migraci. Najdete v článku [uživatelská příručka migrace](active-directory-b2c-user-migration.md) podrobnosti.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jaký tok uživatele heslo se používá pro místní účty v Azure AD B2C?
Tok uživatele Azure AD B2C heslo pro místní účty je na základě zásad pro službu Azure AD. V Azure AD B2C registrovat a zaregistrujte se nebo přihlašovací jméno a heslo toky uživatelů pro resetování použít síly hesla "silné" a není vypršení platnosti hesla. Přečtěte si [zásady hesel služby Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) další podrobnosti. Informace o uzamčení účtu a hesla najdete v tématu [spravuje hrozby k prostředkům a datům v Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Můžete použít Azure AD Connect k migraci identit zákazníků, které jsou uložené na můj místní služby Active Directory k Azure AD B2C?
Ne, Azure AD Connect není navržena pro práci s Azure AD B2C. Zvažte použití [rozhraní Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) pro migraci uživatele.  Najdete v článku [uživatelská příručka migrace](active-directory-b2c-user-migration.md) podrobnosti.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Může Azure AD B2C stránky v rámci elementu iFrame otevřete Moje aplikace?
Ne, z bezpečnostních důvodů Azure AD B2C stránky nelze otevřít v elementu iFrame.  Naše služba komunikuje s prohlížečem, který chcete zakázat prvky IFRAME.  Bezpečnostní komunitě v obecné a specifikace OAUTH2 nedoporučujeme používání prvky IFrame pro činnosti identity kvůli riziku opěry pro kliknutí.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Funguje s systémy CRM, jako je Microsoft Dynamics Azure AD B2C?
Integrace s Microsoft Dynamics 365 Portal je k dispozici.  Zobrazit [konfigurace portálu Dynamics 365 pro ověřování pomocí Azure AD B2C](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Provádí Azure AD B2C pracovat s místní SharePoint 2016 nebo starší?
Azure AD B2C není určena pro SharePoint externí sdílení partnera scénář; Zobrazit [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) místo.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Použít Azure AD B2C nebo B2B ke správě externí identity?
V tomto článku o [externí identity](../active-directory/active-directory-b2b-compare-external-identities.md) Další informace o použití příslušných funkcí pro vaše scénáře externí identita.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jaké generování sestav auditování a funkce Azure AD B2C poskytuje? Jsou stejné jako v Azure AD Premium?
Ne, Azure AD B2C nepodporuje stejnou sadu sestav jako Azure AD Premium. Existují však mnoho commonalities:

* **Přihlašování zprávy** obsahují záznam o každé přihlášení s omezenou podrobnosti.
* **Sestavy auditu** zahrnují aktivita správce i aktivita aplikace. 
* **Sestavy využití** patří počet uživatelů, počet přihlášení a objemu vícefaktorové ověřování. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Lze lokalizovat uživatelského rozhraní stránky, které obsluhuje Azure AD B2C? Jaké jazyky jsou podporovány?
Ano!  Přečtěte si informace o [přizpůsobení jazyka](active-directory-b2c-reference-language-customization.md), což je ve verzi public preview.  Poskytujeme překlady 36 jazyků a můžete přepsat libovolný řetězec tak, aby odpovídala vašim potřebám.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Můžete použít vlastní adresy URL na stránkách registrace a přihlašování, které jsou obsluhovány pomocí Azure AD B2C Například můžete změnit adresu URL z login.microsoftonline.com login.contoso.com?
Není aktuálně. Tato funkce je do našeho plánu. Ověření domény v **domén** karta na portálu Azure portal není dosažení tohoto cíle.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak odstranit tenanta Azure AD B2C?
Postupujte podle těchto kroků odstraňte vašeho tenanta Azure AD B2C:

1. Odstraňte všechny zásady ve vašem tenantovi Azure AD B2C.
1. Teď může přihlásit k [webu Azure portal](https://portal.azure.com/) jako správce předplatného. (Použít stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k registraci do Azure.)
1. Přepnutí na tenanta Azure AD B2C chcete odstranit.
2. Přejděte do služby Active Directory nabídky na levé straně.
3. Vyberte **Uživatelé a skupiny**.
4. Vyberte jednotlivé uživatele v důsledku (vyloučit správce předplatného uživatele, kterého jste aktuálně přihlášení jako). Klikněte na tlačítko **odstranit** v dolní části stránky a klikněte na tlačítko **Ano** po zobrazení výzvy.
5. Klikněte na tlačítko **registrace aplikací**.
6. Vyberte aplikaci s názvem **b2c-extensions-app**. Klikněte na tlačítko **odstranit** a klikněte na tlačítko **Ano** po zobrazení výzvy.
7. Vyberte **Přehled**.
8. Klikněte na tlačítko **odstranění adresáře**. K dokončení procesu, postupujte podle pokynů na obrazovce.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Získat Azure AD B2C jako součást sady Enterprise mobility?
Ne, Azure AD B2C je služba Azure s průběžnými platbami a není součástí Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak mohu ohlásit problémy s Azure AD B2C?
Zobrazit [soubor žádosti o podporu pro Azure Active Directory B2C](active-directory-b2c-support.md).
