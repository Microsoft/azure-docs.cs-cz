---
title: Provádění kontroly přístupu sady Moje role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak k provádění kontroly přístupu z role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a8ef3d0ff602ce4580755db0ada97fcf7cdbbc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208227"
---
# <a name="perform-an-access-review-of-my-azure-resource-roles-in-pim"></a>Provádění kontroly přístupu sady Moje role prostředků Azure v PIM
Privileged Identity Management (PIM) pro prostředky Azure zjednodušuje, jak podniky správy privilegovaného přístupu k prostředkům v Azure. 

Pokud máte přiřazenou roli správce privilegovaných rolí správce ve vaší organizaci může vás vyzve k pravidelně potvrzení pro vaše úloha stále potřebovat danou roli. Může se zobrazit e-mailu, který obsahuje odkaz, nebo můžete přejít přímo na [webu Azure portal](https://portal.azure.com). Postupujte podle kroků v tomto článku provádět svým přezkoumat přiřazených rolí.

Pokud jste správce privilegovaných rolí, odkud chtějí kontroly přístupu, získáte další podrobnosti najdete v [zahájení kontroly přístupu](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Můžete použít aplikaci Azure Active Directory (Azure AD) PIM [webu Azure portal](https://portal.azure.com/) provádět revizi. Pokud nemáte aplikaci na portálu, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte vaše uživatelské jméno v pravém horním rohu webu Azure portal a vyberte adresář, kde se dozvíte, jak můžete pracovat.
3. Vyberte **všechny služby**a použít **filtr** pole pro hledání *Azure AD Privileged Identity Management*.
4. Zkontrolujte **připnout na řídicí panel**a pak vyberte **vytvořit**. Otevře se aplikace PIM.

## <a name="approve-or-deny-access"></a>Schvalte nebo zamítněte přístup
Pokud, schvalte nebo zamítněte přístup, je právě tím rozdílem, revidující, zda stále použít tuto roli nebo ne. Zvolte **schválit** Pokud budete chtít zůstat v roli, nebo **Odepřít** Pokud už nepotřebujete přístup. Změny stavu jenom v případě, že revidující se vztahuje výsledky.

Postupujte podle těchto kroků můžete najít a dokončení kontroly přístupu:
1. Přejděte do aplikace Azure AD PIM.
2. Vyberte **kontrolovat přístup** okno.

   ![PIM – snímek obrazovky aplikace s vybrané okna kontroly přístupu](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Vyberte revizi, kterou chcete provést. 
4. Zvolte buď **schválit** nebo **Odepřít**. V **poskytují pole důvod**, možná budete muset zahrnout důvod svého rozhodnutí.

   ![Přezkoumání snímku obrazovky podrobností stránky](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>Další postup

- [Provedení kontroly přístupu rolí adresáře Azure AD v nástroji PIM](pim-how-to-perform-security-review.md)
