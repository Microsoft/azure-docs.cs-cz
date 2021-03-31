---
title: Vytvoření první funkce pomocí šablon Azure Resource Manager
description: Pomocí šablony Azure Resource Manager (šablona ARM) můžete vytvářet a nasazovat do Azure jednoduchou funkci bez serveru, která se aktivovala protokolem HTTP.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422820"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Rychlý Start: vytvoření a nasazení Azure Functionsch prostředků ze šablony ARM

V tomto článku použijete šablonu Azure Resource Manager (šablonu ARM) k vytvoření funkce, která reaguje na požadavky HTTP. 

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

### <a name="azure-account"></a>Účet Azure 

Než začnete, musíte mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Tento článek vyžaduje, aby se projekt kódu místní funkce spouštěl v prostředcích Azure, které vytvoříte. Pokud nevytvoříte nejprve projekt k publikování, nebudete moci dokončit nasazení tohoto článku. 

Zvolte jednu z následujících karet, použijte odkaz a dokončete část a vytvořte aplikaci Function App v jazyce podle vašeho výběru:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vytvořte svůj projekt místních funkcí ve zvoleném jazyce v Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Vytvoření projektu místní funkce v aplikaci Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Vytvořte svůj projekt místních funkcí v zvoleném jazyce z příkazového řádku:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Po vytvoření projektu místně vytvoříte prostředky potřebné ke spuštění nové funkce v Azure. 

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Tato šablona vytvoří tyto čtyři prostředky Azure:

+ [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): vytvoří účet Azure Storage, který je vyžadován funkcemi.
+ [**Microsoft. Web/serverových farem**](/azure/templates/microsoft.web/serverfarms): vytvořte plán hostování spotřeby bez serveru pro aplikaci Function App.
+ [**Microsoft. Web/weby**](/azure/templates/microsoft.web/sites): Vytvoření aplikace Function App.
+ [**Microsoft. Insights/Components**](/azure/templates/microsoft.insights/components): vytvořte instanci Application Insights pro monitorování.

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Ověření nasazení

V dalším kroku ověříte prostředky hostování aplikace Function App, které jste vytvořili, publikováním projektu do Azure a voláním koncového bodu HTTP funkce.

### <a name="publish-the-function-project-to-azure"></a>Publikování projektu funkce do Azure

K publikování projektu na nové prostředky Azure použijte následující postup:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Ve výstupu zkopírujte adresu URL triggeru HTTP. Tuto funkci použijete k otestování funkce běžící v Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V možnosti **vybrat cíl publikování** zvolte možnost **plán spotřeby Azure Functions** s **volbou existující** a vyberte **vytvořit profil**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Zvolit existující cíl publikování":::

1. Zvolte své **předplatné**, rozbalte skupinu prostředků, vyberte aplikaci Function App a vyberte **OK**.

1. Po dokončení publikování zkopírujte **adresu URL webu**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Zkopírovat adresu URL webu ze souhrnu publikování":::

1. Připojit cestu `/api/<FUNCTION_NAME>?name=Functions` , kde `<FUNCTION_NAME>` je název vaší funkce. Adresa URL, která volá funkci triggeru HTTP, je v následujícím formátu:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Pomocí této adresy URL otestujete funkci triggeru HTTP běžící v Azure.

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Pokud chcete publikovat místní kód do aplikace Function App v Azure, použijte `publish` příkaz:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

V tomto příkladu nahraďte `<FUNCTION_APP_NAME>` názvem vaší aplikace Function App. Možná se budete muset znovu přihlásit pomocí `az login` . 

Ve výstupu zkopírujte adresu URL triggeru HTTP. Tuto funkci použijete k otestování funkce běžící v Azure.

---

### <a name="invoke-the-function-on-azure"></a>Vyvolání funkce v Azure

Vložte adresu URL zkopírovanou pro požadavek HTTP do adresního řádku prohlížeče, ujistěte se, že `name` řetězec dotazu byl `?name=Functions` přidán na konec této adresy URL a pak provede požadavek. 

Měla by se zobrazit odpověď podobná této:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku a přidáte výstupní vazbu fronty Azure Storage, zachovejte všechny prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Nahraďte `<RESOURCE_GROUP_NAME>` názvem vaší skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Teď, když jste publikovali první funkci, další informace získáte, když do své funkce přidáte výstupní vazbu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md)

---
