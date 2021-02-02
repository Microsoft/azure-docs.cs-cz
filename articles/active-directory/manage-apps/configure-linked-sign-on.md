---
title: Principy propojených přihlášení v Azure Active Directory
description: Pochopení propojených přihlašování v Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3e2d3ab6a23ce588c3aa393e5096ac75e88a5365
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255276"
---
# <a name="understand-linked-sign-on"></a>Principy propojených přihlášení

V [řadě rychlých startů](view-applications-portal.md) při správě aplikací jste zjistili, jak používat Azure AD jako zprostředkovatele identity (IDP) pro aplikaci. V průvodci rychlým startem můžete nakonfigurovat jednotné přihlašování založené na SAML nebo OIDC. Je **propojena** jiná možnost. V tomto článku najdete další podrobnosti o možnosti propojeno.

Možnost **propojit** umožňuje nakonfigurovat cílové umístění, když uživatel vybere aplikaci v [mých aplikacích](https://myapps.microsoft.com/) nebo na portálu Office 365 vaší organizace.

Mezi běžné scénáře, kde je možnost propojení užitečná, patří:
- Přidejte odkaz na vlastní webovou aplikaci, která aktuálně používá federaci, například Active Directory Federation Services (AD FS) (AD FS).
- Přidejte přímé odkazy na konkrétní SharePointové stránky nebo jiné webové stránky, které chcete zobrazit na přístupových panelech uživatele.
- Přidejte odkaz na aplikaci, která nevyžaduje ověření. 
 
 Možnost **propojený** neposkytuje funkce přihlašování prostřednictvím přihlašovacích údajů Azure AD. Ale můžete dál používat některé z dalších funkcí **podnikových aplikací**. Můžete například použít protokoly auditu a přidat vlastní logo a název aplikace.

## <a name="before-you-begin"></a>Než začnete

Pokud chcete rychle vymezit, Projděte si [řadu rychlých startů](view-applications-portal.md) při správě aplikací. V rychlém startu, ve kterém nakonfigurujete jednotné přihlašování, taky najdete **propojenou** možnost. 

Možnost **propojený** neposkytuje funkci přihlašování prostřednictvím služby Azure AD. Možnost jednoduše nastaví umístění, do kterého se budou uživatelé posílat při výběru aplikace v [okně moje aplikace](https://myapps.microsoft.com/) nebo ve spouštěči aplikace Microsoft 365.  Vzhledem k tomu, že přihlášení neposkytuje funkci přihlašování prostřednictvím služby Azure AD, není podmíněný přístup k dispozici pro aplikace nakonfigurované s propojeným jednotným přihlašováním.

> [!IMPORTANT] 
> Existují některé scénáře, kdy možnost **jednotného přihlašování** nebude v navigaci pro aplikaci v **podnikových aplikacích**. 
>
> Pokud byla aplikace zaregistrovaná pomocí nástroje **Registrace aplikací** pak je funkce jednotného přihlašování nastavená tak, aby ve výchozím nastavení používala protokol OAuth OIDC. V takovém případě se možnost **jednotného přihlašování** nezobrazí v části **podnikové aplikace** v navigaci. Když použijete **Registrace aplikací** k přidání vlastní aplikace, nakonfigurujete možnosti v souboru manifestu. Další informace o souboru manifestu naleznete v tématu [Azure Active Directory manifest aplikace](../develop/reference-app-manifest.md). Další informace o standardech jednotného přihlašování najdete v tématu [ověřování a autorizace pomocí platformy Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Další scénáře, kdy v navigaci chybí **jednotné přihlašování** , patří mezi ně, pokud je aplikace hostovaná v jiném tenantovi nebo pokud váš účet nemá požadovaná oprávnění (globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu). Oprávnění mohou také způsobit situaci, kdy můžete otevřít **jednotné přihlašování** , ale nebudete je moci uložit. Další informace o rolích pro správu Azure AD najdete v tématu ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Konfigurovat odkaz

Chcete-li nastavit odkaz pro aplikaci, vyberte možnost **propojeno** na stránce **jednotného přihlašování** . Pak zadejte odkaz a vyberte **Uložit**. Potřebujete připomenutí, kde najít tyto možnosti? Podívejte se na [řadu rychlých startů](view-applications-portal.md).
 
Po nakonfigurování aplikace přiřaďte k ní uživatele a skupiny. Když přiřadíte uživatele, můžete určit, kdy se aplikace zobrazí v [okně moje aplikace](https://myapps.microsoft.com/) nebo ve spouštěči aplikace Microsoft 365.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](./assign-user-or-group-access-portal.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
