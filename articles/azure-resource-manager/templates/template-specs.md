---
title: Přehled specifikací šablon
description: Popisuje, jak vytvořit specifikace šablony a sdílet je s ostatními uživateli ve vaší organizaci.
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: b0dfc41bddccc6b5c5c924168044cffc0aa5e2b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728467"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager specifikace šablon (Preview)

Specifikace šablony je typ prostředku pro uložení šablony Azure Resource Manager (šablona ARM) v Azure pro pozdější nasazení. Tento typ prostředku umožňuje sdílet šablony ARM s ostatními uživateli ve vaší organizaci. Stejně jako u jakéhokoli jiného prostředku Azure můžete ke sdílení specifikace šablony použít řízení přístupu na základě role Azure (Azure RBAC).

**Microsoft. Resources/templateSpecs** je typ prostředku pro specifikace šablon. Skládá se z hlavní šablony a libovolného počtu propojených šablon. Azure bezpečně ukládá specifikace šablon do skupin prostředků. Specifikace šablon podporují [správu verzí](#versioning).

K nasazení specifikace šablony použijte standardní nástroje Azure, jako je PowerShell, Azure CLI, Azure Portal, REST a další podporované sady SDK a klienti. Použijete stejné příkazy jako pro šablonu.

> [!NOTE]
> Specifikace šablony jsou momentálně ve verzi Preview. Pokud ho chcete použít, musíte se [zaregistrovat v seznamu čekání](https://aka.ms/templateSpecOnboarding).

## <a name="why-use-template-specs"></a>Proč používat specifikace šablon?

Pokud v současné době máte šablony v úložišti GitHub nebo účtu úložiště, při pokusu o sdílení a používání šablon dojde k několika problémům. Aby ho uživatel mohl nasadit, musí být buď místní, nebo musí být adresa URL pro tuto šablonu veřejně přístupná. Chcete-li se vyhnout tomuto omezení, můžete sdílet kopie šablony s uživateli, kteří je potřebují nasadit, nebo otevřít přístup k úložišti nebo účtu úložiště. Když uživatelé vlastní místní kopie šablony, můžou se tyto kopie nakonec odchýlit od původní šablony. Když provedete veřejně přístupný úložiště nebo účet úložiště, můžete uživatelům, kteří mají přístup k šabloně, umožnit neúmyslnému.

Výhodou použití specifikací šablon je, že můžete vytvářet kanonické šablony a sdílet je s týmy ve vaší organizaci. Specifikace šablony jsou zabezpečené, protože jsou k dispozici pro Azure Resource Manager pro nasazení, ale nejsou přístupné pro uživatele bez oprávnění Azure RBAC. Uživatelé potřebují ke specifikaci šablony jenom přístup pro čtení, aby mohli nasadit její šablonu, takže šablonu můžete sdílet, aniž by ji ostatní mohli upravovat.

Šablony, které zahrnete do specifikace šablony, by měli ověřit správci ve vaší organizaci, aby postupoval podle požadavků a pokynů pro organizaci.

## <a name="create-template-spec"></a>Vytvořit specifikaci šablony

Následující příklad ukazuje jednoduchou šablonu pro vytvoření účtu úložiště v Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Když vytvoříte specifikaci šablony, příkazy PowerShellu nebo rozhraní příkazového řádku se přenesou do hlavního souboru šablony. Pokud hlavní šablona odkazuje na propojené šablony, budou příkazy vyhledat a zabalit je k vytvoření specifikace šablony. Další informace najdete v tématu [Vytvoření specifikace šablony s propojenými šablonami](#create-a-template-spec-with-linked-templates).

Vytvořte specifikaci šablony pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Všechny specifikace šablon v předplatném můžete zobrazit pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az ts list
```

---

Podrobnosti o specifikaci šablony včetně jejích verzí můžete zobrazit pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Po vytvoření specifikace šablony ji mohou nasadit uživatelé s přístupem **pro čtení** do specifikace šablony. Informace o udělení přístupu najdete v tématu [kurz: udělení přístupu skupině k prostředkům Azure pomocí Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Specifikace šablony se dají nasadit prostřednictvím portálu, PowerShellu, rozhraní příkazového řádku Azure nebo jako propojené šablony ve větším nasazení šablony. Uživatelé v organizaci mohou nasadit specifikaci šablony do libovolného oboru v Azure (skupina prostředků, předplatné, skupina pro správu nebo tenant).

Místo předání cesty nebo identifikátoru URI pro šablonu nasadíte specifikaci šablony zadáním jejího ID prostředku. ID prostředku má následující formát:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Všimněte si, že ID prostředku obsahuje číslo verze specifikace šablony.

Například můžete nasadit specifikaci šablony pomocí následujícího příkazu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

V praxi se obvykle spouštíte `Get-AzTemplateSpec` a získáte ID specifikace šablony, kterou chcete nasadit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Parametry

Předání parametrů šablony do specifikace šablony je přesně stejně jako předávání parametrů šabloně ARM. Přidejte hodnoty parametrů buď vloženou, nebo v souboru parametrů.

Pokud chcete předat parametr jako vložený, použijte:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Chcete-li vytvořit místní soubor parametrů, použijte:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

A předat tomuto souboru parametrů:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>Vytvoření specifikace šablony s propojenými šablonami

Pokud hlavní šablona pro specifikaci šablony odkazuje na propojené šablony, příkazy PowerShellu a rozhraní příkazového řádku můžou automaticky vyhledávat a zabalit propojené šablony z místního disku. Nemusíte ručně konfigurovat účty úložiště nebo úložiště k hostování specifikací šablon – vše je v prostředku specifikace šablony obsaženo samostatně.

Následující příklad se skládá z hlavní šablony se dvěma propojenými šablonami. Příklad je pouze výňatek z šablony. Všimněte si, že používá vlastnost s názvem `relativePath` pro odkazování na jiné šablony. `apiVersion` `2020-06-01` Pro prostředek nasazení je nutné použít nebo novější.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Pokud se pro předchozí příklad spustí příkaz PowerShell nebo CLI, vyhledá příkaz tři soubory – hlavní šablonu, šablonu webové aplikace ( `webapp.json` ) a šablonu databáze ( `database.json` ) a zabalí je do specifikace šablony.

Další informace najdete v tématu [kurz: Vytvoření specifikace šablony s propojenými šablonami](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Nasazení specifikace šablony jako propojené šablony

Po vytvoření specifikace šablony ji snadno znovu použijete ze šablony ARM nebo jiné specifikace šablony. Odkaz na specifikaci šablony můžete propojit přidáním jejího ID prostředku do šablony. Specifikace propojené šablony se automaticky nasadí při nasazení hlavní šablony. Toto chování vám umožní vyvíjet modulární specifikace šablony a znovu je použít podle potřeby.

Můžete například vytvořit šablonu specifikace, která nasadí síťové prostředky, a další specifikaci šablony, která nasadí prostředky úložiště. V šablonách ARM propojíte tyto dvě specifikace šablony, kdykoli budete potřebovat nakonfigurovat sítě nebo prostředky úložiště.

Následující příklad je podobný jako předchozí příklad, ale `id` vlastnost slouží k propojení na specifikaci šablony namísto `relativePath` vlastnosti pro propojení s místní šablonou. Používá `2020-06-01` se pro verzi rozhraní API pro prostředek nasazení. V příkladu jsou specifikace šablony ve skupině prostředků s názvem **templateSpecsRG**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Další informace o propojování specifikací šablon najdete v tématu [kurz: nasazení specifikace šablony jako propojené šablony](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Správa verzí

Když vytvoříte specifikaci šablony, zadáte pro ni číslo verze. Při iteraci kódu šablony můžete buď aktualizovat existující verzi (pro opravy hotfix) nebo publikovat novou verzi. Verze je textový řetězec. Můžete se rozhodnout, že budete postupovat podle systému správy verzí, včetně sémantické správy verzí. Uživatelé specifikace šablony můžou poskytnout číslo verze, které chtějí použít při jeho nasazení.

## <a name="next-steps"></a>Další kroky

* Chcete-li vytvořit a nasadit specifikace šablony, přečtěte si téma [rychlý Start: vytvoření a nasazení specifikace šablony](quickstart-create-template-specs.md).

* Další informace o propojování šablon v rámci specifikací šablon najdete v tématu [kurz: Vytvoření specifikace šablony s propojenými šablonami](template-specs-create-linked.md).

* Další informace o nasazení specifikace šablony jako propojené šablony najdete v tématu [kurz: nasazení specifikace šablony jako propojené šablony](template-specs-deploy-linked-template.md).
