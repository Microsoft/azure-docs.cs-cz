---
title: Získejte přehled o celém tenantovi pro Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak spravovat stav zabezpečení ve velkém měřítku použitím zásad pro všechna předplatná propojená s vaším klientem Služby Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559352"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Získejte přehled o celém tenantovi pro Azure Security Center
Tento článek vysvětluje, jak spravovat stav zabezpečení vaší organizace ve velkém měřítku použitím zásad zabezpečení pro všechna předplatná Azure propojená s vaším klientem Azure Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Skupiny pro správu
Skupiny pro správu Azure poskytují možnost efektivně spravovat přístup, zásady a vytváření sestav na skupinách předplatných a také efektivně spravovat celý majetek Azure prováděním akcí v kořenové skupině pro správu. Každý klient Azure AD je uveden a jedna skupina pro správu nejvyšší úrovně s názvem kořenové skupiny pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato skupina umožňuje globální zásady a přiřazení RBAC, které mají být použity na úrovni adresáře. 

Kořenová skupina pro správu se vytvoří automaticky, když uděláte některou z následujících akcí: 
1. Přihlaste se k používání skupin pro správu Azure tak, že přejdete do **skupin pro správu** na [webu Azure Portal](https://portal.azure.com).
2. Vytvořte skupinu pro správu prostřednictvím volání rozhraní API.
3. Vytvořte skupinu pro správu pomocí PowerShellu.

Podrobný přehled skupin pro správu najdete v článku [Uspořádání prostředků pomocí skupin pro správu Azure.](../azure-resource-manager/management-groups-overview.md)

## <a name="create-a-management-group-in-the-azure-portal"></a>Vytvoření skupiny pro správu na webu Azure Portal
Předplatná můžete uspořádat do skupin pro správu a použít zásady zásad správného řízení na skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. Zatímco skupiny pro správu nejsou nutné k napalubě Centra zabezpečení, důrazně doporučujeme vytvořit alespoň jednu skupinu pro správu, aby byla vytvořena kořenová skupina pro správu. Po vytvoření skupiny se s ní budou propojit všechna předplatná v rámci vašeho klienta Azure AD. Pokyny pro Prostředí PowerShell a další informace naleznete v [tématu Vytvoření skupin pro správu pro správu prostředků a organizací](../azure-resource-manager/management-groups-create.md).

 
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **všechny skupiny správy služeb** > **Management groups**.
3. Na hlavní stránce vyberte **Nová skupina pro správu.** 

    ![Hlavní skupina](./media/security-center-management-groups/main.png) 
4.  Vyplňte pole ID skupiny pro správu. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není upravitelný po vytvoření, protože se používá v celém systému Azure k identifikaci této skupiny. 
    - Pole se zobrazovatým názvem je název, který se zobrazí na webu Azure Portal. Samostatný zobrazovaný název je volitelné pole při vytváření skupiny pro správu a lze jej kdykoli změnit.  

      ![Vytvořit](./media/security-center-management-groups/create_context_menu.png)  
5.  Vyberte **Uložit**.

### <a name="view-management-groups-in-the-azure-portal"></a>Zobrazení skupin pro správu na webu Azure Portal
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Chcete-li zobrazit skupiny pro správu, vyberte **všechny služby** v hlavní nabídce Azure.
3. V části **Obecné**vyberte **položku Skupiny pro správu**.

    ![Vytvoření skupiny pro správu](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udělení viditelnosti na úrovni tenanta a možnosti přiřadit zásady

Chcete-li získat přehled o stavu zabezpečení všech předplatných registrovaných v tenantovi Azure AD, role RBAC s dostatečnými oprávněními ke čtení je nutné přiřadit v kořenové skupině pro správu.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení přístupu pro globálního správce ve službě Azure Active Directory
Správce klienta Azure Active Directory nemá přímý přístup k předplatným Azure. Jako správce adresáře však mají právo povýšit se na roli, která má přístup. Správce klienta Azure AD musí zvýšit sám na správce přístupu uživatelů na úrovni kořenové skupiny pro správu, aby mohli přiřadit role RBAC. Pokyny k Prostředí PowerShell a další informace najdete [v tématu Zvýšení přístupu globálního správce ve službě Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Přihlaste se k [portálu Azure Nebo](https://portal.azure.com) k [Centru pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na **Azure Active Directory** a potom klikněte na **vlastnosti**.

   ![Azure AD Properties – snímek obrazovky](./media/security-center-management-groups/aad-properties.png)

3. V části **Správa přístupu pro prostředky Azure**nastavte přepínač na **Ano**.

   ![Globální správce může spravovat předplatná Azure a skupiny pro správu – snímek obrazovky](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Když nastavíte přepínač na Ano, je vám přiřazena role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (/). To vám uděluje oprávnění k přiřazení rolí ve všech předplatných Azure a skupin pro správu přidružených k tomuto adresáři Azure AD. Tento přepínač je k dispozici jenom uživatelům, kterým je přiřazena role globálního správce ve službě Azure AD.

   - Když nastavíte přepínač na ne, role správce přístupu uživatelů v Azure RBAC se odebere z vašeho uživatelského účtu. Už nemůžete přiřazovat role ve všech předplatných Azure a skupinách pro správu, které jsou přidružené k tomuto adresáři Azure AD. Můžete zobrazit a spravovat jenom předplatná Azure a skupiny pro správu, ke kterým jste získali přístup.

4. Kliknutím na **Uložit** nastavení uložte.

    - Toto nastavení není globální vlastností a vztahuje se pouze na aktuálně přihlášeného uživatele.

5. Proveďte úkoly, které je třeba provést při přístupu se zvýšenými oprávněními. Po dokončení nastavte přepínač zpět na **ne**.


### <a name="assign-rbac-roles-to-users"></a>Přiřazení rolí RBAC uživatelům
Chcete-li získat viditelnost pro všechny předplatná, správci klienta musí přiřadit příslušnou roli RBAC všem uživatelům, které chtějí udělit viditelnost celého tenanta, včetně sebe, na úrovni kořenové skupiny pro správu. Doporučené role, které chcete přiřadit, jsou správce **zabezpečení** nebo **čtečka zabezpečení**. Obecně platí role správce zabezpečení je nutné použít zásady na kořenové úrovni, zatímco Security Reader bude stačit k zajištění viditelnosti na úrovni tenanta. Další informace o oprávněních udělených těmito rolemi naleznete [v popisu předdefinované role správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) nebo v popisu [předdefinované role Čtečky zabezpečení](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Přiřazení rolí RBAC uživatelům prostřednictvím portálu Azure: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Chcete-li zobrazit skupiny pro správu, vyberte **všechny služby** v hlavní nabídce Azure a pak vyberte **Skupiny pro správu**.
1.  Vyberte skupinu pro správu a klepněte na **podrobnosti**.

    ![Podrobnosti o skupinách pro správu – snímek obrazovky](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klikněte na **ovládací prvek přístupu (IAM)** a potom na **přiřazení rolí**.

1. Klepněte na tlačítko **Přidat přiřazení role**.

1. Vyberte roli, kterou chcete přiřadit, a uživatele a klepněte na tlačítko **Uložit**.  
   
   ![Snímek obrazovky role Přidat čtečku zabezpečení](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Přiřazení rolí RBAC uživatelům pomocí prostředí PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).
2. Spusťte následující příkazy: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Po zobrazení výzvy se přihlaste pomocí přihlašovacích údajů globálního správce. 

    ![Přihlaste se k rychlému zobrazení obrazovky](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udělte oprávnění ke roli čtenáře spuštěním následujícího příkazu:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Chcete-li roli odebrat, použijte následující příkaz: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Otevření nebo aktualizace Centra zabezpečení
Jakmile budete mít zvýšený přístup, otevřete nebo aktualizujte Azure Security Center, abyste ověřili, že máte přehled o všech předplatných v rámci vašeho klienta Azure AD. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Ujistěte se, že vyberete všechna předplatná ve voliči předplatného, které chcete zobrazit v Centru zabezpečení.

    ![Snímek obrazovky s výběrem předplatného](./media/security-center-management-groups/subscription-selector.png)

1. V hlavní nabídce Azure vyberte **Všechny služby** a pak vyberte **Centrum zabezpečení**.
2. V **přehledu**je graf pokrytí předplatného.

    ![Snímek obrazovky grafu pokrytí předplatného](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kliknutím na **Pokrytí** zobrazíte seznam zahrnutých předplatných. 

    ![Snímek obrazovky se seznamem pokrytí předplatného](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Odebrání přístupu se zvýšenými oprávněními 
Jakmile jsou role RBAC přiřazeny uživatelům, měl by se správce klienta odebrat z role správce přístupu uživatelů.

1. Přihlaste se k [portálu Azure Nebo](https://portal.azure.com) k [Centru pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na **Azure Active Directory** a potom klikněte na **vlastnosti**.

3. V části **Globální správce můžete spravovat předplatná Azure a skupiny pro správu**, nastavte přepínač na **ne**.

4. Kliknutím na **Uložit** nastavení uložte.



## <a name="adding-subscriptions-to-a-management-group"></a>Přidání předplatných do skupiny pro správu
Do skupiny pro správu, kterou jste vytvořili, můžete přidat předplatná. Tyto kroky nejsou povinné pro získání viditelnosti pro celý klient a globální zásady a správu přístupu.

1. V části **Skupiny pro správu**vyberte skupinu pro správu, do které chcete přidat předplatné.

    ![Vyberte skupinu pro správu, do které chcete přidat předplatné.](./media/security-center-management-groups/management-group-subscriptions.png)

2. Vyberte **Přidat existující**.

    ![Přidat existující](./media/security-center-management-groups/add-existing.png)

3. Zadejte předplatné v části **Přidat existující prostředek** a klepněte na tlačítko **Uložit**.

4. Opakujte kroky 1 až 3, dokud nepřidáte všechna předplatná v oboru.

   > [!NOTE]
   > Skupiny pro správu mohou obsahovat předplatná i podřízené skupiny pro správu. Když přiřadíte uživateli roli RBAC nadřazené skupině pro správu, přístup je zděděn předplatnými podřízené skupiny pro správu. Zásady nastavené v nadřazené skupině pro správu jsou také zděděny podřízenými objekty. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak získat viditelnost pro celý tenanta pro Azure Security Center. Další informace o službě Security Center najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)

