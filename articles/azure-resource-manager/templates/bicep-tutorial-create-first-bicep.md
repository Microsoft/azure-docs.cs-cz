---
title: Kurz – vytvoření & nasazení Azure Resource Manager souborů bicep
description: Vytvořte svůj první soubor bicep pro nasazení prostředků Azure. V tomto kurzu se dozvíte o syntaxi souborů bicep a o tom, jak nasadit účet úložiště.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594270"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Kurz: vytvoření a nasazení prvního souboru Azure Resource Manager bicep

V tomto kurzu se seznámíte s [bicep](./bicep-overview.md). Ukazuje, jak vytvořit soubor počátečního bicep a nasadit ho do Azure. Dozvíte se o struktuře souboru bicep a o nástrojích, které budete potřebovat pro práci se soubory bicep. Dokončení tohoto kurzu trvá přibližně **12 minut** , ale skutečný čas se bude lišit podle toho, kolik nástrojů potřebujete nainstalovat.

Tento kurz je první z řad. Jak budete postupovat podle řady, upravíte úvodní soubor bicep krok za krokem, dokud neprojdete všechny základní části souboru bicep. Tyto prvky jsou stavebními bloky pro mnohem složitější soubory bicep. Doufáme, že na konci seriálu jste si jisti, že vytváříte vlastní soubory bicep a jste připraveni automatizovat nasazení pomocí souborů bicep.

Pokud se chcete dozvědět o výhodách používání bicep a proč byste měli automatizovat nasazení pomocí souborů bicep, přečtěte si téma [bicep](./bicep-overview.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Získat nástroje

Pojďme začít tím, že si zajistěte, aby byly k dispozici nástroje, které potřebujete k vytváření a nasazování souborů bicep. Nainstalujte tyto nástroje na místní počítač.

### <a name="editor"></a>Editor

K vytváření souborů bicep potřebujete dobrý Editor. Doporučujeme Visual Studio Code s rozšířením bicep. Pokud potřebujete tyto nástroje nainstalovat, přečtěte si téma [Konfigurace vývojového prostředí bicep](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Nasazení z příkazového řádku

Soubory bicep můžete nasadit pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. V případě Azure CLI potřebujete verzi 2.20.0 nebo novější; pro Azure PowerShell potřebujete verzi 5.6.0 nebo novější. Pokyny k instalaci najdete v těchto tématech:

- [Instalace Azure PowerShellu](/powershell/azure/install-az-ps)
- [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace rozhraní příkazového řádku Azure CLI v systému Linux](/cli/azure/install-azure-cli-linux)
- [Instalace Azure CLI v macOS](/cli/azure/install-azure-cli-macos)

Po instalaci Azure PowerShell nebo rozhraní příkazového řádku Azure se ujistěte, že poprvé se přihlašujete. Nápovědu najdete v tématu věnovaném [přihlášení k prostředí PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

V pořádku jste připraveni začít s bicep.

## <a name="create-your-first-bicep-file"></a>Vytvoření prvního souboru bicep

1. Otevřete Visual Studio Code s nainstalovaným rozšířením bicep.
1. V nabídce **soubor** vyberte **nový soubor** a vytvořte nový soubor.
1. V nabídce **soubor** vyberte **Uložit jako**.
1. Pojmenujte soubor _azuredeploy_ a vyberte příponu souboru _bicep_ . Úplný název souboru je _azuredeploy. bicep_.
1. Uložte soubor do pracovní stanice. Vyberte cestu, která se snadno pamatuje, protože tuto cestu zadáte později při nasazování souboru bicep.
1. Zkopírujte a vložte následující obsah do souboru:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Toto prostředí Visual Studio Code vypadá takto:

    ![Visual Studio Code prvního souboru bicep](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    Deklarace prostředků má čtyři komponenty:

    - **Resource**: klíčové slovo.
    - **symbolický název** (STG): symbolický název je identifikátor pro odkazování na prostředek v celém souboru bicep. Nejedná se o to, který název prostředku bude při nasazení. Název prostředku je definován vlastností **Name** .  Podívejte se na čtvrtou komponentu v tomto seznamu. Aby bylo možné kurzy snadno sledovat, použije se jako symbolický název prostředku účtu úložiště v této sérii kurzů **STG** . Chcete-li zjistit, jak použít symbolický název k získání úplného seznamu vlastností objektu, přečtěte si téma [Přidání výstupů](./bicep-tutorial-add-outputs.md).
    - **typ prostředku** ( Microsoft.Storage/storageAccounts@2019-06-01 ): skládá se z poskytovatele prostředků (Microsoft. Storage), typu prostředku (StorageAccounts) a apiVersion (2019-06-01). Každý poskytovatel prostředků publikuje své vlastní verze rozhraní API, takže tato hodnota je specifická pro daný typ. Další typy a apiVersions pro různé prostředky Azure můžete najít na základě [odkazu na šablonu ARM](/azure/templates/).
    - **vlastnosti** (vše uvnitř = {...}): Jedná se o konkrétní vlastnosti, které chcete určit pro daný typ prostředku. Jsou to přesně stejné vlastnosti, které jsou pro vás k dispozici v šabloně ARM. Každý prostředek má `name` vlastnost. Většina prostředků má také `location` vlastnost, která nastaví oblast, kde je prostředek nasazen. Ostatní vlastnosti se liší podle typu prostředku a verze rozhraní API. Je důležité pochopit připojení mezi verzí rozhraní API a dostupnými vlastnostmi, takže se můžete pustit do více podrobností.

        Pro tento účet úložiště si můžete prohlédnout tuto verzi rozhraní API na adrese [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Všimněte si, že jste nepřidali všechny vlastnosti do souboru bicep. Mnohé z vlastností jsou volitelné. `Microsoft.Storage`Poskytovatel prostředků může uvolnit novou verzi rozhraní API, ale verze, kterou nasazujete, se nemusí měnit. Tuto verzi můžete dál používat a ví, že výsledky nasazení budou konzistentní.

        Pokud si zobrazíte starší verzi rozhraní API, například [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), uvidíte, že je k dispozici menší sada vlastností.

        Pokud se rozhodnete změnit verzi rozhraní API pro určitý prostředek, ujistěte se, že jste vyhodnotili vlastnosti této verze a odpovídajícím způsobem upravte soubor bicep.

1. Nahraďte `{provide-unique-name}` včetně složených závorek `{}` jedinečným názvem účtu úložiště.

    > [!IMPORTANT]
    > Název účtu úložiště musí být jedinečný v rámci Azure. Název musí obsahovat jenom malá písmena nebo číslice. Nemůže být delší než 24 znaků. Můžete vyzkoušet způsob pojmenování, jako je například použití **store1** jako předpona a přidání vašich počátečních a dnešních dat. Název, který použijete, může například vypadat jako **store1abc09092019**.

    Odhadování jedinečného názvu účtu úložiště není jednoduché a nefunguje dobře pro automatizaci rozsáhlých nasazení. Později v této sérii kurzů budete používat funkce bicep, které usnadňují vytváření jedinečných názvů.

1. Soubor uložte.

Gratulujeme, vytvořili jste svůj první soubor bicep.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI, přihlaste se pomocí přihlašovacích údajů Azure.

Výběrem karet v následujících částech kódu si můžete vybrat mezi Azure PowerShell a Azure CLI. Příklady rozhraní příkazového řádku v tomto článku se napíší pro prostředí bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Pokud máte více předplatných Azure, vyberte předplatné, které chcete použít. Nahraďte `[SubscriptionID/SubscriptionName]` a hranaté závorky `[]` informace o vašem předplatném:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Když nasadíte soubor bicep, zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu k nasazení vytvořte skupinu prostředků. Použijte k tomu Azure CLI nebo Azure PowerShell.

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

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

Bicep je transparentní abstrakcí prostřednictvím šablon Azure Resource Manager (šablon ARM). Každý soubor bicep se zkompiluje do šablony Standard ARM před tím, než se nasadí. Soubor bicep můžete buď zkompilovat do šablony ARM před jeho nasazením, nebo přímo nasadit soubor bicep. K nasazení souboru bicep použijte rozhraní příkazového řádku Azure nebo Azure PowerShell. Použijte skupinu prostředků, kterou jste vytvořili. Zadejte název nasazení, abyste ho mohli snadno identifikovat v historii nasazení. Pro usnadnění práce vytvořte také proměnnou, která ukládá cestu k souboru bicep. Tato proměnná vám usnadní spouštění příkazů nasazení, protože nebudete muset znovu zadávat cestu při každém nasazování. Nahraďte `{provide-the-path-to-the-bicep-file}` včetně složených závorek `{}` cestou k souboru bicep s názvem přípony souboru _. bicep_ .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Příkaz pro nasazení vrátí výsledky. Vyhledejte, pokud `ProvisioningState` chcete zjistit, zda nasazení proběhlo úspěšně.

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **skupiny prostředků**.

1. V posledním postupu vyberte nasazení skupiny prostředků. Výchozí název je **myResourceGroup**. V rámci skupiny prostředků se nezobrazí žádný prostředek nasazený.

1. Všimněte si, že v pravém horním rohu přehledu se zobrazí stav nasazení. Vyberte **1 úspěšné**.

   ![Zobrazit stav nasazení](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Zobrazí se historie nasazení pro skupinu prostředků. Vyberte **firstbicep**.

   ![Vybrat nasazení](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Zobrazí se souhrn nasazení. Je nasazený jeden účet úložiště. Na levé straně si můžete zobrazit vstupy, výstupy a šablony použité během nasazování.

   ![Zobrazit souhrn nasazení](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní provádíte zastavování, možná budete chtít odstranit skupinu prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchý soubor bicep k nasazení do Azure.  V dalších kurzech se dozvíte, jak přidat parametry, proměnné, výstupy a moduly do souboru bicep. Tyto funkce jsou stavebními bloky pro mnohem složitější soubory bicep.

> [!div class="nextstepaction"]
> [Přidání parametrů](bicep-tutorial-add-parameters.md)
