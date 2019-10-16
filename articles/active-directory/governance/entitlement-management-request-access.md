---
title: Požádat o přístup k balíčku přístupu ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Naučte se používat portál pro přístup k vyžádání přístupu k balíčku přístupu v Azure Active Directory správě nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86ab995327522ac5dead9b3f944e0760084aedce
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389089"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Požádat o přístup k balíčku přístupu ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Díky správě opravňujících k Azure AD umožňuje balíček pro přístup jednorázovou instalaci prostředků a zásad, které automaticky spravují přístup po dobu života balíčku přístupu. 

Správce balíčků přístupu může nakonfigurovat zásady, které vyžadují schválení pro uživatele, kteří mají přístup k balíčkům přístupu. Uživatel, který potřebuje přístup k přístupovému balíčku, může odeslat žádost o získání přístupu. Tento článek popisuje, jak odeslat žádost o přístup.

## <a name="sign-in-to-the-my-access-portal"></a>Přihlášení k portálu pro přístup

Prvním krokem je přihlášení k portálu pro přístup, kde si můžete vyžádat přístup k balíčku přístupu.

**Požadovaná role:** Žadatele

1. Vyhledejte e-mail nebo zprávu z projektu nebo obchodního manažera, se kterým pracujete. E-mail by měl obsahovat odkaz na balíček pro přístup, ke kterému budete potřebovat přístup. Odkaz začíná na `myaccess`, obsahuje pomocný parametr adresáře a končí ID balíčku přístupu.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otevřete odkaz.

1. Přihlaste se k portálu přístupu.

    Ujistěte se, že používáte svůj organizační (pracovní nebo školní) účet. Pokud si nejste jistí, obraťte se na svého projektu nebo obchodní manažer.

## <a name="request-an-access-package"></a>Vyžádání balíčku pro přístup

Po nalezení balíčku přístupu na portálu pro přístup můžete odeslat žádost.

**Požadovaná role:** Žadatele

1. V seznamu vyhledejte balíček pro přístup.  V případě potřeby můžete hledat zadáním hledaného řetězce a následným výběrem filtru **název**, **katalog**nebo **prostředky** .

    ![Můj portál pro přístup – vyhledávání prostředků](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. Klikněte na značku zaškrtnutí a vyberte balíček pro přístup.

    ![Portál přístupu – přístupové balíčky](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknutím na **požádat o přístup** otevřete podokno žádosti o přístup.

1. Pokud se zobrazí pole **obchodní odůvodnění** , zadejte odůvodnění přístupu pro potřebu přístupu.

1. Pokud je **požadavek na určité období** povolený, vyberte **Ano** nebo **ne**.

1. V případě potřeby zadejte počáteční a koncové datum.

    ![Můj portál přístupu – požádat o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Po dokončení kliknutím na **Odeslat** odešlete žádost.

1. Kliknutím na **Historie žádostí** zobrazíte seznam žádostí a stav.

    Pokud přístupový balíček vyžaduje schválení, je žádost nyní ve stavu čeká na schválení.

## <a name="cancel-a-request"></a>Zrušení žádosti

Pokud odešlete žádost o přístup a žádost zůstane stále ve stavu **čeká na schválení** , můžete žádost zrušit.

**Požadovaná role:** Žadatele

1. Na portálu pro přístup na levé straně klikněte na **Historie žádostí** , abyste viděli seznam žádostí a stav.

1. Klikněte na odkaz **Zobrazit** u žádosti, kterou chcete zrušit.

1. Pokud je požadavek stále ve stavu **čekání na schválení** , můžete kliknout na **zrušit žádost** o zrušení žádosti.

    ![Portál pro přístup – zrušit požadavek](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknutím na **Historie žádostí** potvrďte, že žádost byla zrušena.

## <a name="select-a-policy"></a>Výběr zásady

Pokud požadujete přístup k balíčku přístupu, který má několik zásad, které platí, může se zobrazit výzva k výběru zásady. Správce balíčků pro přístup může například nakonfigurovat balíček přístupu se dvěma zásadami pro dvě skupiny interních zaměstnanců. První zásada může mít povolený přístup po 60 dnech a vyžaduje schválení. Druhá zásada může mít povolený přístup 2 dny a nemusí vyžadovat schválení. Pokud se setkáte s tímto scénářem, musíte vybrat zásadu, kterou chcete použít.

**Požadovaná role:** Žadatele

## <a name="next-steps"></a>Další kroky

- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
