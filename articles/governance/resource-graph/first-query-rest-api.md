---
title: 'Rychlý Start: první REST API dotaz'
description: V tomto rychlém startu budete postupovat podle pokynů pro volání koncového bodu grafu prostředků pro REST API a spuštění prvního dotazu.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920050"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí REST API

Prvním krokem při použití Azure Resource graphu s REST API je ověřit, jestli máte k dispozici nástroj pro volání rozhraní REST API. Tento rychlý Start vás provede procesem spuštění dotazu a načtením výsledků voláním koncového bodu Azure Resource Graph REST API.

Na konci tohoto procesu budete mít nástroje pro volání koncových bodů REST API a spuštění prvního dotazu na diagram prostředku.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Začínáme s REST API

Pokud rozhraní REST API neznáte, nejprve si přečtěte [referenční informace k rozhraní Azure REST API](/rest/api/azure/), kde najdete obecný přehled rozhraní REST API, zejména identifikátoru URI požadavku a textu požadavku. Tento článek používá tyto koncepty k poskytnutí pokynů pro práci s Azure Resource graphem a předpokládá, že je jejich znalost funkční. Začátečníkům se doporučuje používat k automatické autorizaci nástroje, jako je [ARMClient](https://github.com/projectkudu/ARMClient) a další.

Specifikace grafu prostředků Azure najdete v tématu [REST API Azure Resource graphu](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>REST API a PowerShell

Pokud nemáte nástroj na volání REST API, můžete k zadání těchto pokynů použít PowerShell. Následující ukázka kódu získá hlavičku pro ověřování pomocí Azure. Vygenerujte ověřovací hlavičku, která je někdy označovaná jako **nosný token**, a zadejte identifikátor URI v REST API pro připojení s libovolnými parametry nebo **text žádosti**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

`{subscriptionId}` `$restUri` Chcete-li získat informace o vašem předplatném, nahraďte proměnnou v proměnné.
`$response`Proměnná obsahuje výsledek `Invoke-RestMethod` rutiny, která se dá analyzovat pomocí rutin, jako je [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json). Pokud koncový bod služby REST API očekává **text žádosti**, zadejte do parametru `-Body` proměnnou `Invoke-RestMethod` ve formátu JSON.

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Díky nástrojům REST API přidaným do vašeho prostředí podle vlastního výběru je čas si vyzkoušet jednoduchý dotaz pro graf prostředků. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

V těle žádosti každého volání REST API existuje proměnná, která se používá, kterou je třeba nahradit vlastní hodnotou:

- Proměnnou `{subscriptionID}` nahraďte ID předplatného.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí REST API a `resources` koncového bodu:

   - Identifikátor URI v REST API

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Text požadavku

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Jelikož tento příklad dotazu neposkytuje modifikátor řazení, jako je `order by` spuštění tohoto dotazu vícekrát, je pravděpodobně výsledkem jiné sady prostředků na žádost.

1. Aktualizujte volání `resouces` koncového bodu a změňte **dotaz** na `order by` vlastnost **Name** :

   - Identifikátor URI v REST API

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Text požadavku

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Aktualizujte volání `resources` koncového bodu a změňte **dotaz** tak, aby nejprve `order by` vlastnost **název** a pak `limit` na horních pět výsledků:

   - Identifikátor URI v REST API

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Text požadavku

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

Další příklady volání REST API pro Azure Resource Graph najdete v tématu [Azure Resource Graph – příklady REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Vyčištění prostředků

REST API nemá žádné knihovny ani moduly k odinstalaci. Pokud jste nainstalovali nástroj, jako je _ARMClient_ nebo _post_ , aby bylo volání možné a už ho nepotřebujete, můžete nástroj odinstalovat hned teď.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali REST API koncový bod grafu prostředků a spustili svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)
