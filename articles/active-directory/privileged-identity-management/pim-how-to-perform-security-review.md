---
title: Kontrola přístupu k rolím Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak zkontrolovat přístup k rolím Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bd548041b086eef8d788a696497163c756fd5b7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804420"
---
# <a name="review-access-to-azure-ad-roles-in-pim"></a>Kontrola přístupu k rolím Azure AD v PIM

Azure Active Directory (AD) Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům v Azure AD a dalších online služby Microsoftu, jako je například sada Office 365 nebo Microsoft Intune.  

Pokud jste přihlášeni k roli správce, může správce privilegovaných rolí vaší organizace požádat o pravidelné potvrzení, že pro vaši úlohu stále potřebujete tuto roli. Můžete obdržet e-mail, který obsahuje odkaz, nebo můžete přejít přímo na [Azure Portal](https://portal.azure.com). Postupujte podle kroků v tomto článku a proveďte si vlastní kontrolu vašich přiřazených rolí.

Pokud jste správce privilegovaných rolí nebo globální správce, kteří mají zájem o kontroly přístupu, získáte další podrobnosti o [tom, jak spustit kontrolu přístupu](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
K provedení kontroly můžete použít aplikaci Azure AD Privileged Identity Management (PIM) v [Azure Portal](https://portal.azure.com/) .  Pokud na portálu nemáte aplikaci Azure AD Privileged Identity Management, začněte pomocí těchto kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V pravém horním rohu Azure Portal vyberte své uživatelské jméno a vyberte adresář, ve kterém budete pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schválit nebo zamítnout přístup
Když schválíte nebo odepřete přístup, stačí pouze sdělit kontrolorovi, zda tuto roli stále používáte. Vyberte **schválit** , pokud chcete zůstat v roli, nebo **zakažte** , pokud už nepotřebujete přístup. Stav se hned nemění, dokud kontrolor nepoužije výsledky.
Pomocí těchto kroků můžete najít a dokončit kontrolu přístupu:

1. V aplikaci PIM vyberte **zkontrolovat privilegovaný přístup**. Pokud máte nějaké recenze, které čekají na přístup, zobrazí se v okně kontroly přístupu Azure AD.
2. Vyberte revizi, kterou chcete dokončit.
3. Pokud jste tuto kontrolu nevytvořili, zobrazí se jako jediný uživatel v revizi. Zaškrtněte políčko vedle svého jména.
4. Vyberte buď **schválit** , nebo **Odepřít**. Do textového pole **Zadejte důvod** možná budete muset zahrnout důvod svého rozhodnutí.  
5. Zavřete okno **Kontrola rolí Azure AD** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
