---
title: Umožňuje zobrazit a spravovat žádosti balíčku pro přístup ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Naučte se, jak zobrazit, rezpracovat a zrušit požadavky na balíček přístupu v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430273"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Umožňuje zobrazit a spravovat žádosti balíčku pro přístup ve správě nároků Azure AD (Preview).

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V části Správa opravňujících k Azure AD vidíte, kdo požaduje přístupové balíčky, jejich zásady a stav. Tento článek popisuje, jak zobrazit, znovu zpracovat a zrušit žádosti pro balíčky přístupu.

## <a name="view-requests"></a>Zobrazit žádosti

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Kliknutím na konkrétní požadavek zobrazíte další podrobnosti.

    ![Seznam žádostí pro balíček pro přístup](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Zobrazit chyby doručení žádosti

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Vyberte žádost, kterou chcete zobrazit.

    Pokud v žádosti dojde k žádným chybám doručení, stav žádosti se **nedoručení** nebo **částečně doručí**.

    Pokud dojde k chybám doručení, v podokně podrobností žádosti dojde k počtu chyb doručení.

1. Kliknutím na počet zobrazíte všechny chyby doručení žádosti.

## <a name="reprocess-a-request"></a>Znovu zpracovat požadavek

Pokud v požadavku dojde k chybě, můžete žádost znovu zpracovat a znovu ji vyzkoušet. Můžete zpracovat pouze požadavek se stavem **doručení se nezdařilo** nebo byl **částečně doručen** a datum dokončení je kratší než jeden týden.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete znovu zpracovat.

1. V podokně podrobností žádosti klikněte na znovu **zpracovat požadavek**.

    ![Znovu zpracovat neúspěšnou žádost](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Můžete zrušit pouze nevyřízenou žádost, která ještě nebyla doručena, nebo jejíž doručení se nezdařilo.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete zrušit.

1. V podokně podrobností žádosti klikněte na **Zrušit požadavek**.

## <a name="next-steps"></a>Další kroky

- [Nastavení žádosti o změnu a schválení pro balíček pro přístup](entitlement-management-access-package-request-policy.md)
- [Zobrazení, přidání a odebrání přiřazení balíčku pro přístup](entitlement-management-access-package-assignments.md)