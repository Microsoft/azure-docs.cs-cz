---
title: Rychlý Start pro zásady pojmenování skupin – Azure Active Directory | Microsoft Docs
description: Tento článek vysvětluje, jak ve službě Azure Active Directory přidat nové uživatele nebo odstranit existující uživatele.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0dcf05f829cfcdc79fcd7be903b96fb601c095a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379089"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rychlý start: Zásady pojmenování pro skupiny v Azure Active Directory

V tomto rychlém startu nastavíte zásady pojmenování v organizaci Azure Active Directory (Azure AD) pro uživatelem vytvořené Microsoft 365 skupiny, které vám pomůžou s řazením a hledáním skupin ve vaší organizaci. Zásady pojmenování můžete použít například k následujícím účelům:

* Informování o funkci skupiny, členství, geografické oblasti a uživateli, který skupinu vytvořil.
* Pomoc s uspořádáním skupin do kategorií v adresáři.
* Blokování použití určitých slov v názvech a aliasech skupin.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Konfigurace zásady pojmenování skupin v Azure Portal

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele.
1. Vyberte **skupiny**a pak výběrem **zásady pojmenování** otevřete stránku zásady pojmenování.

    ![Otevřete stránku zásady pojmenování v centru pro správu.](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazit nebo upravit zásady pojmenování přípon předpon

1. Na stránce **zásady pojmenování** vyberte **zásady pojmenování skupin**.
1. Aktuální předponu nebo zásady pojmenování přípon můžete zobrazit nebo upravit jednotlivě tak, že vyberete atributy nebo řetězce, které chcete vykonat jako součást zásad pojmenování.
1. Pokud chcete ze seznamu odebrat předponu nebo příponu, vyberte předponu nebo příponu a pak vyberte **Odstranit**. Současně lze odstranit více položek.
1. Vyberte **Uložit** pro změny zásady, aby se projevily.

### <a name="view-or-edit-the-custom-blocked-words"></a>Zobrazit nebo upravit vlastní blokovaná slova

1. Na stránce **zásady pojmenování** vyberte **blokovaná slova**.

    ![seznam blokovaných slov pro úpravy a nahrání pro zásady pojmenování](./media/groups-quickstart-naming-policy/blockedwords.png)

1. Výběrem položky **Stáhnout**zobrazíte nebo upravíte aktuální seznam vlastních blokovaných slov.
1. Kliknutím na ikonu souboru Nahrajte nový seznam vlastních blokovaných slov.
1. Vyberte **Uložit** pro změny zásady, aby se projevily.

Hotovo. Nastavili jste zásady pojmenování a přidali jste vlastní blokovaná slova.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="remove-the-naming-policy-using-azure-portal"></a>Odeberte zásady pojmenování pomocí Azure Portal

1. Na stránce **zásady pojmenování** vyberte **Odstranit zásadu**.
1. Po potvrzení odstranění se odeberou zásady pojmenování, včetně všech zásad pojmenování přípon předpon a všech blokovaných slov.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak nastavit zásady pojmenování pro vaši organizaci Azure AD prostřednictvím Azure Portal.

Přejděte k dalšímu článku, kde najdete další informace, včetně rutin PowerShellu pro zásady pojmenování, technických omezení, přidání seznamu vlastních blokovaných slov a prostředí pro koncové uživatele v aplikacích Microsoft 365.
> [!div class="nextstepaction"]
> [Prostředí PowerShell pro zásady pojmenování](groups-naming-policy.md)