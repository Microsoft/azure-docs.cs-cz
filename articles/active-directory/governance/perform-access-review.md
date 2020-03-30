---
title: Kontrola přístupu ke skupinám & aplikacím v rámci kontrol přístupu – Azure AD
description: Přečtěte si, jak zkontrolovat přístup členů skupiny nebo přístupu k aplikacím v recenzích přístupu ke službě Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128457"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu ke skupinám a aplikacím v recenzích přístupu Azure AD

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup ke skupinám a aplikacím ve službě Azure AD a dalších službách Microsoft Online Services pomocí funkce s názvem Kontroly přístupu azure ad.

Tento článek popisuje, jak určený recenzent provádí kontrolu přístupu pro členy skupiny nebo uživatele s přístupem k aplikaci.

## <a name="open-the-access-review"></a>Otevření kontroly přístupu

Prvním krokem k provedení kontroly přístupu je vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mail od microsoftu, který vás požádá o kontrolu přístupu. Zde je příklad e-mailu pro kontrolu přístupu pro skupinu.

    ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/perform-access-review/access-review-email.png)

1. Kliknutím na odkaz **Zahájit recenzi** otevřete kontrolu přístupu.

Pokud e-mail nemáte, můžete pomocí těchto kroků najít nevyřízené kontroly přístupu.

1. Přihlaste se k [https://myapps.microsoft.com](https://myapps.microsoft.com)portálu MyApps na adrese .

    ![Portál MyApps uvádí aplikace, ke kterým máte oprávnění](./media/perform-access-review/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Kliknutím na dlaždici **Kontroly aplikace Access** zobrazíte seznam nevyřízených kontrol přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny](./media/perform-access-review/access-reviews-list.png)

1. Klikněte na odkaz **Zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

## <a name="perform-the-access-review"></a>Provedení kontroly přístupu

Po otevření kontroly přístupu se zobrazí jména uživatelů, kteří je třeba zkontrolovat.

Pokud je žádost o kontrolu vlastního přístupu, bude stránka vypadat jinak. Další informace naleznete [v tématu Kontrola přístupu ke skupinám nebo aplikacím](review-your-access.md).

![Kontrola otevřeného přístupu se seznamem uživatelů, kteří potřebují kontrolu](./media/perform-access-review/perform-access-review.png)

Přístup můžete schválit nebo odepřít dvěma způsoby:

- Můžete schválit nebo odepřít přístup jednomu nebo více uživatelům, nebo
- Můžete přijmout systémová doporučení, což je nejjednodušší a nejrychlejší způsob.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Schválení nebo odepření přístupu pro jednoho nebo více uživatelů

1. Zkontrolujte seznam uživatelů a rozhodněte se, zda mají schválit nebo odepřít jejich další přístup.

1. Chcete-li schválit nebo odepřít přístup pro jednoho uživatele, klepnutím na řádek otevřete okno a určete akci, kterou chcete provést. Chcete-li schválit nebo odepřít přístup více uživatelům, přidejte vedle uživatelů zaškrtnutí a klepnutím na tlačítko **Zkontrolovat uživatele X** otevřete okno a určete akci, kterou chcete provést.

1. Klepněte na **tlačítko Schválit** nebo **Odepřít**. Pokud si nejste jisti, můžete klepnout na tlačítko **Nevědět**. Pokud tak učiníte, bude mít uživatel zachovat jejich přístup, ale výběr se projeví v protokolech auditu.

    ![Okno akce, které obsahuje možnosti Schválit, Odepřít a Nevědět](./media/perform-access-review/approve-deny.png)

1. V případě potřeby zadejte důvod do pole **Důvod.**

    Správce kontroly přístupu může vyžadovat zadání důvodu pro schválení trvalého přístupu nebo členství ve skupině.

1. Po zadání akce, kterou chcete provést, klepněte na tlačítko **Uložit**.

    Pokud chcete změnit odpověď, vyberte řádek a aktualizujte odpověď. Můžete například schválit dříve odepřeného uživatele nebo dříve schváleného uživatele. Odpověď můžete kdykoli změnit, dokud nebude kontrola přístupu ukončena.

    Pokud existuje více recenzentů, zaznamená se poslední odeslaná odpověď. Vezměme si příklad, kdy správce určí dva recenzenty – Alici a Boba. Alice nejprve otevře kontrolu přístupu a přístup schválí. Před ukončením kontroly Bob otevře kontrolu přístupu a odepře přístup. Poslední odpověď na zamítnutí je to, co je zaznamenáno.

    > [!NOTE]
    > Pokud je uživateli odepřen přístup, nejsou okamžitě odebrány. Jsou odebrány po ukončení kontroly nebo po zastavení kontroly správcem.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Schválení nebo odepření přístupu na základě doporučení

Abychom vám usnadnili a urychlili přístup k recenzím, poskytujeme také doporučení, která můžete přijmout jediným kliknutím. Doporučení jsou generovány na základě aktivity přihlášení uživatele.

1. Na modrém panelu v dolní části stránky klikněte na **Přijmout doporučení**.

    ![Výpis kontroly otevřeného přístupu s tlačítkem Přijmout doporučení](./media/perform-access-review/accept-recommendations.png)

    Zobrazí se souhrn doporučených akcí.

    ![Okno, které zobrazuje souhrn doporučených akcí](./media/perform-access-review/accept-recommendations-summary.png)

1. Chcete-li doporučení přijmout, klepněte na tlačítko **Ok.**

## <a name="next-steps"></a>Další kroky

- [Vyplnění kontroly přístupu skupin nebo aplikací](complete-access-review.md)
