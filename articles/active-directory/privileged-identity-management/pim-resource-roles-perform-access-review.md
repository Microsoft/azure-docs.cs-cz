---
title: Jak provést kontrola přístupu pro prostředky Azure Privileged Identity Management | Microsoft Docs
description: Tento dokument popisuje, jak provést a přístup k zkontrolujte v PIM pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Provedení privileged Identity Management - role prostředků – zkontrolujte přístup
Správa privilegovaných identit pro prostředky Azure zjednodušuje, jak spravovat podniky privilegovaného přístupu k prostředkům v Azure. 

Pokud jste přiřazeni k roli správce privilegovaných rolí správce ve vaší organizaci může požádáni, abyste pravidelně potvrďte, že je stále nutné této role pro úlohu. Může získat e-mailu, který obsahuje odkaz, nebo můžete přejít přímo na [portál Azure](https://portal.azure.com). Postupujte podle kroků v tomto článku provést samoobslužné zkontrolujte přiřazené role.

Pokud jste správcem privilegovaných rolí zájem o recenze přístup, získat další podrobnosti o [spuštění kontrola přístupu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Můžete použít aplikaci Azure AD Privileged Identity Management (PIM) v [portál Azure](https://portal.azure.com/) k provedení zkontrolovali.  Pokud nemáte aplikaci Azure AD Privileged Identity Management na portálu, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte své uživatelské jméno v pravém horním rohu portálu Azure a vyberte adresář, kde vám budou pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schválí nebo zamítne přístup
Při schválit nebo zamítnout přístup, můžete se právě informuje kontrolorovi zda můžete dál používat tuto roli nebo ne. Zvolte **schválit** Pokud chcete, aby zůstali v roli, nebo **Odepřít** Pokud nepotřebujete přístup. Stav vaší nedojde ke změně hned, dokud kontrolorovi platí výsledky.
Postupujte podle těchto kroků můžete najít a dokončit kontrolu přístupu:
1. Přejděte do aplikace Azure AD Privileged Identity Management.
2. Vyberte okno Kontrola přístupu

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Vyberte, které se má provést kontrola. 
4. Vyberte buď **schválit** nebo **Odepřít**. Musíte zahrnout důvod pro vaše rozhodnutí v **zadat příslušný důvod** textové pole.

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
