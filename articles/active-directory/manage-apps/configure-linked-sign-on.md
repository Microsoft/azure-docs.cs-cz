---
title: Propojené přihlášení pro aplikace Azure AD – platforma identit Microsoftu
description: Konfigurace propojeného jednotného přihlašování (SSO) do podnikových aplikací Azure AD v platformě Microsoft identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063539"
---
# <a name="configure-linked-sign-on"></a>Konfigurace propojeného přihlašování

Přidáte-li galerii nebo webovou aplikaci mimo galerii, je jednou z možností jednotného přihlášení, které máte k dispozici, [propojené přihlášení](what-is-single-sign-on.md). Tuto možnost vyberte, pokud chcete přidat odkaz na aplikaci na přístupovém panelu Azure AD vaší organizace nebo na portálu Office 365. Pomocí této metody můžete přidat odkazy na vlastní webové aplikace, které aktuálně používají službu AD FS (nebo jinou federační službu) namísto Azure AD pro ověřování. Nebo můžete přidat přímé odkazy na konkrétní stránky SharePointu nebo jiné webové stránky, které se mají zobrazit na panelech přístupu uživatele.

## <a name="before-you-begin"></a>Než začnete

Pokud aplikace nebyla přidána do vašeho klienta Azure AD, přečtěte si informace [o přidání aplikace galerie](add-gallery-app.md) nebo přidání [aplikace, která není galerie.](add-non-gallery-app.md)

### <a name="open-the-app-and-select-linked-sign-on"></a>Otevření aplikace a výběr propojeného přihlášení

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce cloudových aplikací nebo správce aplikace pro vašeho klienta Azure AD.

1. Přejděte k**aplikacím** **Azure Active Directory** > Enterprise . Zobrazí se náhodný vzorek aplikací v tenantovi Azure AD. 

1. V nabídce **Typ aplikace** vyberte **Všechny aplikace**a pak vyberte **Použít**.

1. Do vyhledávacího pole zadejte název aplikace a vyberte ji z výsledků.

1. V části **Správa** vyberte **Jednotné přihlašování**. 

1. Vyberte **Propojeno**.

1. Zadejte adresu URL aplikace, na kterou chcete vytvořit odkaz. Zadejte adresu URL a vyberte **Uložit**. 
 
1. K aplikaci můžete přiřadit uživatele a skupiny, což způsobí, že se aplikace zobrazí ve [spouštěči aplikací Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo na [přístupovém panelu Azure AD](end-user-experiences.md) pro tyto uživatele.

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
