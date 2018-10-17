---
title: Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI
description: Tento článek vás provede po krocích povolením rozšíření Resource Graph pro rozhraní příkazového řádku Azure CLI a spuštěním svého prvního dotazu.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: df01a57cda063e301efbc4d99f3bac8dbb4b2e66
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054162"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Prvním krokem k použití Azure Resource Graph je zajistit, aby bylo nainstalováno rozšíření [ Azure CLI ](/cli/azure/). Tento rychlý start vás provede procesem přidání rozšíření k instalaci rozhraní příkazového řádku Azure CLI. Rozšíření můžete použít pomocí Azure CLI nainstalované místně nebo prostřednictvím [Azure Cloud Shell](https://shell.azure.com).

Na konci tohoto procesu se provede přidání rozšíření k instalaci rozhraní příkazového řádku Azure CLI podle výběru a spuštění vaší úplně první dotaz na graf prostředků.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="add-the-resource-graph-extension"></a>Přidat rozšíření Resource Graph

K povolení rozhraní příkazového řádku Azure CLI k dotazu Azure Resource Graph, je nutné přidat rozšíření. Toto rozšíření funguje bez ohledu na to rozhraní příkazového řádku Azure je možné, včetně [bash ve Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (samostatné a uvnitř portálu), [image Dockeru rozhraní příkazového řádku Azure](https://hub.docker.com/r/microsoft/azure-cli/), nebo lokálně nainstalované.

1. Zkontrolujte, že je nainstalovaný nejnovější agent Azure CLI (minimálně **2.0.45**). Pokud ještě není nainstalovaná, postupujte podle [těchto pokynů](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. V prostředí Azure CLI podle vašeho výběru ho importujete pomocí následujícího příkazu:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ověřte, že bylo toto rozšíření nainstalováno v požadované (verzi minimálně **0.1.7**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Teď, když se rozšíření rozhraní příkazového řádku Azure CLI přidalo do vašeho prostředí podle výběru, můžete vyzkoušet jednoduchý dotaz na Resource Graph. Dotaz vrátí prvních pět zdrojů Azure pomocí **Názvem** a **Typem zdroje** každého zdroje.

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

1. Aktualizujte dotaz na `order by` **název** vlastnosti a potom `limit` na prvních 5 výsledků:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Pokud konečný dotaz proběhne více než jednou za předpokladu, že se ve vašem prostředí nic nemění, vrácené výsledky budou konzistentní jak se očekává – seřazené podle **názvu** vlastností, ale stále s omezením na prvních 5 výsledků.

## <a name="cleanup"></a>Vyčištění

Pokud chcete odebrat rozšíření prostředků grafu z prostředí příkazového řádku Azure CLI, můžete tak provést pomocí následujícího příkazu:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> Tím se neodstraní dříve stažený soubor s rozšířením. Pouze ho odebere ze spuštěného prostředí Azure CLI.

## <a name="next-steps"></a>Další kroky

- Získejte další informace o [dotazovacím jazyce](./concepts/query-language.md)
- Naučte se [prozkoumat zdroje](./concepts/explore-resources.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Zobrazit ukázky [Starter dotazy](./samples/starter.md)
- Zobrazit ukázky [Pokročilé dotazy](./samples/advanced.md)
- Váš názor na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)