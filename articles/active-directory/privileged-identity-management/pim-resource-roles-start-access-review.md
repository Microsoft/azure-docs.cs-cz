---
title: Vytvoření kontroly přístupu pro role prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Naučte se vytvářet kontrolu přístupu k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/09/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4276b48584ecbad91794de58abafd7e3367f6877
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564029"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management

Potřeba přístup k privilegovaným rolím prostředků Azure, které zaměstnanci mění v čase. Chcete-li snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup. K vytvoření kontrol přístupu pro privilegovaný přístup k rolím prostředků Azure můžete použít Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Můžete také nakonfigurovat opakované kontroly přístupu, ke kterým dochází automaticky. Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu.

## <a name="prerequisite-role"></a>Požadovaná role

 Chcete-li vytvořit kontroly přístupu, musíte být přiřazeni k roli [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) k roli Azure pro daný prostředek.

## <a name="open-access-reviews"></a>Otevřít recenze přístupů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je přiřazený k jedné z požadovaných rolí.

1. Otevřete **Azure AD Privileged Identity Management**.

1. V nabídce vlevo vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, například předplatné.

1. V části Spravovat vyberte **recenze přístupů**.

    ![Prostředky Azure – seznam kontrol přístupu zobrazuje stav všech revizí](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Spustit kontrolu přístupu

Po zadání nastavení pro kontrolu přístupu klikněte na **Spustit**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam kontrol přístupu zobrazuje stav zahájené Revize](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail kontrolorům krátce po zahájení kontroly. Pokud se rozhodnete Neodesílat e-maily Azure AD, nezapomeňte informovat kontrolory, které čekají na dokončení kontroly přístupu. Můžete jim Ukázat pokyny, jak [zkontrolovat přístup k rolím prostředků Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu

Průběh můžete sledovat, když kontroloři dokončí revize na stránce **Přehled** kontroly přístupu. V adresáři se nezměnila žádná přístupová práva, dokud se [Kontrola nedokončí](pim-resource-roles-complete-access-review.md).

![Stránka s přehledem kontroly přístupu, která zobrazuje podrobnosti o kontrole](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Pokud se jedná o jednorázovou revizi, potom po uplynutí doby kontroly přístupu nebo když správce zastaví kontrolu přístupu, postupujte podle kroků v části [dokončení kontroly přístupu k rolím prostředků Azure](pim-resource-roles-complete-access-review.md) , aby bylo možné zobrazit a použít výsledky.  

Pokud chcete spravovat řadu kontrol přístupu, přejděte na kontrolu přístupu a v části naplánované recenze Najděte nadcházející výskyty a upravte koncové datum nebo přidejte nebo odeberte kontrolory odpovídajícím způsobem.

Na základě vašich výběrů v **nastavení po dokončení** se spustí automatické použití po koncovém datu revize nebo při ručním zastavení kontroly. Stav kontroly se změní ze **dokončených** do mezistavů, jako je **použití** a nakonec na stav **použito**. Měli byste očekávat, že v několika minutách se v případě potřeby odeberou Zakázaní uživatelé z rolí.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k rolím prostředků Azure](pim-resource-roles-perform-access-review.md)
- [Dokončení kontroly přístupu pro role prostředků Azure](pim-resource-roles-complete-access-review.md)
- [Vytvoření kontroly přístupu pro role Azure AD](pim-how-to-start-security-review.md)
