---
title: V seznamu aplikací neočekávaná aplikace | Dokumentace Microsoftu
description: Jak zobrazit všechny aplikace ve vašem tenantovi a pochopit, jak aplikace se objeví v seznamu všechny aplikace v rámci podnikových aplikací
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 754a06cc8bb9cc6e660e2ac5f01ef2c40176eb05
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356460"
---
# <a name="unexpected-application-in-my-applications-list"></a>V seznamu aplikací neočekávaná aplikace

Tento článek vám pomůže lépe porozumět zobrazení aplikací ve vaší **všechny aplikace** seznamu v části **podnikové aplikace**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak zobrazit všechny aplikace ve vašem tenantovi

Pokud chcete zobrazit všechny aplikace ve vašem tenantovi, budete muset použít **filtr** ovládací prvek zobrazíte **všechny aplikace** v části **všechny aplikace** seznamu. Postupujte následovně:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

6.  Klikněte na možnost použití **filtr** ovládacího prvku v horní části **seznam všech aplikací**.

7.  Na **filtr** podokno, nastavte **zobrazit** umožňuje **všechny aplikace.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Proč konkrétní aplikaci zobrazí v seznamu všechny aplikace?

Při filtrování do **všechny aplikace**, **všechny aplikace** **seznamu** ukazuje, každý instanční objekt ve vašem tenantovi. Instanční objekty se mohou zobrazit v tomto seznamu různými způsoby:

1.  Když přidáte libovolnou aplikaci z Galerie aplikací, včetně:

   1. **Galerie aplikací Azure AD** – což je aplikace, který byl předem integrovaných pro jednotné přihlašování s Azure AD

   2. **Proxy aplikace** – aplikaci spuštěnou v místním prostředí, která byste chtěli poskytnout zabezpečené jednotné přihlašování k externě

   3. **Vlastní aplikace** – což je aplikace, která vaše organizace chce vývoj na platformě vývoje aplikací Azure AD, ale možná ještě neexistuje, který

   4. **Aplikace mimo galerii** – používání vlastních aplikací. Libovolný webový odkaz, který chcete nebo jakékoliv aplikace, která vykreslí pole uživatelského jména a hesla, podporuje přes protokol SAML nebo OpenID Connect nebo SCIM, kterou chcete integrovat pro jednotné přihlašování s Azure AD podporuje.

2.  Při registraci nebo přihlášení k 3<sup>VP</sup> stran aplikace integrované s Azure Active Directory. Jedním z příkladů je [Smartsheet](https://app.smartsheet.com/b/home) nebo [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Při registraci nebo přidání licence pro uživatele nebo skupiny k první aplikaci stran, jako jsou [Microsoft Office 365](http://products.office.com/)

4.  Když přidáte tak, že vytvoříte vlastní aplikaci pomocí registrace nové aplikace [registru aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  Když přidáte tak, že vytvoříte vlastní aplikaci pomocí registrace nové aplikace [portál pro registraci aplikace V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)

6.  Pokud jste přidali aplikaci vyvíjíte pomocí sady Visual Studio [metody ověřování ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) nebo [připojené služby](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  Když vytvoříte instanční objekt služby pomocí [modulu Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  Pokud jste [souhlas aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) jako správce, aby používala data ve vašem tenantovi

9.  Když [uživatel vyjádří souhlas aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) používat data ve vašem tenantovi

10. Když povolíte určité služby, které ukládají data ve vašem tenantovi. Jedním z příkladů je resetování hesel, která je modelovaná jako objekt služby k uložení hesla resetovat zásady bezpečně.

Pokud chcete získat další podrobnosti o tom, jak se aplikace přidávají do adresáře, přečtěte si [jak a proč se aplikace přidávají do služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat konkrétní uživatele nebo skupiny přiřazení k aplikaci

Odebrání uživatele nebo skupiny přiřazení k aplikaci, postupujte podle kroků uvedených v [přiřazení uživatele nebo skupinu odebrat podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

K zakázání všech přihlášení uživatelů k aplikaci, postupujte podle kroků uvedených v [zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Zcela odstranit aplikaci

K **odstranit aplikaci**, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete odstranit.

7.  Po načtení aplikace, klikněte na tlačítko **odstranit** ikonu z hlavní aplikace **přehled** podokně.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny operace vyjádření souhlasu budoucí uživatele do žádné aplikace

Zakázání souhlas uživatele, pro celý adresář zabrání koncovým uživatelům od vyjádření souhlasu s libovolnou aplikaci. Správci můžou udělit souhlas stále na behalves uživatele. Další informace o souhlasu s aplikací a proč může nebo nemusí chtít vyjádřit souhlas, přečtěte si [uživatele porozumění a souhlas správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

K **zakázat všechny operace vyjádření souhlasu budoucí uživatele v adresáři celý**, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **uživatelská nastavení**.

6.  Všechny operace vyjádření souhlasu uživatele budoucí zakázat nastavením **uživatelé můžou povolit aplikacím přístup ke svým datům** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
