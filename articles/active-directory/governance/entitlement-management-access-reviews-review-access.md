---
title: Kontrola přístupu k balíčku přístupu v Azure AD – Správa nároků
description: Naučte se, jak dokončit kontrolu přístupu balíčků pro přístup pro správu opravňujících k přístupu v Azure Active Directorych kontrolách přístupu (Preview).
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
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798593"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Kontrola přístupu k balíčku přístupu v Azure AD – Správa nároků

Správa nároků Azure AD zjednodušuje způsob, jakým podniky spravují přístup ke skupinám, aplikacím a webům SharePointu. Tento článek popisuje, jak provést kontroly přístupu pro jiné uživatele, kteří jsou přiřazeni k balíčku přístupu jako určený kontrolor.

## <a name="prerequisites"></a>Požadavky

Chcete-li zkontrolovat přiřazení balíčku aktivních přístupů uživatelů, je nutné splnit požadavky na kontrolu přístupu:
- Azure AD Premium P2
- Globální správce
- Určený správce uživatele, vlastník katalogu nebo správce balíčků přístupu

Další informace najdete v tématu [licenční požadavky](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Otevřít kontrolu přístupu

K vyhledání a otevření kontroly přístupu použijte následující postup:

1. Můžete obdržet e-mail od Microsoftu, který vás vyzve ke kontrole přístupu. Vyhledejte e-mail a otevřete tak kontrolu přístupu. Tady je příklad e-mailu pro kontrolu přístupu:
    
    ![E-mail kontrolora kontroly přístupu](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Kliknutím na odkaz **Kontrola přístupu uživatele** otevřete kontrolu přístupu. 

1. Pokud nemáte e-mail, můžete najít své nedokončené kontroly přístupu, a to tak, že přejdete přímo na https://myaccess.microsoft.com .  (Pro státní správu USA použijte `https://myaccess.microsoft.us` místo toho.)

1. Kliknutím na kontroly **přístupu** na levém navigačním panelu zobrazte seznam nedokončených revizí přístupu, které jste vám přiřadili.
    
    ![Vybrat kontroly přístupu na mém přístupu](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klikněte na recenzi, kterou chcete začít.
    
    ![Vybrat kontrolu přístupu](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Provést kontrolu přístupu

Po otevření kontroly přístupu se zobrazí jména uživatelů, pro které potřebujete kontrolu. Existují dva způsoby, jak můžete schválit nebo odepřít přístup:
- Pro jednoho nebo více uživatelů můžete ručně schválit nebo odepřít přístup.
- Můžete přijmout doporučení systému.

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ruční schválení nebo zamítnutí přístupu pro jednoho nebo více uživatelů
1. Zkontrolujte seznam uživatelů a určete, kteří uživatelé musí mít přístup i nadále.

    ![Seznam uživatelů ke kontrole](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Chcete-li schválit nebo odepřít přístup, vyberte přepínač nalevo od jména uživatele.

1. Na panelu nad uživatelskými jmény vyberte **schválit** nebo **Odepřít** .

    ![Vybrat uživatele](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Pokud si nejste jisti, můžete kliknout na tlačítko **neznát** .

    Pokud tento výběr provedete, uživatel zachová přístup a tento výběr přejde do protokolů auditu. Protokol zobrazí všechny další kontrolory, u kterých jste stále dokončili kontrolu.

1. Možná budete muset zadat důvod pro vaše rozhodnutí. Zadejte důvod a klikněte na **Odeslat**.

    ![Schválit nebo zamítnout přístup](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Vaše rozhodnutí můžete kdykoli změnit na konci kontroly. Provedete to tak, že vyberete uživatele ze seznamu a změníte rozhodnutí. Můžete například schválit přístup pro uživatele, který jste předtím zamítli.

Pokud existuje více revidujících, je zaznamenána poslední odeslaná odpověď. Vezměte v úvahu příklad, kdy správce určí dva revidující – Alice a Bob. Alice otevře kontrolu jako první a schválí přístup. Před ukončením kontroly Bob otevře kontrolu a odmítne přístup. V tomto případě se zaznamená poslední rozhodnutí o odepření přístupu.

>[!NOTE]
>Pokud má uživatel odepřený přístup, neodebere se hned z balíčku pro přístup. Uživatel bude po skončení kontroly odebrán z balíčku pro přístup nebo Správce ukončí kontrolu.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Schválení nebo zamítnutí přístupu pomocí systémem vygenerovaných doporučení

Chcete-li zkontrolovat přístup více uživatelům rychleji, můžete použít vygenerovaná systémová doporučení a přijmout doporučení jediným kliknutím. Doporučení se generují na základě přihlašovací aktivity uživatele.

1.  Na panelu v horní části stránky klikněte na **přijmout doporučení**.
    
    ![Vyberte přijmout doporučení.](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Zobrazí se souhrn doporučených akcí.

1.  Doporučení přijměte kliknutím na **Odeslat** .

## <a name="next-steps"></a>Další kroky

- [Samoobslužné přezkoumání balíčků přístupu](entitlement-management-access-reviews-self-review.md)