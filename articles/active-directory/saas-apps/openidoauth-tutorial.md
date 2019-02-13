---
title: Konfigurace OpenID/OAuth aplikaci v galerii aplikací Azure AD | Dokumentace Microsoftu
description: Postup konfigurace OpenID/OAuth aplikaci v galerii aplikací Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0742a08e0169d47307f28ff08491b0f7c7c4a914
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201512"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurace OpenID/OAuth aplikaci v galerii aplikací Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces přidávání OpenID aplikaci z Galerie

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory](common/select-azuread.png))

2. Přejděte na **podnikové aplikace** > **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Vyberte **novou aplikaci** nahoře v dialogovém okně.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte název aplikace. Na panelu výsledků vyberte požadovanou aplikaci a zaregistrovat aplikaci.

    ![V seznamu výsledků Openid](common/search-new-app.png)

    > [!NOTE]
    > Pro aplikace s OpenID Connect a OAuth **přidat** ve výchozím nastavení je tlačítko neaktivní. Zde by měl vybrat správce tenanta registrace tlačítko a poskytnout souhlas pro aplikaci. Aplikace se pak přidá do tenanta zákazníka, kde můžete provést konfiguraci. Není nutné explicitně přidat aplikaci.

    ![Tlačítko Přidat](./media/openidoauth-tutorial/addbutton.png)

5. Když vyberete odkaz na registraci, budete přesměrováni na stránku služby Azure Active Directory (Azure AD) pro přihlašovací údaje.

6. Po úspěšném ověření vyjadřujete souhlas s souhlas z stránka pro odsouhlasení podmínek. Potom se zobrazí na domovské stránce aplikace.

    > [!NOTE]
    > Můžete přidat pouze jednu instanci aplikace. Pokud jste už přidali a pokusili znovu zadali svůj souhlas, nebude přidána znovu v tenantovi. Proto logicky můžete použít pouze jednu instanci aplikace v tenantovi.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověření pomocí OpenID Connect

Základní tok přihlášení obsahuje následující kroky:

![Tok ověření pomocí OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Víceklientské aplikace 
Víceklientské aplikace určena pro použití v mnoha organizacích, ne jenom jedné organizace. Toto jsou obvykle software-as-a-service (SaaS) aplikací autorem nezávislý výrobce softwaru (ISV). 

Víceklientské aplikace potřeba ho zřídit v každém adresáři, ve kterém budou používat. Uživatel nebo správce souhlas k registraci je vyžadují. Tento proces souhlas začne, když aplikace byl zaregistrován v adresáři a je jim přístup k rozhraní Graph API nebo možná jiného webového rozhraní API. Pokud uživatel nebo správce z jiné organizace zaregistruje k používání aplikace, zobrazí dialogové okno oprávnění, které aplikace potřebuje. 

Uživatel nebo správce může potom souhlas aplikace. Souhlas poskytuje přístup k aplikaci do uvedeného data a nakonec zaregistruje aplikaci v adresáři.

> [!NOTE]
> Pokud vaše aplikace se zpřístupnění uživatelům ve více adresářů, je třeba mechanismus pro určení kterého tenanta ve kterých se nacházejí. Aplikace jedním tenantem stačí podívat do vlastního adresáře pro uživatele. Víceklientské aplikace potřebuje k identifikaci konkrétního uživatele ze všech adresářů ve službě Azure AD.
> 
> Chcete-li provést tuto úlohu, Azure AD poskytuje společný koncový bod ověřování kde jakékoli víceklientské aplikace může směrovat požadavky na přihlášení, místo konkrétního klienta endpoint. Tento koncový bod je [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) pro všechny adresáře ve službě Azure AD. Koncový bod specifickým pro tenanta může být [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> Společný koncový bod je důležité vzít v úvahu při vyvíjíte vaší aplikaci. Budete potřebovat logiku potřebnou pro zpracování více tenantů během přihlášení, odhlášení a ověření tokenu.

Ve výchozím nastavení Azure AD podporuje víceklientské aplikace. Snadno přístupu napříč organizací a po přijetí souhlasu se snadno používá.

## <a name="consent-framework"></a>Rámec pro udělení souhlasu

Rozhraní pro udělování souhlasu Azure AD můžete použít k vývoji víceklientské webové a nativní klientské aplikace. Tyto aplikace povolit přihlášení podle uživatelské účty z tenanta služby Azure AD, jiné než ten, ve kterém aplikace bude zaregistrovaná. Také může být potřeba přístup k webovým rozhraním API, jako:
- Rozhraní Microsoft Graph API, službám Office 365, přístup k Azure AD a Intune. 
- Rozhraní API pro jiné služby Microsoftu.
- Vlastní webová rozhraní API. 

Rozhraní je založené na uživatele nebo správce udělení souhlasu pro aplikaci, která vyzve k registraci do svého adresáře. Registrace může zahrnovat přístup k datům adresáře. Po souhlas, klientská aplikace můžete volání rozhraní Microsoft Graph API jménem uživatele a použijte informace, podle potřeby.

[Microsoft Graph API](https://developer.microsoft.com/graph/) poskytuje přístup k datům v Office 365, jako je třeba:

- Kalendáře a zprávy z Exchange.
- Webům a seznamům ze Sharepointu.
- Dokumenty z Onedrivu.
- Poznámkové bloky z Onenotu.
- Úlohy z plánovače.
- Sešity v Excelu.

Rozhraní Graph API také poskytuje přístup pro uživatele a skupiny ze služby Azure AD a dalších datových objektů z více cloudovým službám Microsoftu.

Následující kroky ukazují, jak souhlasu prostředí funguje pro uživatele a vývojáře aplikací:

1. Předpokládejme, že máte webovou aplikaci klienta, která potřebuje požádat o specifické oprávnění pro přístup k prostředku nebo rozhraní API. Na webu Azure portal slouží k deklaraci žádosti oprávnění v době konfigurace. Jako další nastavení konfigurace se stanou součástí registrace služby Azure AD aplikace:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vezměte v úvahu, že byla aktualizována oprávnění vaší aplikace. Aplikace běží a uživatel je použití poprvé. První aplikace potřebuje k získání autorizačního kódu z Azure AD / zajistí autorizaci koncového bodu. Autorizační kód lze potom získat nový přístupový a obnovovací token.

3. Pokud není uživatel ověřen, Azure AD / authorize koncový bod výzvy k přihlášení.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Jakmile se uživatel přihlásil, Azure AD Určuje, zda uživatel potřebuje zobrazit stránka pro odsouhlasení podmínek. Toto rozhodnutí je založená na, jestli uživatel (nebo správce ve vaší organizaci) už udělené souhlasu s aplikací.

   Pokud nebyl udělen souhlas, Azure AD zobrazí výzvu k souhlasu a zobrazí požadovaná oprávnění, které jsou potřebné funkce. Oprávnění, která se zobrazí v dialogovém okně souhlasu odpovídat vybraných v delegovaná oprávnění na webu Azure Portal.

    ![Stránka pro odsouhlasení podmínek](./media/openidoauth-tutorial/consentpage.png)

Běžný uživatel může některá oprávnění vyjádřit souhlas. Další oprávnění vyžadují souhlas správce tenanta.

## <a name="difference-between-admin-consent-and-user-consent"></a>Rozdíl mezi souhlas správce a souhlasu uživatele

Jako správce může také souhlas delegovaná oprávnění aplikací schválit za všechny uživatele ve vašem tenantovi. Souhlas správce zabraňuje dialogové okno souhlasu zobrazilo pro každého uživatele v tenantovi. Uživatelé, kteří mají roli správce může poskytnout souhlas na webu Azure Portal. Z **nastavení** stránky pro vaši aplikaci, vyberte **požadovaná oprávnění** > **udělit oprávnění**.

![Tlačítko udělit oprávnění](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udělení výslovný souhlas s použitím **udělit oprávnění** je tlačítko vyžadované pro jednostránkové aplikace (SPA), které používají ADAL.js. V opačném případě aplikace selže při vyžádání tokenu přístupu.

Oprávnění jen pro aplikace vždy vyžadují souhlas správce tenanta. Pokud vaše aplikace požaduje na oprávnění jen pro aplikace a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva. Zpráva, že uživatel není možné vyjádřit souhlas.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, musíte mít gesta jako tlačítko nebo odkaz, kde může správce zahájit akci. Žádost, která vaše aplikace odešle pro tuto akci je obvykle požadavek ověřování OAuth2 nebo OpenID Connect. Tato žádost obsahuje *řádku = admin_consent* parametr řetězce dotazu. 

Poté, co správce schválil a instanční objekt je vytvořen v tenantovi zákazníka, není třeba novější žádostí o přihlášení *řádku = admin_consent* parametru. Vzhledem k tomu, že správce rozhodl, že požadovaná oprávnění jsou přijatelné, žádní jiní uživatelé v tenantovi požádejte ho o souhlas od tohoto okamžiku.

Správce tenanta můžete zakázat možnost pro pravidelné uživatelům udělit souhlas s aplikací. Pokud tato možnost je zakázaná, je vždy vyžadována pro aplikace pro použití v tenantovi souhlas správce. Pokud chcete otestovat aplikaci s svolení koncového uživatele zakázaná, můžete najít konfigurační přepínač v [webu Azure portal](https://portal.azure.com/). Je [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) části **podnikové aplikace**.

*Řádku = admin_consent* parametr je také možné aplikacemi, které požádat o oprávnění, které nevyžadují souhlas správce. Příkladem je aplikace, která vyžaduje, kde tenanta admin "uživatel zaregistruje do služby" jeden času a žádné jiné se uživatelům zobrazí výzva svolení od tohoto okamžiku v prostředí.

Představte si, že aplikace vyžaduje souhlas správce a správce přihlášení bez *řádku = admin_consent* parametr odeslání. Když správce se úspěšně vyjádří souhlas aplikace, bude se vztahovat pouze ke svému uživatelskému účtu. Běžní uživatelé bude stále nemůže přihlásit nebo aplikaci vyjádřit souhlas. Tato funkce je užitečná, pokud chcete poskytnout možnost Procházet aplikace před povolením přístupu uživatelů správce klienta.
