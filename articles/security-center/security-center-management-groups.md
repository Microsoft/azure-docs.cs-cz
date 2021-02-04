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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556428"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Uspořádání skupin pro správu, předplatných a viditelnosti v nejrůznějších klientech

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


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Udělení oprávnění na úrovni tenanta sobě

Uživatel s rolí Azure Active Directory (AD) **globálního správce** může mít odpovědnost v rámci tenanta, ale nemá oprávnění Azure zobrazovat informace o celé organizaci v Azure Security Center. Vyžaduje se zvýšení oprávnění, protože přiřazení role Azure AD neuděluje přístup k prostředkům Azure. 

> [!TIP]
> Přečtěte si další informace o zvýšení oprávnění pro roli globálního správce v části [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../role-based-access-control/elevate-access-global-admin.md).

Přiřazení oprávnění na úrovni tenanta:

1. Pokud vaše organizace spravuje přístup k prostředkům s [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)nebo jakýmkoli jiným nástrojem PIM, musí být globální role správce pro uživatele aktivní podle níže uvedeného postupu.

1. Jako globální uživatel správce bez přiřazení do kořenové skupiny pro správu tenanta otevřete stránku **přehled** Security Center a v proužku vyberte odkaz **viditelnost v rámci tenanta** . 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Povolit oprávnění na úrovni tenanta v Azure Security Center":::

1. Vyberte novou roli Azure, která se má přiřadit. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulář pro definování oprávnění na úrovni tenanta, která se mají přiřadit uživateli":::

    > [!TIP]
    > Obecně platí, že role správce zabezpečení je nutná k uplatnění zásad na kořenové úrovni, zatímco čtečka zabezpečení bude stačit k poskytování viditelnosti na úrovni tenanta. Další informace o oprávněních udělených těmito rolemi najdete v [popisu předdefinované role správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) nebo v [popisu předdefinované role čtenáře zabezpečení](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Rozdíly mezi těmito rolemi, které jsou specifické pro Security Center, najdete v tabulce v části [role a povolené akce](security-center-permissions.md#roles-and-allowed-actions).

    Zobrazení v celé organizaci se dosahuje přidělením rolí na úrovni kořenové skupiny pro správu tenanta.  

1. Odhlaste se od Azure Portal a pak se znovu přihlaste.

1. Jakmile budete mít zvýšený přístup, otevřete nebo aktualizujte Azure Security Center a ověřte si, že máte přehled o všech předplatných v rámci vašeho tenanta Azure AD. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Požádat o oprávnění na úrovni tenanta, pokud není dostatečná

Pokud se přihlásíte k Security Center a zobrazí se informační zpráva s oznámením, že vaše zobrazení je omezené, můžete kliknout na tlačítko pro odeslání žádosti globálnímu správci vaší organizace. V žádosti můžete zahrnout roli, kterou chcete přiřadit, a globální správce provede rozhodnutí o tom, která role se má udělit. 

Jedná se o rozhodnutí globálního správce, jestli chcete tyto žádosti přijmout nebo odmítnout. 

> [!IMPORTANT]
> Jednu žádost můžete odeslat jenom každých 7 dní.

Chcete-li požádat o zvýšená oprávnění od globálního správce:

1. Z Azure Portal otevřete Azure Security Center.

1. Pokud se zobrazí nápis "zobrazují se omezené informace". vyberte ji.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner s informující uživatele, který může požádat o oprávnění na úrovni tenanta.":::

1. Ve formuláři podrobný požadavek vyberte požadovanou roli a odůvodnění, proč potřebná oprávnění potřebujete.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Stránka podrobností pro vyžádání oprávnění na úrovni tenanta od globálního správce Azure":::

1. Vyberte **požádat o přístup**.

    Globálnímu správci se pošle e-mail. E-mail obsahuje odkaz na Security Center, kde může žádost schválit nebo odmítnout.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-mail globálnímu správci pro nová oprávnění":::

    Poté, co globální správce vybere **kontrolu žádosti** a dokončí proces, rozhodnutí se odešle e-mailem žádajícímu uživateli. 

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
V tomto článku jste se dozvěděli, jak získat Azure Security Center viditelnosti v úrovni tenanta. Související informace najdete v těchto tématech:

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)