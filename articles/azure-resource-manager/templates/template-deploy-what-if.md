---
title: Template deployment co-if (Preview)
description: Než nasadíte šablonu Azure Resource Manager, určete, jaké změny se budou probíhat u vašich prostředků.
author: mumian
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: jgao
ms.openlocfilehash: b5b19bf9d630230fbdb8cec41cc77718bbbb4585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192378"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operace nasazení šablony ARM – if (Preview)

Před nasazením šablony Azure Resource Manager (ARM) můžete chtít zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operaci citlivostní operace, která vám umožní zjistit, jak se prostředky změní, pokud šablonu nasadíte. Operace citlivosti neprovede žádné změny stávajících prostředků. Místo toho odhadne změny, pokud je zadaná šablona nasazena.

> [!NOTE]
> Operace citlivosti je aktuálně ve verzi Preview. Ve verzi Preview můžou výsledky někdy Ukázat, že se prostředek změní, když se ve skutečnosti žádná změna nestane. Pracujeme na tom, abychom tyto problémy snížili, ale potřebujeme vaši technickou podporu. Nahlaste tyto problémy [https://aka.ms/whatifissues](https://aka.ms/whatifissues)na adrese.

Můžete použít operaci citlivostní operace s příkazy prostředí PowerShell nebo operacemi REST API.

## <a name="install-powershell-module"></a>Nainstalovat modul prostředí PowerShell

Pokud chcete použít co v PowerShellu, musíte mít PowerShell Core (6. x nebo 7. x). Pokud máte PowerShell 5. x nebo starší, [aktualizujte svou verzi PowerShellu](/powershell/scripting/install/installing-powershell).

Po ujištění, že máte správnou verzi PowerShellu, nainstalujte do galerie PowerShellu verzi Preview modulu AZ. Resources.

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

## <a name="see-results"></a>Zobrazit výsledky

Výstup v prostředí PowerShell obsahuje barevně kódované výsledky, které vám pomůžou se zobrazením různých typů změn.

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

Pro operaci citlivostní operace můžete použít buď Azure PowerShell, nebo Azure REST API.

### <a name="azure-powershell"></a>Azure PowerShell

Chcete-li zobrazit náhled změn před nasazením šablony, přidejte do příkazu `-Whatif` nasazení parametr Switch.

* `New-AzResourceGroupDeployment -Whatif`pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Whatif`a `New-AzDeployment -Whatif` pro nasazení na úrovni předplatného

Nebo můžete použít parametr `-Confirm` Switch k zobrazení náhledu změn a zobrazit výzvu k pokračování v nasazení.

* `New-AzResourceGroupDeployment -Confirm`pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Confirm`a `New-AzDeployment -Confirm` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt, který lze programově kontrolovat změny, použijte:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`pro nasazení skupin prostředků
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`nebo `$results = Get-AzDeploymentWhatIfResult` pro nasazení na úrovni předplatného

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

Můžete řídit úroveň podrobností vrácených o předpokládaných změnách. V příkazech nasazení (`New-Az*Deployment`) použijte parametr **-WhatIfResultFormat** . V příkazech programového objektu`Get-Az*DeploymentWhatIf`() použijte parametr **ResultFormat** .

Nastavte parametr Format na **FullResourcePayloads** , abyste získali seznam prostředků, které se změní, a podrobnosti o vlastnostech, které se změní. Nastavte parametr Format na **ResourceIdOnly** a získejte tak seznam prostředků, které se změní. Výchozí hodnota je **FullResourcePayloads**.  

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

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Změna testu

Po dokončení nasazení budete připraveni k otestování operace citlivostní zpracování. Tentokrát nasadíte [šablonu, která mění virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Chybí původní značky, podsíť se odebrala a předpona adresy se změnila.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

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

## <a name="confirm-deletion"></a>Potvrzení odstranění

Operace citlivosti podporuje použití [režimu nasazení](deployment-modes.md). Při nastavení na režim úplné se odstraní prostředky, které nejsou v šabloně. Následující příklad nasadí [šablonu, která nemá definované žádné prostředky](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) v režimu úplného zobrazení.

Chcete-li zobrazit náhled změn před nasazením šablony `-Confirm` , použijte parametr Switch s příkazem nasazení. Pokud se změní podle očekávání, potvrďte, že chcete nasazení dokončit.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

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
- Pokud chcete nasadit šablony s REST, přečtěte si téma [nasazení prostředků pomocí šablon ARM a Správce prostředků REST API](deploy-rest.md).
