---
title: Použití šablony Azure Resource Manager k nasazení privátního koncového bodu pro webovou aplikaci
description: Naučte se používat šablonu ARM k nasazení privátního koncového bodu pro vaši webovou aplikaci.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652028"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Vytvoření aplikace App Service a nasazení privátního koncového bodu pomocí šablony Azure Resource Manager

V tomto rychlém startu použijete šablonu Azure Resource Manager (ARM) k vytvoření webové aplikace a zpřístupníte ji pomocí privátního koncového bodu.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Požadavek

Potřebujete účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Tato šablona vytvoří privátní koncový bod pro webovou aplikaci Azure.

### <a name="review-the-template"></a>Kontrola šablony

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Nasazení šablony

Tady je postup, jak nasadit šablonu Azure Resource Manager do Azure:

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte tento odkaz:  [nasadit do Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Šablona vytvoří virtuální síť, webovou aplikaci, soukromý koncový bod a privátní zónu DNS.
2. Vyberte nebo vytvořte skupinu prostředků.
3. Zadejte název vaší webové aplikace, Azure App Serviceho plánu a privátního koncového bodu.
5. Přečtěte si prohlášení o podmínkách a ujednáních. Pokud souhlasíte, vyberte Souhlasím **s podmínkami a ujednáními uvedenými nad**  >  **nákupem**. Dokončení nasazení může trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci privátního koncového bodu, odstraňte skupinu prostředků. Tím se odstraní soukromý koncový bod a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

- Další šablony Azure Resource Manager pro Azure App Service webové aplikace najdete v [ukázkách šablon ARM](../samples-resource-manager-templates.md).
