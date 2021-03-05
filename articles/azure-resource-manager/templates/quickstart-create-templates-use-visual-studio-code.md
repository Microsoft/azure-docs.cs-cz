---
title: Vytvořit šablonu – Visual Studio Code
description: Použijte Visual Studio Code a rozšíření Azure Resource Manager nástrojů pro práci na šablonách Azure Resource Manager (šablony ARM).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 97f956c1b1c1ea18242dc656c533451eda29dc6d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179047"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code

Nástroje Azure Resource Manager pro Visual Studio Code poskytují podporu jazyků, fragmenty prostředků a automatického dokončování prostředků. Tyto nástroje vám pomůžou vytvořit a ověřit šablony Azure Resource Manager (šablony ARM). V tomto rychlém startu použijete rozšíření k vytvoření šablony ARM od začátku. V takovém případě se můžete setkat s funkcemi rozšíření, jako jsou fragmenty šablon ARM, ověřování, dokončování a podpora souborů parametrů.

K dokončení tohoto rychlého startu potřebujete [Visual Studio Code](https://code.visualstudio.com/)s nainstalovanou [rozšířením nástroje Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) . Potřebujete také rozhraní příkazového [řádku Azure CLI](/cli/azure/) nebo [modul Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-3.7.0) nainstalovaný a ověřený.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-an-arm-template"></a>Vytvoření šablony ARM

Vytvořte a otevřete pomocí Visual Studio Code nový soubor s názvem *azuredeploy.js*. Zadejte `arm` do editoru kódu, který iniciuje Azure Resource Manager fragmentů pro generování šablon ARM.

Tuto možnost vyberte `arm!` , pokud chcete vytvořit šablonu vymezenou pro nasazení skupiny prostředků Azure.

![Obrázek znázorňující Azure Resource Manager fragmenty uživatelského rozhraní](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Tento fragment kódu vytvoří základní stavební bloky pro šablonu ARM.

![Obrázek znázorňující plně vygenerované šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Všimněte si, že režim Visual Studio Code jazyka se změnil z formátu *JSON* na *šablonu Azure Resource Manager*. Toto rozšíření zahrnuje jazykový Server specifický pro šablony ARM, které poskytuje ověřování, dokončování a další jazykové služby specifické pro šablonu ARM.

![Obrázek znázorňující Azure Resource Manager jako režim Visual Studio Code jazyka](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Přidání prostředku Azure

Přípona obsahuje fragmenty pro mnoho prostředků Azure. Tyto fragmenty kódu lze použít k snadnému přidání prostředků do nasazení šablony.

Umístěte kurzor do bloku **prostředků** šablony, zadejte `storage` a vyberte fragment *úložiště ARM* .

![Obrázek znázorňující přidání prostředku do šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Tato akce přidá prostředek úložiště do šablony.

![Obrázek znázorňující Azure Storage prostředek v šabloně ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

Klávesa **TAB** se dá použít k tabulátoru prostřednictvím konfigurovatelných vlastností účtu úložiště.

![Obrázek znázorňující, jak se dá Klávesa TAB použít k procházení konfigurace prostředků](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Dokončení a ověření

Jednou z nejúčinnějších schopností rozšíření je jeho integrace se schématy Azure. Schémata Azure poskytují rozšíření s možnostmi dokončování ověřování a prostředků. Pojďme Upravit účet úložiště, aby se zobrazilo ověření a dokončení v akci.

Nejdřív aktualizujte druh účtu úložiště na neplatnou hodnotu, třeba `megaStorage` . Všimněte si, že tato akce vytvoří upozornění, což znamená, že není `megaStorage` platná hodnota.

![Obrázek znázorňující neplatnou konfiguraci úložiště](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Chcete-li použít možnosti dokončování, odstraňte `megaStorage` kurzor do dvojitých uvozovek a stiskněte klávesu `ctrl`  +  `space` . Tato akce zobrazí seznam dokončení platných hodnot.

![Obrázek znázorňující automatické dokončování rozšíření](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Přidat parametry šablony

Nyní vytvořte a použijte parametr pro zadání názvu účtu úložiště.

Umístěte kurzor do bloku Parameters, přidejte návratový znak, zadejte `"` a potom vyberte `new-parameter` fragment. Tato akce přidá obecný parametr do šablony.

![Obrázek znázorňující parametr přidaný do šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Aktualizujte název parametru na `storageAccountName` a popis `Storage Account Name` .

![Obrázek znázorňující parametr Completed v šabloně ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Názvy účtů Azure Storage mají minimální délku 3 znaky a musí být v rozmezí 24. Přidejte obojí `minLength` a `maxLength` do parametru a zadejte odpovídající hodnoty.

![Obrázek, který znázorňuje minLength a maxLength přidaný do parametru šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Nyní můžete v prostředku úložiště aktualizovat vlastnost název tak, aby používala parametr. Uděláte to tak, že odeberete aktuální název. Zadejte dvojité uvozovky a levou hranatou závorku `[` , která vytvoří seznam funkcí šablon ARM. V seznamu vyberte *parametry* .

![Obrázek znázorňující automatické dokončování při použití parametrů v prostředcích šablon ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Když zadáte jednoduchou uvozovku `'` uvnitř kulatých závorek, vytvoří se seznam všech parametrů definovaných v šabloně, v tomto případě *storageAccountName*. Vyberte parametr.

![Obrázek znázorňující parametr Completed v prostředku šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Vytvoření souboru parametrů

Soubor parametrů šablony ARM umožňuje ukládat hodnoty parametrů specifické pro prostředí a předávat tyto hodnoty do jako skupinu v době nasazení. Například můžete mít soubor parametrů s hodnotami specifickými pro testovací prostředí a jiný pro produkční prostředí.

Rozšíření usnadňuje vytvoření souboru parametrů z existujících šablon. Provedete to tak, že kliknete pravým tlačítkem na šablonu v editoru kódu a vyberete `Select/Create Parameter File` .

![Obrázek znázorňující proces kliknutí pravým tlačítkem na vytvoření souboru parametrů ze šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Vyberte `New`  >  `All Parameters` > vyberte název a umístění souboru parametrů.

![Obrázek znázorňující dialog název a uložit soubor při vytváření souboru parametrů ze šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Tato akce vytvoří nový soubor parametrů a namapuje ho se šablonou, ze které byl vytvořen. Při výběru šablony můžete zobrazit a upravit aktuální mapování souborů nebo parametrů na stavovém řádku Visual Studio Code.

![Obrázek znázorňující mapování souboru nebo parametru na stavovém řádku Visual Studio Code.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Teď, když je soubor parametrů namapován na šablonu, rozšíření ověří jak šablonu, tak i soubor parametrů společně. Chcete-li toto ověřování zobrazit v praxi, přidejte do `storageAccountName` parametru v souboru parametrů hodnotu se dvěma znaky a soubor uložte.

![Obrázek znázorňující neplatnou šablonu kvůli problému se souborem parametrů](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Přejděte zpět do šablony ARM a Všimněte si, že byla vyvolána chyba oznamující, že hodnota nesplňuje kritéria parametru.

![Obrázek znázorňující platnou šablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Aktualizujte hodnotu podle potřeby, uložte soubor a přejděte zpět do šablony. Všimněte si, že chyba v parametru byla vyřešena.

## <a name="deploy-the-template"></a>Nasazení šablony

Pomocí kombinace kláves otevřete integrovaný terminál Visual Studio Code `ctrl`  +  ```` ` ```` a k nasazení šablony použijte buď Azure CLI, nebo modul Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
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
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)
