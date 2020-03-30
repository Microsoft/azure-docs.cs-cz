---
title: Rychlý start zásad pojmenování skupin – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak ve službě Azure Active Directory přidat nové uživatele nebo odstranit existující uživatele.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026938"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rychlý start: Zásady pojmenování pro skupiny v Azure Active Directory

V tomto rychlém startu nastavíte ve svém tenantovi Azure Active Directory (Azure AD) zásady pojmenování pro skupiny Office 365 vytvořené uživatelem, které vám pomůžou řadit a prohledávat skupiny ve vašem tenantovi. Zásady pojmenování můžete použít například k následujícím účelům:

* Informování o funkci skupiny, členství, geografické oblasti a uživateli, který skupinu vytvořil.
* Pomoc s uspořádáním skupin do kategorií v adresáři.
* Blokování použití určitých slov v názvech a aliasech skupin.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurace zásad pojmenování skupin pro klienta pomocí portálu Azure Portal

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele.
1. Vyberte **Skupiny**, pak vyberte **Zásady pojmenování** a otevřete stránku Zásady pojmenování.

    ![Otevřete stránku Zásady pojmenování v Centru pro správu](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazení nebo úprava zásady pojmenování předpony

1. Na stránce **Zásady pojmenování** vyberte **zásady pojmenování skupin**.
1. Aktuální zásady pojmenování předpony nebo přípony můžete zobrazit nebo upravit jednotlivě výběrem atributů nebo řetězců, které chcete vynutit jako součást zásad pojmenování.
1. Pokud chcete odebrat předponu nebo příponu ze seznamu, vyberte předponu nebo příponu a pak vyberte **Odstranit**. Současně lze odstranit více položek.
1. Vyberte **Uložit,** aby změny zásad y vstoupily v platnost.

### <a name="view-or-edit-the-custom-blocked-words"></a>Zobrazení nebo úprava vlastních blokovaných slov

1. Na stránce **Zásady pojmenování** vyberte **Blokovaná slova**.

    ![upravit a odeslat seznam blokovaných slov pro zásady pojmenování](./media/groups-naming-policy/blockedwords.png)

1. Zobrazení nebo úprava aktuálního seznamu vlastních blokovaných slov výběrem **možnosti Stáhnout**.
1. Nahrajte nový seznam vlastních blokovaných slov výběrem ikony souboru.
1. Vyberte **Uložit,** aby změny zásad y vstoupily v platnost.

A to je vše. Nastavili jste zásady pojmenování a přidali jste vlastní blokovaná slova.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="remove-the-naming-policy-using-azure-portal"></a>Odebrání zásad pojmenování pomocí portálu Azure Portal

1. Na stránce **Zásady pojmenování** vyberte **Odstranit zásady**.
1. Po potvrzení odstranění je zásada pojmenování odebrána, včetně všech zásad pojmenování předpony a všech vlastních blokovaných slov.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak nastavit zásady pojmenování pro vaši organizaci Azure AD prostřednictvím portálu Azure.

Přejde k dalšímu článku další informace, včetně rutin prostředí PowerShell pro zásady pojmenování, technická omezení, přidání seznamu vlastních blokovaných slov a prostředí koncových uživatelů v aplikacích Office 365.
> [!div class="nextstepaction"]
> [Zásady pojmenování Prostředí PowerShell](groups-naming-policy.md)