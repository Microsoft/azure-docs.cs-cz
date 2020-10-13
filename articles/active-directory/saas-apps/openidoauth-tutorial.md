---
title: Konfigurace aplikace OpenID/OAuth z Galerie aplikací Azure AD | Microsoft Docs
description: Postup konfigurace aplikace OpenID/OAuth z Galerie aplikací Azure AD
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: ce4cccba678a934780dc987f1441ea658a70f8eb
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996624"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurace aplikace OpenID/OAuth z Galerie aplikací Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces přidání aplikace OpenID z Galerie

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png))

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. V horní části dialogového okna vyberte **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte název aplikace. Z panelu výsledek vyberte požadovanou aplikaci a zaregistrujte se do aplikace.

    ![OpenID v seznamu výsledků](common/search-new-app.png)

    > [!NOTE]
    > V případě aplikací OpenID Connect a OAuth je tlačítko **Přidat** ve výchozím nastavení zakázané. V tomto případě by měl správce tenanta vybrat tlačítko pro registraci a poskytnout mu souhlas aplikace. Aplikace se pak přidá do tenanta zákazníka, kde můžete provádět konfigurace. Není nutné explicitně přidávat aplikaci.

    ![Tlačítko Přidat](./media/openidoauth-tutorial/addbutton.png)

5. Když vyberete odkaz pro registraci, budete přesměrováni na stránku Azure Active Directory (Azure AD) pro přihlašovací údaje pro přihlášení.

6. Po úspěšném ověření se souhlasem přijměte ze stránky souhlasu. Potom se zobrazí domovská stránka aplikace.

    > [!NOTE]
    > Můžete přidat pouze jednu instanci aplikace. Pokud jste už přidali a pokusili jste ho zadat znovu, nepřidá se znovu v tenantovi. Takže můžete logicky použít jenom jednu instanci aplikace v tenantovi.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější tok přihlášení obsahuje následující kroky:

![Tok ověřování pomocí OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Víceklientské aplikace
Víceklientské aplikace je určena pro použití v mnoha organizacích, nikoli pouze v jedné organizaci. Jsou to obvykle aplikace typu software jako služba (SaaS) napsané nezávislým dodavatelem softwaru (ISV).

Víceklientské aplikace je potřeba zřídit v každém adresáři, kde se budou používat. K jejich registraci vyžadují souhlas uživatele nebo správce. Tento postup souhlasu začíná, když je aplikace registrovaná v adresáři a má přístup k Graph API nebo možná jiné webové rozhraní API. Když se uživatel nebo správce z jiné organizace přihlásí k používání aplikace, zobrazí se dialogové okno s oprávněním, která aplikace potřebuje.

Uživatel nebo správce pak mohou aplikaci udělit souhlas. Souhlas poskytne aplikaci přístup k uvedeným datům a nakonec registruje aplikaci v adresáři.

> [!NOTE]
> Pokud aplikaci zpřístupníte uživatelům ve více adresářích, budete potřebovat mechanismus, který určí, na kterém tenantovi se nachází. Jedna aplikace pro jednoho tenanta potřebuje, aby pro uživatele vypadala jenom ve svém vlastním adresáři. Víceklientská aplikace potřebuje identifikovat konkrétního uživatele ze všech adresářů ve službě Azure AD.
>
> K provedení této úlohy Azure AD poskytuje běžný koncový bod ověřování, ve kterém může kterákoli klientská aplikace směrovat požadavky na přihlášení místo koncového bodu specifického pro tenanta. Tento koncový bod je `https://login.microsoftonline.com/common` pro všechny adresáře ve službě Azure AD. Může být koncový bod pro konkrétního tenanta `https://login.microsoftonline.com/contoso.onmicrosoft.com` .
>
> Běžný koncový bod je důležité vzít v úvahu při vývoji aplikace. Budete potřebovat potřebnou logiku pro zpracování více tenantů během přihlašování, odhlašování a ověřování tokenu.

Ve výchozím nastavení Azure AD propaguje víceklientské aplikace. Snadno se k nim dostanete v organizacích a dají se snadno použít po přijetí souhlasu.

## <a name="consent-framework"></a>Rámec pro udělení souhlasu

Pomocí rozhraní pro vyjádření souhlasu Azure AD můžete vyvíjet víceklientské webové a nativní klientské aplikace. Tyto aplikace umožňují přihlášení pomocí uživatelských účtů z klienta služby Azure AD, které se liší od toho, kde je aplikace zaregistrovaná. Můžou taky potřebovat přístup k webovým rozhraním API, jako jsou:
- Rozhraní Microsoft Graph API pro přístup k Azure AD, Intune a službám v Microsoft 365.
- Další rozhraní API služeb Microsoftu.
- Vaše vlastní webová rozhraní API.

Rozhraní je založeno na uživateli nebo správci, který poskytuje souhlas s aplikací, která je popsána v adresáři. Registrace může zahrnovat přístup k datům adresáře. Po přijetí souhlasu může klientská aplikace zavolat rozhraní API Microsoft Graph jménem uživatele a podle potřeby informace použít.

[Rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/) poskytuje přístup k datům v Microsoft 365, třeba:

- Kalendáře a zprávy ze systému Exchange.
- Weby a seznamy ze služby SharePoint.
- Dokumenty z OneDrivu
- Poznámkové bloky z OneNotu.
- Úkoly z plánovače.
- Sešity z Excelu.

Graph API taky poskytuje přístup k uživatelům a skupinám z Azure AD a dalších datových objektů z dalších cloudových služeb Microsoftu.

Následující kroky ukazují, jak funguje souhlas pro vývojáře aplikací a uživatele:

1. Předpokládejme, že máte aplikaci webového klienta, která potřebuje požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Azure Portal slouží k deklaraci žádostí o oprávnění v době konfigurace. Podobně jako u jiných nastavení konfigurace se stanou součástí registrace Azure AD aplikace. Pro cestu požadavku oprávnění potřebujete postupovat podle následujících kroků:

    a. Na levé straně nabídky klikněte na **Registrace aplikací** a otevřete aplikaci zadáním názvu aplikace do vyhledávacího pole.

    ![Snímek obrazovky, který zobrazuje vybranou položku Registrace aplikací v nabídce vlevo a zvýrazněné pole pro hledání "aplikace I D".](./media/openidoauth-tutorial/application.png)

    b. Klikněte na **Zobrazit oprávnění rozhraní API**.

    ![Snímek obrazovky se stránkou "zavolat na P I" s vybraným tlačítkem zobrazit oprávnění P I](./media/openidoauth-tutorial/api-permission.png)

    c. Klikněte na **Přidat oprávnění**.

    ![Snímek obrazovky, který zobrazuje oddíl A P I oprávnění s vybraným tlačítkem Přidat oprávnění](./media/openidoauth-tutorial/add-permission.png)

    d. Klikněte na **Microsoft Graph**.

    ![Snímek obrazovky se stránkou "žádost o oprávnění", která obsahuje kartu Microsoft a P i a "Microsoft Graph".](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Vyberte požadované možnosti z **delegovaných oprávnění** a **oprávnění aplikace**.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vezměte v úvahu, že byla aktualizována oprávnění aplikace. Aplikace je spuštěná a uživatel je bude v první době používat. Nejdřív aplikace potřebuje získat autorizační kód z koncového bodu Azure AD/Authorize. Autorizační kód pak můžete použít k získání nového přístupového a obnovovacího tokenu.

3. Pokud uživatel ještě není ověřený, koncový bod Azure AD/Authorize vyzve k přihlášení.

    ![Snímek obrazovky s výzvou k přihlášení pro účet](./media/openidoauth-tutorial/authentication.png)

4. Až se uživatel přihlásí, Azure AD určí, jestli se uživatel musí zobrazit na stránce souhlasu. Toto rozhodnutí je založené na tom, jestli uživatel (nebo správce organizace) už udělil souhlas s aplikací.

   Pokud nebylo uděleno souhlas, Azure AD vyzve uživatele k vyjádření souhlasu a zobrazí požadovaná oprávnění, která potřebuje k tomu, aby fungovala. Oprávnění, která se zobrazují v dialogovém okně souhlasu, se shodují s těmi vybranými v delegovaných oprávněních v Azure Portal.

    ![Stránka pro vyjádření souhlasu](./media/openidoauth-tutorial/consentpage.png)

Běžný uživatel může vyjádřit souhlas s některými oprávněními. Další oprávnění vyžadují souhlas správce tenanta.

## <a name="difference-between-admin-consent-and-user-consent"></a>Rozdíl mezi souhlasem správce a souhlasem uživatele

Jako správce můžete také vyjádřit souhlas s delegovanými oprávněními aplikace jménem všech uživatelů ve vašem tenantovi. Souhlas se správou brání tomu, aby se v dialogovém okně pro každého uživatele v tenantovi zobrazovalo dialogové okno souhlasu. Uživatelé, kteří mají roli správce, mohou poskytnout souhlas v Azure Portal. Na stránce **Nastavení** aplikace vyberte **požadovaná oprávnění**  >  **udělení souhlasu správce**.

![Tlačítko udělit oprávnění](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udělení výslovného souhlasu pomocí tlačítka **udělení souhlasu správce** se teď vyžaduje pro jednostránkové aplikace (jednostránkové), které používají ADAL.js. V opačném případě dojde k chybě aplikace po vyžádání přístupového tokenu.

Oprávnění pouze pro aplikace vždy vyžadují souhlas správce tenanta. Pokud vaše aplikace požaduje oprávnění pouze pro přístup k aplikacím a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva. Zpráva říká uživateli, že není možné vyjádřit souhlas.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, musíte mít gesto, jako je tlačítko nebo odkaz, kde může správce zahájit akci. Požadavek, který vaše aplikace posílá pro tuto akci, je obvyklým požadavkem na autorizaci OAuth2/OpenID Connect. Tato žádost obsahuje parametr *prompt = admin_consent* řetězec dotazu.

Po souhlasu správce a Vytvoření instančního objektu v tenantovi zákazníka nemusí později žádosti o přihlášení vyžadovat parametr *prompt = admin_consent* . Vzhledem k tomu, že správce rozhodl, že jsou požadovaná oprávnění přijatelná, žádné další uživatele v tenantovi nebudou vyzváni k jejich souhlasu od tohoto okamžiku.

Správce klienta může zakázat možnost pro běžné uživatele, aby jim souhlasili s aplikacemi. Pokud je tato možnost zakázaná, bude pro použití v tenantovi vždycky potřeba souhlas správce. Pokud chcete otestovat aplikaci pomocí souhlasu koncového uživatele, můžete najít konfigurační přepínač v [Azure Portal](https://portal.azure.com/). V části [nastavení uživatele](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) v části **podnikové aplikace**.

Parametr *prompt = admin_consent* můžou používat i aplikace, které vyžadují oprávnění, které nevyžadují souhlas správce. Příkladem je aplikace, která vyžaduje prostředí, kde se správce tenanta zaregistruje jednou a žádné další uživatele se k souhlasu od tohoto okamžiku neobjeví.

Představte si, že aplikace vyžaduje souhlas správce a že se správce přihlásí bez odeslání *výzvy = admin_consent* parametr. Když správce úspěšně souhlasí s aplikací, vztahuje se pouze na svůj uživatelský účet. Pravidelným uživatelům se stále nebude možné přihlásit ani vyjádřit souhlas s aplikací. Tato funkce je užitečná v případě, že chcete správci tenanta dát možnost prozkoumat vaši aplikaci předtím, než povolíte přístup k ostatním uživatelům.
