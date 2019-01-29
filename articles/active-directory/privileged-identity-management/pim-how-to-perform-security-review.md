---
title: Provádění kontroly přístupu sady Moje role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak k provádění kontroly přístupu vaší rolí adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f80eadf15148865f62e2d0f51e2e9d96ac4d147a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190518"
---
# <a name="perform-an-access-review-of-my-azure-ad-directory-roles-in-pim"></a>Provádění kontroly přístupu sady Moje role adresáře Azure AD v PIM
Azure Active Directory (AD) Privileged Identity Management zjednodušuje, jak podniky spravovat privilegovaný přístup k prostředkům v Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.  

Pokud máte přiřazenou roli správce privilegovaných rolí správce ve vaší organizaci může vás vyzve k pravidelně potvrzení pro vaše úloha stále potřebovat danou roli. Může se zobrazit e-mailu, který obsahuje odkaz, nebo můžete přejít přímo na [webu Azure portal](https://portal.azure.com). Postupujte podle kroků v tomto článku provádět svým přezkoumat přiřazených rolí.

Pokud jste správcem privilegovaných rolí nebo globální správce zájem o kontroly přístupu, získáte další podrobnosti najdete v [zahájení kontroly přístupu](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Můžete použít v aplikaci Azure AD Privileged Identity Management (PIM) [webu Azure portal](https://portal.azure.com/) provádět revizi.  Pokud nemáte aplikaci Azure AD Privileged Identity Management na portálu, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte své uživatelské jméno v pravém horním rohu webu Azure portal a vyberte adresář, kde se dozvíte, jak můžete pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schvalte nebo zamítněte přístup
Pokud, schvalte nebo zamítněte přístup, je právě tím rozdílem, revidující, zda stále použít tuto roli nebo ne. Zvolte **schválit** Pokud budete chtít zůstat v roli, nebo **Odepřít** Pokud už nepotřebujete přístup. Stav nezmění hned, dokud revidující se vztahuje výsledky.
Postupujte podle těchto kroků můžete najít a dokončení kontroly přístupu:

1. V aplikaci PIM vyberte **revize privilegovaný přístup**. Pokud máte k dispozici žádné kontroly přístupu, se zobrazí v okně kontrol přístupu Azure AD.
2. Vyberte revizi, kterou chcete provést.
3. Pokud jste vytvořili revizi, vypadá jako jediným uživatelem v revizi. Vyberte zaškrtávací políčko vedle vašeho názvu.
4. Zvolte buď **schválit** nebo **Odepřít**. Možná budete muset zahrnout důvod pro své rozhodnutí v **zadat příslušný důvod** textového pole.  
5. Zavřít **revize Azure AD role** okno.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
