---
title: Template deployment citlivosti
description: Než nasadíte šablonu Azure Resource Manager, určete, jaké změny se budou probíhat u vašich prostředků.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 3f35f00e5fcd26c7aa4bb685dc80f48eabb88b6d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547930"
---
# <a name="arm-template-deployment-what-if-operation"></a>Operace what-if (citlivostní analýza) při nasazení šablony ARM

Před nasazením šablony Azure Resource Manager (šablona ARM) můžete zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operaci citlivostní operace, která vám umožní zjistit, jak se prostředky změní, pokud šablonu nasadíte. Operace what-if neprovede žádné změny stávajících prostředků. Místo toho předpoví změny, k nimž by došlo při nasazení zadané šablony.

Můžete použít operaci s citlivostní operací s Azure PowerShell, Azure CLI nebo REST API operacemi. Co když je podporováno pro nasazení skupiny prostředků, předplatného, skupiny pro správu a na úrovni tenanta.

## <a name="install-azure-powershell-module"></a>Nainstalovat modul Azure PowerShell

Pokud chcete v PowerShellu použít co v prostředí PowerShell, musíte mít verzi **4,2 nebo novější z modulu AZ Module**.

K instalaci modulu použijte:

```powershell
Install-Module -Name Az -Force
```

Další informace o instalaci modulů najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Instalace modulu CLI Azure

Pokud chcete v Azure CLI použít operaci what-if, musíte mít Azure CLI 2.5.0 nebo novější. V případě potřeby si [nainstalujte nejnovější verzi Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Zobrazit výsledky

Když v prostředí PowerShell nebo Azure CLI použijete co dělat, výstup obsahuje barevně kódované výsledky, které vám pomůžou zobrazit různé typy změn.

![Správce prostředků fullresourcepayload a typ změny nasazení šablony](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textový výstup je:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> Operace citlivosti nemůže vyřešit [odkazovou funkci](template-functions-resource.md#reference). Pokaždé, když nastavíte vlastnost na výraz šablony, který obsahuje referenční funkci, co-if hlásí vlastnost se změní. K tomuto chování dochází, protože co porovná aktuální hodnotu vlastnosti (například `true` nebo `false` pro logickou hodnotu) s nerozpoznaným výrazem šablony. Tyto hodnoty se tak zjevně neshodují. Když nasadíte šablonu, vlastnost se změní pouze v případě, že je výraz šablony přeložen na jinou hodnotu.

## <a name="what-if-commands"></a>Příkazy citlivostní informace

### <a name="azure-powershell"></a>Azure PowerShell

Chcete-li zobrazit náhled změn před nasazením šablony, použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) nebo [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Přidejte `-Whatif` parametr Switch do příkazu pro nasazení.

* `New-AzResourceGroupDeployment -Whatif` pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Whatif` a `New-AzDeployment -Whatif` pro nasazení na úrovni předplatného

Pomocí `-Confirm` parametru Switch můžete zobrazit náhled změn a zobrazit výzvu k pokračování v nasazení.

* `New-AzResourceGroupDeployment -Confirm` pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Confirm` a `New-AzDeployment -Confirm` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt, který můžete programově kontrolovat změny, použijte příkaz [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) nebo [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` pro nasazení skupin prostředků
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` nebo `$results = Get-AzDeploymentWhatIfResult` pro nasazení na úrovni předplatného

### <a name="azure-cli"></a>Azure CLI

Chcete-li zobrazit náhled změn před nasazením šablony, použijte:

* [AZ Deployment Group Co-if](/cli/azure/deployment/group#az-deployment-group-what-if) pro nasazení skupin prostředků
* [AZ Deployment sub a if](/cli/azure/deployment/sub#az-deployment-sub-what-if) on Deployment na úrovni předplatného
* [AZ Deployment mg co-if](/cli/azure/deployment/mg#az-deployment-mg-what-if) pro nasazení skupin pro správu
* [AZ Deployment tenant co-if](/cli/azure/deployment/tenant#az-deployment-tenant-what-if) pro nasazení klientů

Můžete použít `--confirm-with-what-if` přepínač (nebo jeho krátký tvar `-c` ) k zobrazení náhledu změn a zobrazení výzvy k pokračování v nasazení. Přidat tento přepínač do:

* [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create)
* [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [AZ Deployment g Create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [AZ Deployment tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Například použijte `az deployment group create --confirm-with-what-if` nebo `-c` pro nasazení skupiny prostředků.

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt JSON, který můžete programově kontrolovat změny, použijte `--no-pretty-print` přepínač. Použijte například `az deployment group what-if --no-pretty-print` pro nasazení skupiny prostředků.

Pokud chcete vrátit výsledky bez barev, otevřete [konfigurační soubor Azure CLI](/cli/azure/azure-cli-configuration) . Nastavte **no_color** na **Ano**.

### <a name="azure-rest-api"></a>Azure REST API

Pro REST API použijte:

* [Nasazení – what if](/rest/api/resources/deployments/whatif) pro nasazení skupin prostředků
* [Nasazení – what if v oboru předplatného](/rest/api/resources/deployments/whatifatsubscriptionscope) pro nasazení předplatných
* [Nasazení – what if v oboru skupiny pro správu](/rest/api/resources/deployments/whatifatmanagementgroupscope) pro nasazení skupin pro správu
* [Nasazení – what if v oboru tenanta](/rest/api/resources/deployments/whatifattenantscope) pro nasazení klientů.

## <a name="change-types"></a>Typy změn

Operace citlivostní zpracování seznamu obsahuje šest různých typů změn:

- **Vytvořit**: prostředek aktuálně neexistuje, ale je definován v šabloně. Prostředek se vytvoří.

- **Odstranit**: Tento typ změny platí pouze v případě, že je pro nasazení použit [režim úplného režimu](deployment-modes.md) . Prostředek existuje, ale není v šabloně definovaný. V úplném režimu se prostředek odstraní. V tomto typu změny jsou zahrnuty pouze prostředky, které [podporují odstranění režimu dokončení](complete-mode-deletion.md) .

- **Ignore**: prostředek existuje, ale není definovaný v šabloně. Prostředek se nebude nasazovat ani upravovat.

- Žádná **Změna**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí, ale vlastnosti prostředku se nezmění. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads` , což je výchozí hodnota.

- **Upravit**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí a vlastnosti prostředku se změní. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads` , což je výchozí hodnota.

- **Nasazení**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí. Vlastnosti prostředku se mohou nebo nemusí změnit. Operace vrátí tento typ změny, pokud nemá dostatek informací k určení, jestli se některé vlastnosti změní. Tento stav se zobrazí pouze v případě, že je [ResultFormat](#result-format) nastaveno na `ResourceIdOnly` .

## <a name="result-format"></a>Formát výsledku

Můžete řídit úroveň podrobností, která se vrátí o předpokládaných změnách. Máte dvě možnosti:

* **FullResourcePayloads** – vrátí seznam prostředků, které se změní, a zobrazí podrobnosti o vlastnostech, které se změní.
* **ResourceIdOnly** – vrátí seznam prostředků, které se změní.

Výchozí hodnota je **FullResourcePayloads**.

Pro příkazy nasazení prostředí PowerShell použijte `-WhatIfResultFormat` parametr. V příkazech programového objektu použijte `ResultFormat` parametr.

Pro rozhraní příkazového řádku Azure použijte `--result-format` parametr.
 
Následující výsledky znázorňují dva různé formáty výstupu:

- Úplné datové vytížení prostředků

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Pouze ID prostředku

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Spustit operaci s citlivostí

### <a name="set-up-environment"></a>Nastavení prostředí

Pokud chcete zjistit, jak to funguje, spustíme některé testy. Nejdřív nasaďte [šablonu, která vytvoří virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Tuto virtuální síť použijete k otestování toho, jak jsou změny hlášeny podle toho, co je potřeba.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Změna testu

Po dokončení nasazení budete připraveni k otestování operace citlivostní zpracování. Tentokrát nasadíte [šablonu, která mění virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Chybí původní značky, podsíť se odebrala a předpona adresy se změnila.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

Výstup citlivostní analýzy bude vypadat asi takto:

![Výstup operace s popisem operace s Správce prostředků šablonou](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textový výstup je:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

V horní části výstupu si všimněte, že jsou definovány barvy, aby označovaly typ změn.

V dolní části výstupu se zobrazí vlastník značky byl odstraněn. Předpona adresy se změnila z 10.0.0.0/16 na 10.0.0.0/15. Podsíť s názvem subnet001 se odstranila. Zapamatujte si, že tyto změny nebyly nasazeny. Zobrazí se náhled změn, ke kterým dojde, pokud šablonu nasadíte.

Některé vlastnosti, které jsou uvedené jako odstraněné, se ve skutečnosti nezmění. Vlastnosti mohou být nesprávně hlášeny jako odstraněné, pokud nejsou v šabloně, ale budou automaticky nastaveny během nasazování jako výchozí hodnoty. Tento výsledek se považuje za "hluk" v reakci citlivosti. Konečný nasazený prostředek bude mít nastavené hodnoty pro vlastnosti. V případě, že se operace co-if dospěla, tyto vlastnosti se vyfiltrují z výsledku.

## <a name="programmatically-evaluate-what-if-results"></a>Programové vyhodnocení výsledků

Nyní programicky vyhodnotí výsledky citlivosti nastavením příkazu na proměnnou.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Můžete zobrazit souhrn každé změny.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Potvrzení odstranění

Operace citlivosti podporuje použití [režimu nasazení](deployment-modes.md). Při nastavení na režim úplné se odstraní prostředky, které nejsou v šabloně. Následující příklad nasadí [šablonu, která nemá definované žádné prostředky](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) v režimu úplného zobrazení.

Pokud chcete zobrazit náhled změn před nasazením šablony, použijte v příkazu pro nasazení parametr confirm switch. Pokud jsou změny podle očekávání, odpovězte na to, že chcete nasazení dokončit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Vzhledem k tomu, že v šabloně nejsou definovány žádné prostředky a režim nasazení je nastaven na dokončeno, bude virtuální síť odstraněna.

![Správce prostředků režim nasazení výstupu operace citlivosti nasazení šablon](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Textový výstup je:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Zobrazí se očekávané změny a můžete potvrdit, že se nasazení má spustit.

## <a name="sdks"></a>Sady SDK

Můžete použít operaci citlivostní zpracování prostřednictvím sad Azure SDK.

* V případě Pythonu použijte [co dělat](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* Pro jazyk Java použijte [třídu DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* Pro .NET použijte [třídu DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Další kroky

- Pokud chcete použít operaci citlivostní operace v kanálu, přečtěte si téma [test šablon ARM pomocí What-If v kanálu](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
- Pokud si všimnete nesprávných výsledků operace citlivostní zpracování, nahlaste prosím problémy na adrese [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Microsoft Learn modul, který se zabývá používáním možnosti co když, najdete v tématu [Náhled změn a ověření prostředků Azure pomocí nástrojů co-if a šablony ARM test Toolkit](/learn/modules/arm-template-test/).
