---
title: Získejte potřebný Přehled celého tenanta pro Azure Security Center | Dokumentace Microsoftu
description: Přečtěte si o získat přehled o celého tenanta v Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144563"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Získejte potřebný Přehled celého tenanta pro Azure Security Center
Tento článek vám pomůže začít pracovat způsobem několik akcí, které Maximalizace výhod, které poskytuje Azure Security Center. Provedení těchto akcí umožňuje získat přehled o ve všech předplatných Azure, které jsou propojeny do svého tenanta Azure Active Directory a efektivně spravovat stavu zabezpečení vaší organizace ve velkém měřítku pomocí zásad zabezpečení napříč více předplatná aggregative způsobem.

## <a name="management-groups"></a>Skupiny pro správu
Skupiny pro správu Azure umožňují efektivně spravovat přístup, zásady a vytváření sestav o skupinách předplatných, jakož i provádět akce na skupinu root management efektivně spravovat celý portfolia Azure. Každý tenant Azure AD se přiřadí skupinu správy nejvyšší úrovně s názvem kořenové skupině pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato skupina umožňuje globální zásady a přiřazení RBAC uplatňovat na úrovni adresáře. 

Skupinu root management se automaticky vytvoří, když uděláte některý z následujících akcí: 
1. Přihlášení k používání skupin pro správu Azure tak, že přejdete do **skupin pro správu** v [webu Azure portal](https://portal.azure.com).
2. Vytvořte skupinu pro správu prostřednictvím volání rozhraní API.
3. Vytvoření skupiny pro správu pomocí prostředí PowerShell.

Podrobný přehled skupin pro správu, najdete v článku [uspořádání prostředků se skupinami pro správu Azure](../azure-resource-manager/management-groups-overview.md) článku.

## <a name="create-a-management-group-in-the-azure-portal"></a>Vytvořit skupinu pro správu na webu Azure Portal
Můžete uspořádat do skupin pro správu předplatných a zásad správného řízení zásad do skupin pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. Zatímco skupin pro správu nebude muset připojit Security Center, důrazně doporučujeme vytvořit alespoň jednu skupinu pro správu, vytvořit skupinu root management. Po vytvoření skupiny se k němu propojí Všechna předplatná v rámci vašeho tenanta Azure AD. Pokyny pro prostředí PowerShell a další informace najdete v tématu [vytvoření skupin pro správu ke správě prostředků a organizace](../azure-resource-manager/management-groups-create.md).

 
1. Přihlaste se k [portálu Azure](http://portal.azure.com).
2. Vyberte **všechny služby** > **skupin pro správu**.
3. Na hlavní stránce vyberte **skupiny pro správu nový.** 

    ![Hlavní skupiny](./media/security-center-management-groups/main.png) 
4.  Vyplňte pole ID skupiny správy. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odeslání příkazů na tuto skupinu pro správu. Tento identifikátor není upravovat po jeho vytvoření, protože se používá k identifikaci této skupiny v rámci systému Azure. 
    - Pole zobrazovaného názvu je název, který se zobrazí na portálu Azure portal. Samostatné zobrazované jméno je volitelné pole při vytváření rozhraní pro správu skupin a můžete kdykoli změnit.  

      ![Vytvořit](./media/security-center-management-groups/create_context_menu.png)  
5.  Vyberte **uložit**

### <a name="view-management-groups-in-the-azure-portal"></a>Zobrazení skupin pro správu na webu Azure Portal
1. Přihlaste se k [webu Azure portal](http://portal.azure.com).
2. Chcete-li zobrazit skupiny pro správu, vyberte **všechny služby** v hlavní nabídce Azure.
3. V části **Obecné**vyberte **skupin pro správu**.

    ![Vytvořit skupinu pro správu](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udělují viditelnost na úrovni tenanta a schopnost přiřazení zásad

Získat přehled o stavu zabezpečení všech předplatných registrované v tenantu Azure AD, je potřeba přiřadit kořenové skupiny pro správu roli RBAC s dostatečnými oprávněními ke čtení.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory
Správce tenanta Azure Active Directory nemá přímý přístup k předplatným Azure. Nicméně jako správce adresáře, mají právo sami zvýšit oprávnění na roli, která mají přístup. Správce tenanta Azure AD je potřeba zvýšit samotného správce uživatelských přístupů na kořenové úrovni skupiny správy, může přiřadit role RBAC. Pokyny pro PowerShell a další informace najdete v tématu [zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Přihlaste se k [webu Azure portal](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **vlastnosti**.

   ![Azure AD – vlastnosti – snímek obrazovky](./media/security-center-management-groups/aad-properties.png)

3. V části **globální správce může spravovat předplatná Azure a skupiny pro správu**, nastavte přepínač na **Ano**.

   ![Globální správce může spravovat předplatná Azure a skupiny pro správu – snímek obrazovky](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Pokud nastavíte přepínač na **Ano**, váš účet globálního správce (aktuálně přihlášeného uživatele) je přidán do role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (`/`), která uděluje přístup k zobrazení a sestavu na Všechna předplatná Azure přidružené k tenantovi Azure AD.

   - Pokud nastavíte přepínač na **ne**, váš účet globálního správce (aktuálně přihlášeného uživatele) je odebrán z role správce přístupu uživatelů v Azure RBAC. Nelze zobrazit všechna předplatná Azure, které jsou spojené s tenantem Azure AD a můžete zobrazit a spravovat pouze předplatná Azure ke kterým vám byl udělen přístup.

4. Klikněte na tlačítko **Uložit** uložte nastavení.

    - Toto nastavení není globální vlastnost a platí jenom pro aktuálně přihlášeného uživatele.

5. Provádění úkolů, které je třeba provést na přístup se zvýšeným oprávněním. Jakmile budete hotovi, nastavte přepínač zpět **ne**.

### <a name="open-or-refresh-security-center"></a>Otevřete nebo aktualizujte Security Center
Jakmile musí mít zvýšená přístup, otevřete nebo aktualizujte Azure Security Center k ověření, že máte přehled o všech předplatných v rámci vašeho tenanta Azure AD. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Ujistěte se, že vyberete všechna předplatná v modulu pro výběr předplatného, který chcete zobrazit ve službě Security Center.
    ![Snímek obrazovky výběru předplatného](./media/security-center-management-groups/subscription-selector.png)
1. Vyberte **všechny služby** v hlavní nabídce Azure zvolte **Security Center**.
2. V **přehled**, je graf pokrytí předplatného. 
    ![Snímek obrazovky grafu pokrytí předplatného](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Klikněte na **pokrytí** zobrazíte seznam předplatných zahrnuté. 
    ![Snímek obrazovky seznamu pokrytí předplatného](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>Přiřazení role RBAC uživatelům
Jakmile správce klienta s vyšší úrovní přístupu, může přiřadit roli RBAC uživatelům důležité na kořenové úrovni skupiny pro správu. Doporučenou rolí k přiřazení je [ **čtečky**](../role-based-access-control/built-in-roles.md#reader). Tato role je potřeba zadat viditelnost na úrovni tenanta. Přiřazené role se automaticky rozšíří do všech skupin pro správu a předplatná v rámci kořenové skupině pro správu. Další informace o rolích RBAC najdete v tématu [dostupných rolí](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles). 

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Spusťte následující příkazy: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Po zobrazení výzvy, přihlaste se pomocí přihlašovacích údajů globálního správce. 

    ![Přihlaste se příkazový řádek – snímek obrazovky](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udělení oprávnění role čtenáře spuštěním následujícího příkazu:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Pokud chcete odebrat roli, použijte následující příkaz: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Odebrat přístup se zvýšeným oprávněním 
Jakmile byly přiřazeny role RBAC uživatelům, správce tenanta by měl sám odebrat z role správce přístupu uživatelů.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **vlastnosti**.

3. V části **globální správce může spravovat předplatná Azure a skupiny pro správu**, nastavte přepínač na **ne**.

4. Klikněte na tlačítko **Uložit** uložte nastavení.



## <a name="adding-subscriptions-to-a-management-groups"></a>Přidávání do skupin pro správu předplatných
Odběry můžete přidat do skupiny pro správu, který jste vytvořili. Tyto kroky nejsou povinné pro získání viditelnosti celého tenanta a globálních zásad a správu přístupu.

1. V části **skupin pro správu**, vyberte skupinu pro správu přidat předplatné.

    ![Vyberte skupinu pro správu přidat předplatné](./media/security-center-management-groups/management-group-subscriptions.png)

2. Vyberte **přidat existující**.

    ![Přidat existující](./media/security-center-management-groups/add-existing.png)

3. Zadejte předplatné v rámci **přidat existující prostředek** a klikněte na tlačítko **Uložit**.

4. Opakujte kroky 1 až 3, dokud nepřidáte všechna předplatná v oboru.

 > [!NOTE]
 > Skupiny pro správu může obsahovat předplatná a podřízené skupiny pro správu. Když přiřadíte uživatele roli RBAC nadřazená skupina pro správu, je přístup dědí podřízené skupiny správy předplatných. Zásady nastavené v nadřazené skupině pro správu jsou také dědí podřízené objekty. 

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak získat přehled celého tenanta pro Azure Security Center. Další informace o službě Security Center najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Monitorování stavu zabezpečení pomocí Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Správa a reakce na výstrahy zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)

