---
title: Vidí klienta úrovni pro Azure Security Center | Microsoft Docs
description: Informace o získat přehled o úrovni klienta v Azure Security Center.
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
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 2c95b06ce34b850d1bfaf60e47d6e5fede148a38
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025677"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Vidí klienta úrovni pro Azure Security Center
Tento článek vám pomůže začít pracovat způsobem několik akcí, které zvýšit výhody, které poskytuje Azure Security Center. Provedení těchto akcí umožňuje získat viditelnost ve všech předplatných Azure, které jsou závislé na klienta služby Azure Active Directory a efektivně spravovat postavení zabezpečení vaší organizace ve velkém měřítku s použitím několika zásady zabezpečení odběry aggregative způsobem.

## <a name="management-groups"></a>Skupiny pro správu
Skupiny pro správu Azure umožňují efektivně spravovat přístup, zásady a vytváření sestav pro skupiny předplatných, jakož i efektivně spravovat celý Azure majetku provedením akcí u kořenové skupiny pro správu. Každý klient Azure AD se přiřadí nejvyšší úrovně správy skupinu s názvem kořenové skupiny pro správu. Tuto skupinu pro správu kořenového je integrovaná do hierarchie, které chcete mít všechny skupiny pro správu a odběry přeložte do ní. Tato skupina umožňuje globální zásady a přiřazení RBAC má být použita na úrovni adresáře. 

Skupina pro správu kořenového je vytvořena automaticky při provedení některé z následujících akcí: 
1. Vyjádřit výslovný souhlas použití skupin pro správu Azure tak, že přejdete do **skupin pro správu** v [portál Azure](https://portal.azure.com).
2. Vytvoření skupiny pro správu prostřednictvím volání rozhraní API.
3. Vytvoření skupiny pro správu pomocí prostředí PowerShell.

Podrobný přehled skupin pro správu, najdete v článku [uspořádání prostředků se skupinami pro správu Azure](../azure-resource-manager/management-groups-overview.md) článku.

## <a name="create-a-management-group-in-the-azure-portal"></a>Vytvoření skupiny pro správu na portálu Azure
Můžete uspořádat odběry do skupiny pro správu a zásad řízení ke skupinám pro správu. Všechny odběry v rámci skupiny pro správu automaticky dědí zásady uplatněné na skupině pro správu. Při skupin pro správu nejsou potřeba zařadit Security Center, důrazně doporučujeme vytvořit alespoň jednu skupinu pro správu, tak se vytvoří skupinu root management. Po vytvoření skupiny všechny odběry v klientovi Azure AD se propojit k němu. Pokyny pro prostředí PowerShell a další informace najdete v tématu [vytvoření skupiny pro správu pro správu prostředků a organizace](../azure-resource-manager/management-groups-create.md).

 
1. Přihlaste se k [portálu Azure](http://portal.azure.com).
2. Vyberte **všechny služby** > **skupin pro správu**.
3. Na hlavní stránce vyberte **skupiny pro správu nové.** 

    ![Hlavní skupiny](./media/security-center-management-groups/main.png) 
4.  Vyplňte pole ID skupiny správy. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odeslání příkazů na tuto skupinu pro správu. Tento identifikátor není upravovat po vytvoření, protože v celém systému Azure se používá k identifikaci této skupiny. 
    - Pole název zobrazení je název, který se zobrazí na portálu Azure. Samostatné zobrazovaný název je volitelné pole při vytváření správy skupiny a můžete kdykoli změnit.  

      ![Vytvořit](./media/security-center-management-groups/create_context_menu.png)  
5.  Vyberte **uložit**

### <a name="view-management-groups-in-the-azure-portal"></a>Zobrazení skupiny pro správu na portálu Azure
1. Přihlaste se k [portál Azure](http://portal.azure.com).
2. Chcete-li zobrazit skupiny pro správu, vyberte **všechny služby** v hlavní nabídce Azure.
3. V části **Obecné**, vyberte **skupin pro správu**.

    ![Vytvoření skupiny pro správu](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Udělují viditelnost na úrovni klienta a umožňuje přiřadit zásady

Získáte tak přehled do postavení zabezpečení všechny odběry registrované v klientovi Azure AD, je potřeba přiřadit kořenové skupiny pro správu role RBAC s dostatečná oprávnění ke čtení.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení oprávnění přístupu pro globálního správce ve službě Azure Active Directory
Správce klienta Azure Active Directory nemá přímý přístup k předplatným služby Azure. Však jako správce adresáře, mají práva k sami zvýšení oprávnění k roli, která nemá přístup. Správce klienta Azure AD je potřeba zvýšit samotné správce přístupu uživatelů na kořenové úrovni skupiny správy, se můžete přiřadit role RBAC. Pokyny pro PowerShell a další informace najdete v tématu [zvýšení přístupu pro globálního správce ve službě Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Přihlaste se k [portál Azure](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V seznamu navigace klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

   ![Azure AD – vlastnosti – snímek obrazovky](./media/security-center-management-groups/aad-properties.png)

3. V části **globálního správce může spravovat předplatná Azure a skupiny pro správu**, nastavení přepínače **Ano**.

   ![Globální správce může spravovat předplatná Azure a skupiny pro správu – snímek obrazovky](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Pokud nastavíte přepínač na **Ano**, váš účet globálního správce (aktuálně přihlášeného uživatele) je přidán do role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (`/`), který uděluje přístup k zobrazení a sestavy na Všechna předplatná Azure přidružená klientovi Azure AD.

   - Pokud nastavíte přepínač na **ne**, váš účet globálního správce (aktuálně přihlášeného uživatele) je odebrán z role správce přístupu uživatelů v Azure RBAC. Nelze zobrazit všechny odběry služby Azure přidružených ke klientovi Azure AD, a můžete zobrazit a spravovat pouze předplatná Azure ke kterým máte přístup.

4. Klikněte na tlačítko **Uložit** uložit vaše nastavení.

    - Toto nastavení není globální vlastnost a platí pouze pro aktuálně přihlášeného uživatele.

5. Proveďte úlohy, které musíte udělat na přístup se zvýšeným oprávněním. Když jste hotovi, nastavení přepínače zpět na **ne**.

### <a name="assign-rbac-roles-to-users"></a>Přiřadit role RBAC uživatelům
Jakmile správce klienta s vyšší úrovní přístupu, se můžete přiřadit role RBAC uživatelům důležité na kořenové úrovni skupiny správy. Doporučenou rolí přiřadit je [ **čtečky**](../role-based-access-control/built-in-roles.md#reader). Tato role je nutné k zajištění viditelnost na úrovni klienta. Přiřazené role se automaticky rozšíří do všech skupin pro správu a předplatná kořenové skupiny pro správu. Další informace o rolích RBAC najdete v tématu [dostupných rolí](../active-directory/active-directory-assign-admin-roles-azure-portal.md#available-roles).

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Spusťte následující příkazy: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Pokud budete vyzváni, přihlaste se pomocí přihlašovacích údajů globálního správce. 

    ![Přihlaste se výzva – snímek obrazovky](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udělte oprávnění role čtečky spuštěním následujícího příkazu:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Chcete-li odebrat roli, použijte následující příkaz: 

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
Jakmile byly přiřazeny role RBAC uživatelům, měli sám sebe z role správce přístupu uživatelů odebrat správce klienta.

1. Přihlaste se k [portál Azure](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

2. V seznamu navigace klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

3. V části **globálního správce může spravovat předplatná Azure a skupiny pro správu**, nastavení přepínače **ne**.

4. Klikněte na tlačítko **Uložit** uložit vaše nastavení.

### <a name="open-or-refresh-security-center"></a>Otevřete nebo aktualizujte Security Center
Po přiřazení role RBAC, otevřete nebo aktualizujte Azure Security Center k ověření, že máte přehled o všech odběrů v klientovi Azure AD. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Vyberte **všechny služby** v rámci Azure hlavní nabídky zvolte **Security Center**.
3. V **přehled**, je graf pokrytí předplatného. 
    ![Předplatné pokrytí grafu – snímek obrazovky](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. Klikněte na **pokrytí** zobrazíte seznam zahrnutých odběrů. 
    ![Snímek obrazovky seznamu pokrytí předplatného](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>Přidání odběrů do skupiny pro správu
Odběry můžete přidat do skupiny pro správu, který jste vytvořili. Tyto kroky nejsou povinné pro získání klienta celou viditelnost a globálních zásad a správu přístupu.

1. V části **skupin pro správu**, vyberte skupinu pro správu pro přidání předplatného.

    ![Vyberte skupiny pro správu přidat předplatné](./media/security-center-management-groups/management-group-subscriptions.png)

2. Vyberte **přidat existující**.

    ![Přidat existující](./media/security-center-management-groups/add-existing.png)

3. Zadejte předplatné pod **přidat existující prostředek** a klikněte na tlačítko **Uložit**.

4. Opakujte kroky 1 až 3, dokud nejsou přidány všechny odběry v oboru.

 > [!NOTE]
 > Skupiny pro správu může obsahovat odběry a podřízených skupin pro správu. Při přiřazení uživatele roli RBAC do nadřazené skupiny pro správu přístupu zdědí podřízené skupiny správy předplatných. Zásady nastavené v nadřazené skupiny pro správu jsou také zděděny podřízené objekty. 

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili vidí klienta úrovni pro Azure Security Center. Další informace o službě Security Center najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Monitorování stavu zabezpečení pomocí Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Spravovat a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md)

