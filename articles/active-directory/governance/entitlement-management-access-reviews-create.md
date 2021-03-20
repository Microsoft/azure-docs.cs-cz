---
title: Vytvoření kontroly přístupu balíčku přístupu v Azure AD – Správa nároků
description: Přečtěte si, jak vytvořit zásady kontroly přístupu pro balíčky přístupu pro správu oprávnění ve Azure Active Directory kontroly přístupu (Preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798507"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Vytvoření kontroly přístupu balíčku přístupu v Azure AD – Správa nároků

Pokud chcete snížit riziko zastaralého přístupu, měli byste povolit pravidelné kontroly uživatelů, kteří mají aktivní přiřazení k balíčku přístupu ve správě nároků ve službě Azure AD. Můžete povolit recenze, když vytvoříte nový balíček pro přístup nebo upravíte existující balíček pro přístup. Tento článek popisuje, jak povolit kontroly přístupu pro balíčky přístupu.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete povolit recenze balíčků pro přístup, musíte splnit požadavky pro vytvoření balíčku přístupu:
- Azure AD Premium P2
- Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

Další informace najdete v tématu [licenční požadavky](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Vytvoření kontroly přístupu balíčku přístupu

Kontroly přístupu můžete povolit při [vytváření nového přístupového balíčku](entitlement-management-access-package-create.md) nebo při [úpravách existujících zásad balíčku přístupu](entitlement-management-access-package-lifecycle-policy.md) . Pomocí těchto kroků povolíte kontroly přístupu balíčku pro přístup:

1. Otevřete kartu **životní cyklus** pro balíček přístupu a posuňte se dolů k **recenzím přístupu**.

1. Přesuňte přepínač **vyžadovat revize přístupu** na **Ano**.

    ![Přidat kontrolu přístupu](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Zadejte datum, kdy začnou pročínat další **zahájení**.

1. V dalším kroku nastavte **četnost přezkoumání** na **ročně**, v **rámci každoročního** **roku, čtvrtletně** nebo **měsíčně**.
Toto nastavení určuje, jak často budou kontroly přístupu provedeny.

1. Nastavte **dobu trvání** , která definuje počet dní, po které se každá kontrola opakujících řad otevře pro vstup od revidujících. Můžete například naplánovat roční přezkum, který začíná 1. ledna a je otevřen ke kontrole po dobu 30 dnů, aby kontroloři měli až do konce měsíce.

1. U **revidujících** vyberte **vlastní kontrolu** , pokud chcete, aby uživatelé prováděli vlastní kontrolu přístupu, nebo vyberte **konkrétní kontrolory** , pokud chcete určit kontrolora.

    ![Vyberte Přidat kontrolory.](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Pokud jste vybrali **konkrétní kontrolory**, určete, kteří uživatelé budou provádět kontrolu přístupu:
    1. Vyberte **Přidat revidující**.
    1. V podokně **Vybrat revidující** vyhledejte a vyberte uživatele, které chcete zobrazit jako kontrolora.
    1. Po výběru kontrolorů klikněte na tlačítko **Vybrat** .

    ![Zadat kontrolory](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klikněte na tlačítko **zkontrolovat + vytvořit** , pokud vytváříte nový balíček pro přístup nebo **aktualizujete** , pokud upravujete balíček přístupu, a to v dolní části stránky.

## <a name="view-the-status-of-the-access-review"></a>Zobrazit stav kontroly přístupu

Po počátečním datu bude v části **recenze přístupů** uvedená kontrola přístupu. Chcete-li zobrazit stav kontroly přístupu, postupujte podle těchto kroků:

1. V části zásady **správného řízení identity** klikněte na **přístupové balíčky** a potom vyberte balíček pro přístup se stavem kontroly přístupu, který chcete zkontrolovat.   

1. Po dokončení přehledu přístupového balíčku klikněte v levé nabídce na **recenze přístupu** .
    
    ![Vybrat kontroly přístupu](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Zobrazí se seznam obsahující všechny zásady, ke kterým jsou přidružené kontroly přístupu. Kliknutím na kontrolu zobrazíte její sestavu.

    ![Seznam revizí přístupu](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Při zobrazení sestavy se zobrazí počet zkontrolovaných uživatelů a akce provedené kontrolorem.

    ![Zobrazit stav kontroly](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>E-mailová oznámení kontroly přístupu
Můžete určit kontrolory nebo si uživatelé můžou zkontrolovat svůj přístup sami. Ve výchozím nastavení vám Azure AD pošle e-maily kontrolorům nebo samoobslužným recenzentům krátce po zahájení kontroly.

E-mail obsahuje pokyny, jak zkontrolovat přístup k balíčkům přístupu. Pokud je kontrola přístupná pro uživatele, zobrazte si pokyny, jak provést vlastní přezkoumání balíčků pro přístup.
  
Pokud jste uživatelům typu Host přiřazeni jako kontroloři a nepřijali svou pozvánku na hostovi služby Azure AD, nebudou dostávat e-maily z kontrol přístupu služby Azure AD. Před přijetím e-mailů musí nejdřív přijmout pozvánku a vytvořit účet ve službě Azure AD. 

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k balíčkům přístupu](entitlement-management-access-reviews-review-access.md)
- [Samoobslužné přezkoumání balíčků přístupu](entitlement-management-access-reviews-self-review.md)
