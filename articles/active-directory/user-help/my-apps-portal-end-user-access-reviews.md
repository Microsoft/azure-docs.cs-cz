---
title: Provedení kontroly přístupu z portálu moje aplikace – Azure Active Directory | Microsoft Docs
description: Naučte se zobrazovat a spravovat přístup k zabezpečení pro aplikace a skupiny vaší organizace.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6681720fc7c0fe04c99bf7957be14865c663dd67
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383160"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Provedení kontroly přístupu z portálu moje aplikace

Svůj pracovní nebo školní účet můžete použít s portálem web **Moje aplikace** , abyste mohli zobrazit a spustit spoustu cloudových aplikací vaší organizace, abyste mohli aktualizovat některé z vašich informací o profilu a účtu, abyste viděli informace o **skupinách** a mohli provádět  **kontroly přístupu** pro vaše aplikace a skupiny. Pokud nemáte přístup k portálu **Moje aplikace** , musíte se obrátit na helpdesk, aby vám udělil oprávnění.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, můžete najít další informace o tom, jak nastavit a spravovat cloudové aplikace v [dokumentaci pro správu aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

Pokud vám správce udělil oprávnění k provádění vlastních kontrol přístupu, můžete ke správě skupin nebo aplikací přistupovat z dlaždice kontroly **přístupu** na stránce portálu **Moje aplikace** .

>[!Note]
>Pokud nevidíte dlaždici kontroly **přístupu** , znamená to, že nemáte oprávnění k provádění kontrol přístupu nebo že nemáte žádné nevyřízené recenze čekající na vaše schválení. Pokud se domníváte, že byste měli mít přístup k této dlaždici, požádejte o pomoc svoji technickou podporu.

### <a name="to-perform-your-access-reviews"></a>Provedení kontrol přístupu

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Otevřete webový prohlížeč a pokračujte na https://myapps.microsoft.com nebo použijte odkaz poskytovaný vaší organizací. Například můžete být přesměrováni na přizpůsobenou stránku vaší organizace, https://myapps.microsoft.com/contoso.com jako je například.

    Zobrazí se stránka **aplikace** s informacemi o cloudových aplikacích vlastněných vaší organizací a k dispozici pro použití.

    ![Stránka aplikace na portálu moje aplikace](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam kontrol přístupu čekajících na vaše schválení.

    ![Stránka kontroly přístupu s nevyřízenými recenzemi přístupu pro organizaci](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Vyberte možnost **zahájit revizi** a spusťte tak kontrolu přístupu.

5. Zkontrolujte svůj přístup a určete, zda je stále nutné.

    ![Stránka pro kontrolu přístupu se zobrazenými podrobnostmi o kontrole](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Pokud jste správce a máte povolený kontrolu přístupu vaší organizace ke skupinám a aplikacím, zobrazí se jiná stránka. Další informace o kontrole skupin nebo aplikací pro vaši organizaci najdete v tématu [Kontrola přístupu ke skupinám nebo aplikacím v rámci kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Vyberte **Ano** , pokud chcete zachovat přístup nebo **ne** pro odebrání přístupu.

    Pokud vyberete **Ano**, možná budete muset zadat odůvodnění v poli **důvod** .

    ![Stránka pro kontrolu přístupu, která zobrazuje pole důvod s ukázkovým textem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Vyberte **odeslat**.

    Vaše kontrola přístupu je hotová a vrátíte se na portál **Moje aplikace** .

    >[!Note]
    >Přístup můžete kdykoli změnit, dokud období kontroly přístupu skončí. Když odeberete přístup k aplikaci nebo skupině, neodebere se okamžitě. K odebrání dojde po skončení období kontroly přístupu nebo po ukončení revize správcem.

## <a name="next-steps"></a>Další postup

- [Přístup k aplikacím a jejich používání na portálu moje aplikace](my-apps-portal-end-user-access.md)

- [Změna informací o profilu](my-apps-portal-end-user-update-profile.md).

- [Zobrazit a aktualizovat informace související se skupinami](my-apps-portal-end-user-groups.md).
