---
title: Kurz – vytvoření a nasazení první šablony Azure Resource Manager
description: Popisuje kroky k vytvoření první šablony Azure Resource Manager. Dozvíte se o syntaxi souboru šablony a o tom, jak nasadit účet úložiště.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e84c3bfa5a5f43ec652f12d70718ab63bd7a19e5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029552"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Kurz: vytvoření a nasazení první šablony Azure Resource Manager

V tomto kurzu se seznámíte s Správce prostředků šablon. Ukazuje, jak vytvořit úvodní šablonu a jak ji nasadit do Azure. Seznámíte se se strukturou šablony a nástroji, které budete potřebovat pro práci se šablonami. Dokončení tohoto kurzu trvá přibližně **12 minut** , ale skutečný čas se bude lišit podle toho, kolik nástrojů potřebujete nainstalovat.

Tento kurz je první z řad. Jak budete postupovat podle řady, upravíte počáteční šablonu krok za krokem, dokud neprojdete všechny základní části šablony Správce prostředků. Tyto prvky jsou stavebními bloky pro mnohem složitější šablony. Doufáme, že na konci řady jste si jistí, že máte jistotu, že vytváříte vlastní šablony a jste připraveni automatizovat nasazení pomocí šablon.

Pokud se chcete dozvědět o výhodách používání šablon a proč byste měli automatizovat nasazení se šablonami, přečtěte si téma [Azure Resource Manager šablony](template-deployment-overview.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="get-tools"></a>Získat nástroje

Pojďme začít tím, že budete mít k dispozici nástroje, které potřebujete k vytváření a nasazování šablon.

### <a name="editor"></a>Editor

Šablony jsou soubory JSON. K vytváření šablon potřebujete dobrý Editor JSON. Doporučujeme Visual Studio Code s rozšířením nástroje Správce prostředků Tools. Pokud potřebujete nainstalovat tyto nástroje, přečtěte si téma [použití Visual Studio Code k vytvoření šablon Azure Resource Manager](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Nasazení z příkazového řádku

K nasazení šablony budete taky potřebovat buď Azure PowerShell, nebo rozhraní příkazového řádku Azure. Pokyny k instalaci najdete v těchto tématech:

- [Nainstalovat Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalace rozhraní příkazového řádku Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace rozhraní příkazového řádku Azure CLI v systému Linux](/cli/azure/install-azure-cli-linux)

Po instalaci Azure PowerShell nebo rozhraní příkazového řádku Azure se ujistěte, že poprvé se přihlašujete. Nápovědu najdete v tématu věnovaném [přihlášení k prostředí PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

V pořádku budete připraveni začít se získáváním informací o šablonách.

## <a name="create-your-first-template"></a>Vytvoření první šablony

1. Otevřete Visual Studio Code s nainstalovaným rozšířením nástroje Správce prostředků Tools.
1. V nabídce **soubor** vyberte **nový soubor** a vytvořte nový soubor.
1. V nabídce **soubor** vyberte **Uložit jako**.
1. Pojmenujte soubor **azuredeploy** a vyberte příponu souboru **JSON** . Úplný název souboru **azuredeploy. JSON**.
1. Uložte soubor do pracovní stanice. Vyberte cestu, která se snadno pamatuje, protože tuto cestu zadáte později při nasazování šablony.
1. Zkopírujte následující kód JSON a vložte ho do souboru:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Toto prostředí VS Code vypadá takto:

    ![Šablona sady Visual Studio Code First Správce prostředků](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Tato šablona neimplementuje žádné prostředky. Začínáme prázdnou šablonou, takže se můžete seznámit s postupem nasazení šablony, přičemž se minimalizuje pravděpodobnost, že došlo k nějakému problému.

    Soubor JSON má tyto prvky:

    - **$Schema**: Určuje umístění souboru schématu JSON. Soubor schématu popisuje vlastnosti, které jsou k dispozici v rámci šablony. Například schéma definuje **prostředky** jako jednu z platných vlastností šablony. Nedělejte si starosti s tím, že datum schématu je 2015-01-01. Tato verze schématu je aktuální a zahrnuje všechny nejnovější funkce. Datum schématu se nezměnilo, protože od jeho úvodu nedošlo k žádným změnám.
    - **contentversion –** : Určuje verzi šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tuto hodnotu použijte k dokumentování významných změn v šabloně. Při nasazování prostředků pomocí šablony můžete tuto hodnotu použít k tomu, abyste se ujistili, že je používána pravá šablona.
    - **prostředky**: obsahuje prostředky, které chcete nasadit nebo aktualizovat. V současné době je prázdná, ale později přidáte prostředky.

1. Uložte soubor.

Blahopřejeme, vytvořili jste svou první šablonu.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI, přihlaste se pomocí přihlašovacích údajů Azure.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Vytvořit skupinu prostředků

Když nasadíte šablonu, zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu k nasazení vytvořte skupinu prostředků buď pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. Vyberte karty v následující části kódu a vyberte si mezi Azure PowerShell a Azure CLI.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Nasadit šablonu

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell. Použijte skupinu prostředků, kterou jste vytvořili. Zadejte název nasazení, abyste ho mohli snadno identifikovat v historii nasazení. Pro usnadnění práce vytvořte také proměnnou, která ukládá cestu k souboru šablony. Tato proměnná usnadňuje spuštění příkazů nasazení, protože nemusíte znovu zadávat cestu pokaždé, když nasadíte.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Příkaz pro nasazení vrátí výsledky. Vyhledejte `ProvisioningState`, abyste viděli, jestli se nasazení úspěšně dokončilo.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

![Stav zřizování nasazení PowerShellu](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

![Stav zřizování nasazení Azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Ověřit nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **skupiny prostředků**.

1. V posledním postupu vyberte nasazení skupiny prostředků. Výchozí název je **myResourceGroup**. V rámci skupiny prostředků se nezobrazí žádný prostředek nasazený.

1. Všimněte si, že v pravém horním rohu přehledu se zobrazí stav nasazení. Vyberte **1 úspěšné**.

   ![Zobrazit stav nasazení](./media/template-tutorial-create-first-template/deployment-status.png)

1. Zobrazí se historie nasazení pro skupinu prostředků. Vyberte **blanktemplate**.

   ![Vybrat nasazení](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Zobrazí se souhrn nasazení. V tomto případě není k dispozici hodně prostředků, protože nebyly nasazeny žádné prostředky. Později v této sérii může být užitečné zkontrolovat souhrn v historii nasazení. Na levé straně si můžete zobrazit vstupy, výstupy a šablony použité během nasazování.

   ![Zobrazit souhrn nasazení](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní provádíte zastavování, možná budete chtít odstranit skupinu prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchou šablonu pro nasazení do Azure. V dalším kurzu přidáte účet úložiště do šablony a nasadíte ho do vaší skupiny prostředků.

> [!div class="nextstepaction"]
> [Přidat prostředek](template-tutorial-add-resource.md)
