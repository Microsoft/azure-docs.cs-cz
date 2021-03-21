---
title: Zobrazení aplikací pomocí klienta Azure Active Directory pro správu identit
description: Seznamte se s tím, jak zobrazit všechny aplikace pomocí klienta Azure Active Directory pro správu identit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 443e8c83ec29f9f0478e3881b9e6d6a8eb0bf403
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259782"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Zobrazení aplikací pomocí tenanta Azure AD pro správu identit
Základní informace [o řadě rychlý Start při správě aplikací](view-applications-portal.md) vás seznámí se základy. V takovém případě se dozvíte, jak zobrazit všechny aplikace pomocí tenanta Azure AD pro správu identit. Tento článek komentáře trochu hlubších typů aplikací, které najdete.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Proč se v seznamu všechny aplikace zobrazuje konkrétní aplikace?
Při filtrování na **všechny aplikace** zobrazuje seznam **všechny aplikace**  všechny objekty instančních objektů ve vašem tenantovi. Objekty zabezpečení služby se můžou v tomto seznamu zobrazit různými způsoby:
- Když přidáte libovolnou aplikaci z Galerie aplikací, včetně:
   - **Azure AD – podnikové aplikace** – aplikace přidané do vašeho tenanta pomocí možnosti **podnikové aplikace** na portálu Azure AD. Obvykle se aplikace integrují pomocí standardu SAML.
   - **Azure AD-registrace aplikací** – aplikace přidané do vašeho tenanta pomocí možnosti **Registrace aplikací** na portálu Azure AD. Obvykle vlastní vyvíjené aplikace s využitím standardů Open ID Connect a OAuth.
   - Aplikace **proxy aplikací** – aplikace spuštěná v místním prostředí, které chcete pro externě poskytovat zabezpečené jednotné přihlašování
- Při registraci k nebo přihlašování k aplikaci třetí strany, která je integrovaná s Azure Active Directory. Jedním z příkladů je [Smartsheet](https://app.smartsheet.com/b/home) nebo [Docusign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplikace Microsoftu, například Microsoft 365.
- Při přidání nové registrace aplikace vytvořením aplikace vytvořené v [registru pomocí aplikace](../develop/quickstart-register-app.md)
- Při přidání nové registrace aplikace vytvořením aplikace vytvořené pomocí [portálu pro registraci aplikací v 2.0](../develop/quickstart-register-app.md)
- Když přidáte aplikaci, vyvíjíte pomocí [metod ověřování ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) nebo [připojených služeb](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) sady Visual Studio.
- Když vytvoříte instanční objekt služby pomocí [modulu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)
- Když [souhlasíte s aplikací](../develop/howto-convert-app-to-be-multi-tenant.md) jako správce pro používání dat ve vašem tenantovi
- Když [Uživatel souhlasí s aplikací](../develop/howto-convert-app-to-be-multi-tenant.md) , aby používal data ve vašem tenantovi
- Pokud povolíte určité služby, které budou ukládat data ve vašem tenantovi. Jedním z příkladů je resetování hesla, které se modeluje jako instanční objekt a bezpečně ukládá zásady pro resetování hesel.

Další informace o tom, jak a proč se aplikace přidávají do adresáře, najdete v tématu [jak se přidávají aplikace do služby Azure AD](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
