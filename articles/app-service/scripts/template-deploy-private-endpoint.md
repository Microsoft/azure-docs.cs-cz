---
title: Pomocí této šablony Azure Resource Manager budete moci nasadit privátní koncový bod pro webovou aplikaci.
description: Naučte se používat šablonu ARM k nasazení privátního koncového bodu pro vaši webovou aplikaci.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535693"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Vytvoření aplikace App Service a nasazení privátního koncového bodu pomocí šablony Azure Resource Manager

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření webové aplikace a zpřístupníte ji pomocí privátního koncového bodu.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Požadavek

Potřebujete účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Tato šablona vytvoří privátní koncový bod pro webovou aplikaci Azure.

### <a name="review-the-template"></a>Kontrola šablony

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Nasazení šablony

Tady je postup, jak nasadit šablonu Azure Resource Manager do Azure:

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte **nasadit do Azure**. Šablona vytvoří virtuální síť, webovou aplikaci, soukromý koncový bod a privátní zónu DNS.

   [Nasazení do Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků.
3. Zadejte název webové aplikace, App Service plán, soukromý koncový bod.
5. Přečtěte si prohlášení o podmínkách a ujednáních. Pokud souhlasíte, vyberte Souhlasím s výše uvedenými podmínkami a ujednáními > nákupu. Dokončení nasazení může trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci privátního koncového bodu, odstraňte skupinu prostředků. Tím se odstraní soukromý koncový bod a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

- Další šablony Azure Resource Manager pro Azure App Service Web Apps najdete v [ukázkách šablon ARM](../samples-resource-manager-templates.md).
