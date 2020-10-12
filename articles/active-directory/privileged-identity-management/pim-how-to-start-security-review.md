---
title: Vytvoření kontroly přístupu rolí Azure AD v PIM – Azure AD | Microsoft Docs
description: Naučte se vytvářet kontrolu přístupu pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b3070a1296b368ea2c56038ec696942416a6fe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743877"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Vytvoření kontroly přístupu rolí Azure AD v Privileged Identity Management

Chcete-li snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup. K vytváření kontrol přístupu pro privilegované role Azure AD můžete použít Azure AD Privileged Identity Management (PIM). Můžete také nakonfigurovat opakované kontroly přístupu, ke kterým dochází automaticky.

Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu pro privilegované role Azure AD.

## <a name="prerequisites"></a>Požadavky

[Správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Otevřít recenze přístupů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role správce privilegovaných rolí.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. V části Spravovat vyberte kontroly **přístupu**a pak vyberte **Nový**.

    ![Role Azure AD – seznam kontrol přístupu zobrazuje stav všech revizí](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Spustit kontrolu přístupu

Po zadání nastavení pro kontrolu přístupu vyberte **Spustit**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam kontrol přístupu zobrazuje stav spuštěných revizí](./media/pim-how-to-start-security-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail kontrolorům krátce po zahájení kontroly. Pokud se rozhodnete Neodesílat e-maily Azure AD, nezapomeňte informovat kontrolory, které čekají na dokončení kontroly přístupu. Můžete jim Ukázat pokyny, jak [zkontrolovat přístup k rolím Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu

Průběh můžete sledovat, když kontroloři dokončí revize na stránce **Přehled** kontroly přístupu. V adresáři se nezměnila žádná přístupová práva, dokud se [Kontrola nedokončí](pim-how-to-complete-review.md).

![Stránka s přehledem kontroly přístupu, která zobrazuje podrobnosti o kontrole](./media/pim-how-to-start-security-review/access-review-overview.png)

Pokud se jedná o jednorázovou revizi, potom po uplynutí doby kontroly přístupu nebo když správce zastaví kontrolu přístupu, postupujte podle kroků v části [dokončení kontroly přístupu rolí Azure AD](pim-how-to-complete-review.md) , které vám umožní zobrazit a použít výsledky.  

Pokud chcete spravovat řadu kontrol přístupu, přejděte na kontrolu přístupu a v části naplánované recenze Najděte nadcházející výskyty a upravte koncové datum nebo přidejte nebo odeberte kontrolory odpovídajícím způsobem.

Na základě vašich výběrů v **nastavení po dokončení**se spustí automatické použití po koncovém datu revize nebo při ručním zastavení kontroly. Stav kontroly se změní ze **dokončených** do mezistavů, jako je **použití** a nakonec na stav **použito**. Měli byste očekávat, že v několika minutách se v případě potřeby odeberou Zakázaní uživatelé z rolí.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k rolím Azure AD](pim-how-to-perform-security-review.md)
- [Dokončení kontroly přístupu pro role Azure AD](pim-how-to-complete-review.md)
- [Vytvoření kontroly přístupu pro role prostředků Azure](pim-resource-roles-start-access-review.md)
