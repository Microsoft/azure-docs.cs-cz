---
title: Kontrola přístupu ke skupinám & aplikacím v recenzích přístupu – Azure AD
description: Přečtěte si, jak zkontrolovat vlastní přístup ke skupinám nebo aplikacím v recenzích přístupu ke službě Azure Active Directory.
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
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422399"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu ke skupinám nebo aplikacím v recenzích přístupu azure ad

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup ke skupinám nebo aplikacím ve službě Azure AD a dalších službách Microsoft Online Services pomocí funkce s názvem Kontroly přístupu azure ad.

Tento článek popisuje, jak zkontrolovat vlastní přístup ke skupině nebo aplikaci.

## <a name="open-the-access-review"></a>Otevření kontroly přístupu

Prvním krokem k provedení kontroly přístupu je vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mail od microsoftu, který vás požádá o kontrolu přístupu. Zde je příklad e-mailu pro kontrolu vašeho přístupu ke skupině.

    ![Příklad e-mailu od microsoftu pro kontrolu přístupu ke skupině](./media/review-your-access/access-review-email.png)

1. Kliknutím na odkaz **Zkontrolovat přístup** otevřete kontrolu přístupu.

Pokud e-mail nemáte, můžete pomocí těchto kroků najít nevyřízené kontroly přístupu.

1. Přihlaste se k [https://myapps.microsoft.com](https://myapps.microsoft.com)portálu MyApps na adrese .

    ![Portál MyApps uvádí aplikace, ke kterým máte oprávnění](./media/review-your-access/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Na pravé straně stránky klikněte na dlaždici **Kontroly aplikace Access** a zobcete seznam nevyřízených kontrol přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam nevyřízených kontrol přístupu pro vaše aplikace a skupiny](./media/review-your-access/access-reviews-list.png)

1. Klikněte na odkaz **Zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

## <a name="perform-the-access-review"></a>Provedení kontroly přístupu

Po otevření kontroly přístupu se vám přístup zobrazí.

1. Zkontrolujte přístup a rozhodněte se, zda stále potřebujete přístup.

    Pokud je žádost o kontrolu přístupu pro ostatní, stránka bude vypadat jinak. Další informace naleznete v [tématu Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md).

    ![Kontrola otevřeného přístupu s dotazem, zda stále potřebujete přístup ke skupině](./media/review-your-access/perform-access-review.png)

1. Chcete-li zachovat přístup, klepněte na tlačítko **Ano** nebo klepnutím na **tlačítko Ne** přístup odeberte.

1. Klepnete-li na tlačítko **Ano**, bude pravděpodobně nutné zadat odůvodnění v poli **Důvod.**

    ![Dokončená kontrola přístupu s dotazem, zda stále potřebujete přístup ke skupině](./media/review-your-access/perform-access-review-submit.png)

1. Klepněte na **tlačítko Odeslat**.

    Váš výběr se odeslal a vrátili jste se na portál MyApps.

    Pokud chcete změnit odpověď, znovu otevřete stránku s recenzemi přístupu a aktualizujte odpověď. Odpověď můžete kdykoli změnit, dokud nebude kontrola přístupu ukončena.

    > [!NOTE]
    > Pokud jste uvedli, že již nepotřebujete přístup, nejste okamžitě odebráni. Po ukončení kontroly nebo po zastavení kontroly správcem budete odebráni.

## <a name="next-steps"></a>Další kroky

- [Vyplnění kontroly přístupu skupin nebo aplikací](complete-access-review.md)
