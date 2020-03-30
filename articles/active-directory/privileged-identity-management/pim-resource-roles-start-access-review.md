---
title: Vytvoření kontroly přístupu rolí prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit kontrolu přístupu rolí prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847070"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Vytvoření kontroly přístupu rolí prostředků Azure ve správě privilegovaných identit

Přístup k privilegovaným rolím prostředků Azure pro zaměstnance se v průběhu času mění. Chcete-li snížit riziko spojené se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup. Pomocí azure active directory (Azure AD) privileged identity management (PIM) můžete vytvořit kontroly přístupu pro privilegované role prostředků Azure. Můžete také nakonfigurovat opakované kontroly přístupu, ke kterým dochází automaticky.

Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu pro privilegované role prostředků Azure.

## <a name="prerequisites"></a>Požadavky

[Správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Otevřené kontroly přístupu

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je členem role správce privilegované role.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. V levé nabídce vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa vyberte **možnost Access reviews**.

    ![Prostředky Azure – seznam recenzí aplikace Access zobrazující stav všech recenzí](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Spuštění kontroly přístupu

Po zadání nastavení kontroly přístupu klepněte na tlačítko **Start**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam recenzí aplikace Access zobrazující stav zahájené kontroly](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD odešle e-mail recenzentům krátce po spuštění kontroly. Pokud se rozhodnete, že nemáte Azure AD odeslat e-mail, nezapomeňte informovat recenzenty, že kontrola přístupu čeká na jejich dokončení. Můžete jim zobrazit pokyny, jak [zkontrolovat přístup k rolím prostředků Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu

Průběh můžete sledovat, jak recenzenti dokončují své recenze, na stránce **Přehled** kontroly přístupu. Dokud nebude [kontrola dokončena,](pim-resource-roles-complete-access-review.md)nebudou v adresáři změněna žádná přístupová práva .

![Stránka přehledu recenzí aplikace Access zobrazující podrobnosti recenze](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Pokud se jedná o jednorázovou kontrolu, pak po uplynutí období kontroly přístupu nebo po zastavení kontroly přístupu postupujte podle pokynů v [tématu Dokončení kontroly přístupu rolí prostředků Azure](pim-resource-roles-complete-access-review.md) zobrazíte a použijete výsledky.  

Chcete-li spravovat řadu kontrol přístupu, přejděte k recenzi přístupu a nadcházející výskyty najdete v naplánovaných recenzích a upravte koncové datum nebo odpovídajícím způsobem přidejte nebo odeberte recenzenty.

Na základě vašeho výběru v **nastavení Po dokončení**bude automatické použití provedeno po datu ukončení recenze nebo při ručním zastavení kontroly. Stav revize se změní z **Dokončeno** do zprostředkujících stavů, jako je **použití** a nakonec do stavu **Použité**. Měli byste očekávat, že odepření uživatelé, pokud existuje, jsou odebrány z rolí během několika minut.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k rolím prostředků Azure](pim-resource-roles-perform-access-review.md)
- [Dokončení kontroly přístupu rolí prostředků Azure](pim-resource-roles-complete-access-review.md)
- [Vytvoření kontroly přístupu rolí Azure AD](pim-how-to-start-security-review.md)
