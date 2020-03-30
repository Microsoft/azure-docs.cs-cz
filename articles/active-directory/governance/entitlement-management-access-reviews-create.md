---
title: Vytvoření kontroly přístupu balíčku přístupu ve správě nároků Azure AD
description: Přečtěte si, jak vytvořit zásady kontroly přístupu pro balíčky přístupu ke správě nároků v recenzích přístupu k Azure Active Directory (Preview).
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
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608836"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Vytvoření kontroly přístupu balíčku přístupu ve správě nároků Azure AD

Chcete-li snížit riziko zastaralého přístupu, měli byste povolit pravidelné kontroly uživatelů, kteří mají aktivní přiřazení k balíčku přístupu ve správě nároků Azure AD. Recenze můžete povolit při vytváření nového balíčku přístupu nebo při úpravě existujícího balíčku pro přístup. Tento článek popisuje, jak povolit kontroly přístupu k balíčkům přístupu.

## <a name="prerequisites"></a>Požadavky

Chcete-li povolit recenze přístupových balíčků, musíte splnit požadavky pro vytvoření přístupového balíčku:
- Azure AD Premium P2
- Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

Další informace naleznete v tématu [Licenční požadavky](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Vytvoření kontroly přístupu přístupového balíčku

Můžete povolit kontroly přístupu při [vytváření nového balíčku přístupu](entitlement-management-access-package-create.md) nebo úpravě existující zásady [balíčku přístupu.](entitlement-management-access-package-lifecycle-policy.md) Chcete-li povolit kontroly přístupu k přístupu k balíčku přístupu, postupujte takto:

1. Otevřete kartu **Životní cyklus** pro přístupový balíček a přejděte dolů na **Access Reviews**.

1. Přesuňte přepínač **Vyžadovat kontroly přístupu** na **Ano**.

    ![Přidání kontroly přístupu](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Zadejte datum, kdy budou recenze zahájeny vedle **možnosti Spuštění dne**.

1. Dále nastavte **frekvenci kontroly** **na roční**, **dvakrát ročně**, **čtvrtletně** nebo **měsíčně**.
Toto nastavení určuje, jak často dojde k kontrolám přístupu.

1. Nastavte **dobu trvání,** která definuje, kolik dní bude každá revize opakované řady otevřena pro vstup od recenzentů. Můžete například naplánovat každoroční recenzi, která začíná 1.

1. Vedle **položky Recenzenti**vyberte **možnost Vlastní kontrola,** pokud chcete, aby uživatelé provedli vlastní kontrolu přístupu, nebo vyberte **konkrétní recenzenty,** pokud chcete určit recenzenta.

    ![Vyberte Přidat recenzenty.](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Pokud jste vybrali **možnost Konkrétní recenzenti**, určete, kteří uživatelé budou kontrolu přístupu kontrolovat:
    1. Vyberte **Přidat recenzenty**.
    1. V podokně **Vybrat recenzenty** vyhledejte a vyberte uživatele, kterým chcete být recenzentem.
    1. Po výběru recenzentů klikněte na tlačítko **Vybrat.**

    ![Určení recenzentů](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klikněte na **Zkontrolovat + Vytvořit,** pokud vytváříte nový přístupový balíček nebo **Aktualizovat,** pokud upravujete přístupový balíček, v dolní části stránky.

## <a name="view-the-status-of-the-access-review"></a>Zobrazení stavu kontroly přístupu

Po počátečním datu bude v části **Kontroly přístupu** uvedena kontrola přístupu. Chcete-li zobrazit stav kontroly přístupu, postupujte takto:

1. V **části Zásady správného řízení identit**klikněte na **balíčky aplikace Access** a vyberte balíček přístupu se stavem kontroly přístupu, který chcete zkontrolovat.   

1. Jakmile se dostanete do přehledu přístupového balíčku, klikněte v levé nabídce na **Recenze aplikace Access.**
    
    ![Výběr kontrol přístupu](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Zobrazí se seznam obsahující všechny zásady, které mají k nim spojené kontroly přístupu. Kliknutím na recenzi zobrazíte její přehled.

    ![Seznam kontrol přístupu](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Při zobrazení sestavy se zobrazuje počet zkontrolovaných uživatelů a akce provedené recenzentem v nich.

    ![Zobrazit stav recenze](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Přístup k e-mailovým oznámením o recenzích
Můžete určit recenzenty nebo si uživatelé mohou zkontrolovat svůj přístup sami. Ve výchozím nastavení azure ad odešle e-mail recenzentům nebo recenzentům pro sebe sama krátce po zahájení kontroly.

E-mail bude obsahovat pokyny, jak zkontrolovat přístup k balíčkům. Pokud je kontrola na uživatelích, aby zkontrolovali jejich přístup, ukažte jim pokyny, jak provést vlastní kontrolu svých přístupových balíčků.
  
Pokud jste jako recenzenty přiřadili uživatele typu Host a nepřijali pozvánku pro hosty Azure AD, nebudou dostávat e-maily z kontrol přístupu k Azure AD. Musí nejprve přijmout pozvání a vytvořit účet s Azure AD před tím, než můžou přijímat e-maily. 

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k přístupovým balíčkům](entitlement-management-access-reviews-review-access.md)
- [Vlastní kontrola přístupových balíčků](entitlement-management-access-reviews-self-review.md)
