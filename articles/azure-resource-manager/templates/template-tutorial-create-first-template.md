---
title: Kurz – vytvoření šablony nasazení &
description: Vytvořte si první šablonu Azure Resource Manageru. V kurzu se dozvíte o syntaxi souboru šablony a o tom, jak nasadit účet úložiště.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b05bccf10ef5f273a74ca49e02162fd0408230f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411727"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Kurz: Vytvoření a nasazení první šablony ARM

Tento kurz vás seznámí se šablonami Azure Resource Manager (ARM). Ukazuje, jak vytvořit počáteční šablonu a nasadit ji do Azure. Dozvíte se o struktuře šablony a nástrojích, které budete potřebovat pro práci se šablonami. Dokončení tohoto výukového programu trvá přibližně **12 minut,** ale skutečný čas se bude lišit v závislosti na tom, kolik nástrojů je třeba nainstalovat.

Tento kurz je první ze série. Jak budete postupovat přes řadu, můžete upravit počáteční šablonu krok za krokem, dokud jste prozkoumali všechny základní části šablony ARM. Tyto prvky jsou stavebními kameny pro mnohem složitější šablony. Doufáme, že do konce série jste si jisti, vytvářet vlastní šablony a připraveni automatizovat nasazení s šablonami.

Pokud se chcete dozvědět o výhodách používání šablon a proč byste měli automatizovat nasazení pomocí šablon, přečtěte [si informace o šablonách Azure Resource Manageru](overview.md).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="get-tools"></a>Získejte nástroje

Začněme tím, že se ujistíte, že máte nástroje, které potřebujete k vytváření a nasazování šablon.

### <a name="editor"></a>Editor

Šablony jsou soubory JSON. Chcete-li vytvořit šablony, potřebujete dobrý editor JSON. Doporučujeme Visual Studio kód s rozšířenínástroje Správce prostředků. Pokud potřebujete nainstalovat tyto nástroje, najdete [v tématu Použití kódu Visual Studio k vytvoření arm šablony](use-vs-code-to-create-template.md).

### <a name="command-line-deployment"></a>Nasazení příkazového řádku

K nasazení šablony taky potřebujete azure prostředí nebo nastavení příkazového uživatelského příkazu Azure. Pokud používáte Azure CLI, musíte mít nejnovější verzi. Pokyny k instalaci naleznete v následujících tématech:

- [Instalace prostředí Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace Azure CLI na Linux](/cli/azure/install-azure-cli-linux)

Po instalaci Azure PowerShellnebo Azure CLI, ujistěte se, že přihlášení poprvé. Nápovědu najdete [v tématu Přihlášení – PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [Přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Dobře, jste připraveni začít se učit o šablonách.

## <a name="create-your-first-template"></a>Vytvoření první šablony

1. Otevřete kód sady Visual Studio s nainstalovaným rozšířením Nástroje správce prostředků.
1. V nabídce **Soubor** vyberte **Nový soubor,** chcete-li vytvořit nový soubor.
1. V nabídce **Soubor** vyberte **Uložit jako**.
1. Pojmenujte soubor **azuredeploy** a vyberte příponu souboru **JSON.** Úplný název souboru **azuredeploy.json**.
1. Uložte soubor na pracovní stanici. Vyberte cestu, která je snadno zapamatovatelné, protože budete poskytovat tuto cestu později při nasazení šablony.
1. Zkopírujte a vložte do souboru následující json:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Tady je to, jak vaše prostředí VS Code vypadá:

    ![První šablona kódu visual studia správce prostředků](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Tato šablona nenasazuje žádné prostředky. Začínáme s prázdnou šablonou, takže se můžete seznámit s kroky nasazení šablony a zároveň minimalizovat pravděpodobnost, že se něco pokazí.

    Soubor JSON má tyto prvky:

    - **$schema**: Určuje umístění souboru schématu JSON. Soubor schématu popisuje vlastnosti, které jsou k dispozici v rámci šablony. Schéma například definuje **prostředky** jako jednu z platných vlastností šablony. Nebojte se, že datum schématu je 2019-04-01. Tato verze schématu je aktuální a obsahuje všechny nejnovější funkce. Datum schématu nebylo změněno, protože od jeho zavedení nedošlo k žádným změnám.
    - **contentVersion**: Určuje verzi šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tato hodnota slouží k dokumentaci významných změn v šabloně. Při nasazování prostředků pomocí šablony lze tuto hodnotu použít k ujistěte se, že je použita správná šablona.
    - **prostředky**: Obsahuje prostředky, které chcete nasadit nebo aktualizovat. V současné době je prázdný, ale prostředky přidáte později.

1. Uložte soubor.

Gratulujeme, vytvořili jste první šablonu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI, přihlaste se pomocí přihlašovacích údajů Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Při nasazení šablony zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu nasazení vytvořte skupinu prostředků pomocí azure cli nebo Azure PowerShellu. Vyberte karty v následující části kódu a vyberte si mezi Azure PowerShellem a Azure CLI. Příklady CLI v tomto článku jsou napsány pro prostředí Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Nasazení šablony

Chcete-li nasadit šablonu, použijte buď Azure CLI nebo Azure PowerShell. Použijte skupinu prostředků, kterou jste vytvořili. Pojmenujte nasazení, abyste ho mohli snadno identifikovat v historii nasazení. Pro větší pohodlí také vytvořte proměnnou, která ukládá cestu k souboru šablony. Tato proměnná usnadňuje spuštění příkazů nasazení, protože není nutné znovu zadat cestu při každém nasazení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Příkaz nasazení vrátí výsledky. Vyhledejte, `ProvisioningState` zda bylo nasazení úspěšné.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Stav zřizování nasazení prostředí PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Stav zřizování zřizování azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Pokud se nasazení nezdařilo, zobrazte protokoly ladění pomocí **přepínače ladění** s příkazem deployment.  Můžete také použít **podrobný** přepínač k zobrazení úplné protokoly ladění.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levé nabídce vyberte **položku Skupiny prostředků**.

1. Vyberte nasazení skupiny prostředků v posledním postupu. Výchozí název je **myResourceGroup**. Neuvidíte žádný prostředek nasazený v rámci skupiny prostředků.

1. Všimněte si v pravém horním části přehledu, stav nasazení se zobrazí. Vyberte **1 Proběhlo úspěšně**.

   ![Zobrazit stav nasazení](./media/template-tutorial-create-first-template/deployment-status.png)

1. Zobrazí se historie nasazení pro skupinu prostředků. Vyberte **blanktemplate**.

   ![Vybrat nasazení](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Zobrazí se souhrn nasazení. V tomto případě není moc vidět, protože byly nasazeny žádné prostředky. Dále v této sérii může být užitečné zkontrolovat souhrn v historii nasazení. Všimněte si, na levé straně můžete zobrazit vstupy, výstupy a šablony používané během nasazení.

   ![Zobrazit souhrn nasazení](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete skupinu prostředků odstranit.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchou šablonu pro nasazení do Azure. V dalším kurzu přidáte účet úložiště do šablony a nasadíte ho do skupiny prostředků.

> [!div class="nextstepaction"]
> [Přidání zdroje](template-tutorial-add-resource.md)
