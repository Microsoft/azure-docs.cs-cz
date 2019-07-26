---
title: Kontrola přístupu ke skupinám nebo aplikacím v recenzích přístupu – Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak zkontrolovat přístup ke členům skupiny nebo přístupu k aplikacím v Azure Active Directory kontroly přístupu.
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
ms.openlocfilehash: 3be5d6eb443d90d3413510576a9609fe43df7caa
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499618"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu ke skupinám nebo aplikacím v prohlídekch Azure AD Access

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup k skupinám a aplikacím v Azure AD a dalších online službách Microsoftu s funkcí nazvanou kontroly přístupu Azure AD.

Tento článek popisuje, jak určený kontrolor provede kontrolu přístupu pro členy skupiny nebo uživatelů, kteří mají přístup k aplikaci.

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace najdete v tématu o uživatelích, [kteří musí mít licence?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Otevřít kontrolu přístupu

Prvním krokem k provedení kontroly přístupu je vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mail od Microsoftu, který vás vyzve ke kontrole přístupu. Tady je příklad e-mailu pro kontrolu přístupu pro skupinu.

    ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/perform-access-review/access-review-email.png)

1. Kliknutím na odkaz **zahájit kontrolu** otevřete kontrolu přístupu.

Pokud e-maily nemáte, můžete najít své nedokončené kontroly přístupu pomocí následujících kroků.

1. Přihlaste se k portálu Mojeapl na [https://myapps.microsoft.com](https://myapps.microsoft.com)adrese.

    ![Seznam aplikací v portálu Mojeapl, pro které máte oprávnění](./media/perform-access-review/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam nedokončených revizí přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny](./media/perform-access-review/access-reviews-list.png)

1. Klikněte na odkaz **zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

## <a name="perform-the-access-review"></a>Provést kontrolu přístupu

Po otevření kontroly přístupu uvidíte jména uživatelů, kteří je třeba zkontrolovat.

Pokud se jedná o žádost o kontrolu vašeho vlastního přístupu, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu pro vlastní skupiny nebo aplikace](review-your-access.md).

![Otevřít kontrolu přístupu pro uživatele, kteří potřebují zkontrolovat](./media/perform-access-review/perform-access-review.png)

Existují dva způsoby, jak můžete schválit nebo odepřít přístup:

- Můžete schválit nebo odepřít přístup pro jednoho nebo více uživatelů nebo
- Můžete přijmout doporučení systému, což je nejjednodušší a nejrychlejší způsob.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Schválení nebo zamítnutí přístupu pro jednoho nebo více uživatelů

1. Projděte si seznam uživatelů a rozhodněte se, jestli chcete svůj trvalý přístup schválit nebo zamítnout.

1. Chcete-li schválit nebo odepřít přístup pro jednoho uživatele, kliknutím na řádek otevřete okno a určete akci, která má být provedena. Chcete-li schválit nebo odepřít přístup pro více uživatelů, přidejte zaškrtnutí vedle uživatelů a potom kliknutím na tlačítko **zkontrolovat X uživatele** otevřete okno, které určuje akci, která má být provedena.

1. Klikněte na **schválit** nebo **Odepřít**. Pokud si nejste jisti, můžete kliknout na **neznát**. Tím dojde k tomu, že uživatel zachová svůj přístup, ale výběr se projeví v protokolech auditu.

    ![Okno akce, které zahrnuje schválení, zamítnutí a neznalost možností](./media/perform-access-review/approve-deny.png)

1. V případě potřeby zadejte důvod do pole **důvod** .

    Správce kontroly přístupu může vyžadovat, abyste zadali důvod pro schválení pokračujícího přístupu nebo členství ve skupině.

1. Až zadáte akci, která se má provést, klikněte na **Uložit**.

    Pokud chcete změnit odpověď, vyberte řádek a aktualizujte odpověď. Můžete například schválit dříve zamítnutého uživatele nebo odepřít dříve schváleného uživatele. Odpověď můžete kdykoli změnit, dokud se neukončí kontrola přístupu.

    Pokud existuje více revidujících, je zaznamenána poslední odeslaná odpověď. Vezměte v úvahu příklad, kdy správce určí dva revidující – Alice a Bob. Alice otevře nejprve kontrolu přístupu a schválí přístup. Před ukončením kontroly Bob otevře kontrolu přístupu a odepře přístup. Poslední odpověď na zamítnutí je zaznamenána.

    > [!NOTE]
    > Pokud má uživatel odepřený přístup, neodstraní se okamžitě. Po skončení recenze dojde k jejich odebrání nebo když správce zastaví kontrolu.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Schválit nebo odepřít přístup na základě doporučení

Abychom zajistili lepší a rychlejší kontroly přístupu, poskytujeme také doporučení, která můžete přijmout jediným kliknutím. Doporučení se generují na základě přihlašovací aktivity uživatele.

1. Na modrém panelu v dolní části stránky klikněte na **přijmout doporučení**.

    ![Otevřete seznam kontroly přístupu se zobrazením tlačítka přijmout doporučení.](./media/perform-access-review/accept-recommendations.png)

    Zobrazí se souhrn doporučených akcí.

    ![Okno, které zobrazuje souhrn doporučených akcí](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknutím na **OK** přijměte doporučení.

## <a name="next-steps"></a>Další kroky

- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)
