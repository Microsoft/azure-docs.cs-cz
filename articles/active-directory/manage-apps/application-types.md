---
title: Neočekávaná aplikace v seznamu Moje aplikace | Microsoft Docs
description: Jak zobrazit všechny aplikace ve vašem tenantovi a pochopit, jak se aplikace zobrazí v seznamu všechny aplikace v části podnikové aplikace
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "65781095"
---
# <a name="unexpected-application-in-my-applications-list"></a>Neočekávaná aplikace v seznamu Moje aplikace

Tento článek vám pomůže pochopit, jak se aplikace zobrazí v seznamu **všechny aplikace** v části **podnikové aplikace**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak zobrazit všechny aplikace ve vašem tenantovi

Chcete-li zobrazit všechny aplikace ve vašem tenantovi, je nutné pomocí ovládacího prvku **filtru** zobrazit **všechny aplikace** v seznamu **všechny aplikace** . Postupujte následovně:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

6.  klikněte na tlačítko použít ovládací prvek **filtru** v horní části **seznamu všechny aplikace**.

7.  V podokně **filtru** nastavte možnost **Zobrazit** pro **všechny aplikace.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Proč se v seznamu všechny aplikace zobrazuje konkrétní aplikace?

Při filtrování na **všechny aplikace**zobrazuje seznam **všechny aplikace** **List** všechny objekty instančních objektů ve vašem tenantovi. Objekty zabezpečení služby se můžou v tomto seznamu zobrazit různými způsoby:

1. Když přidáte libovolnou aplikaci z Galerie aplikací, včetně:

   1. **Aplikace Galerie Azure AD** – aplikace, která je předem integrovaná pro jednotné přihlašování pomocí Azure AD

   2. Aplikace **proxy aplikací** – aplikace spuštěná v místním prostředí, které chcete pro externě poskytovat zabezpečené jednotné přihlašování

   3. Aplikace založené na **vlastním** prostředí – aplikace, kterou vaše organizace chce vyvíjet na platformě pro vývoj aplikací Azure AD, ale ta ještě nemusí existovat.

   4. **Aplikace mimo galerii** – Přineste si vlastní aplikace! Libovolný webový odkaz, který chcete, nebo libovolnou aplikaci, která vykreslí pole uživatelského jména a hesla, podporuje protokoly SAML nebo OpenID Connect nebo podporuje SCIM, které chcete integrovat pro jednotné přihlašování pomocí Azure AD.

2. Při registraci k nebo přihlašování k aplikaci je k disAzure Active Directory<sup>rd</sup> aplikace, která je integrovaná s. Jedním z příkladů je [Smartsheet](https://app.smartsheet.com/b/home) nebo [Docusign](https://www.docusign.net/member/MemberLogin.aspx).

3. Při registraci do nebo přidání licence pro uživatele nebo skupinu do aplikace první strany, například [systém Microsoft Office 365](https://products.office.com/)

4. Při přidání nové registrace aplikace vytvořením aplikace vytvořené v [registru pomocí aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Při přidání nové registrace aplikace vytvořením aplikace vytvořené pomocí [portálu pro registraci aplikací v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Když přidáte aplikaci, kterou vyvíjíte, můžete použít [metody ověřování ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) nebo [připojené služby](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) sady Visual Studio.

7. Když vytvoříte instanční objekt služby pomocí [modulu Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Když [souhlasíte s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) jako správce pro používání dat ve vašem tenantovi

9. Když [Uživatel souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , aby používal data ve vašem tenantovi

10. Pokud povolíte určité služby, které budou ukládat data ve vašem tenantovi. Jedním z příkladů je resetování hesla, které se modeluje jako instanční objekt a bezpečně ukládá zásady pro resetování hesel.

Pokud chcete získat další informace o tom, jak se aplikace přidávají do vašeho adresáře, přečtěte si, [jak a proč se aplikace přidávají do služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat přiřazení konkrétního uživatele nebo skupiny do aplikace

Chcete-li odebrat přiřazení uživatele nebo skupiny k aplikaci, postupujte podle kroků uvedených v části [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat přihlášení všech uživatelů k aplikaci, postupujte podle kroků uvedených v tématu [zakázání přihlášení uživatelů pro podnikovou aplikaci v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Chci úplně odstranit aplikaci

K **odstranění aplikace**použijte následující postup:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete odstranit.

7. Po načtení aplikace klikněte na **Odstranit** ikonu v horním podokně s **přehledem** aplikace.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny operace souhlasu uživatele s budoucími uživateli u libovolné aplikace

Zákaz souhlasu uživatele pro celý adresář zabrání koncovým uživatelům v posílání do libovolné aplikace. Správci mohou i nadále souhlasit jménem uživatele. Chcete-li získat další informace o souhlasu aplikace a o tom, proč můžete nebo nebudete chtít souhlas, přečtěte si článek [porozumění souhlasu uživatele a správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Pokud chcete **Zakázat všechny operace souhlasu uživatele s ostatními uživateli v celém adresáři**, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **nastavení uživatele**.

6.  Zakažte všechny operace souhlasu s ostatními uživateli tím, že nastavením **uživatelů umožníte aplikacím přístup k jejich** přepínači **ne** a klikněte na tlačítko **Uložit** .

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
