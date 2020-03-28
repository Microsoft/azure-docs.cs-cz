---
title: Konfigurace aplikace OpenID/OAuth z galerie aplikací Azure AD | Dokumenty společnosti Microsoft
description: Kroky ke konfiguraci aplikace OpenID/OAuth z galerie aplikací Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8a2c962c69ead28c4e79b663010eab77a499f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048418"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurace aplikace OpenID/OAuth z galerie aplikací Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces přidání aplikace OpenID z galerie

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory](common/select-azuread.png))

2. Přejděte na **podnikové aplikace** > **Všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. V horní části dialogového okna vyberte **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte název aplikace. Vyberte požadovanou aplikaci z panelu výsledků a přihlaste se k aplikaci.

    ![Openid v seznamu výsledků](common/search-new-app.png)

    > [!NOTE]
    > U aplikací OpenID Connect a OAuth je tlačítko **Přidat** ve výchozím nastavení zakázáno. Zde by měl správce klienta vybrat tlačítko registrace a poskytnout souhlas s aplikací. Aplikace je pak přidána do klienta zákazníka, kde můžete provést konfigurace. Není nutné přidávat aplikaci explicitně.

    ![Tlačítko Přidat](./media/openidoauth-tutorial/addbutton.png)

5. Když vyberete odkaz na registraci, budete přesměrováni na stránku Azure Active Directory (Azure AD) pro přihlašovací údaje.

6. Po úspěšném ověření souhlasíte se souhlasem ze stránky souhlasu. Poté se zobrazí domovská stránka aplikace.

    > [!NOTE]
    > Můžete přidat pouze jednu instanci aplikace. Pokud jste již jeden přidali a pokusili jste se souhlas znovu poskytnout, nebude znovu přidán v tenantovi. Takže logicky můžete použít pouze jednu instanci aplikace v tenantovi.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější tok přihlášení obsahuje následující kroky:

![Tok ověřování pomocí OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Víceklientská aplikace 
Víceklientská aplikace je určena pro použití v mnoha organizacích, nikoli pouze v jedné organizaci. Obvykle se jedná o aplikace se softwarem jako službou (SaaS) napsané nezávislým dodavatelem softwaru (ISV). 

Víceklientské aplikace je třeba zřídit v každém adresáři, kde budou použity. Vyžadují souhlas uživatele nebo správce k jejich registraci. Tento proces souhlasu se spustí, když byla aplikace zaregistrována v adresáři a je mu udělen přístup k rozhraní Graph API nebo jinému webovému rozhraní API. Když se k použití aplikace přihlásí uživatel nebo správce z jiné organizace, zobrazí se v dialogovém okně oprávnění, která aplikace potřebuje. 

Uživatel nebo správce pak může souhlasit s aplikací. Souhlas poskytuje aplikaci přístup k uvedeným údajům a nakonec ji zaregistruje v adresáři.

> [!NOTE]
> Pokud aplikaci zpřístupňujete uživatelům ve více adresářích, potřebujete mechanismus k určení, ve kterém tenantovi se právě jsou. Aplikace s jedním tenantem potřebuje pouze hledat ve vlastním adresáři pro uživatele. Víceklientská aplikace potřebuje identifikovat konkrétního uživatele ze všech adresářů ve službě Azure AD.
> 
> K provedení tohoto úkolu Azure AD poskytuje společný koncový bod ověřování, kde všechny víceklientské aplikace můžete přímé požadavky na přihlášení, namísto koncového bodu specifického pro klienta. Tento koncový `https://login.microsoftonline.com/common` bod je pro všechny adresáře ve službě Azure AD. Koncový bod specifický pro `https://login.microsoftonline.com/contoso.onmicrosoft.com`klienta může být . 
>
> Společný koncový bod je důležité zvážit při vývoji aplikace. Budete potřebovat potřebnou logiku pro zpracování více klientů během přihlášení, odhlášení a ověření tokenu.

Ve výchozím nastavení Azure AD podporuje víceklientské aplikace. Jsou snadno přístupné napříč organizacemi a po přijetí souhlasu se snadno používají.

## <a name="consent-framework"></a>Rámec pro udělení souhlasu

K vývoji víceklientských webových a nativních klientských aplikací můžete použít rámec souhlasu Azure AD. Tyto aplikace umožňují přihlášení podle uživatelských účtů z klienta Azure AD, odlišné od toho, kde je aplikace registrována. Mohou také potřebovat přístup k webovým apim, jako jsou:
- Rozhraní Microsoft Graph API pro přístup k Azure AD, Intune a službám v Office 365. 
- Rozhraní API jiných služeb společnosti Microsoft.
- Vlastní webová api. 

Rámec je založen na uživateli nebo správci, který udá souhlas s aplikací, která žádá o registraci ve svém adresáři. Registrace může zahrnovat přístup k datům adresáře. Po udělení souhlasu může klientská aplikace volat rozhraní Microsoft Graph API jménem uživatele a podle potřeby použít informace.

Rozhraní [Microsoft Graph API](https://developer.microsoft.com/graph/) poskytuje přístup k datům v Office 365, třeba k těmto datům:

- Kalendáře a zprávy z Exchange.
- Weby a seznamy ze SharePointu.
- Dokumenty z OneDrivu.
- Poznámkové bloky z OneNotu
- Úkoly z plánovače.
- Sešity z Excelu.

Rozhraní Graph API také poskytuje přístup uživatelům a skupinám z Azure AD a dalších datových objektů z více cloudových služeb Microsoftu.

Následující kroky ukazují, jak prostředí souhlasu funguje pro vývojáře a uživatele aplikace:

1. Předpokládejme, že máte aplikaci webového klienta, která potřebuje požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Portál Azure se používá k deklarování žádostí o oprávnění v době konfigurace. Stejně jako ostatní nastavení konfigurace se stanou součástí registrací Azure AD aplikace. Pro cestu žádosti o oprávnění potřebujete postupujte podle následujících kroků:

    a. Klikněte na registrace aplikací z levé **strany** nabídky a otevřete aplikaci zadáním názvu aplikace do vyhledávacího pole.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Klepněte na **tlačítko Zobrazit oprávnění rozhraní API**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Klikněte na **Přidat oprávnění**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Klikněte na **Microsoft Graph**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Vyberte požadované možnosti z **delegovaných oprávnění** a **oprávnění aplikací**.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Zvažte, že oprávnění aplikace byla aktualizována. Aplikace je spuštěna a uživatel se chystá použít poprvé. Nejprve aplikace potřebuje získat autorizační kód z koncového bodu Azure AD /authorize. Autorizační kód lze poté použít k získání nového přístupového a obnovovacího tokenu.

3. Pokud uživatel ještě není ověřen, výzvy koncového bodu Azure AD /authorize pro přihlášení.

    ![Ověřování](./media/openidoauth-tutorial/authentication.png)

4. Po přihlášení uživatele Azure AD určuje, pokud uživatel potřebuje zobrazit stránku souhlasu. Toto určení je založeno na tom, zda uživatel (nebo správce jejich organizace) již udělil souhlas aplikace.

   Pokud souhlas nebyl udělen, Azure AD vyzve uživatele k souhlasu a zobrazí požadovaná oprávnění, která potřebuje k fungování. Oprávnění, která se zobrazí v dialogovém okně souhlas, odpovídají oprávněním vybraným v delegovaných oprávněních na webu Azure Portal.

    ![Stránka souhlasu](./media/openidoauth-tutorial/consentpage.png)

Běžný uživatel může souhlasit s některými oprávněními. Další oprávnění vyžadují souhlas správce klienta.

## <a name="difference-between-admin-consent-and-user-consent"></a>Rozdíl mezi souhlasem správce a souhlasem uživatele

Jako správce můžete také souhlasit s delegovanými oprávněními aplikace jménem všech uživatelů ve vašem tenantovi. Souhlas správce zabrání zobrazení dialogového okna souhlasu pro každého uživatele v klientovi. Uživatelé, kteří mají roli správce, můžou na webu Azure Portal poskytnout souhlas. Na stránce **Nastavení** aplikace vyberte **Možnost Požadovaná oprávnění** > **Udělit souhlas správce**.

![Tlačítko Udělit oprávnění](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udělení výslovného souhlasu pomocí tlačítka **Udělit souhlas správce** je nyní vyžadováno pro jednostránkové aplikace, které používají soubor ADAL.js. V opačném případě se aplikace nezdaří při požadavku na přístupový token.

Oprávnění pouze pro aplikace vždy vyžadují souhlas správce klienta. Pokud vaše aplikace požaduje oprávnění pouze pro aplikaci a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva. Zpráva říká, že uživatel není schopen souhlasit.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, musíte mít gesto, jako je tlačítko nebo odkaz, kde může správce spustit akci. Požadavek, který aplikace odešle pro tuto akci, je obvyklá žádost o autorizaci OAuth2/OpenID Connect. Tento požadavek obsahuje parametr řetězce *prompt=admin_consent* dotazu. 

Poté, co správce souhlasil a instanční objekt služby je vytvořen v tenantovi zákazníka, pozdější požadavky na přihlášení nepotřebují *parametr prompt=admin_consent.* Vzhledem k tomu, že správce rozhodl, že požadovaná oprávnění jsou přijatelná, žádní další uživatelé v tenantovi nejsou vyzváni k udělení souhlasu od tohoto okamžiku.

Správce klienta může zakázat možnost běžných uživatelů souhlasit s aplikacemi. Pokud je tato funkce zakázána, je vždy vyžadován souhlas správce, aby se aplikace používala v tenantovi. Pokud chcete aplikaci otestovat se zakázaným souhlasem koncového uživatele, najdete konfigurační přepínač na [webu Azure Portal](https://portal.azure.com/). Je v části [Uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) v části **Podnikové aplikace**.

Parametr *prompt=admin_consent* mohou také používat aplikace, které požadují oprávnění, která nevyžadují souhlas správce. Příkladem je aplikace, která vyžaduje prostředí, kde správce klienta "zaregistruje" jednou a žádní jiní uživatelé jsou vyzváni k souhlasu od tohoto okamžiku.

Představte si, že aplikace vyžaduje souhlas správce a správce se přihlásí bez odeslání parametru *prompt=admin_consent.* Když správce úspěšně souhlasí s aplikací, platí pouze pro jeho uživatelský účet. Běžní uživatelé se stále nebudou moci přihlásit nebo s ní souhlasit. Tato funkce je užitečná, pokud chcete dát správci klienta možnost prozkoumat vaši aplikaci před povolením přístupu ostatních uživatelů.
