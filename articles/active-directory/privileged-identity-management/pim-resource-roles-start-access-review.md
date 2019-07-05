---
title: Vytvoření kontroly přístupu z role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit kontrolu přístupu z role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0067bd6dc2f47c5460220295d486910d9195782d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476241"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Vytvoření kontroly přístupu z role prostředků Azure v PIM

Přístup k privilegovanému prostředku Azure role pro zaměstnance mění v průběhu času. Pokud chcete snížit riziko spojené s přiřazení rolí zastaralé, měli byste pravidelně kontrolovat přístup. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete použít k vytvoření kontroly přístupu pro privilegované role prostředků Azure. Můžete také nakonfigurovat opakování kontroly přístupu, které probíhá automaticky.

Tento článek popisuje, jak vytvořit jeden nebo více kontroly přístupu pro role privilegovanému prostředku Azure.

## <a name="prerequisites"></a>Požadavky

- [Správce privilegované Role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Kontroly přístupu otevřít

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem role správce privilegovaných rolí.

1. Otevřít **Azure AD Privileged Identity Management**.

1. V nabídce vlevo klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa, klikněte na tlačítko **kontrol přístupu**.

    ![Prostředky Azure – kontroly seznamu, který ukazuje stav všech kontrol přístupu](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Zahájení kontroly přístupu

Po zadání nastavení pro kontroly přístupu, klikněte na tlačítko **Start**. Kontroly přístupu se zobrazí v seznamu se indikátor stavu.

![Kontroly seznamu, který ukazuje stav začít kontrolu přístupu](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail revidující krátce po spuštění kontroly. Pokud se rozhodnete odeslat e-mailu se službou Azure AD, nezapomeňte informovat revidující, které čeká na všesměrově jejich dokončení kontroly přístupu. Je možné zobrazit pokyny k [kontrola přístupu pro role prostředků Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu.

Průběh můžete sledovat, jak recenzenti dokončí své recenze **přehled** stránky kontroly přístupu. Žádné přístupová práva jsou změněny v adresáři, dokud [se kontrola dokončí](pim-resource-roles-complete-access-review.md).

![Stránka s přehledem zobrazení podrobností revize kontroly přístupu](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Pokud je to jednorázové kontroly, po období kontroly přístupu je nad nebo správce zastavení kontroly přístupu, postupujte podle pokynů v [dokončení kontroly přístupu z role prostředků Azure](pim-resource-roles-complete-access-review.md) chcete zobrazit a použít výsledky.  

Ke správě řadu přístup kontroly, přejděte na kontrolu přístupu a budete najít připravované výskyty v naplánované kontroly a upravit koncové datum nebo přidání nebo odebrání revidujících odpovídajícím způsobem.

Na základě vašeho výběru v **nastavení činností po dokončení**, se automaticky použít se spustí po koncové datum kontrola nebo když ručně zastavte kontrolu. Stav kontroly se změní z **dokončeno** prostřednictvím průběžných stavů například **použití** a nakonec do stavu **použito**. Měli byste očekávat zobrazíte zamítnutým uživatelům, pokud existuje, se odebrala role za pár minut.

## <a name="next-steps"></a>Další postup

- [Kontrola přístupu pro role prostředků Azure](pim-resource-roles-perform-access-review.md)
- [Dokončení kontroly přístupu z role prostředků Azure](pim-resource-roles-complete-access-review.md)
- [Vytvoření kontroly přístupu Azure AD rolí](pim-how-to-start-security-review.md)
