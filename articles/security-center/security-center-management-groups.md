---
title: Získání viditelnosti pro Azure Security Center v úrovni tenanta | Microsoft Docs
description: Tento článek vysvětluje, jak spravovat stav zabezpečení ve velkém měřítku pomocí zásad pro všechna předplatná propojená s vaším klientem Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 6bbc38d79f51ba4ffcc3795718d276a7e9c0bf03
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440782"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Získání viditelnosti pro Azure Security Center v úrovni tenanta
Tento článek vysvětluje, jak spravovat stav zabezpečení vaší organizace pomocí zásad zabezpečení pro všechna předplatná Azure propojená s vaším klientem Azure Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Skupiny pro správu
Skupiny pro správu Azure umožňují efektivně spravovat přístup, zásady a vytváření sestav pro skupiny předplatných a efektivně spravovat celou službu Azure, a to prováděním akcí v kořenové skupině pro správu. Každému tenantovi služby Azure AD je přiřazena jedna skupina pro správu nejvyšší úrovně s názvem kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato skupina umožňuje použití globálních zásad a přiřazení rolí Azure na úrovni adresáře. 

Kořenová skupina pro správu se vytvoří automaticky, když provedete některou z následujících akcí: 
1. Pokud chcete používat skupiny pro správu Azure, přejděte na **skupiny pro správu** v [Azure Portal](https://portal.azure.com).
2. Vytvořte skupinu pro správu prostřednictvím volání rozhraní API.
3. Vytvořte skupinu pro správu pomocí PowerShellu.

Podrobný přehled skupin pro správu najdete v článku [uspořádání prostředků pomocí skupin pro správu Azure](../azure-resource-manager/management-groups-overview.md) .

## <a name="create-a-management-group-in-the-azure-portal"></a>Vytvoření skupiny pro správu v Azure Portal
Předplatná můžete uspořádat do skupin pro správu a použít zásady správného řízení pro skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. I když skupiny pro správu nejsou vyžadovány k připojení Security Center, důrazně doporučujeme vytvořit alespoň jednu skupinu pro správu, aby bylo možné vytvořit kořenovou skupinu pro správu. Po vytvoření skupiny se na ni budou propojit všechna předplatná v rámci vašeho tenanta Azure AD. Pokyny pro PowerShell a další informace najdete v tématu [Vytvoření skupin pro správu pro správu prostředků a organizací](../azure-resource-manager/management-groups-create.md).

 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny**  >  **skupiny pro správu**služeb.
3. Na hlavní stránce vyberte **Nová skupina pro správu.** 

    ![Hlavní skupina](./media/security-center-management-groups/main.png) 
4.  Vyplňte pole ID skupiny pro správu. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není po vytvoření upravitelný, protože se používá v rámci systému Azure k identifikaci této skupiny. 
    - Pole Zobrazovaný název je název, který se zobrazí v Azure Portal. Samostatné zobrazované jméno je volitelné pole při vytváření skupiny pro správu a může být kdykoli změněno.  

      ![Vytvořit](./media/security-center-management-groups/create_context_menu.png)  
5.  Vyberte **Uložit**.

### <a name="view-management-groups-in-the-azure-portal"></a>Zobrazení skupin pro správu v Azure Portal
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud chcete zobrazit skupiny pro správu, vyberte **všechny služby** v hlavní nabídce Azure.
3. V části **Obecné**vyberte **skupiny pro správu**.

    ![Vytvoření skupiny pro správu](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udělení viditelnosti na úrovni tenanta a možnost přiřazování zásad

Aby bylo možné získat přehled o stav zabezpečení všech předplatných registrovaných v tenantovi Azure AD, musí být pro kořenovou skupinu pro správu přiřazena role Azure s dostatečným oprávněním ke čtení.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení přístupu pro globálního správce v Azure Active Directory
Správce tenanta Azure Active Directory nemá přímý přístup k předplatným Azure. Správci adresáře ale mají právo zvýšit úroveň na roli, která má přístup. Správce tenanta Azure AD musí zvýšit úroveň na správce přístupu uživatele na úrovni kořenové skupiny pro správu, aby mohl přiřadit role Azure. Pokyny k prostředí PowerShell a další informace najdete v tématu [zvýšení přístupu pro globálního správce v Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [centru pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

   ![Vlastnosti Azure AD – snímek obrazovky](./media/security-center-management-groups/aad-properties.png)

3. V části **Správa přístupu pro prostředky Azure**nastavte přepínač na **Ano**.

   ![Správa přístupu pro prostředky Azure – snímek obrazovky](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Když nastavíte přepínač na Ano, bude vám přiřazena role správce přístupu uživatele v Azure RBAC v kořenovém oboru (/). Tím udělíte oprávnění k přiřazování rolí ve všech předplatných Azure a skupinách pro správu přidružených k tomuto adresáři služby Azure AD. Tento přepínač je dostupný jenom uživatelům, kteří mají přiřazenou roli globálního správce ve službě Azure AD.

   - Když nastavíte přepínač Ne, role správce přístupu uživatele v Azure RBAC se odebere z vašeho uživatelského účtu. Ve všech předplatných Azure a skupinách pro správu, které jsou přidružené k tomuto adresáři služby Azure AD, už nemůžete přiřazovat role. Můžete zobrazit a spravovat pouze předplatná Azure a skupiny pro správu, kterým jste udělili přístup.

4. Uložte nastavení kliknutím na **Uložit** .

    - Toto nastavení není globálních vlastností a platí pouze pro aktuálně přihlášeného uživatele.

5. Proveďte úlohy, které potřebujete k zajištění přístupu se zvýšenými oprávněními. Až skončíte, nastavte přepínač zpět na **ne**.


### <a name="assign-azure-roles-to-users"></a>Přiřazení rolí Azure uživatelům
Aby mohli správci klientů získat přehled o všech předplatných, musí přiřadit příslušnou roli Azure všem uživatelům, kteří chtějí udělit přístup v celé klientovi, včetně samotného, na úrovni kořenové skupiny pro správu. Doporučené role k přiřazení jsou buď **Správce zabezpečení** , nebo **čtecí modul zabezpečení**. Obecně platí, že role správce zabezpečení je nutná k uplatnění zásad na kořenové úrovni, zatímco čtečka zabezpečení bude stačit k poskytování viditelnosti na úrovni tenanta. Další informace o oprávněních udělených těmito rolemi najdete v [popisu předdefinované role správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) nebo v [popisu předdefinované role čtenáře zabezpečení](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Přiřaďte role Azure uživatelům prostřednictvím Azure Portal: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Pokud chcete zobrazit skupiny pro správu, vyberte v hlavní nabídce Azure možnost **všechny služby** a pak vyberte **skupiny pro správu**.
1.  Vyberte skupinu pro správu a klikněte na **Podrobnosti**.

    ![Snímek obrazovky s Skupiny pro správu podrobností](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klikněte na **řízení přístupu (IAM)** a pak na **přiřazení rolí**.

1. Klikněte na **Přidat přiřazení role**.

1. Vyberte roli, kterou chcete přiřadit, a uživatele a pak klikněte na **Uložit**.  
   
   ![Snímek obrazovky s přidáním role čtenář zabezpečení](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Přiřazení rolí Azure uživatelům pomocí PowerShellu: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

### <a name="open-or-refresh-security-center"></a>Otevřít nebo aktualizovat Security Center
Jakmile budete mít zvýšený přístup, otevřete nebo aktualizujte Azure Security Center a ověřte si, že máte přehled o všech předplatných v rámci vašeho tenanta Azure AD. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Ujistěte se, že jste vybrali všechna předplatná v selektoru předplatného, které chcete zobrazit v Security Center.

    ![Snímek obrazovky selektor odběrů](./media/security-center-management-groups/subscription-selector.png)

1. V hlavní nabídce Azure vyberte **všechny služby** a pak vyberte **Security Center**.
2. V **přehledu**je k dispozici graf pokrytí předplatného.

    ![Snímek obrazovky grafu pokrytí předplatných](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kliknutím na **pokrytí** zobrazíte seznam předplatných, které jsou popsány. 

    ![Snímek obrazovky seznamu pokrytí předplatných](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu 
Jakmile role Azure přiřadíte uživatelům, správce tenanta by se měl odebrat z role správce přístupu uživatele.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [centru pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V navigačním seznamu klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

3. V části **Správa přístupu pro prostředky Azure**nastavte přepínač na **ne**.

4. Uložte nastavení kliknutím na **Uložit** .



## <a name="adding-subscriptions-to-a-management-group"></a>Přidání předplatných do skupiny pro správu
Můžete přidat odběry do skupiny pro správu, kterou jste vytvořili. Tyto kroky nejsou povinné pro získání viditelnosti a globální správy zásad a přístupu v celém tenantovi.

1. V části **skupiny pro správu**vyberte skupinu pro správu, do které chcete přidat své předplatné.

    ![Vyberte skupinu pro správu, do které chcete přidat odběr.](./media/security-center-management-groups/management-group-subscriptions.png)

2. Vyberte **Přidat existující**.

    ![Přidat existující](./media/security-center-management-groups/add-existing.png)

3. Do pole **Přidat existující prostředek** zadejte předplatné a klikněte na **Uložit**.

4. Opakujte kroky 1 až 3, dokud nepřidáte všechna předplatná v oboru.

   > [!NOTE]
   > Skupiny pro správu můžou obsahovat předplatné i podřízené skupiny pro správu. Když uživateli přiřadíte roli Azure pro nadřazenou skupinu pro správu, zdědí se přístup k předplatným podřízené skupiny pro správu. Zásady nastavené v nadřazené skupině pro správu jsou také děděny podřízenými objekty. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak získat Azure Security Center viditelnosti v úrovni tenanta. Další informace o službě Security Center najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
