---
title: Vytvoření souborů bicep – Visual Studio Code
description: Použití Visual Studio Code a rozšíření bicep k bicep souborů pro nasazení prostředků Azure
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4d1064351ddfacdebfa67fd9b2f517f592de3a7c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612877"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Rychlý Start: vytvoření souborů bicep pomocí Visual Studio Code

Rozšíření bicep pro Visual Studio Code poskytuje jazykovou podporu a automatického dokončování prostředků. Tyto nástroje vám pomůžou vytvořit a ověřit soubory [bicep](./bicep-overview.md) . V tomto rychlém startu použijete rozšíření k vytvoření souboru bicep od začátku. V takovém případě se můžete setkat s funkcemi rozšíření, jako je ověřování a dokončování.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

K dokončení tohoto rychlého startu potřebujete [Visual Studio Code](https://code.visualstudio.com/)s nainstalovanou [příponou bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) . Budete také potřebovat nejnovější rozhraní příkazového [řádku Azure CLI](/cli/azure/) nebo nejnovější nainstalovaný a ověřený [modul Azure PowerShell](/powershell/azure/new-azureps-module-az) .

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-bicep-file"></a>Vytvoření souboru bicep

Vytvořte a otevřete pomocí Visual Studio Code nový soubor s názvem *azuredeploy. bicep*.

## <a name="add-an-azure-resource"></a>Přidání prostředku Azure

Přidejte do souboru bicep základní prostředek účtu úložiště.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

Deklarace prostředků má čtyři komponenty:

- **Resource**: klíčové slovo.
- **symbolický název** (STG): symbolický název je identifikátor pro odkazování na prostředek v celém souboru bicep. Nejedná se o to, který název prostředku bude při nasazení. Název prostředku je definován vlastností **Name** .  Podívejte se na čtvrtou komponentu v tomto seznamu.
- **typ prostředku** ( Microsoft.Storage/storageAccounts@2019-06-01 ): skládá se z poskytovatele prostředků (Microsoft. Storage), typu prostředku (StorageAccounts) a apiVersion (2019-06-01). Každý poskytovatel prostředků publikuje své vlastní verze rozhraní API, takže tato hodnota je specifická pro daný typ. Další typy a apiVersions pro různé prostředky Azure můžete najít na základě [odkazu na šablonu ARM](/azure/templates/).
- **vlastnosti** (vše uvnitř = {...}): Zadejte vlastnosti pro typ prostředku. Každý prostředek má `name` vlastnost. Většina prostředků má také `location` vlastnost, která nastaví oblast, kde je prostředek nasazen. Ostatní vlastnosti se liší podle typu prostředku a verze rozhraní API.

## <a name="completion-and-validation"></a>Dokončení a ověření

Jednou z nejúčinnějších schopností rozšíření je jeho integrace se schématy Azure. Schémata Azure poskytují rozšíření s možnostmi dokončování ověřování a prostředků. Pojďme Upravit účet úložiště, aby se zobrazilo ověření a dokončení v akci.

Nejdřív aktualizujte druh účtu úložiště na neplatnou hodnotu, třeba `megaStorage` . Všimněte si, že tato akce vytvoří upozornění, což znamená, že není `megaStorage` platná hodnota.

![Obrázek znázorňující neplatnou konfiguraci úložiště](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Chcete-li použít možnosti dokončování, je nutné odebrat `megaStorage` , umístit kurzor dovnitř jedné uvozovky a stisknout klávesu `ctrl`  +  `space` . Tato akce zobrazí seznam dokončení platných hodnot.

![Obrázek znázorňující automatické dokončování rozšíření](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Přidání parametrů

Nyní vytvořte a použijte parametr pro zadání názvu účtu úložiště.

Na začátek souboru přidejte následující kód:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Názvy účtů Azure Storage mají minimální délku 3 znaky a musí být v rozmezí 24. Použijte `minLength` a `maxLength` k poskytnutí příslušných hodnot.

Nyní můžete v prostředku úložiště aktualizovat vlastnost název tak, aby používala parametr. Provedete to tak, že odeberete aktuální název prostředku úložiště včetně jednoduchých uvozovek. stiskněte klávesu `ctrl`  +  `space` . V seznamu vyberte parametr **storageAccountName** . Všimněte si, že parametry lze odkazovat přímo pomocí jejich názvů v bicep. Šablony JSON vyžadují funkci Parameter ().

![Obrázek znázorňující automatické dokončování při použití parametrů v prostředcích bicep](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Nasazení souboru bicep

Otevřete terminálu Integrated Visual Studio Code pomocí `ctrl`  +  ```` ` ```` kombinace kláves, změňte aktuální adresář na místo, kde se nachází soubor bicep, a potom k nasazení souboru bicep použijte buď modul Azure CLI nebo Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure nepotřebujete, odstraňte pomocí modulu Azure CLI nebo Azure PowerShell skupinu prostředků pro rychlý Start.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Bicep kurzy pro začátečníky](./bicep-tutorial-create-first-bicep.md)
