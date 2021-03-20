---
title: Správa přístupu vaší organizace k aplikacím & skupinám – Azure AD
description: Naučte se provádět kontrolu přístupu ke správě přístupu zabezpečení pro aplikace a skupiny vaší organizace na portálu moje aplikace.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095014"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Provedení kontroly přístupu z portálu moje aplikace

Svůj pracovní nebo školní účet můžete použít na portálu web **Moje aplikace** a provádět kontroly přístupu pro vaše aplikace a skupiny. Kontroly přístupu vám pomůžou spravovat zastaralý přístup nebo měnit požadavky na přístup a ujistit se, že se kontrolují a aktualizují.

Pokud nemáte přístup k portálu **Moje aplikace** , obraťte se na helpdesk, aby vám udělil oprávnění.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určený pro uživatele **mých aplikací** . Pokud jste správce, můžete najít další informace o tom, jak nastavit a spravovat cloudové aplikace v [dokumentaci pro správu aplikací](../manage-apps/index.yml).
>
> Pokud se vám při přihlašování pomocí osobního účet Microsoft zobrazí chyba, můžete se přihlásit pomocí názvu domény vaší organizace (například contoso.com) nebo **ID tenanta** vaší organizace od správce v jedné z následujících adres URL:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

Pokud vám správce udělil oprávnění k provádění vlastních kontrol přístupu, můžete ke správě skupin nebo aplikací přistupovat z dlaždice kontroly **přístupu** na stránce portálu **Moje aplikace** .

>[!Note]
>Pokud nevidíte dlaždici kontroly **přístupu** , znamená to, že nemáte oprávnění k provádění kontrol přístupu nebo že nemáte žádné nevyřízené recenze čekající na vaše schválení. Pokud se domníváte, že byste měli mít přístup k této dlaždici, požádejte o pomoc helpdesk.

## <a name="to-perform-your-access-reviews"></a>Provedení kontrol přístupu

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

1. Otevřete webový prohlížeč a pokračujte na https://myapps.microsoft.com nebo použijte odkaz poskytovaný vaší organizací. Například můžete být přesměrováni na přizpůsobenou stránku vaší organizace, jako je například https://myapps.microsoft.com/contoso.com .

    Zobrazí se stránka **aplikace** s informacemi o cloudových aplikacích vlastněných vaší organizací a k dispozici pro použití.

    ![Stránka aplikace na portálu moje aplikace](media/my-apps-portal/my-apps-home.png)

1. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam kontrol přístupu čekajících na vaše schválení.

    ![Stránka kontroly přístupu s nevyřízenými recenzemi přístupu pro organizaci](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. Vyberte možnost **zahájit revizi** a spusťte tak kontrolu přístupu.

5. Zkontrolujte svůj přístup a určete, zda je stále nutné.

    ![Stránka pro kontrolu přístupu se zobrazenými podrobnostmi o kontrole](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Pokud jste správce a máte povolený kontrolu přístupu vaší organizace ke skupinám a aplikacím, zobrazí se jiná stránka. Další informace o kontrole skupin nebo aplikací pro vaši organizaci najdete v tématu [Kontrola přístupu ke skupinám nebo aplikacím v rámci kontrol přístupu Azure AD](../governance/perform-access-review.md).

6. Vyberte **Ano** , pokud chcete zachovat přístup nebo **ne** pro odebrání přístupu.

    Pokud vyberete **Ano**, možná budete muset zadat odůvodnění v poli **důvod** .

    ![Stránka pro kontrolu přístupu, která zobrazuje pole důvod s ukázkovým textem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Vyberte **Odeslat**.

    Vaše kontrola přístupu je hotová a vrátíte se na portál **Moje aplikace** .

    >[!Note]
    >Přístup můžete kdykoli změnit, dokud období kontroly přístupu skončí. Když odeberete přístup k aplikaci nebo skupině, neodebere se okamžitě. K odebrání dojde po skončení období kontroly přístupu nebo po ukončení revize správcem.

## <a name="next-steps"></a>Další kroky

- [Přístup k aplikacím a jejich používání na portálu moje aplikace](my-apps-portal-end-user-access.md)
- [Změna informací o profilu](./my-account-portal-settings.md)
- [Zobrazit a aktualizovat informace související se skupinami](my-apps-portal-end-user-groups.md)