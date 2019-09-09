---
title: Kontrola přístupu k rolím prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak zkontrolovat přístup k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804099"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Kontrola přístupu k rolím prostředků Azure v PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům v Azure. 

Pokud jste přihlášeni k roli správce, může správce privilegovaných rolí vaší organizace požádat o pravidelné potvrzení, že pro vaši úlohu stále potřebujete tuto roli. Můžete obdržet e-mail, který obsahuje odkaz, nebo můžete přejít přímo na [Azure Portal](https://portal.azure.com). Postupujte podle kroků v tomto článku a proveďte si vlastní kontrolu vašich přiřazených rolí.

Pokud jste správce privilegovaných rolí, který zajímá kontroly přístupu, získáte další podrobnosti o [tom, jak spustit kontrolu přístupu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
K provedení kontroly můžete použít aplikaci Azure Active Directory (Azure AD) PIM v [Azure Portal](https://portal.azure.com/) . Pokud nemáte aplikaci na portálu, začněte podle těchto kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V pravém horním rohu Azure Portal vyberte své uživatelské jméno a vyberte adresář, ve kterém budete pracovat.
3. Vyberte **všechny služby**a pomocí pole **Filtr** vyhledejte *Azure AD Privileged Identity Management*.
4. Zaškrtněte políčko **Připnout na řídicí panel**a potom vyberte **vytvořit**. Otevře se aplikace PIM.

## <a name="approve-or-deny-access"></a>Schválit nebo zamítnout přístup
Když schválíte nebo odepřete přístup, stačí pouze sdělit kontrolorovi, zda tuto roli stále používáte. Vyberte **schválit** , pokud chcete zůstat v roli, nebo **zakažte** , pokud už nepotřebujete přístup. Stav se změní pouze v případě, že Kontrolor použije výsledky.

Pomocí těchto kroků můžete najít a dokončit kontrolu přístupu:
1. Přejděte k aplikaci Azure AD PIM.
2. Vyberte okno **Kontrola přístupu** .

   ![Snímek obrazovky PIM aplikace s vybraným oknem kontrola přístupu](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Vyberte revizi, kterou chcete dokončit. 
4. Vyberte buď **schválit** , nebo **Odepřít**. V poli **Zadejte důvod**možná budete muset zahrnout důvod vašeho rozhodnutí.

   ![Snímek obrazovky se stránkou s podrobnostmi o kontrole](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Další kroky

- [Provedení kontroly přístupu mých rolí Azure AD v PIM](pim-how-to-perform-security-review.md)
