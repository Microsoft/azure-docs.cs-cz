---
title: Uspořádání předplatných do skupin pro správu a přiřazení rolí uživatelům pro Azure Security Center
description: Naučte se organizovat vaše předplatná Azure do skupin pro správu v Azure Security Center a přiřazovat role uživatelům ve vaší organizaci.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099382"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Uspořádání předplatných do skupin pro správu a přiřazení rolí uživatelům

Tento článek vysvětluje, jak spravovat stav zabezpečení vaší organizace pomocí zásad zabezpečení pro všechna předplatná Azure propojená s vaším klientem Azure Active Directory.

Aby bylo možné získat přehled o stav zabezpečení všech předplatných registrovaných v tenantovi Azure AD, musí být pro kořenovou skupinu pro správu přiřazena role Azure s dostatečným oprávněním ke čtení.


## <a name="organize-your-subscriptions-into-management-groups"></a>Uspořádání předplatných do skupin pro správu

### <a name="introduction-to-management-groups"></a>Seznámení se skupinami pro správu

Skupiny pro správu Azure umožňují efektivně spravovat přístup, zásady a vytváření sestav pro skupiny předplatných a efektivně spravovat celou službu Azure, a to prováděním akcí v kořenové skupině pro správu. Předplatná můžete uspořádat do skupin pro správu a použít zásady správného řízení pro skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. 

Každému tenantovi služby Azure AD je přiřazena jedna skupina pro správu nejvyšší úrovně s názvem **Kořenová skupina pro správu**. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato skupina umožňuje použití globálních zásad a přiřazení rolí Azure na úrovni adresáře. 

Kořenová skupina pro správu se vytvoří automaticky, když provedete některou z následujících akcí: 
- Otevřete **skupiny pro správu** v [Azure Portal](https://portal.azure.com).
- Vytvořte skupinu pro správu pomocí volání rozhraní API.
- Vytvořte skupinu pro správu pomocí PowerShellu. Pokyny pro PowerShell najdete v tématu [Vytvoření skupin pro správu pro správu prostředků a organizací](../governance/management-groups/create-management-group-portal.md).

Skupiny pro správu nejsou vyžadovány k připojování Security Center, doporučujeme však vytvořit alespoň jednu, aby byla vytvořena kořenová skupina pro správu. Po vytvoření skupiny se na ni budou propojit všechna předplatná v rámci vašeho tenanta Azure AD. 


Podrobný přehled skupin pro správu najdete v článku [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md) .

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Zobrazení a vytvoření skupin pro správu v Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete **skupiny pro správu** pomocí vyhledávacího pole v horním panelu.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Přístup ke skupinám pro správu":::

    Zobrazí se seznam skupin pro správu.

1. Chcete-li vytvořit skupinu pro správu, vyberte možnost **Přidat skupinu pro správu**, zadejte příslušné podrobnosti a vyberte možnost **Uložit**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Přidání skupiny pro správu do Azure":::

    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není po vytvoření upravitelný, protože se používá v rámci systému Azure k identifikaci této skupiny. 
    - Pole Zobrazovaný název je název, který se zobrazí v Azure Portal. Samostatné zobrazované jméno je volitelné pole při vytváření skupiny pro správu a může být kdykoli změněno.  


### <a name="add-subscriptions-to-a-management-group"></a>Přidání předplatných do skupiny pro správu
Můžete přidat odběry do skupiny pro správu, kterou jste vytvořili.

1. V části **skupiny pro správu** vyberte skupinu pro správu svého předplatného.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Vybrat skupinu pro správu předplatného":::

1. Když se otevře stránka skupiny, vyberte **Podrobnosti** .

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Otevření stránky podrobností skupiny pro správu":::

1. Na stránce podrobností skupiny vyberte **Přidat předplatné**, pak vyberte vaše předplatná a vyberte **Uložit**. Opakujte, dokud nepřidáte všechna předplatná v oboru.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Přidání předplatného do skupiny pro správu":::
   > [!IMPORTANT]
   > Skupiny pro správu můžou obsahovat předplatné i podřízené skupiny pro správu. Když uživateli přiřadíte roli Azure pro nadřazenou skupinu pro správu, zdědí se přístup k předplatným podřízené skupiny pro správu. Zásady nastavené v nadřazené skupině pro správu jsou také děděny podřízenými objekty. 



## <a name="assign-azure-roles-to-other-users"></a>Přiřazení rolí Azure jiným uživatelům

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Přiřaďte role Azure uživatelům prostřednictvím Azure Portal: 
1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
1. Pokud chcete zobrazit skupiny pro správu, vyberte v hlavní nabídce Azure možnost **všechny služby** a pak vyberte **skupiny pro správu**.
1.  Vyberte skupinu pro správu a vyberte **Podrobnosti**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Snímek obrazovky s Skupiny pro správu podrobností":::

1. Vyberte **řízení přístupu (IAM)** a pak **přiřazení rolí**.
1. Vyberte **Přidat přiřazení role**.
1. Vyberte roli, kterou chcete přiřadit, a pak vyberte **Uložit**.  
   
   ![Snímek obrazovky s přidáním role čtenář zabezpečení](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Přiřazení rolí Azure uživatelům pomocí PowerShellu: 
1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).
2. Spusťte následující příkazy: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Po zobrazení výzvy se přihlaste pomocí přihlašovacích údajů globálního správce. 

    ![Snímek obrazovky s přihlašovacím výzvou](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udělte oprávnění role čtenáře spuštěním následujícího příkazu:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. K odebrání role použijte následující příkaz: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu 

Jakmile role Azure přiřadíte uživatelům, správce tenanta by se měl odebrat z role správce přístupu uživatele.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [centru pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu vyberte **Azure Active Directory** a pak vyberte **vlastnosti**.

3. V části **Správa přístupu pro prostředky Azure** nastavte přepínač na **ne**.

4. Nastavení uložíte tak, že vyberete **Uložit**.



## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak organizovat předplatná do skupin pro správu a přiřazovat role uživatelům. Související informace najdete v těchto tématech:

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)