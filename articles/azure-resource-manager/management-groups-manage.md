---
title: Jak změnit, odstranit nebo Správa skupin pro správu – Azure | Dokumentace Microsoftu
description: Zjistěte, jak spravovat a aktualizovat vaše hierarchie skupin správy.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: f7677d8b545c28522c370f3af422a55f3a656646
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091182"
---
# <a name="manage-your-resources-with-management-groups"></a>Správa vašich prostředků pomocí skupin pro správu

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů napříč několika předplatnými. Můžete změnit, odstranit a správu těchto kontejnerů mít hierarchie, které lze použít s [Azure Policy](../azure-policy/azure-policy-introduction.md) a [Azure Role přístupu na základě ovládacích prvků (RBAC)](../role-based-access-control/overview.md). Další informace o skupinách správy, najdete v článku [uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md).

Pokud chcete provést změny ve skupině pro správu, musíte mít roli vlastníka nebo přispěvatele skupiny pro správu. A zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat přístup a oprávnění pomocí RBAC](../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Změňte název skupiny pro správu

Název skupiny pro správu můžete změnit pomocí portálu, Powershellu nebo rozhraní příkazového řádku Azure.

### <a name="change-the-name-in-the-portal"></a>Změňte název na portálu

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**  
3. Vyberte skupiny pro správu, kterou chcete přejmenovat.
4. Vyberte **přejmenovat skupinu** možnosti v horní části stránky.

    ![Přejmenovat skupinu](media/management-groups/detail_action_small.png)
5. Po otevření nabídky, zadejte nový název, který chcete zobrazit.

    ![Přejmenovat skupinu](media/management-groups/rename_context.png)
6. Vyberte **Uložit**.

### <a name="change-the-name-in-powershell"></a>Změňte název v prostředí PowerShell

Aktualizovat pomocí názvu zobrazení **aktualizace AzureRmManagementGroup**. Například změnit název skupiny správy z "Contoso IT" do "Skupiny společnosti Contoso", spusťte následující příkaz: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Změňte název v rozhraní příkazového řádku Azure

Azure CLI použijte příkaz update.

```azurecli-interactive
az account management-group update --name Contoso --display-name "Contoso Group" 
```

---

## <a name="delete-a-management-group"></a>Odstranit skupinu pro správu

Pokud chcete odstranit skupinu pro správu, musí být splněny následující požadavky:

1. Neexistují žádné podřízené skupiny pro správu nebo předplatná v rámci skupiny pro správu.
    - Pokud chcete přejít u určitého předplatného mimo skupinu pro správu, přečtěte si téma [přesunout do jiné skupiny pro správu předplatného](#Move-subscriptions-in-the-hierarchy).
    - Přesunout skupinu pro správu do jiné skupiny pro správu, najdete v článku [přesunutí správy skupin v hierarchii](#Move-management-groups-in-the-hierarchy).
2. Máte oprávnění k zápisu na skupinu vlastníka nebo přispěvatele rolí správy skupiny pro správu. A zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat přístup a oprávnění pomocí RBAC](../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Odstranit na portálu

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**  
3. Vyberte skupiny pro správu, kterou chcete odstranit.
4. Vyberte **Odstranit**.
    - Pokud ikona je zakázaná, váš výběr myš najede myší na ikonu se dozvíte z důvodu.

    ![Odstranit skupinu](media/management-groups/delete.png)
5. Je okno, které se otevře, potvrzení, že chcete odstranit skupinu pro správu.

    ![Odstranit skupinu](media/management-groups/delete_confirm.png)
6. Vyberte **Ano**


### <a name="delete-in-powershell"></a>Odstranit v prostředí PowerShell

Použití **odebrat AzureRmManagementGroup** příkazu v Powershellu k odstranění skupin pro správu. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI

Pomocí Azure CLI použijte příkaz az účet skupiny pro správu odstranit.

```azurecli-interactive
az account management-group delete --name Contoso
```
---

## <a name="view-management-groups"></a>Zobrazení skupin pro správu

Můžete zobrazit všechny skupiny pro správu, kterou máte v roli RBAC s přímým přístupem nebo zděděná.  

### <a name="view-in-the-portal"></a>Zobrazit na portálu

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Hierarchie skupiny správy stránka načte, kde můžete prozkoumat všechny skupiny pro správu a předplatná, ke kterým máte přístup k. Výběr názvu skupiny přejdete dolů úrovně v hierarchii. Navigační funguje stejně jako Průzkumník souborů. 
    ![Hlavní](media/management-groups/main.png)
4. Pokud chcete zobrazit podrobnosti o skupině pro správu, vyberte **(podrobnosti)** odkaz vedle názvu skupiny pro správu. Pokud tento odkaz není k dispozici, nemáte oprávnění k zobrazení této skupiny pro správu.  

### <a name="view-in-powershell"></a>Zobrazení v prostředí PowerShell

Pomocí příkazu Get-AzureRmManagementGroup k načtení všech skupin.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Jedna skupina pro správu vaší informace použijte parametr - GroupName

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Zobrazení v rozhraní příkazového řádku Azure

Pomocí příkazu seznamu k načtení všech skupin.  

```azurecli-interactive
az account management-group list
```
Jedna skupina pro správu vaší informace použijte příkaz show

```azurecli-interactive
az account management-group show --name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Přesunout předplatná v hierarchii

Jedním z důvodů, chcete-li vytvořit skupinu pro správu je pohromadě předplatných. Podřízené položky z jiné skupiny pro správu lze provést pouze skupiny pro správu a předplatných. Předplatné, které se přesune do skupiny pro správu dědí všechny zásady a přístup uživatelů z nadřazené skupiny pro správu.

Pokud chcete přesunout předplatné, jsou několik oprávnění, které je potřeba:

- "Vlastník" role v podřízených předplatného.
- "Vlastník" nebo "Přispěvatel" role v nové skupině pro správu nadřazené.
- Role "Vlastník" nebo "Přispěvatel" staré nadřazené skupiny pro správu.

A zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat přístup a oprávnění pomocí RBAC](../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Přesunout předplatná na portálu

**Přidání existujícího předplatného do skupiny pro správu**

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**
3. Vyberte skupiny pro správu, které máte v plánu má být nadřazená.
4. V horní části stránky vyberte **přidat předplatné**.
5. Vyberte předplatné, v seznamu správné ID.

    ![Podřízená položka](media/management-groups/add_context_sub.png)
1. Vyberte možnost "Uložit"

**Přejít do jiné skupiny pro správu u určitého předplatného**

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Vyberte skupiny pro správu plánování, který je aktuálně nadřazeného.  
4. Vyberte tři tečky na konci řádku pro předplatné v seznamu, který chcete přesunout.

    ![Přesunout](media/management-groups/move_small.png)
5. Vyberte **přesunout**
6. V nabídce, která se otevře, vyberte **nadřazená skupina pro správu**.

    ![Přesunout](media/management-groups/move_small_context.png)
7. Vyberte **uložit**

### <a name="move-subscriptions-in-powershell"></a>Přesunout předplatná v prostředí PowerShell

Pokud chcete přesunout předplatné v prostředí PowerShell, použijte příkaz Přidat AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Chcete-li odebrat propojení mezi a předplatném a skupině pro správu použijte příkaz Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Přesun předplatných v Azure CLI

Pokud chcete přesunout předplatné v rozhraní příkazového řádku, použijte příkaz Přidat.

```azurecli-interactive
az account management-group subscription add --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Odebrat ze skupiny pro správu předplatného, použijte příkaz remove předplatného.  

```azurecli-interactive
az account management-group subscription remove --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Přesunutí správy skupin v hierarchii  

Při přesunu nadřazenou skupinu pro správu, všechny podřízené prostředky, které obsahují skupiny pro správu, předplatná, skupiny prostředků a přesun prostředků s nadřazenými.   

### <a name="move-management-groups-in-the-portal"></a>Přesunutí skupin pro správu na portálu

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**
3. Vyberte skupiny pro správu, které máte v plánu má být nadřazená.
5. V horní části stránky vyberte **přidat skupinu pro správu**.
6. V nabídce, která se otevře vyberte, pokud chcete novou, nebo použijte existující skupinu pro správu.
    - Nový výběr se vytvoří nová skupina pro správu.
    - Výběr existujícího zobrazíte rozevírací seznam všech skupin pro správu, které můžete přesunout do této skupiny pro správu.  

    ![Přesunutí](media/management-groups/add_context_MG.png)
7. Vyberte **uložit**

### <a name="move-management-groups-in-powershell"></a>Přesunutí skupin pro správu v prostředí PowerShell

Pomocí příkazu Update-AzureRmManagementGroup v prostředí PowerShell přesunout skupinu pro správu v jiné skupině.  

```powershell
Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  

### <a name="move-management-groups-in-azure-cli"></a>Přesunutí správy skupin v Azure CLI

Přesunout skupinu pro správu pomocí Azure CLI pomocí příkazu update.

```azurecli-interactive
az account management-group update --name Contoso --parent "Contoso Tenant"
```

---

## <a name="next-steps"></a>Další postup

Další informace o skupinách správy, naleznete v tématu:

- [Uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md)
- [Vytvoření skupin pro správu k uspořádání prostředků Azure](management-groups-create.md)
- [Instalace modulu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Projděte si specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Nainstalujte rozšíření Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
