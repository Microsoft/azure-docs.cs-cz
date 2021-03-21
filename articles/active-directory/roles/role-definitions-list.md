---
title: Vypsat definice rolí Azure AD – Azure AD
description: Přečtěte si, jak vypsat předdefinované a vlastní role Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467832"
---
# <a name="list-azure-ad-role-definitions"></a>Vypsat definice rolí Azure AD

Definice role je kolekce oprávnění, která se dají provést, jako je čtení, zápis a odstranění. Obvykle se nazývá jenom role. Azure Active Directory má více než 60 předdefinovaných rolí, nebo můžete vytvořit vlastní role. Pokud jste někdy přemýšleli "co tyto role opravdu dělají?", můžete zobrazit podrobný seznam oprávnění pro každou roli.

Tento článek popisuje, jak vytvořit seznam integrovaných a vlastních rolí Azure AD společně s jejich oprávněními.

## <a name="list-all-roles"></a>Vypsat všechny role

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) a vyberte **Azure Active Directory**.

1. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

    ![seznam rolí na portálu Azure AD](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Na pravé straně vyberte tři tečky a pak **Popis** , abyste viděli úplný seznam oprávnění pro roli.

    Stránka obsahuje odkazy na příslušnou dokumentaci, která vám pomůže s tím, jak spravovat role.

    ![Snímek obrazovky zobrazující stránku Globální správce – popis](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](permissions-reference.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
