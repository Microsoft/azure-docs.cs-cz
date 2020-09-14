---
title: Kontrola přístupu k rolím Azure AD v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak zkontrolovat přístup k rolím Azure Active Directory v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd0b63e08f44167b89b6e2d491722f3708b4036
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055431"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Kontrola přístupu k rolím Azure AD v Privileged Identity Management

Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům v Azure Active Directory (AD) a dalších online služby Microsoftu, jako je Microsoft 365 nebo Microsoft Intune. Podle kroků v tomto článku můžete úspěšně zkontrolovat přiřazené role.

Pokud jste přihlášeni k roli správce, může správce privilegovaných rolí vaší organizace požádat o pravidelné potvrzení, že pro vaši úlohu stále potřebujete tuto roli. Můžete obdržet e-mail, který obsahuje odkaz, nebo můžete přejít přímo na [Azure Portal](https://portal.azure.com) a začít.

Pokud jste správce privilegovaných rolí nebo globální správce, kteří mají zájem o kontroly přístupu, získáte další podrobnosti o [tom, jak spustit kontrolu přístupu](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Přidat dlaždici řídicího panelu PIM

Pokud nemáte službu Privileged Identity Management připnuté na řídicí panel v Azure Portal, postupujte podle těchto kroků a začněte.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V pravém horním rohu Azure Portal vyberte své uživatelské jméno a vyberte organizaci Azure AD, na které budete pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schválit nebo zamítnout přístup

Když schválíte nebo odepřete přístup, stačí pouze sdělit kontrolorovi, zda tuto roli stále používáte. Vyberte **schválit** , pokud chcete zůstat v roli, nebo **zakažte** , pokud už nepotřebujete přístup. Stav se hned nemění, dokud kontrolor nepoužije výsledky.
Pomocí těchto kroků můžete najít a dokončit kontrolu přístupu:

1. Ve službě Privileged Identity Management vyberte **zkontrolovat privilegovaný přístup**. Pokud máte nějaké recenze, které čekají na přístup, zobrazí se na stránce kontroly **přístupu** Azure AD.
2. Vyberte revizi, kterou chcete dokončit.
3. Pokud jste tuto kontrolu nevytvořili, zobrazí se jako jediný uživatel v revizi. Zaškrtněte políčko vedle svého jména.
4. Vyberte buď **schválit** , nebo **Odepřít**. Do textového pole **Zadejte důvod** možná budete muset zahrnout důvod svého rozhodnutí.  
5. Zavřete okno **Kontrola rolí Azure AD** .

## <a name="next-steps"></a>Další kroky

- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
