---
title: Kontrola přístupu pro vlastní skupiny nebo aplikace v recenzích přístupu – Azure Active Directory | Microsoft Docs
description: Naučte se kontrolovat vlastní přístup k skupinám nebo aplikacím v Azure Active Directory kontroly přístupu.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b48e035476e5381104b442f87dcac03ac11778
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499677"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu pro vlastní skupiny nebo aplikace v prohlídekch služby Azure AD

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup ke skupinám nebo aplikacím v Azure AD a dalších online službách Microsoftu s funkcí nazvanou kontroly přístupu Azure AD.

Tento článek popisuje, jak zkontrolovat vlastní přístup ke skupině nebo aplikaci.

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace najdete v tématu o uživatelích, [kteří musí mít licence?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Otevřít kontrolu přístupu

Prvním krokem k provedení kontroly přístupu je vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mail od Microsoftu, který vás vyzve ke kontrole přístupu. Tady je příklad e-mailu pro kontrolu přístupu ke skupině.

    ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/review-your-access/access-review-email.png)

1. Kliknutím na odkaz **zkontrolovat přístup** otevřete kontrolu přístupu.

Pokud e-maily nemáte, můžete najít své nedokončené kontroly přístupu pomocí následujících kroků.

1. Přihlaste se k portálu Mojeapl na [https://myapps.microsoft.com](https://myapps.microsoft.com)adrese.

    ![Seznam aplikací v portálu Mojeapl, pro které máte oprávnění](./media/review-your-access/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Na pravé straně stránky klikněte na dlaždici kontroly **přístupu** , abyste viděli seznam nedokončených kontrol přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam nevyřízených kontrol přístupu pro vaše aplikace a skupiny](./media/review-your-access/access-reviews-list.png)

1. Klikněte na odkaz **zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

## <a name="perform-the-access-review"></a>Provést kontrolu přístupu

Po otevření kontroly přístupu uvidíte svůj přístup.

1. Zkontrolujte svůj přístup a rozhodněte se, jestli stále potřebujete přístup.

    Pokud je žádost o kontrolu přístupu pro ostatní, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md).

    ![Otevřete kontrolu přístupu s dotazem, jestli stále potřebujete přístup ke skupině.](./media/review-your-access/perform-access-review.png)

1. Kliknutím na **Ano** zachováte přístup, nebo kliknutím na **ne** odeberte přístup.

1. Pokud kliknete na **Ano**, možná budete muset zadat odůvodnění v poli **důvod** .

    ![Dokončila se kontrola přístupu s dotazem, jestli stále potřebujete přístup ke skupině.](./media/review-your-access/perform-access-review-submit.png)

1. Klikněte na **Submit** (Odeslat).

    Váš výběr se odešle a vrátíte se na portál MyApp.

    Pokud chcete změnit odpověď, otevřete znovu stránku kontroly přístupu a aktualizujte svou odpověď. Odpověď můžete kdykoli změnit, dokud se neukončí kontrola přístupu.

    > [!NOTE]
    > Pokud jste označili, že už nepotřebujete přístup, neodebere se okamžitě. Po ukončení recenze dojde k odebrání nebo když správce zastaví kontrolu.

## <a name="next-steps"></a>Další postup

- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)
