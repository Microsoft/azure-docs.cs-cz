---
title: Jak změnit, odstranit nebo spravovat skupiny pro správu - Azure | Microsoft Docs
description: Zjistěte, jak udržovat a aktualizovat vaše hierarchie skupin správy.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: rithorn
ms.openlocfilehash: 0a13627232904f4b14cdb5cbf5c3ca927d9ea167
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754497"
---
# <a name="manage-your-resources-with-management-groups"></a>Spravovat prostředky s skupin pro správu 
Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v rámci více předplatných. Můžete změnit, odstranit a správě těchto kontejnerů do mají hierarchie, které lze použít s [zásad Azure](../azure-policy/azure-policy-introduction.md) a [Azure Role přístupu na základě ovládacích prvků (RBAC)](../role-based-access-control/overview.md). Další informace o skupinách správy najdete v tématu [uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md).

Funkce skupiny správy je dostupná ve verzi public preview. K použití skupin pro správu, přihlaste se k [portál Azure](https://portal.azure.com) nebo můžete použít [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), nebo [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) na Spravujte skupiny pro správu.

Změnit skupinu pro správu, musí mít roli vlastníka nebo přispěvatele skupiny pro správu. Chcete-li zjistit, jaká oprávnění mají, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat oprávnění a přístupová práva pomocí RBAC](../role-based-access-control/overview.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Změňte název skupiny pro správu 
Název skupiny pro správu můžete změnit pomocí portálu, prostředí PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="change-the-name-in-the-portal"></a>Změňte název na portálu

1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**  
3. Vyberte skupinu pro správu, kterou chcete přejmenovat. 
4. Vyberte **přejmenovat skupinu** možnost v horní části stránky. 

    ![Přejmenovat skupinu](media/management-groups/detail_action_small.png)
5. Když se otevře v nabídce, zadejte nový název, který chcete zobrazit.

    ![Přejmenovat skupinu](media/management-groups/rename_context.png) 
4. Vyberte **Uložit**. 

### <a name="change-the-name-in-powershell"></a>Změňte název v prostředí PowerShell

K aktualizaci použijte název zobrazení **aktualizace AzureRmManagementGroup**. Například změnit název skupiny správy z "IT společnosti Contoso" na "Skupina společnosti Contoso", spusťte následující příkaz: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Změňte název v rozhraní příkazového řádku Azure

Pokud používáte Azure CLI použijte příkaz aktualizace. 

```azurecli-interactive
az account management-group update --name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Odstranit skupinu pro správu
Pokud chcete odstranit skupinu pro správu, musí být splněny následující požadavky:
1. Neexistují žádné podřízené skupiny pro správu nebo předplatných v rámci skupiny pro správu. 
    - Pro přesun předplatného mimo skupinu pro správu, najdete v části [předplatné přesunout do jiné skupiny managemnt](#Move-subscriptions-in-the-hierarchy). 
    - Přesunout skupinu pro správu do jiné skupiny pro správu, najdete v tématu [přesunutí skupin pro správu v hierarchii](#Move-management-groups-in-the-hierarchy). 
2. Máte oprávnění k zápisu na skupinu vlastníkem nebo přispěvatelem roli správy skupiny pro správu. Chcete-li zjistit, jaká oprávnění mají, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o role RBAC najdete v tématu [spravovat oprávnění a přístupová práva pomocí RBAC](../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Odstranit na portálu

1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu**  
3. Vyberte skupinu pro správu, které chcete odstranit. 
    
    ![Odstranění skupiny](media/management-groups/delete.png)
4. Vyberte **Odstranit**. 
    - Pokud je zakázána na ikonu, vaše myši pro výběr ukazatele myši na ikonu se dozvíte z důvodu. 
5. Je okno, které se otevře, potvrzení, že chcete odstranit skupinu pro správu. 

    ![Odstranění skupiny](media/management-groups/delete_confirm.png) 
6. Vyberte **Ano** 


### <a name="delete-in-powershell"></a>Odstranit v prostředí PowerShell

Použití **odebrat AzureRmManagementGroup** příkaz prostředí PowerShell k odstranění skupin pro správu. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI
Pomocí rozhraní příkazového řádku Azure použijte příkaz az účet skupiny pro správu odstraňte. 

```azurecli-interactive
az account management-group delete --name Contoso
```
---

## <a name="view-management-groups"></a>Zobrazení skupiny pro správu
Můžete zobrazit všechny skupiny pro správu, které máte přímý nebo zděděné role RBAC na.  

### <a name="view-in-the-portal"></a>Zobrazit na portálu
1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Hierarchie skupiny správy stránky zatížení, kde můžete prozkoumat všechny skupiny pro správu a odběry, které máte přístup. Výběr názvu skupiny přejdete dolů úrovně v hierarchii. Navigačním funguje stejně jako soubor explorer. 
    ![Main](media/management-groups/main.png)
4. Pokud chcete zobrazit podrobnosti o skupině pro správu, vyberte **(podrobnosti)** odkaz vedle názvu skupiny pro správu. Pokud tento odkaz není dostupná, nemáte oprávnění k zobrazení této skupiny pro správu.  

### <a name="view-in-powershell"></a>Zobrazení v prostředí PowerShell
Můžete použít příkaz Get-AzureRmManagementGroup načíst všechny skupiny.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Pro skupinu správy informace použijte parametr - GroupName

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Zobrazení v rozhraní příkazového řádku Azure
Můžete použít příkaz seznamu načíst všechny skupiny.  

```azurecli-interactive
az account management-group list
```
Pro skupinu správy informace použijte příkaz Zobrazit

```azurecli-interactive
az account management-group show --name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Přesunout odběry v hierarchii
Jedním z důvodů pro vytvoření skupiny pro správu je vytvořit balíček odběrů společně. Podřízené objekty jiné skupiny pro správu lze provést pouze skupiny pro správu a odběry. Předplatné, které přesune do skupiny pro správu zdědí všechny zásady přístupu uživatele a z nadřazené skupiny pro správu. 

Pokud chcete přesunout předplatné, jsou k dispozici několik oprávnění, které je potřeba: 
- "Vlastník" role v podřízených předplatné.
- "Vlastník" nebo "Přispěvatel" role v nové skupině pro správu nadřazené. 
- "Vlastník" nebo "Přispěvatel" role staré nadřazené skupiny pro správu.
Chcete-li zjistit, jaká oprávnění mají, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o role RBAC najdete v tématu [spravovat oprávnění a přístupová práva pomocí RBAC](../role-based-access-control/overview.md). 

### <a name="move-subscriptions-in-the-portal"></a>Přesunout předplatná v portálu

**Přidat existujícímu předplatnému do skupiny pro správu**
1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Vyberte skupinu pro správu, které chcete být nadřazená.      
5. V horní části stránky, vyberte **přidat existující**. 
6. V nabídce, která otevřít, vyberte **typ prostředku** položky, které se pokoušíte přesunout, což je **předplatné**.
7. Vyberte odběr, v seznamu se správné ID. 

    ![Podřízená položka](media/management-groups/add_context_2.png)
8. Vyberte "Uložit."

**Odebrat předplatné ze skupiny pro správu**
1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Vyberte plánování tedy nadřazenou skupinu pro správu.  
4. Vyberte v seznamu, který chcete přesunout se třemi tečkami na konci řádku pro předplatné.

    ![Přesunout](media/management-groups/move_small.png)
5. Vyberte **přesunout**
6. V nabídce, které se otevře, vyberte **nadřazené skupiny pro správu**.

    ![Přesunout](media/management-groups/move_small_context.png)
7. Vyberte **uložit**

### <a name="move-subscriptions-in-powershell"></a>Přesunout odběry v prostředí PowerShell
Pokud chcete přesunout předplatné v prostředí PowerShell, můžete použít příkaz AzureRmManagementGroupSubscription přidat.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Chcete-li odebrat propojení mezi a předplatného a skupiny pro správu použijte příkaz Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Přesunout odběry v rozhraní příkazového řádku Azure
Pokud chcete přesunout předplatné v rozhraní příkazového řádku, můžete použít příkaz Přidat. 

```azurecli-interactive
az account management-group subscription add --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Odebrat ze skupiny pro správu předplatného, použijte příkaz remove předplatné.  

```azurecli-interactive
az account management-group subscription remove --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Přesunutí skupin pro správu v hierarchii  
Když přesunete nadřazenou skupinu pro správu, všechny podřízené prostředky, které obsahují skupiny pro správu, odběry, skupiny prostředků a přesun prostředků se nadřazenou položkou.   

### <a name="move-management-groups-in-the-portal"></a>Přesunutí skupin pro správu na portálu

1. Přihlaste se [portálu Azure](https://portal.azure.com)
2. Vyberte **všechny služby** > **skupin pro správu** 
3. Vyberte skupinu pro správu, které chcete být nadřazená.      
5. V horní části stránky, vyberte **přidat existující**.
6. V nabídce, která otevřít, vyberte **typ prostředku** položky, které se pokoušíte přesunout, což je **skupiny pro správu**.
7. Vyberte skupinu pro správu s správné ID a název.

    ![Přesunutí](media/management-groups/add_context.png)
8. Vyberte **uložit**

### <a name="move-management-groups-in-powershell"></a>Přesunutí skupin pro správu v prostředí PowerShell
Použijte příkaz Update-AzureRmManagementGroup v prostředí PowerShell přesunout skupinu pro správu v rámci jiné skupiny.  

```powershell
Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Přesunutí skupin pro správu v rozhraní příkazového řádku Azure
Použijte příkaz update přesunout skupinu pro správu pomocí rozhraní příkazového řádku Azure. 

```azurecli-interactive
az account management-group update --name Contoso --parent "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Další postup 
Další informace o skupinách správy najdete v tématu: 
- [Uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md)
- [Vytvoření skupiny pro správu k uspořádání prostředků Azure](management-groups-create.md)
- [Instalace modulu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Zkontrolujte specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Nainstalujte rozšíření rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
