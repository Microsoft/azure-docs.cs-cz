---
title: Jak spravovat přiřazení RBAC v synapse v synapse studiu
description: Tento článek popisuje, jak přiřadit a odvolat synapse role RBAC k objektům zabezpečení AAD.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102185"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Jak spravovat přiřazení rolí RBAC synapse v synapse studiu

Synapse RBAC používá role k přiřazování oprávnění uživatelům, skupinám a dalším objektům zabezpečení pro povolení přístupu a používání prostředků synapse a artefaktů kódu.  [Další informace](./synapse-workspace-synapse-rbac.md)

Tento článek popisuje, jak přidat a odstranit přiřazení rolí synapse RBAC.

>[!Note]
>- Pokud chcete spravovat přiřazení rolí RBAC synapse, musíte mít roli správce synapse v pracovním prostoru nebo v rozsahu nižší úrovně, který obsahuje objekty, které chcete spravovat. Pokud jste správce synapse v pracovním prostoru, můžete udělit přístup ke všem objektům v pracovním prostoru. 
>- **Uživatelé typu Host** z jiného tenanta služby AD nemůžou zobrazit nebo spravovat přiřazení rolí, a to ani v případě, že je přiřazena role správce synapse.
>- Uživatelé s oprávněním ke správě **přiřazení rolí služby** **Azure RBAC** v pracovním prostoru můžou také při přístupu k pracovnímu prostoru získat přístup k pracovnímu prostoru v případě, že k němu nejsou přiřazeni žádní synapse Administrators
>- Přístup k fondům SQL se spravuje pomocí oprávnění SQL.  S výjimkou rolí správce synapse a synapse správců systému SQL synapse role RBAC neudělují přístup k fondům SQL.

>[!important]
>- Změny provedené v přiřazení rolí RBAC synapse můžou trvat až 2-5 minut. 
>- Pokud spravujete oprávnění synapse RBAC úpravou členství ve skupinách zabezpečení, budou se změny členství spravovat pomocí Azure Active Directory.  Změny členství ve skupinách mohou trvat až 10-15 minut nebo déle.

## <a name="open-synapse-studio"></a>Otevřít synapse Studio  

Pokud chcete přiřadit roli uživateli, skupině, objektu služby nebo spravované identitě, [otevřete nejprve synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor. 

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png) 
 
 Po otevření pracovního prostoru rozbalte část **zabezpečení** na levé straně a vyberte **řízení přístupu**. 

 ![Vyberte Access Control v části zabezpečení vlevo.](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Obrazovka řízení přístupu uvádí aktuální přiřazení rolí.  Seznam můžete filtrovat podle hlavního názvu nebo e-mailu a selektivně filtrovat typy objektů, role nebo obory, které jsou zahrnuté. Z této obrazovky můžete přidat nebo odebrat přiřazení rolí.  

## <a name="add-a-synapse-role-assignment"></a>Přidání přiřazení role synapse

Na obrazovce řízení přístupu vyberte **+ Přidat** a vytvořte přiřazení nové role.

![Kliknutím na + Přidat vytvoříte přiřazení nové role.](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Na kartě přidat přiřazení role můžete vytvořit přiřazení rolí v oboru pracovního prostoru nebo rozsahu položky pracovního prostoru. 

## <a name="add-workspace-scoped-role-assignment"></a>Přidat přiřazení role v rámci pracovního prostoru

Nejdřív jako obor vyberte **pracovní prostor** a pak vyberte **roli synapse RBAC**.  Vyberte **objekty** , které mají být přiřazeny k roli, a pak vytvořte přiřazení rolí. 

![Přidat přiřazení role pracovního prostoru – vybrat roli](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Přiřazená role bude platit pro všechny použitelné objekty v pracovním prostoru.

## <a name="add-workspace-item-scoped-role-assignment"></a>Přidat přiřazení role v rámci rozsahu položky pracovního prostoru

Chcete-li přiřadit roli pro jemnější rozsah, vyberte **položku pracovní prostor** jako obor a pak vyberte **typ položky** rozsah.       

![Přidat přiřazení role položky pracovního prostoru – vybrat typ položky](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Vyberte konkrétní **položku** , která se má použít jako obor, a pak vyberte **roli** , kterou chcete přiřadit z rozevírací nabídky.  Rozevírací seznam obsahuje jenom role, které jsou platné pro vybraný typ položky. [Další informace](./synapse-workspace-synapse-rbac.md).  

![Přidat přiřazení role položky pracovního prostoru – vybrat roli](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Pak **Vyberte objekty (y)** , ke kterým se má role přiřadit.  Můžete iterativní výběr více objektů zabezpečení.  Pokud chcete vytvořit přiřazení rolí, vyberte **použít** .

## <a name="remove-a-synapse-rbac-role-assignment"></a>Odebrání přiřazení role RBAC synapse

Pokud chcete odvolat přístup synapse RBAC, odeberte příslušná přiřazení rolí.  Na obrazovce řízení přístupu použijte filtry k vyhledání přiřazení rolí, které chcete odebrat.  Zkontrolujte přiřazení rolí a pak vyberte **Odebrat přístup**.   

![Odstranění přiřazení role pro odebrání přístupu](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Pamatujte, že změny přiřazení rolí budou trvat až 2-5 minut.   

## <a name="next-steps"></a>Další kroky

[Pochopení rolí RBAC synapse potřebných k provádění běžných úloh](./synapse-workspace-understand-what-role-you-need.md)