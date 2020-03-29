---
title: Neočekávaná aplikace v seznamu aplikací | Dokumenty společnosti Microsoft
description: Jak zobrazit všechny aplikace v tenantovi a pochopit, jak se aplikace zobrazují v seznamu Všechny aplikace v části Podnikové aplikace
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781095"
---
# <a name="unexpected-application-in-my-applications-list"></a>Neočekávaná aplikace v seznamu aplikací

Tento článek vám pomůže pochopit, jak se aplikace zobrazují v seznamu **Všechny aplikace** v části **Podnikové aplikace**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak zobrazit všechny aplikace ve vašem tenantovi

Chcete-li zobrazit všechny aplikace ve vašem tenantovi, musíte použít ovládací **prvek Filtr** k zobrazení **všech aplikací** v seznamu **Všechny aplikace.** Postupujte následovně:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

6.  Klepněte na tlačítko Použít ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace**.

7.  V podokně **Filtr** nastavte možnost **Zobrazit** na **všechny aplikace.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Proč se konkrétní aplikace zobrazuje v seznamu všech aplikací?

Při filtrování na **všechny aplikace**, **seznam** **všech aplikací** zobrazuje každý objekt instančního objektu ve vašem tenantovi. Objekty Instančního objektu se mohou v tomto seznamu zobrazit různými způsoby:

1. Když přidáte libovolnou aplikaci z galerie aplikací, včetně:

   1. **Aplikace Azure AD Gallery** – aplikace, která byla předem integrovaná pro jednotné přihlašování pomocí Azure AD

   2. **Aplikace proxy aplikací** – aplikace spuštěná v místním prostředí, která chcete zajistit zabezpečené jednotné přihlašování externě

   3. **Vlastní vyvinuté aplikace** – aplikace, které vaše organizace chce vyvíjet na platformě pro vývoj aplikací Azure AD, ale která ještě neexistuje

   4. **Non-Galerie Aplikace** - Přineste si vlastní aplikace! Libovolný webový odkaz, který chcete, nebo jakákoli aplikace, která vykresluje uživatelské jméno a heslo pole, podporuje protokoly SAML nebo OpenID Connect nebo podporuje SCIM, které chcete integrovat pro jednotné přihlašování s Azure AD.

2. Při registraci nebo přihlášení k aplikaci<sup>třetích</sup> stran integrované do služby Azure Active Directory. Jedním z příkladů je [Smartsheet](https://app.smartsheet.com/b/home) nebo [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Při registraci nebo přidání licence uživateli nebo skupině do aplikace první strany, jako je [Microsoft Office 365](https://products.office.com/)

4. Přidáte-li novou registraci aplikace vytvořením vlastní vyvinuté aplikace pomocí [registru aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Přidáte-li novou registraci aplikace vytvořením vlastní vyvinuté aplikace pomocí [portálu Registrace aplikací V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Když přidáte aplikaci, kterou vyvíjíte pomocí [ASP.net metody ověřování](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) sady Visual Studio nebo připojené [služby](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. Při vytváření objektu instančního objektu pomocí [modulu Azure AD PowerShell Module](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Když jako správce [souhlasíte s tím,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) že aplikace bude používat data ve vašem tenantovi

9. Když [uživatel souhlasí s tím, aby aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) používala data ve vašem tenantovi

10. Když povolíte určité služby, které ukládají data ve vašem tenantovi. Jedním z příkladů je resetování hesla, který je modelován jako instanční objekt pro bezpečné uložení zásad y proobnovení hesla.

Další podrobnosti o tom, jak se aplikace přidávají do vašeho adresáře, načlánekněte článek [Jak a proč se aplikace přidávají do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat přiřazení konkrétního uživatele nebo skupiny k aplikaci

Pokud chcete odebrat přiřazení uživatele nebo skupiny do aplikace, postupujte podle pokynů uvedených v článku [Odebrání uživatele nebo skupiny z podnikové aplikace.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat všechna přihlášení uživatelů k aplikaci, postupujte podle pokynů uvedených v článku [Zakázat přihlášení uživatelů k podnikové aplikaci.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Chci zcela odstranit aplikaci

Chcete-li **aplikaci odstranit**, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete odstranit.

7. Po načtení aplikace **klikněte** na Odstranit ikonu v podokně **Přehled** horní aplikace.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny budoucí operace se souhlasem uživatele pro libovolnou aplikaci

Zakázání souhlasu uživatele pro celý adresář zabrání koncovým uživatelům v souhlasu s jakoukoli aplikací. Správci mohou souhlasit jménem uživatele. Další informace o souhlasu s aplikací a o tom, proč můžete nebo nemusíte chtít souhlas, najdete v článek [Principy souhlasu uživatele a správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

**Chcete-li zakázat všechny budoucí operace se souhlasem uživatele v celém adresáři**, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na **položku Uživatelská nastavení**.

6.  Zakažte všechny budoucí operace souhlasu uživatelů nastavením, že **uživatelé mohou aplikacím povolit přístup k datům,** přepíná na **Ne,** a klikněte na tlačítko **Uložit.**

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
