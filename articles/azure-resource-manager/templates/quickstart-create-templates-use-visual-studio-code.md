---
title: Vytvořit šablonu – kód Visual Studia
description: Při práci na šablonách Resource Manageru můžete použít Visual Studio Code a rozšíření Nástroje Azure Resource Manageru.
author: neilpeterson
ms.date: 03/27/2019
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 4b1ecbf3a1f6083261e87537e20d52e755b77424
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640867"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Úvodní příručka: Vytvoření šablon Azure Resource Manager u kódu Sady Visual Studio

Nástroje Správce prostředků Azure pro kód Visual Studia poskytují jazykovou podporu, fragmenty prostředků a automatické dokončování prostředků. Tyto nástroje pomáhají vytvářet a ověřovat šablony Azure Resource Manageru. V tomto rychlém startu použijete rozšíření k vytvoření šablony Azure Resource Manager od začátku. Při tom dochází k rozšíření schopnosti, jako jsou fragmenty šablony ARM, ověřování, dokončení a podporu souboru parametrů.

K dokončení tohoto rychlého startu potřebujete [kód Visual Studia](https://code.visualstudio.com/)s nainstalovaným [rozšířením nástrojů Azure Resource Manager.](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) Potřebujete také nainstalované a ověřené rozhraní [příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo [modul Azure PowerShell.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0)

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-an-arm-template"></a>Vytvoření šablony ARM

Vytvořte a otevřete pomocí kódu Sady Visual Studio nový soubor s názvem *azuredeploy.json*. Zadejte `arm` do editoru kódu, který iniciuje fragmenty Azure Resource Manager pro zaschlé části šablony ARM.

Výběrem této možnosti `arm!` vytvoříte šablonu s vymezeným oborem pro nasazení skupiny prostředků Azure.

![Obrázek s windowsaffingem Správce prostředků Azure](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Tento úryvek vytvoří základní stavební bloky pro šablonu ARM.

![Obrázek znázorňující plně šálnutou šablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Všimněte si, že režim jazyka kódu visual studia se změnil z *JSON* na *šablonu Správce prostředků Azure*. Rozšíření obsahuje jazykový server specifický pro šablony ARM, který poskytuje ověření, dokončení a další jazykové služby specifické pro šablonu ARM.

![Obrázek zobrazující Správce prostředků Azure jako režim jazyka kódu Visual Studia](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Přidání prostředku Azure

Rozšíření obsahuje výstřižky pro mnoho prostředků Azure. Tyto úryvky lze snadno přidat prostředky do nasazení šablony.

Umístěte kurzor do bloku **prostředků** `storage`šablony, zadejte a vyberte fragment *úložiště ramene.*

![Obrázek znázorňující zdroj přidaný do šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Tato akce přidá prostředek úložiště do šablony.

![Obrázek znázorňující prostředek úložiště Azure v šabloně ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

Klávesu **tab** lze použít k tabulátoru prostřednictvím konfigurovatelných vlastností v účtu úložiště.

![Obrázek znázorňující, jak lze použít klávesu tab k navigaci v konfiguraci prostředků](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Dokončení a validace

Jednou z nejvýkonnějších funkcí rozšíření je jeho integrace se schématy Azure. Schémata Azure poskytují rozšíření s možnostmi ověřování a dokončování zhodžené prostředky. Pojďme upravit účet úložiště zobrazíte ověření a dokončení v akci. 

Nejprve aktualizujte druh účtu úložiště na `megaStorage`neplatnou hodnotu, například . Všimněte si, že tato akce `megaStorage` vytvoří upozornění označující, že není platnou hodnotu.

![Obrázek znázorňující neplatnou konfiguraci úložiště](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Chcete-li `megaStorage`použít možnosti dokončení, odeberte , umístěte `ctrl`  +  `space`kurzor do dvojitých uvozovek a stiskněte klávesu . Tato akce představuje seznam dokončení platných hodnot.

![Obrázek znázorňující automatické dokončování rozšíření](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Přidání parametrů šablony

Nyní vytvořte a použijte parametr k určení názvu účtu úložiště.

Umístěte kurzor do bloku parametrů, přidejte `par`návrat vozíku, `arm-param-value` zadejte a vyberte fragment. Tato akce přidá obecný parametr do šablony.

![Obrázek znázorňující parametr přidaný do šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Aktualizujte název parametru `storageAccountName` a popis `Storage Account Name`na .

![Obrázek znázorňující dokončený parametr v šabloně ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Názvy účtů úložiště Azure mají minimální délku 3 znaky a maximálně 24. Přidejte `minLength` `maxLength` jak a k parametru, a zadejte příslušné hodnoty.

![Obrázek znázorňující minLength a maxLength přidaný do parametru šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Nyní na prostředek úložiště aktualizujte vlastnost name, aby se použil parametr. Chcete-li tak učinit, odeberte aktuální název. Zadejte dvojitou uvozovku `[`a otevírací hranatou závorku , která vytvoří seznam funkcí šablony ARM. Vyberte *parametry* ze seznamu. 

![Obrázek znázorňující automatické dokončování při použití parametrů ve zdrojích šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Zadáním jedné `'` uvozovky uvnitř kulatých závorek vytvoříte seznam všech parametrů definovaných v šabloně, v tomto případě *storageAccountName*. Vyberte parametr.

![Obrázek znázorňující dokončený parametr ve zdroji šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Vytvoření souboru parametrů

Soubor parametrů šablony ARM umožňuje ukládat hodnoty parametrů specifické pro prostředí a předat tyto hodnoty jako skupinu v době nasazení. Můžete mít například soubor parametrů s hodnotami specifickými pro testovací prostředí a jiný pro produkční prostředí.

Rozšíření usnadňuje vytvoření souboru parametrů z existujících šablon. Chcete-li tak učinit, klikněte pravým tlačítkem `Select/Create Parameter File`myši na šablonu v editoru kódu a vyberte .

![Obrázek znázorňující proces po kliknutí pravým tlačítkem myši pro vytvoření souboru parametrů ze šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Vyberte `New`  >  `All Parameters` > Vyberte název a umístění souboru parametrů.

![Obrázek znázorňující dialog název a uložit soubor při vytváření souboru parametrů ze šablony ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Tato akce vytvoří nový soubor parametrů a mapuje jej pomocí šablony, ze které byl vytvořen. Aktuální mapování souborů šablony/parametrů můžete zobrazit a upravit na stavovém řádku kódu sady Visual Studio, když je šablona vybraná.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nyní, když byl soubor parametrů mapován na šablonu, přípona ověří šablonu i soubor parametrů společně. Chcete-li zobrazit toto ověření v praxi, `storageAccountName` přidejte hodnotu dvou znaků do parametru v souboru parametru a uložte soubor.

![Obrázek znázorňující zneplatněnou šablonu z důvodu problému se souborem parametrů](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Přejděte zpět do šablony ARM a všimněte si, že byla vyvolána chyba označující, že hodnota nesplňuje kritéria parametru.

![Obrázek s platnou šablonou ARM](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Aktualizujte hodnotu na vhodné, uložte soubor a přejděte zpět do šablony. Všimněte si, že chyba na parametr byl vyřešen.

## <a name="deploy-the-template"></a>Nasazení šablony

Otevřete integrovaný terminál kódu `ctrl`  +  ```` ` ```` Visual Studia pomocí kombinace kláves a použijte k nasazení šablony buď rozhraní příkazového příkazu Azure nebo modulu Azure PowerShell.

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

Když už prostředky Azure nejsou potřeba, použijte modul Azure CLI nebo Azure PowerShell k odstranění skupiny prostředků rychlého startu.

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
