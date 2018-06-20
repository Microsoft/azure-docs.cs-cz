---
title: Kroky pro konfiguraci aplikace OpenID/OAuth z Galerie aplikace Azure AD | Microsoft Docs
description: Kroky pro konfiguraci aplikace OpenID/OAuth z Galerie aplikace Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 176af41197810059a17daf5ab09d29e0169e9640
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225003"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Kroky pro konfiguraci aplikace OpenID/OAuth z Galerie aplikace Azure AD

## <a name="process-of-open-id-application-addition-from-gallery"></a>Proces přidání Open ID aplikace z Galerie

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace](./media/openidoauth-tutorial/tutorial_general_03.png)

4. Do vyhledávacího pole zadejte **název aplikace**, vyberte **požadovaných aplikací** z výsledků panelů přihlašovací až aplikace.

    ![Přidává se aplikace](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Pro aplikace Open ID Connect a OAuth přidat tlačítko ve výchozím nastavení vypnutá. V tomto poli musí správce klienta klikněte na **registrace** tlačítko a zadejte souhlas k aplikaci. S třídou, že aplikace bude se přidají do klienta zákazníka, není nutné explicitně, přidat a proveďte konfiguraci.

    ![Tlačítko Přidat](./media/openidoauth-tutorial/addbutton.png)

5. Když kliknete na přihlašovací odkaz, budete přesměrováni na stránku služby Azure AD pro přihlašovací údaje.

6. Po úspěšném ověření má uživatel tak, aby přijímal souhlasu ze stránky souhlasu a poté, se zobrazí na domovské stránce aplikace.

    > [!NOTE]
    > Zákazníci mohou přidávat pouze jednu instanci aplikace. Pokud máte již přidali a poskytují souhlasu se pokusil znovu ho nebudou přidány znovu v klientovi. Proto logicky uživatelé můžou používat jenom jednu instanci aplikace v klientovi.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější toku přihlášení obsahuje následující kroky – každý z nich je podrobně popsaná níže.

![Tok ověřování pomocí OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Víceklientské aplikace** -víceklientské aplikace určena pro použití v mnoha organizacích není právě jedné organizace. Toto jsou obvykle softwaru jako služba (SaaS) aplikace napsané pomocí nezávislý dodavatel softwaru (ISV). Víceklientské aplikace potřebují v každý adresář, kde se bude používat, který vyžaduje souhlas uživatele nebo správce k registraci jejich zřídit. Tento proces souhlasu spustí, když aplikace je zaregistrován v adresáři a je poskytnut přístup k rozhraní Graph API nebo možná jiné webové rozhraní API. Když uživatel nebo správce z jiné organizace zaregistrovat k používání aplikace, zobrazí se dialogové okno se zobrazí oprávnění, která aplikace vyžaduje. Uživatel nebo správce může pak souhlas k aplikaci, která poskytuje přístup k aplikaci stanovené dat a nakonec zaregistruje aplikaci do svého adresáře.

    > [!NOTE]
    > Pokud bude se vaše aplikace k dispozici uživatelům v několika adresářích, je nutné mechanismus k určení klientů, které jsou ve. Jednoho klienta aplikace potřebuje pouze hledat v její vlastní adresář pro uživatele, zatímco víceklientské aplikace potřebuje k identifikaci konkrétního uživatele ze všech adresářů ve službě Azure AD. K provedení této úlohy, Azure AD poskytuje společný koncový bod ověřování kde jakékoli víceklientské aplikace můžete nastavit žádostí o přihlášení, místo konkrétního klienta endpoint. Tento koncový bod je [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) pro všechny adresáře ve službě Azure AD, že koncový bod konkrétního klienta může být [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). Běžné koncový bod je obzvláště důležité vzít v úvahu při vývoji aplikace, protože budete potřebovat pomocí potřebné logiky pro zpracování více klientů při přihlášení, odhlášení a ověření tokenu.

Tým služby Azure AD ve výchozím nastavení podporuje víceklientské aplikace, jako který lze snadno přistupovat v různých organizacích a je snadno použitelný po přijetí souhlasu.

## <a name="what-is-consent-framework"></a>Co je souhlas Framework?

Rozhraní Azure AD souhlasu usnadňuje vývoj víceklientské web a nativní klientské aplikace. Tyto aplikace povolí přihlášení uživatelské účty z tenanta služby Azure AD, liší od verze, kde je registrovaná aplikace. Také budou potřebovat pro přístup k webové rozhraní API, jako je například Microsoft Graph API (pro přístup k Azure Active Directory, Intune a službám Office 365) a rozhraní API jiných služeb společnosti Microsoft, kromě vlastní webové rozhraní API. Rozhraní je založena na uživatele nebo správce udělení souhlasu k aplikaci, která požaduje být registrováno v jejich adresáři, který může zahrnovat přístup k datům adresáře. Po lze souhlasu, nebudou klientská aplikace volání rozhraní Microsoft Graph API jménem uživatele, a podle potřeby použijte informace.

[Microsoft Graph API](https://graph.microsoft.io/) poskytuje přístup k datům v Office 365 (jako je například kalendáře a zprávy z Exchange, weby a seznamy ze služby SharePoint, dokumenty z Onedrivu, poznámkových bloků z aplikace OneNote, úlohy z Planner, sešity z aplikace Excel, atd.), a také uživatelé a skupiny z Azure AD a jiných datových objektů z více cloudových služeb Microsoftu.

Následující kroky vám ukážou, jak souhlasu prostředí funguje pro vývojáře aplikací a uživatele.

1. Předpokládejme, že máte webové aplikace klienta, které je potřeba požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Portál Azure se používá k deklaraci žádosti o oprávnění v době konfigurace. Jako další nastavení konfigurace se stávají součástí registrace aplikace Azure AD:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Zvažte oprávnění aplikace byly aktualizovány, je aplikace spuštěna a uživatel je použijte první. Aplikace musí nejprve získat autorizační kód z Azure AD / zajistí autorizaci koncového bodu. Autorizační kód se pak lze získat přístup k nové a aktualizovat token.

3. Pokud uživatel již není ověřený, Azure AD je / autorizaci koncového bodu výzvy k přihlášení.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Jakmile se uživatel přihlásil, Azure AD se určují, jestli uživatel musí zobrazený na stránce souhlasu. Toto rozhodnutí je založená na tom, jestli uživatel (nebo správce jeho organizace) již udělil souhlas aplikace. Pokud již byla nebyl udělen souhlas, Azure AD zobrazí výzvu k souhlasu a zobrazí požadovaná oprávnění, musí se funkce. Sada oprávnění, která se zobrazí v dialogovém okně souhlasu odpovídat souborů vybraných v delegovaná oprávnění na webu Azure portal.

    ![Stránka souhlasu](./media/openidoauth-tutorial/consentpage.png)

Některá oprávnění může být souhlas regulární uživatelem, zatímco jiné vyžadují souhlas správce klienta.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Jaký je rozdíl mezi souhlas správce a souhlas uživatele?

Jako správce můžete také souhlas přidělená oprávnění aplikace jménem všechny uživatele ve vašem klientovi. Správce souhlas bránil zobrazení dialogu souhlasu pro každého uživatele v klientovi, můžete provádět na portálu Azure uživatelé s rolí správce. Na stránce nastavení pro vaši aplikaci klikněte na požadované oprávnění a klikněte na tlačítko udělit oprávnění.

![Udělení oprávnění](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udělení výslovným souhlasem pomocí tlačítka udělit oprávnění je momentálně nevyžaduje pro jednostránkové aplikace (SPA), které používají ADAL.js. Aplikace, jinak selže, pokud se požaduje přístupový token.

Oprávnění jen aplikace vždy vyžadovat souhlas správce klienta. Pokud vaše aplikace požaduje oprávnění jen aplikace a uživatel se pokusí přihlásit k aplikaci, se zobrazí chybová zpráva, že uživatel není možné vyjádřit souhlas.

Pokud vaše aplikace používá oprávnění, která vyžadovat souhlas správce, musíte mít gesto například tlačítko nebo odkaz, kde může správce zahájit akci. Žádost o vaše aplikace odešle pro tuto akci je obvykle OAuth2/OpenID Connect autorizace požadavek, který také obsahuje na řádku = admin_consent parametr řetězce dotazu. Dá souhlas správce a instanční objekt se vytvoří v klientovi zákazníka, následných žádostí o přihlášení není nutné řádku = admin_consent parametr. Vzhledem k tomu, že správce určil, že požadovaná oprávnění jsou přijatelné, požádejte ho o souhlas od tohoto okamžiku žádné jiných uživatelů v klientovi. Správce klienta můžete zakázat možnost pro regulární uživatele o souhlas pro aplikace. Pokud tato možnost je vypnuta, je požadován pro aplikace, který se má použít v klientovi vždycky souhlas správce. Pokud chcete k testování aplikace s souhlasu koncového uživatele zakázaná, můžete najít konfigurace přepínač na [portál Azure](https://portal.azure.com/) v [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) oddílu pod **Enterprise aplikace**

Na řádku = admin_consent parametru lze také aplikace, které žádostí o oprávnění, které nevyžadují souhlas správce. Příklad, kdy to by použila je, pokud aplikace vyžaduje prostředí, kde správce tenanta "zaregistruje" jeden čas a žádné jiné uživatele požádejte ho o souhlas od tohoto okamžiku.

Pokud aplikace vyžaduje souhlas správce a správce přihlášení bez řádku = admin_consent parametr odesílány, pokud správce úspěšně souhlasí do aplikace se vztahuje pouze ke svému uživatelskému účtu. Stále běžní uživatelé nebudou moci přihlásit nebo souhlas k aplikaci. Tato funkce je užitečná, pokud chcete poskytnout možnost prozkoumat aplikace před povolením přístupu jiných uživatelů správce klienta.