---
title: 'Rychlý Start: váš první dotaz na Python'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení knihovny grafu prostředků pro Python a spuštění prvního dotazu.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 01e0fd5bfbc5f353474c8305eefc044390c3586a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845101"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Pythonu

Prvním krokem při použití Azure Resource graphu je ověření, že jsou nainstalované požadované knihovny pro Python. Tento rychlý Start vás provede procesem přidávání knihoven do instalace Pythonu.

Na konci tohoto procesu jste přidali knihovny do instalace Pythonu a spustili svůj první dotaz na graf prostředku.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Přidat knihovnu grafu prostředků

Aby bylo možné povolit Python dotazování na Azure Resource Graph, musí být knihovna přidána. Tato knihovna funguje bez ohledu na to, kde je možné použít Python, včetně [bash ve Windows 10](/windows/wsl/install-win10) nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaný nejnovější Python (minimálně **3,8**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [Python.org](https://www.python.org/downloads/).

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.5.1**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Rozhraní příkazového řádku Azure vyžaduje, aby Python mohl používat **ověřování založené** na rozhraní CLI v následujících příkladech. Informace o dalších možnostech najdete v tématu [ověřování pomocí knihoven pro správu Azure pro Python](/azure/developer/python/azure-sdk-authenticate).

1. Ověření prostřednictvím rozhraní příkazového řádku Azure

   ```azurecli
   az login
   ```

1. V prostředí Python dle výběru nainstalujte požadované knihovny pro Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Pokud je Python nainstalovaný pro všechny uživatele, musí být tyto příkazy spuštěné z konzoly se zvýšenými oprávněními.

1. Ověřte, zda byly knihovny nainstalovány. `azure-mgmt-resourcegraph` by měla být **2.0.0** nebo vyšší, `azure-mgmt-resource` měla by být **9.0.0** nebo vyšší a `azure-cli-core` měla by být **2.5.0** nebo vyšší.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

S knihovnami Python přidanými do vašeho prostředí podle vašeho výběru je čas si vyzkoušet jednoduchý dotaz pro graf prostředků. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí nainstalovaných knihoven a `resources` metody:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Aktualizujte volání `getresources` a změňte dotaz na `order by` vlastnost **Name** :

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Aktualizujte volání `getresources` a změňte dotaz tak, aby nejprve `order by` vlastnost **název** a pak `limit` na horních pět výsledků:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat nainstalované knihovny z prostředí Pythonu, můžete k tomu použít následující příkaz:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali knihovny grafu prostředků do prostředí Pythonu a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)
