---
title: Proveďte kontrola přístupu k prostředkům Azure v Privileged Identity Management | Microsoft Docs
description: Tento dokument popisuje, jak provádět kontrola přístupu v PIM pro prostředky Azure, podle role prostředků.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 43245907a16019ac5396c41db036c3160359270f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699603"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Provést kontrola přístupu v PIM, podle role prostředků
Privileged Identity Management (PIM) k prostředkům Azure zjednodušuje, jak spravovat podniky privilegovaného přístupu k prostředkům v Azure. 

Pokud jste přiřazeni k roli správce privilegovaných rolí správce ve vaší organizaci může požádáni, abyste pravidelně potvrďte, že je stále nutné této role pro úlohu. Může získat e-mailu, který obsahuje odkaz, nebo můžete přejít přímo na [portál Azure](https://portal.azure.com). Postupujte podle kroků v tomto článku provést samoobslužné zkontrolujte přiřazené role.

Pokud jste správcem privilegovaných rolí zájem o recenze přístup, získat další podrobnosti o [spuštění kontrola přístupu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Můžete použít aplikaci Azure Active Directory (Azure AD) PIM v [portál Azure](https://portal.azure.com/) k provedení zkontrolovali. Pokud nemáte aplikaci na portálu, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte uživatele, název v pravém horním rohu portálu Azure a vyberte adresář, kde vám budou pracovat.
3. Vyberte **všechny služby**a použít **filtru** pro vyhledání *Azure AD Privileged Identity Management*.
4. Zkontrolujte **připnout na řídicí panel**a potom vyberte **vytvořit**. Otevře se aplikace PIM.

## <a name="approve-or-deny-access"></a>Schválí nebo zamítne přístup
Při schválit nebo zamítnout přístup, můžete se právě informuje kontrolorovi zda můžete dál používat tuto roli nebo ne. Zvolte **schválit** Pokud chcete, aby zůstali v roli, nebo **Odepřít** Pokud nepotřebujete přístup. Vaše změny stavu jenom v případě, že kontrolorovi platí výsledky.

Postupujte podle těchto kroků můžete najít a dokončit kontrolu přístupu:
1. Přejděte do aplikace Azure AD PIM.
2. Vyberte **kontrolujte přístup** okno.

   ![Snímek obrazovky PIM aplikace s okno přístup zkontrolujte vybrané](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Vyberte kontrolní, kterou chcete provést. 
4. Vyberte buď **schválit** nebo **Odepřít**. V **s polem, důvod**, možná bude potřeba přidat důvod pro vaše rozhodnutí.

   ![Snímek obrazovky kontrolní stránce s podrobnostmi o](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
