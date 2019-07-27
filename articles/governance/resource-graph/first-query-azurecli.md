---
title: Spuštění prvního dotazu pomocí rozhraní příkazového řádku Azure
description: Tento článek vás provede po krocích povolením rozšíření Resource Graph pro rozhraní příkazového řádku Azure CLI a spuštěním svého prvního dotazu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: afe32c197b5eab24ba298395bb2c9e83f847d6c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562569"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Rychlý start: Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Prvním krokem k použití služby Azure Resource Graph je zkontrolovat, že je nainstalované rozšíření pro [ Azure CLI ](/cli/azure/). Tento rychlý start vás provede procesem přidání rozšíření k instalaci rozhraní příkazového řádku Azure CLI. Rozšíření můžete použít pomocí Azure CLI nainstalované místně nebo prostřednictvím [Azure Cloud Shell](https://shell.azure.com).

Na konci tohoto procesu budete mít za sebou přidání rozšíření k vybrané instalaci Azure CLI a spuštění prvního dotazu na službu Resource Graph.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="add-the-resource-graph-extension"></a>Přidat rozšíření Resource Graph

K povolení rozhraní příkazového řádku Azure CLI k dotazu Azure Resource Graph, je nutné přidat rozšíření. Toto rozšíření funguje bez ohledu na to rozhraní příkazového řádku Azure je možné, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné a uvnitř portálu), [image Dockeru rozhraní příkazového řádku Azure](https://hub.docker.com/r/microsoft/azure-cli/), nebo lokálně nainstalované.

1. Zkontrolujte, že je nainstalovaná nejnovější verze Azure CLI (alespoň **2.0.45**). Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. V prostředí Azure CLI podle vašeho výběru ho importujete pomocí následujícího příkazu:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ověřte, že rozšíření je nainstalované a má očekávanou verzi (minimálně **0.1.11**):

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
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Aktualizuje dotaz pro `order by` **Název** vlastnosti:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Tak se nejdřív omezí rozsah výsledků dotazu a ty se pak seřadí.

1. Aktualizujte dotaz tak, aby se nejprve výsledky seřadily podle názvu (nastavte `order by` na **Name**) a pak nastavte omezení (`limit`) na prvních pět výsledků:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Pokud se konečný dotaz spustí několikrát, za předpokladu, že se ve vašem prostředí nic nemění, budou vrácené výsledky konzistentní a podle očekávání – seřazené podle vlastnosti **Name**, ale stále s omezením na prvních pět výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat rozšíření prostředků grafu z prostředí příkazového řádku Azure CLI, můžete tak provést pomocí následujícího příkazu:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Další postup

- Získejte další informace o [dotazovacím jazyce](./concepts/query-language.md)
- Naučte se [prozkoumat zdroje](./concepts/explore-resources.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Zobrazit ukázky [Starter dotazy](./samples/starter.md)
- Zobrazit ukázky [Pokročilé dotazy](./samples/advanced.md)
- Váš názor na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)