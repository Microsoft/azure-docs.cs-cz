---
title: Spuštění prvního dotazu pomocí rozhraní příkazového řádku Azure
description: Tento článek vás provede jednotlivými kroky pro povolení rozšíření grafu prostředků pro Azure CLI a spuštění prvního dotazu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 34325fe8e8fdf7f53eb730818dc57800b65076e2
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976713"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Azure CLI

Prvním krokem při použití Azure Resource graphu je ověření, že je nainstalované rozšíření pro rozhraní příkazového [řádku Azure](/cli/azure/) . Tento rychlý Start vás provede procesem přidání rozšíření do instalace Azure CLI. Rozšíření můžete použít s místně nainstalovaným rozhraním Azure CLI nebo prostřednictvím [Azure Cloud Shell](https://shell.azure.com).

Na konci tohoto procesu jste přidali rozšíření k instalaci Azure CLI a spustili jste první dotaz na graf prostředku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="add-the-resource-graph-extension"></a>Přidat rozšíření grafu prostředků

Aby bylo možné povolit Azure CLI dotazování na Azure Resource Graph, je nutné rozšíření přidat. Toto rozšíření funguje bez ohledu na to, kde se dá použít Azure CLI, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné i uvnitř portálu), [Image Docker Azure CLI](https://hub.docker.com/r/microsoft/azure-cli/)nebo lokálně nainstalovaná.

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.0.45**). Pokud ještě není nainstalovaná, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. V prostředí Azure CLI, které si vyberete, ho importujte pomocí následujícího příkazu:

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

## <a name="run-your-first-resource-graph-query"></a>Spustit váš první dotaz na diagram prostředku

Když je rozšíření Azure CLI přidané do vašeho prostředí dle vašeho výběru, je čas si vyzkoušet jednoduchý dotaz na graf prostředků. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí rozšíření `graph` a příkazu `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Vzhledem k tomu, že tento příklad dotazu neposkytuje modifikátor řazení, jako je například `order by`, spuštění tohoto dotazu bude pravděpodobně zajišťovat jinou sadu prostředků na žádost.

1. Aktualizujte dotaz tak, aby `order by` vlastnost **Name** :

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu může spuštění tohoto dotazu několikrát vyvolat jinou sadu prostředků na žádost. Pořadí příkazů dotazu je důležité. V tomto příkladu `order by` přichází za `limit`. Tím se nejdřív omezí výsledky dotazu a pak se seřadí.

1. Aktualizujte dotaz na první @no__t hodnotu vlastnosti **Name** a pak `limit` na prvních 5 výsledků:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Když se konečný dotaz několikrát spustí, za předpokladu, že se nic ve vašem prostředí nemění, vrácené výsledky budou konzistentní a podle očekávání – seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete z prostředí Azure CLI odebrat rozšíření grafu prostředků, můžete k tomu použít následující příkaz:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Další kroky

- Získat další informace o [dotazovacím jazyku](./concepts/query-language.md)
- Naučte se [prozkoumat prostředky](./concepts/explore-resources.md) .
- Spusťte první dotaz pomocí [Azure PowerShell](first-query-powershell.md)
- Zobrazit ukázky [počátečních dotazů](./samples/starter.md)
- Zobrazit ukázky [pokročilých dotazů](./samples/advanced.md)
- Poskytnutí zpětné vazby na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)