---
title: Template deployment co-if (Preview)
description: Než nasadíte šablonu Azure Resource Manager, určete, jaké změny se budou probíhat u vašich prostředků.
author: mumian
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: jgao
ms.openlocfilehash: 70023f4fa5d44c74c7ce14f3a2c09ff14c9d2f8c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581201"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operace nasazení šablony ARM – if (Preview)

Před nasazením šablony Azure Resource Manager (ARM) můžete zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operaci citlivostní operace, která vám umožní zjistit, jak se prostředky změní, pokud šablonu nasadíte. Operace citlivosti neprovede žádné změny stávajících prostředků. Místo toho odhadne změny, pokud je zadaná šablona nasazena.

> [!NOTE]
> Operace citlivosti je aktuálně ve verzi Preview. Ve verzi Preview můžou výsledky někdy Ukázat, že se prostředek změní, když se ve skutečnosti žádná změna nestane. Pracujeme na tom, abychom tyto problémy snížili, ale potřebujeme vaši technickou podporu. Nahlaste tyto problémy [https://aka.ms/whatifissues](https://aka.ms/whatifissues)na adrese.

Můžete použít operaci s citlivostní operací s Azure PowerShell, Azure CLI nebo REST API operacemi.

## <a name="install-powershell-module"></a>Nainstalovat modul prostředí PowerShell

Pokud chcete v PowerShellu použít co dělat, musíte nainstalovat verzi Preview modulu AZ. Resources z Galerie prostředí PowerShell. Než však modul nainstalujete, ujistěte se, že máte PowerShell Core (6. x nebo 7. x). Pokud máte PowerShell 5. x nebo starší, [aktualizujte svou verzi PowerShellu](/powershell/scripting/install/installing-powershell). Modul pro náhled nemůžete nainstalovat do PowerShellu 5. x nebo staršího.

### <a name="install-preview-version"></a>Verze Preview instalace

Pokud chcete nainstalovat modul verze Preview, použijte:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Odinstalace verze Alpha

Pokud jste dříve nainstalovali verzi Alpha modulu citlivostní instalace, odinstalujte tento modul. Verze alfa byla dostupná jenom pro uživatele, kteří si zaregistrovali předběžnou verzi Preview. Pokud jste tuto verzi Preview nenainstalovali, můžete tuto část přeskočit.

1. Spusťte PowerShell jako správce.
1. Ověřte nainstalované verze modulu AZ. Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Pokud máte nainstalovanou verzi s číslem verze ve formátu **2. x. x-Alpha**, odinstalujte tuto verzi.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Zrušte registraci citlivostního úložiště, které jste použili k instalaci verze Preview.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Jste připraveni použít co dělat.

## <a name="install-azure-cli-module"></a>Instalace modulu CLI Azure

Pokud chcete v Azure CLI použít co dělat, musíte mít Azure CLI 2.5.0 nebo novější. V případě potřeby [nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="what-if-commands"></a>Příkazy citlivostní informace

### <a name="azure-powershell"></a>Azure PowerShell

Chcete-li zobrazit náhled změn před nasazením šablony `-Whatif` , přidejte do příkazu Deployment parametr Switch.

* `New-AzResourceGroupDeployment -Whatif`pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Whatif`a `New-AzDeployment -Whatif` pro nasazení na úrovni předplatného

Pomocí parametru `-Confirm` Switch můžete zobrazit náhled změn a zobrazit výzvu k pokračování v nasazení.

* `New-AzResourceGroupDeployment -Confirm`pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Confirm`a `New-AzDeployment -Confirm` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt, který lze programově kontrolovat změny, použijte:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`pro nasazení skupin prostředků
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`nebo `$results = Get-AzDeploymentWhatIfResult` pro nasazení na úrovni předplatného

### <a name="azure-cli"></a>Azure CLI

Chcete-li zobrazit náhled změn před nasazením `what-if` šablony, použijte příkaz s příkazem nasazení.

* `az deployment group what-if`pro nasazení skupin prostředků
* `az deployment sub what-if`pro nasazení na úrovni předplatného

Můžete použít `--confirm-with-what-if` přepínač (nebo jeho krátký tvar `-c`) k zobrazení náhledu změn a zobrazení výzvy k pokračování v nasazení.

* `az deployment group create --confirm-with-what-if`nebo `-c` pro nasazení skupin prostředků
* `az deployment sub create --confirm-with-what-if`nebo `-c` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt JSON, který můžete programově kontrolovat změny, použijte:

* `az deployment group what-if --no-pretty-print`pro nasazení skupin prostředků
* `az deployment sub what-if --no-pretty-print`pro nasazení na úrovni předplatného

### <a name="azure-rest-api"></a>Azure REST API

Pro REST API použijte:

* [Nasazení – what if](/rest/api/resources/deployments/whatif) pro nasazení skupin prostředků
* [Nasazení – what if v oboru předplatného](/rest/api/resources/deployments/whatifatsubscriptionscope) pro nasazení na úrovni předplatného

## <a name="change-types"></a>Změnit typy

Operace citlivostní zpracování seznamu obsahuje šest různých typů změn:

- **Vytvořit**: prostředek aktuálně neexistuje, ale je definován v šabloně. Prostředek se vytvoří.

- **Odstranit**: Tento typ změny platí pouze v případě, že je pro nasazení použit [režim úplného režimu](deployment-modes.md) . Prostředek existuje, ale není definovaný v šabloně. V režimu úplného režimu se prostředek odstraní. V tomto typu změny jsou zahrnuty pouze prostředky, které [podporují odstranění režimu dokončení](complete-mode-deletion.md) .

- **Ignore**: prostředek existuje, ale není definovaný v šabloně. Prostředek se nebude nasazovat ani upravovat.

- Žádná **Změna**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí, ale vlastnosti prostředku se nezmění. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno `FullResourcePayloads`na, což je výchozí hodnota.

- **Upravit**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí a změní se vlastnosti prostředku. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno `FullResourcePayloads`na, což je výchozí hodnota.

- **Nasazení**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí. Vlastnosti prostředku mohou nebo nemusí být změněny. Tato operace vrátí tento typ změny, pokud nemá dostatek informací k určení, zda se změní některé vlastnosti. Tento stav se zobrazí pouze v [ResultFormat](#result-format) případě, že je `ResourceIdOnly`ResultFormat nastaveno na.

## <a name="result-format"></a>Výsledný formát

Můžete řídit úroveň podrobností, která se vrátí o předpokládaných změnách. Máte dvě možnosti:

* **FullResourcePayloads** – vrátí seznam prostředků, které se změní, a zobrazí podrobnosti o vlastnostech, které se změní.
* **ResourceIdOnly** – vrátí seznam prostředků, které se změní.

Výchozí hodnota je **FullResourcePayloads**.

Pro příkazy nasazení prostředí PowerShell použijte `-WhatIfResultFormat` parametr. V příkazech programového objektu použijte `ResultFormat` parametr.

Pro rozhraní `--result-format` příkazového řádku Azure použijte parametr.
 
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

Výstup citlivosti se zobrazuje jako:

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

V dolní části výstupu se zobrazí vlastník značky byl odstraněn. Předpona adresy se změnila z 10.0.0.0/16 na 10.0.0.0/15. Podsíť s názvem subnet001 se odstranila. Zapamatujte si, že tyto změny nebyly skutečně nasazeny. Zobrazí se náhled změn, ke kterým dojde, pokud šablonu nasadíte.

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

Chcete-li zobrazit náhled změn před nasazením šablony, použijte parametr potvrdit přepínač s příkazem nasazení. Pokud jsou změny podle očekávání, potvrďte, že chcete nasazení dokončit.

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

## <a name="next-steps"></a>Další kroky

- Pokud si všimnete nesprávných výsledků z verze Preview, co-if, nahlaste problémy [https://aka.ms/whatifissues](https://aka.ms/whatifissues)na adrese.
- Postup nasazení šablon pomocí Azure PowerShell najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md).
- Pokud chcete nasadit šablony pomocí Azure CLI, přečtěte si téma [nasazení prostředků pomocí šablon ARM a Azure CLI](deploy-cli.md).
- Pokud chcete nasadit šablony s REST, přečtěte si téma [nasazení prostředků pomocí šablon ARM a Správce prostředků REST API](deploy-rest.md).
