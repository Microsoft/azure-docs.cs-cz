---
title: 'Rychlý Start: váš první dotaz rozhraní příkazového řádku Azure'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení rozšíření grafu prostředků pro Azure CLI a spuštění prvního dotazu.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917820"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Azure CLI

Prvním krokem k použití služby Azure Resource Graph je zkontrolovat, že je nainstalované rozšíření pro [ Azure CLI ](/cli/azure/). Tento rychlý start vás provede procesem přidání rozšíření k instalaci rozhraní příkazového řádku Azure CLI. Rozšíření můžete použít pomocí Azure CLI nainstalované místně nebo prostřednictvím [Azure Cloud Shell](https://shell.azure.com).

Na konci tohoto procesu budete mít za sebou přidání rozšíření k vybrané instalaci Azure CLI a spuštění prvního dotazu na službu Resource Graph.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Přidat rozšíření Resource Graph

K povolení rozhraní příkazového řádku Azure CLI k dotazu Azure Resource Graph, je nutné přidat rozšíření. Toto rozšíření funguje bez ohledu na to rozhraní příkazového řádku Azure je možné, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné a uvnitř portálu), [image Dockeru rozhraní příkazového řádku Azure](https://hub.docker.com/_/microsoft-azure-cli), nebo lokálně nainstalované.

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.0.76**). Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows).

1. V prostředí Azure CLI podle vašeho výběru ho importujete pomocí následujícího příkazu:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ověřte, že rozšíření je nainstalované a má očekávanou verzi (minimálně **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Když se rozšíření Azure CLI přidal do vašeho vybraného prostředí, můžete vyzkoušet jednoduchý dotaz na službu Resource Graph. Dotaz vrátí prvních pět zdrojů Azure pomocí **Názvem** a **Typem zdroje** každého zdroje.

1. Spusťte nejdřív první dotaz na Azure Resource Graph pomocí `graph` rozšíření a `query` příkazu:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Aktualizuje dotaz pro `order by`**Název** vlastnosti:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Aktualizujte dotaz tak, aby se nejprve výsledky seřadily podle názvu (nastavte `order by` na **Name**) a pak nastavte omezení (`limit`) na prvních pět výsledků:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat rozšíření prostředků grafu z prostředí příkazového řádku Azure CLI, můžete tak provést pomocí následujícího příkazu:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali rozšíření grafu prostředků do prostředí Azure CLI a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)
