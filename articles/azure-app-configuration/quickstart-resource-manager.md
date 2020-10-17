---
title: Vytvoření úložiště konfigurace aplikace Azure pomocí šablony Azure Resource Manager (šablona ARM)
titleSuffix: Azure App Configuration
description: Naučte se vytvořit úložiště konfigurace aplikace Azure pomocí šablony Azure Resource Manager (šablona ARM).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: feabac62564729338e41bf30eaf8d9f5a6317126
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149006"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Rychlý Start: vytvoření úložiště konfigurace aplikace Azure pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak:

- Nasaďte úložiště konfigurace aplikace pomocí šablony Azure Resource Manager (šablona ARM).
- Vytvořte klíčové hodnoty v App Configuration Storu pomocí šablony ARM.
- Načte klíčové hodnoty v úložišti konfigurace aplikace ze šablony ARM.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Vytvoří nové úložiště konfigurace aplikace se dvěma hodnotami, které jsou uvnitř. Pak použije `reference` funkci k výstupu hodnot dvou prostředků klíč-hodnota. Čtení hodnoty klíče tímto způsobem umožňuje použití na jiných místech v šabloně.

Rychlý Start používá `copy` element k vytvoření více instancí prostředku klíčové hodnoty. Další informace o elementu najdete v `copy` tématu [iterace prostředků v šablonách ARM](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Tato šablona vyžaduje verzi poskytovatele prostředků konfigurace aplikace `2020-07-01-preview` nebo novější. Tato verze používá `reference` funkci ke čtení hodnot klíč-hodnota. `listKeyValue`Funkce, která byla použita ke čtení hodnot klíče v předchozí verzi, není k dispozici počínaje verzí `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): vytvořte úložiště konfigurace aplikace.
- Microsoft. AppConfiguration/configurationStores/klíčové hodnoty: vytvoří klíčovou hodnotu v úložišti konfigurace aplikace.

> [!NOTE]
> `keyValues`Název prostředku je kombinací klíče a popisku. Klíč a popisek jsou připojeny pomocí `$` oddělovače. Popisek je nepovinný. Ve výše uvedeném příkladu `keyValues` prostředek s názvem `myKey` vytvoří klíčovou hodnotu bez popisku.
>
> Procentuální kódování, označované také jako kódování adresy URL, umožňuje klíčům nebo popiskům zahrnout znaky, které nejsou povoleny v názvech prostředků šablony ARM. `%` Nejedná se o povolený znak, takže `~` se místo něj použije. Chcete-li správně zakódovat název, postupujte podle následujících kroků:
>
> 1. Použít kódování adresy URL
> 2. Nahradit `~``~7E`
> 3. Nahradit `%``~`
>
> Například pro vytvoření páru klíč-hodnota s názvem klíče `AppName:DbEndpoint` a názvem popisku `Test` by měl být název prostředku `AppName~3ADbEndpoint$Test` .

## <a name="deploy-the-template"></a>Nasazení šablony

Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří úložiště konfigurace aplikace se dvěma hodnotami klíče uvnitř.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Šablonu můžete nasadit také pomocí následující rutiny prostředí PowerShell. Hodnoty klíč budou ve výstupu konzoly PowerShellu.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Do vyhledávacího pole Azure Portal zadejte **Konfigurace aplikace**. V seznamu vyberte **Konfigurace aplikace** .
1. Vyberte nově vytvořený prostředek konfigurace aplikace.
1. V části **operace**klikněte na **Průzkumník konfigurace**.
1. Ověřte, zda existují dvě hodnoty klíč-hodnota.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, úložiště konfigurace aplikace a všechny související prostředky. Pokud plánujete používat úložiště konfigurace aplikace v budoucnu, můžete ho odstranit. Pokud nebudete tento obchod nadále používat, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Další kroky

Další informace o vytváření dalších aplikací s konfigurací aplikace Azure najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure](quickstart-aspnet-core-app.md)
