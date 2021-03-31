---
title: Postup kontroly přiřazení rolí RBAC synapse v synapse studiu
description: Tento článek popisuje, jak zkontrolovat přiřazení rolí RBAC synapse pomocí synapse studia.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102151"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Kontrola přiřazení rolí RBAC synapse

Role RBAC synapse slouží k přiřazení oprávnění uživatelům, skupinám a dalším objektům zabezpečení pro povolení přístupu a používání prostředků synapse.  [Další informace](./synapse-workspace-synapse-rbac.md)

Tento článek vysvětluje, jak zkontrolovat aktuální přiřazení rolí pro pracovní prostor.

U jakékoli role synapse RBAC můžete vypsat přiřazení rolí RBAC synapse pro všechny obory, včetně přiřazení pro objekty, ke kterým nemáte přístup. Přístup synapse RBAC může udělit jenom správce synapse.  

>[!Note]
>Uživatelé typu Host (uživatelé z jiného tenanta služby AD) nemůžou zobrazit nebo spravovat přiřazení rolí, a to ani v případě, že máte přiřazenou roli správce synapse.    

## <a name="open-synapse-studio"></a>Otevřít synapse Studio  

Pokud chcete zkontrolovat přiřazení rolí, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor. 

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png) 
 
 Po otevření pracovního prostoru vyberte na levé straně centrum pro **správu** a pak rozbalte část **zabezpečení** a vyberte **řízení přístupu**. 

 ![Vyberte Access Control v části zabezpečení vlevo.](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Kontrola přiřazení rolí v pracovním prostoru

Obrazovka řízení přístupu uvádí všechna přiřazení rolí pro pracovní prostor seskupená podle rolí. Každé přiřazení zahrnuje hlavní název, hlavní typ, roli a obor.

![Access Control obrazovce](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Pokud je objektu zabezpečení přiřazena stejná role v různých oborech, zobrazí se u objektu zabezpečení více přiřazení, jednu pro každý obor.  

Pokud je role přiřazena ke skupině zabezpečení, uvidíte role explicitně přiřazené skupině, ale ne role děděné z nadřazených skupin.  

Seznam můžete filtrovat podle hlavního jména nebo e-mailu a selektivně filtrovat typy objektů, role a obory. zadáním svého jména nebo e-mailového aliasu do filtru Název zobrazíte přiřazené role. Pouze správce synapse může měnit vaše role.

>[!Important] 
>Pokud jste přímo nebo nepřímo členem skupiny, která je přiřazená role, můžete mít oprávnění, která nejsou zobrazená.

>[!tip]
>Členství ve skupinách můžete najít pomocí Azure Active Directory v Azure Portal.  

Pokud vytvoříte nový pracovní prostor, budete mít automaticky přiřazenou roli správce synapse v oboru pracovního prostoru a instančnímu objektu pracovního prostoru MSI.

## <a name="next-steps"></a>Další kroky

Naučte [se spravovat přiřazení rolí RBAC synapse](./how-to-manage-synapse-rbac-role-assignments.md).

Zjistěte [, jakou roli potřebujete provést konkrétní úkoly](./synapse-workspace-understand-what-role-you-need.md) .