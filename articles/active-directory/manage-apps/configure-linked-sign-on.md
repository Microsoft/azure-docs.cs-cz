---
title: Propojené přihlašování pro aplikace Azure AD – Microsoft Identity Platform
description: Konfigurace propojeného jednotného přihlašování (SSO) k podnikovým aplikacím Azure AD na platformě Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763478"
---
# <a name="configure-linked-sign-on"></a>Konfigurace propojeného přihlašování

Když přidáte galerii nebo webovou aplikaci mimo galerii, je k dispozici jedna z možností jednotného [přihlašování,](what-is-single-sign-on.md)která je k dispozici. Tuto možnost vyberte, pokud chcete přidat odkaz na aplikaci na přístupovém panelu Azure AD nebo na portálu Office 365 vaší organizace. Pomocí této metody můžete přidat odkazy na vlastní webové aplikace, které aktuálně používají Active Directory Federation Services (AD FS) (nebo jinou federační službu) místo Azure AD k ověřování. Nebo můžete přidat přímé odkazy na konkrétní SharePointové stránky nebo jiné webové stránky, které chcete zobrazit na přístupových panelech uživatele.

## <a name="before-you-begin"></a>Než začnete

Pokud se aplikace nepřidala do tenanta Azure AD, přečtěte si téma [Přidání aplikace Galerie](add-gallery-app.md) nebo [Přidání aplikace mimo galerii](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Otevřete aplikaci a vyberte odkazovaná přihlašování.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce cloudové aplikace nebo správce aplikace pro vašeho TENANTA Azure AD.

1. Přejděte na **Azure Active Directory**  >  **podnikové aplikace**. Zobrazí se náhodná ukázka aplikací v tenantovi Azure AD. 

1. V nabídce **Typ aplikace** vyberte **všechny aplikace**a pak vyberte **použít**.

1. Do vyhledávacího pole zadejte název aplikace a pak vyberte aplikaci z výsledků.

1. V části **Spravovat** vyberte **jednotné přihlašování**. 

1. Vyberte **propojeno**.

1. Zadejte adresu URL aplikace, na kterou se má vytvořit odkaz. Zadejte adresu URL a vyberte **Uložit**. 
 
1. K aplikaci můžete přiřadit uživatele a skupiny, což způsobí, že se aplikace zobrazí ve [Spouštěči aplikací Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo na [přístupovém panelu Azure AD](end-user-experiences.md) pro tyto uživatele.

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
