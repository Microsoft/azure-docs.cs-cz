---
title: Správa přístupu vaší organizace k aplikacím & skupin – Azure AD
description: Přečtěte si, jak provést kontrolu přístupu ke správě přístupu k zabezpečení aplikací a skupin vaší organizace z portálu Moje aplikace.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062370"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Provedení kontroly přístupu z portálu Moje aplikace

Pomocí pracovního nebo školního účtu můžete pomocí webového portálu **Moje aplikace** provádět kontroly přístupu k aplikacím a skupinám. Kontroly přístupu vám pomohou spravovat zastaralý přístup nebo změnit požadavky na přístup a zajistit jejich kontrolu a aktualizaci.

Pokud nemáte přístup k portálu **Moje aplikace,** požádejte o povolení na helpdesku.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určen pro uživatele **služby Moje aplikace.** Pokud jste správce, další informace o nastavení a správě cloudových aplikací najdete v [dokumentaci ke správě aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

Pokud vám správce udělil oprávnění k provádění vlastních kontrol přístupu, můžete spravovat přístup ke skupinám nebo aplikacím z dlaždice **Kontroly aplikace Access** na stránce Portál **mých aplikací.**

>[!Note]
>Pokud dlaždici **Kontroly aplikace Access** nevidíte, znamená to, že nemáte oprávnění k provádění kontrol přístupu nebo že nemáte žádné nevyřízené recenze čekající na schválení. Pokud si myslíte, že byste měli mít přístup k dlaždici, požádejte o pomoc helpdesk.

## <a name="to-perform-your-access-reviews"></a>Provedení kontrol přístupu

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Otevřete webový prohlížeč https://myapps.microsoft.coma přejděte na stránku nebo použijte odkaz poskytnutý vaší organizací. Můžete být například přesměrováni na přizpůsobenou stránku https://myapps.microsoft.com/contoso.compro vaši organizaci, například .

    Zobrazí se stránka **Aplikace,** která zobrazuje všechny cloudové aplikace vlastněné vaší organizací a dostupné k použití.

    ![Stránka Aplikace na portálu Moje aplikace](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Výběrem dlaždice **Kontroly aplikace Access** zobrazíte seznam kontrol přístupu čekajících na vaše schválení.

    ![Stránka Access reviews s čekajícími recenzemi přístupu pro organizaci](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Chcete-li zahájit kontrolu přístupu, vyberte **možnost Zahájit kontrolu.**

5. Zkontrolujte přístup a zjistěte, zda je to stále nutné.

    ![Přístup k recenzi stránky, zobrazující podrobnosti recenze](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Pokud jste správce a máte povoleno kontrolovat přístup vaší organizace ke skupinám a aplikacím, zobrazí se vám jiná stránka. Další informace o kontrole skupin nebo aplikací pro vaši organizaci najdete [v tématu Kontrola přístupu ke skupinám nebo aplikacím v tématu Recenze Přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Chcete-li zachovat přístup, vyberte **ano,** chcete-li přístup odebrat, nebo **ne.**

    Pokud vyberete **Ano**, bude pravděpodobně nutné zadat zarovnání do pole **Důvod.**

    ![Stránka kontroly přístupu s polem Důvod s ukázkovým textem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Vyberte **Odeslat**.

    Vaše kontrola přístupu je dokončena a vrátíte se na portál **Moje aplikace.**

    >[!Note]
    >Přístup můžete kdykoli změnit, dokud období kontroly přístupu neskončí. Pokud odeberete přístup k aplikaci nebo skupině, není okamžitě odebrán. K odebrání dochází, když končí období kontroly přístupu nebo když správce ukončí kontrolu.

## <a name="next-steps"></a>Další kroky

- [Přístup k aplikacím a jejich používání na portálu Moje aplikace](my-apps-portal-end-user-access.md)
- [Změna informací o profilu](my-apps-portal-end-user-update-profile.md)
- [Zobrazení a aktualizace informací týkajících se skupin](my-apps-portal-end-user-groups.md)
