---
title: 'Rychlý Start: vytvoření sdíleného dotazu pomocí Azure CLI'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení rozšíření grafu prostředků pro Azure CLI a vytvoření sdíleného dotazu.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: c888cee5899c45747db1775ffdfbc0d0c78e00c7
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667516"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Rychlý Start: vytvoření sdíleného dotazu v diagramu prostředků pomocí Azure CLI

Prvním krokem při použití Azure Resource graphu pomocí [Azure CLI](/cli/azure/) je ověření, že je rozšíření nainstalované. Tento rychlý start vás provede procesem přidání rozšíření k instalaci rozhraní příkazového řádku Azure CLI. Rozšíření můžete použít pomocí Azure CLI nainstalované místně nebo prostřednictvím [Azure Cloud Shell](https://shell.azure.com).

Na konci tohoto procesu se přidá rozšíření k instalaci Azure CLI a vytvoří se sdílený dotaz pro diagram prostředků.

## <a name="prerequisites"></a>Požadované součásti

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Přidat rozšíření Resource Graph

Pokud chcete povolit Azure CLI pro práci s Azure Resource graphem, musí se rozšíření přidat. Toto rozšíření funguje bez ohledu na to rozhraní příkazového řádku Azure je možné, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné a uvnitř portálu), [image Dockeru rozhraní příkazového řádku Azure](https://hub.docker.com/_/microsoft-azure-cli), nebo lokálně nainstalované.

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.8.0**). Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows).

1. V prostředí Azure CLI podle vlastního výběru pomocí příkazu [AZ Extension Add](/cli/azure/extension#az-extension-add) importujte rozšíření grafu prostředků s následujícím příkazem:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ověřte, že rozšíření je nainstalované a má očekávanou verzi (minimálně **1.1.0**) se [seznamem AZ Extension](/cli/azure/extension#az-extension-list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Vytvoření sdíleného dotazu pro diagram prostředků

S rozšířením Azure CLI přidaným do vašeho prostředí podle vašeho výběru je čas na sdílený dotaz grafu prostředků. Sdílený dotaz je objekt Azure Resource Manager, kterému můžete udělit oprávnění nebo spustit v Průzkumníku Azure Resource Graph. Dotaz shrnuje počet všech prostředků seskupených podle _umístění_.

1. Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) a uložte sdílený dotaz Azure Resource Graph. Tato skupina prostředků má název `resource-graph-queries` a umístění je `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Pomocí `graph` rozšíření a příkazu [AZ Graph Shared-Query Create](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create) vytvořte sdílený dotaz Azure Resource Graph:

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Vypíše sdílené dotazy v nové skupině prostředků. Příkaz [AZ Graph Shared-Query list](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) vrátí pole hodnot.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Chcete-li získat pouze jeden sdílený výsledek dotazu, použijte příkaz [AZ Graph Share-Query show](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show) .

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Spusťte v Azure CLI sdílený dotaz pomocí `{{shared-query-uri}}` syntaxe v příkazu [AZ Graph Query](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query) .
   Nejdřív zkopírujte `id` pole z výsledku `show` příkazu výše. Nahraďte `shared-query-uri` text v příkladu hodnotou z `id` pole, ale ponechte okolní `{{` `}}` znaky a.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}`Syntaxe je funkce ve **verzi Preview** .

Dalším způsobem, jak najít sdílené dotazy grafu prostředků, je prostřednictvím Azure Portal. Na portálu pomocí panelu hledání vyhledejte "dotazy na grafy prostředků". Vyberte sdílený dotaz. Na stránce **Přehled** se zobrazí na kartě **dotaz** uložený dotaz. Tlačítko **Upravit** se otevře v [Průzkumníku grafu prostředků](./first-query-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete z prostředí Azure CLI odebrat sdílený dotaz, skupinu prostředků a rozšíření grafu prostředků, můžete to udělat pomocí následujících příkazů:

- [AZ Graph Shared-Query DELETE](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [AZ Extension Remove](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali rozšíření grafu prostředků do prostředí Azure CLI a vytvořili jste sdílený dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)