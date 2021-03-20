---
title: Nasazení Azure cache pro Redis pomocí šablony Azure Resource Manager
description: Naučte se používat šablonu Azure Resource Manager (šablonu ARM) k nasazení mezipaměti Azure pro prostředek Redis. Šablony jsou k dispozici pro běžné scénáře.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92735971"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Rychlý Start: vytvoření mezipaměti Azure pro Redis pomocí šablony ARM

Naučte se vytvořit šablonu Azure Resource Manager (šablonu ARM), která nasadí mezipaměť Azure pro Redis. Mezipaměť lze použít s existujícím účtem úložiště pro zachování diagnostických dat. Naučíte se také, jak definovat, které prostředky jsou nasazeny a jak definovat parametry, které jsou zadány při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. V současné době se nastavení diagnostiky sdílí pro všechny mezipaměti ve stejné oblasti pro předplatné. Aktualizace jedné mezipaměti v oblasti má vliv na všechny ostatní mezipaměti v oblasti.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště**: Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal). Účet úložiště se používá pro diagnostická data.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

V šabloně jsou definované následující prostředky:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

K dispozici jsou také šablony Správce prostředků pro novou [úroveň Premium](cache-overview.md#service-tiers) .

* [Vytvoření mezipaměti Azure Premium pro Redis s clusteringem](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Vytvoření mezipaměti Azure Premium pro Redis s Trvalost dat](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Vytvoření Redis Cache úrovně Premium nasazených do Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Pokud chcete vyhledat nejnovější šablony, přečtěte si téma [šablony Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/) a vyhledejte _Azure cache pro Redis_.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Vyberte nebo zadejte následující hodnoty:

    * **Předplatné**: vyberte předplatné Azure, které se používá k vytvoření sdílené složky dat a dalších prostředků.
    * **Skupina prostředků**: vyberte **vytvořit novou** a vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků.
    * **Umístění:**: Vyberte umístění pro skupinu prostředků. Účet úložiště a mezipaměť Redis musí být ve stejné oblasti. Ve výchozím nastavení používá mezipaměť Redis stejné umístění jako skupina prostředků. Zadejte tedy stejné umístění jako účet úložiště.
    * **Redis Cache název**: zadejte název pro Redis Cache.
    * **Existující účet úložiště diagnostiky**: Zadejte ID prostředku účtu úložiště. Syntaxe je `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Pro zbývající nastavení použijte výchozí hodnotu.
1. vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a s vybranou volbou **koupit**.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Otevřete mezipaměť Redis, kterou jste vytvořili.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte, jak vytvořit šablonu Azure Resource Manager, která nasadí mezipaměť Azure pro Redis. Informace o tom, jak vytvořit šablonu Azure Resource Manager, která nasadí webovou aplikaci Azure s mezipamětí Azure pro Redis, najdete v tématu [Vytvoření webové aplikace a mezipaměti Azure pro Redis pomocí šablony](./cache-web-app-arm-with-redis-cache-provision.md).
