---
title: Kontrola přístupu k balíčku přístupu ve správě oprávnění Azure AD
description: Přečtěte si, jak dokončit kontrolu přístupu k balíčkům přístupu ke správě nároků v recenzích přístupu ke službě Azure Active Directory (Preview).
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
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968747"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Kontrola přístupu k balíčku přístupu ve správě oprávnění Azure AD

Správa nároků Azure AD zjednodušuje způsob, jakým podniky spravují přístup ke skupinám, aplikacím a webům SharePointu. Tento článek popisuje, jak provádět kontroly přístupu pro ostatní uživatele, kteří jsou přiřazeni k balíčku přístupu jako určený recenzent.

## <a name="prerequisites"></a>Požadavky

Chcete-li zkontrolovat přiřazení balíčků aktivního přístupu uživatelů, musíte splnit požadavky na kontrolu přístupu:
- Azure AD Premium P2
- Globální správce
- Určený správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

Další informace naleznete v tématu [Licenční požadavky](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Otevření kontroly přístupu

Pomocí následujících kroků vyhledejte a otevřete kontrolu přístupu:

1. Můžete obdržet e-mail od společnosti Microsoft, který vás požádá o kontrolu přístupu. Vyhledejte e-mail a otevřete kontrolu přístupu. Zde je příklad e-mailu pro kontrolu přístupu:
    
    ![E-mail recenzenta accessu](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Kliknutím na odkaz **Zkontrolovat přístup uživatele** otevřete kontrolu přístupu. 

1. Pokud e-mail nemáte, můžete své nevyřízené kontroly přístupu https://myaccess.microsoft.comnajít tak, že přejdete přímo na .  (Pro vládu USA `https://myaccess.microsoft.us` použijte místo.)

1. Kliknutím na **Access recenze** na levém navigačním panelu zobrazíte seznam čekajících kontrol přístupu, které vám byly přiřazeny.
    
    ![Výběr kontrol přístupu v aplikaci Můj přístup](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klikněte na recenzi, kterou chcete zahájit.
    
    ![Výběr kontroly přístupu](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Provedení kontroly přístupu

Po otevření kontroly přístupu se zobrazí jména uživatelů, pro které je třeba zkontrolovat. Přístup můžete schválit nebo odepřít dvěma způsoby:
- Přístup můžete ručně schválit nebo odepřít jednomu nebo více uživatelům.
- Můžete přijmout systémová doporučení

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ruční schválení nebo odepření přístupu pro jednoho nebo více uživatelů
1. Zkontrolujte seznam uživatelů a určete, kteří uživatelé musí mít i nadále přístup.

    ![Seznam uživatelů ke kontrole](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Chcete-li přístup schválit nebo odepřít, vyberte přepínací tlačítko nalevo od jména uživatele.

1. V pruhu nad uživatelskými jmény vyberte **Schválit** nebo **Odepřít.**

    ![Výběr uživatele](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Pokud si nejste jisti, můžete kliknout na tlačítko **Nevědět.**

    Pokud provedete tento výběr, uživatel udržuje přístup a tento výběr přejde do protokolů auditu. V protokolu se zobrazí další recenzenti, kterým jste recenzi ještě dokončili.

1. Můžete být požádáni, abyste své rozhodnutí zdůvodní. Zadejte důvod a klepněte na tlačítko **Odeslat**.

    ![Schválení nebo odepření přístupu](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Své rozhodnutí můžete kdykoli změnit před koncem recenze. Chcete-li tak učinit, vyberte uživatele ze seznamu a změňte rozhodnutí. Můžete například schválit přístup pro uživatele, které jste dříve odmítli.

Pokud existuje více recenzentů, zaznamená se poslední odeslaná odpověď. Vezměme si příklad, kdy správce určí dva recenzenty – Alici a Boba. Alice nejprve otevře recenzi a schválí přístup. Před ukončením kontroly Bob recenzi otevře a odepře přístup. V tomto případě se zaznamená poslední rozhodnutí o odepření přístupu.

>[!NOTE]
>Pokud je uživateli odepřen přístup, nejsou okamžitě odebrány z přístupového balíčku. Uživatel bude odebrán z balíčku přístupu po ukončení kontroly nebo správce ukončí kontrolu.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Schválení nebo odepření přístupu pomocí systémově generovaných doporučení

Chcete-li rychleji zkontrolovat přístup pro více uživatelů, můžete použít doporučení generovaná systémem a přijmout doporučení jediným kliknutím. Doporučení jsou generovány na základě aktivity přihlášení uživatele.

1.  Na panelu v horní části stránky klikněte na **Přijmout doporučení**.
    
    ![Vybrat přijmout doporučení](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Zobrazí se souhrn doporučených akcí.

1.  Chcete-li doporučení přijmout, klepněte na **tlačítko Odeslat.**

## <a name="next-steps"></a>Další kroky

- [Vlastní kontrola přístupových balíčků](entitlement-management-access-reviews-self-review.md)