---
title: Vyžádání přístupového balíčku – Správa nároků Azure AD
description: Naučte se používat portál pro přístup k vyžádání přístupu k balíčku přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979907"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Požádat o přístup k balíčku přístupu v Azure AD – Správa nároků

Díky správě opravňujících k Azure AD umožňuje balíček pro přístup jednorázovou instalaci prostředků a zásad, které automaticky spravují přístup po dobu života balíčku přístupu. 

Správce balíčků přístupu může nakonfigurovat zásady, které vyžadují schválení pro uživatele, kteří mají přístup k balíčkům přístupu. Uživatel, který potřebuje přístup k přístupovému balíčku, může odeslat žádost o získání přístupu. Tento článek popisuje, jak odeslat žádost o přístup.

## <a name="sign-in-to-the-my-access-portal"></a>Přihlášení k portálu pro přístup

Prvním krokem je přihlášení k portálu pro přístup, kde si můžete vyžádat přístup k balíčku přístupu.

**Požadovaná role:** Žadatele

1. Vyhledejte e-mail nebo zprávu z projektu nebo obchodního manažera, se kterým pracujete. E-mail by měl obsahovat odkaz na balíček pro přístup, ke kterému budete potřebovat přístup. Odkaz začíná na `myaccess` , obsahuje pomocný parametr adresáře a končí ID balíčku přístupu.  (Pro státní správu USA může být `https://myaccess.microsoft.us` místo toho doména.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otevřete odkaz.

1. Přihlaste se k portálu přístupu.

    Ujistěte se, že používáte svůj organizační (pracovní nebo školní) účet. Pokud si nejste jistí, obraťte se na svého projektu nebo obchodní manažer.

## <a name="request-an-access-package"></a>Vyžádání balíčku pro přístup

Po nalezení balíčku přístupu na portálu pro přístup můžete odeslat žádost.

**Požadovaná role:** Žadatele

1. V seznamu vyhledejte balíček pro přístup.  V případě potřeby můžete hledat zadáním hledaného řetězce a následným výběrem filtru **název**, **katalog**nebo **prostředky** .

    ![Můj portál pro přístup – vyhledávání prostředků](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klikněte na značku zaškrtnutí a vyberte balíček pro přístup.

1. Kliknutím na **požádat o přístup** otevřete podokno žádosti o přístup.

    ![Portál přístupu – přístupové balíčky](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Pokud se zobrazí pole **obchodní odůvodnění** , zadejte odůvodnění přístupu pro potřebu přístupu.

1. Pokud je **požadavek na určité období** povolený, vyberte **Ano** nebo **ne**.

1. V případě potřeby zadejte počáteční a koncové datum.

    ![Můj portál přístupu – požádat o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Po dokončení kliknutím na **Odeslat** odešlete žádost.

1. Kliknutím na **Historie žádostí** zobrazíte seznam žádostí a stav.

    Pokud přístupový balíček vyžaduje schválení, je žádost nyní ve stavu čeká na schválení.

### <a name="select-a-policy"></a>Výběr zásady

Pokud požadujete přístup k balíčku přístupu, který má víc zásad, které platí, můžete být požádáni, abyste vybrali zásadu. Správce balíčků pro přístup může například nakonfigurovat balíček přístupu se dvěma zásadami pro dvě skupiny interních zaměstnanců. První zásada může mít povolený přístup po 60 dnech a vyžaduje schválení. Druhá zásada může mít povolený přístup 2 dny a nemusí vyžadovat schválení. Pokud se setkáte s tímto scénářem, musíte vybrat zásadu, kterou chcete použít.

![Můj portál přístupu – žádost o přístup-více zásad](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Vyplnit informace o žadateli

Než udělíte přístup k balíčku přístupu, můžete požádat o přístup k balíčku pro přístup, který vyžaduje obchodní odůvodnění a další informace o žadateli. Vyplňte všechny informace žadatele vyžadované pro přístup k balíčku přístupu.

![Můj portál přístupu – žádost o přístup-vyplnit informace žadatele](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Odeslat žádost znovu

Když vyžádáte přístup k balíčku přístupu, může být žádost zamítnutá nebo může vypršet platnost vaší žádosti, pokud schvalovatelé neodpoví včas. Pokud potřebujete přístup, můžete to zkusit znovu a odeslat žádost znovu. Následující postup vysvětluje, jak znovu odeslat žádost o přístup:

**Požadovaná role:** Žadatele

1. Přihlaste se k portálu **přístupu** .

1. Klikněte na **historie požadavků** z navigační nabídky vlevo.

1. Najděte balíček pro přístup, pro který znovu odešlete žádost.

1. Kliknutím na značku zaškrtnutí vyberte balíček přístupu.

1. Klikněte na modré **zobrazení** na odkaz napravo od vybraného balíčku přístupu.
    
    ![Vybrat přístup k balíčku a odkazu pro zobrazení](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Otevře se podokno napravo s historií žádostí balíčku pro přístup.
    
    ![Vybrat tlačítko pro opětovné odeslání](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klikněte na tlačítko **znovu odeslat** v dolní části podokna.

## <a name="cancel-a-request"></a>Zrušení žádosti

Pokud odešlete žádost o přístup a žádost zůstane stále ve stavu **čeká na schválení** , můžete žádost zrušit.

**Požadovaná role:** Žadatele

1. Na portálu pro přístup na levé straně klikněte na **Historie žádostí** , abyste viděli seznam žádostí a stav.

1. Klikněte na odkaz **Zobrazit** u žádosti, kterou chcete zrušit.

1. Pokud je požadavek stále ve stavu **čekání na schválení** , můžete kliknout na **zrušit žádost** o zrušení žádosti.

    ![Portál pro přístup – zrušit požadavek](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknutím na **Historie žádostí** potvrďte, že žádost byla zrušena.

## <a name="next-steps"></a>Další kroky

- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
