---
title: 'Rychlý Start: vytvoření sdíleného dotazu pomocí Azure PowerShell'
description: V tomto rychlém startu budete postupovat podle pokynů k vytvoření sdíleného dotazu Resource graphu pomocí Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981540"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Rychlý Start: vytvoření sdíleného dotazu pro diagram prostředku pomocí Azure PowerShell

Tento článek popisuje, jak můžete vytvořit sdílený dotaz Azure Resource Graph pomocí modulu [AZ. ResourceGraph](/powershell/module/az.resourcegraph) PowerShellu.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > I když je modul PowerShell **AZ. ResourceGraph** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Vytvoření sdíleného dotazu pro diagram prostředků

Pomocí modulu PowerShellu **AZ. ResourceGraph** , který jste přidali do vašeho prostředí podle vlastního výběru, je čas vytvořit sdílený dotaz pro diagram prostředků. Sdílený dotaz je objekt Azure Resource Manager, kterému můžete udělit oprávnění nebo spustit v Průzkumníku Azure Resource Graph. Dotaz shrnuje počet všech prostředků seskupených podle _umístění_.

1. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) k uložení sdíleného dotazu Azure Resource Graph. Tato skupina prostředků má název `resource-graph-queries` a umístění je `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Pomocí modulu příkaz **AZ. ResourceGraph** PowerShell a rutiny [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) vytvořte sdílený dotaz Azure Resource Graph:

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Vypíše sdílené dotazy v nové skupině prostředků. Rutina [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) vrací pole hodnot.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Chcete-li získat pouze jeden sdílený výsledek dotazu, použijte `Get-AzResourceGraphQuery` s jeho `Name` parametrem.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat sdílený dotaz a skupinu prostředků grafu prostředků z prostředí Azure, můžete to udělat pomocí následujících příkazů:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili sdílený dotaz Resource Graph pomocí Azure PowerShell. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)