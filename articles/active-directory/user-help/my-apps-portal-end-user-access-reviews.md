---
title: Provádění kontroly přístupu na portálu Moje aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit a spravovat přístup k zabezpečení pro skupiny a aplikace vaší organizace.
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
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482845"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Provádění kontroly přístupu na portálu Moje aplikace
Můžete použít pracovní nebo školní účet s webovou **Moje aplikace** portál, zobrazit a spustit řadu aplikace vaší organizace založené na cloudu, chcete-li aktualizovat některé z vašeho profilu a účtu informace zobrazíte vaše **skupiny** informace a k provádění **kontrol přístupu** vašich aplikací a skupin. Pokud nemáte přístup k **Moje aplikace** portálu, musíte požádat o Helpdesk oprávnění.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o tom, jak nastavit a spravovat vaše cloudové aplikace v [aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Správa kontroly přístupu
Pokud váš správce udělil oprávnění k provedení vlastní kontroly přístupu, můžete spravovat přístup skupiny nebo aplikace z **kontrol přístupu** na dlaždici **Moje aplikace** stránky portálu.

>[!Note]
>Pokud se nezobrazí **kontrol přístupu** dlaždici je buď znamená, že nemáte oprávnění k provedení kontroly přístupu, nebo že nemáte žádné čekající revize čekající na vaše schválení. Pokud se domníváte, že byste měli mít přístup k dlaždici, obraťte se na vaše oddělení technické podpory.

### <a name="to-perform-your-access-reviews"></a>K provedení vaší kontroly přístupu

1.  Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2.  Otevřete webový prohlížeč a přejděte na https://myapps.microsoft.com, nebo použijte odkaz, poskytnuté vaší organizací. Například je může přesměrováni na upravené stránky pro vaši organizaci, jako https://myapps.microsoft.com/contoso.com.

    **Aplikace** vlastněné vaší organizací a k dispozici pro použití se zobrazí stránka zobrazuje všechny cloudové aplikace.

    ![Stránky aplikací na portálu Moje aplikace](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Vyberte **kontrol přístupu** dlaždici zobrazíte seznam přístup revize čekající na vaše schválení.

    ![Stránka s probíhající kontroly přístupu pro organizaci kontroly přístupu](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Vyberte **začít kontrolu** spustit kontrolu přístupu.

5. Kontrolovat přístup a zjistit, zda je zapotřebí.

    ![Přístup kontrolní stránce s podrobnostmi revize](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Pokud jste správce a může kontrolovat přístup vaší organizace do skupin a aplikací, zobrazí se vám na jiné stránce. Další informace týkající se kontroly skupiny nebo aplikace pro vaši organizaci najdete v tématu [kontrolovat přístup skupinám nebo aplikacím v kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Vyberte **Ano** zajistit přístup nebo **ne** aby odebral váš přístup.

    Pokud vyberete **Ano**, možná budete muset zadat odůvodnění v **důvod** pole.

    ![Zkontrolovat přístup stránky zobrazující pole Důvod s ukázkou textu](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Vyberte **odeslat**.

    Dokončení kontrolu přístupu a vy se vrátíte do **Moje aplikace** portálu.

    >[!Note]
    >Můžete změnit váš přístup kdykoli až do skončení období zkontrolujte přístup. Pokud odeberete váš přístup k aplikaci nebo skupiny, není okamžitě odstraněna. Odebrání se stane po skončení doby zkontrolujte přístup, nebo když správce zavře revizi. 

## <a name="next-steps"></a>Další postup

- [Přístup a používání aplikací na portálu Moje aplikace](my-apps-portal-end-user-access.md).

- [Změnit informace z vašeho profilu](my-apps-portal-end-user-update-profile.md).

- [Zobrazit a aktualizovat vaše informace týkající se skupiny](my-apps-portal-end-user-groups.md).