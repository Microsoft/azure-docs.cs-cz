---
title: Neočekávané aplikace v seznamu aplikací | Microsoft Docs
description: Jak zobrazit všechny aplikace ve vašem klientovi a pochopit, jak aplikace se objeví v seznamu všechny aplikace v rámci podnikové aplikace
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
ms.openlocfilehash: 6d8c1a251bcc4a7c0bb736df64c4c701a2fc748a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333871"
---
# <a name="unexpected-application-in-my-applications-list"></a>Neočekávané aplikace v seznamu Moje aplikace

Tento článek vám pomůže lépe porozumět jak aplikace se objeví v vaše **všechny aplikace** v rámci **podnikové aplikace, které**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak zjistit, všechny aplikace ve vašem klientovi

Pokud chcete zobrazit všechny aplikace ve vašem klientovi, budete muset použít **filtru** řízení zobrazíte **všechny aplikace** v části **všechny aplikace** seznamu. Postupujte následovně:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

6.  Klikněte na tlačítko použít **filtru** ovládací prvek v horní části **seznam všech aplikací**.

7.  Na **filtru** podokně nastavit **zobrazit** možnost k **všechny aplikace.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Proč konkrétní aplikace se zobrazuje v seznamu všechny aplikace?

Když filtruje tak, aby **všechny aplikace**, **všechny aplikace** **seznamu** ukazuje každý objekt zabezpečení služby ve vašem klientovi. Hlavní objekty služeb můžete zobrazit v tomto seznamu různými způsoby:

1.  Když přidáte všechny aplikace v galerii aplikací, včetně:

   1. **Galerii aplikací Azure AD** – aplikaci, která jsou předem integrované pro jednotné přihlašování s Azure AD

   2. **Aplikace Proxy aplikace** – aplikace běžící v místním prostředí, které byste chtěli poskytnout zabezpečený jednotné přihlašování k externě

   3. **Aplikace vyvinuté vlastní** – aplikaci, kterou vaše organizace chce vývoji na platformu vývoj aplikací Azure AD, ale který ještě neexistuje

   4. **Aplikace bez Galerie** – přineste si vlastní aplikace! Všechny webový odkaz, který má být nebo jakékoli aplikace, která vykreslí pole uživatelské jméno a heslo, podporuje protokoly SAML nebo OpenID Connect, nebo podporuje SCIM, který chcete integrovat pro jednotné přihlašování s Azure AD.

2.  Když registrujete nebo přihlášení k a 3<sup>VP</sup> aplikaci strany integrované s Azure Active Directory. Jedním z příkladů je [Smartsheet](https://app.smartsheet.com/b/home) nebo [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Pokud registrujete nebo přidání licenci na uživatele nebo skupiny k první aplikaci strany, jako například [Microsoft Office 365](http://products.office.com/)

4.  Když přidáte novou registraci aplikace tak, že vytvoříte vlastní vyvinuté aplikace pomocí [registru aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  Když přidáte novou registraci aplikace tak, že vytvoříte vlastní vyvinuté aplikace pomocí [portálu pro registraci aplikace V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)

6.  Když přidáváte aplikaci provádíte vývoj pomocí sady Visual Studio [metody ověřování ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) nebo [připojení služby](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  Když vytvoříte službu objektu zabezpečení pomocí [modul Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  Pokud jste [souhlas aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) jako správce a používat data ve vašem klientovi

9.  Když [uživatel souhlasí k aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) používat data ve vašem klientovi

10. Když povolíte určité služby, které ukládají data ve vašem klientovi. Jedním z příkladů je resetování hesla, která je modelovaná jako objekt služby k uložení vaše heslo resetovat zásady bezpečně.

Chcete-li získat další informace o tom, jak aplikace jsou přidány do vašeho adresáře, přečtěte si [jak a proč se aplikace přidávají do služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcete odstranit přiřazení konkrétního uživatele nebo skupiny do aplikace

Chcete-li odebrat uživatele nebo skupiny přiřazení k aplikaci, postupujte podle kroků uvedených v [odebrat uživatele nebo skupinu přiřazení z podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcete zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat všechna uživatelská přihlášení k aplikaci, postupujte podle kroků uvedených v [zakázat uživatelská přihlášení pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcete odstranit aplikaci zcela

K **odstranit aplikaci**, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete odstranit.

7.  Po načtení aplikace, klikněte na **odstranit** ikonu z hlavní aplikace **přehled** podokně.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcete zakázat všechny operace souhlasu budoucí uživatele do jakékoli aplikace

Zakázání souhlas uživatele pro souhlas pro žádnou aplikaci koncovým uživatelům zabránit celý adresář. Správci mohou stále souhlas na behalves uživatele. Další informace o aplikaci souhlas, a proto může nebo nemusí být žádoucí souhlas, číst [Principy uživatelů a správce souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

K **zakažte všechny operace souhlasu budoucí uživatele v adresáři celý**, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **uživatelská nastavení**.

6.  Zakažte všechny budoucí uživatele souhlasu operace nastavením **uživatelé můžou aplikace přistupovat ke svým datům** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md)
