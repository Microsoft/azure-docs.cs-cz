---
title: Template deployment co-if (Preview)
description: Než nasadíte šablonu Azure Resource Manager, určete, jaké změny se budou probíhat u vašich prostředků.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388526"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Operace s citlivostí pro nasazení šablony Správce prostředků (Preview)

Před nasazením šablony můžete chtít zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operaci citlivostní operace, která vám umožní zjistit, jak se prostředky změní, pokud šablonu nasadíte. Operace citlivosti neprovede žádné změny stávajících prostředků. Místo toho odhadne změny, pokud je zadaná šablona nasazena.

> [!NOTE]
> Operace citlivosti je aktuálně ve verzi Preview. Pokud ho chcete použít, musíte se [zaregistrovat ve verzi Preview](https://aka.ms/armtemplatepreviews). Ve verzi Preview můžou výsledky někdy Ukázat, že se prostředek změní, když se ve skutečnosti žádná změna nestane. Pracujeme na tom, abychom tyto problémy snížili, ale potřebujeme vaši technickou podporu. Ohlaste prosím tyto problémy na [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Můžete použít operaci citlivostní operace s příkazy prostředí PowerShell nebo operacemi REST API.

Výstup v prostředí PowerShell obsahuje barevně kódované výsledky, které vám pomůžou se zobrazením různých typů změn.

![Správce prostředků fullresourcepayload a typ změny nasazení šablony](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Ouptput textu je:

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

### <a name="azure-powershell"></a>Azure Powershell

Chcete-li zobrazit náhled změn před nasazením šablony, přidejte do příkazu Deployment parametr `-Whatif` Switch.

* `New-AzResourceGroupDeployment -Whatif` pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Whatif` a `New-AzDeployment -Whatif` pro nasazení na úrovni předplatného

Nebo můžete použít parametr `-Confirm` Switch k zobrazení náhledu změn a zobrazit výzvu k pokračování v nasazení.

* `New-AzResourceGroupDeployment -Confirm` pro nasazení skupin prostředků
* `New-AzSubscriptionDeployment -Confirm` a `New-AzDeployment -Confirm` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí textový souhrn, který můžete ručně zkontrolovat. Chcete-li získat objekt, který lze programově kontrolovat změny, použijte:

* `$results = Get-AzResourceGroupDeploymentWhatIf` pro nasazení skupin prostředků
* `$results = Get-AzSubscriptionDeploymentWhatIf` nebo `$results = Get-AzDeploymentWhatIf` pro nasazení na úrovni předplatného

> [!NOTE]
> Před vydáním verze 2.0.1 – alpha5 jste použili příkaz `New-AzDeploymentWhatIf`. Tento příkaz byl nahrazen příkazy `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`a `Get-AzSubscriptionDeploymentWhatIf`. Pokud jste použili starší verzi, je třeba tuto syntaxi aktualizovat. Byl odebrán parametr `-ScopeType`.

### <a name="azure-rest-api"></a>REST API Azure

Pro REST API použijte:

* [Nasazení – what if](/rest/api/resources/deployments/whatif) pro nasazení skupin prostředků
* [Nasazení – what if v oboru předplatného](/rest/api/resources/deployments/whatifatsubscriptionscope) pro nasazení na úrovni předplatného

## <a name="change-types"></a>Změnit typy

Operace citlivostní zpracování seznamu obsahuje šest různých typů změn:

- **Vytvořit**: prostředek aktuálně neexistuje, ale je definován v šabloně. Prostředek se vytvoří.

- **Odstranit**: Tento typ změny platí pouze v případě, že je pro nasazení použit [režim úplného režimu](deployment-modes.md) . Prostředek existuje, ale není definovaný v šabloně. V režimu úplného režimu se prostředek odstraní. V tomto typu změny jsou zahrnuty pouze prostředky, které [podporují odstranění režimu dokončení](complete-mode-deletion.md) .

- **Ignore**: prostředek existuje, ale není definovaný v šabloně. Prostředek se nebude nasazovat ani upravovat.

- Žádná **Změna**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí, ale vlastnosti prostředku se nezmění. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads`, což je výchozí hodnota.

- **Upravit**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí a změní se vlastnosti prostředku. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads`, což je výchozí hodnota.

- **Nasazení**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí. Vlastnosti prostředku mohou nebo nemusí být změněny. Tato operace vrátí tento typ změny, pokud nemá dostatek informací k určení, zda se změní některé vlastnosti. Tato podmínka se zobrazí jenom v případě, že je [ResultFormat](#result-format) nastavené na `ResourceIdOnly`.

## <a name="result-format"></a>Výsledný formát

Můžete řídit úroveň podrobností vrácených o předpokládaných změnách. V příkazech nasazení (`New-Az*Deployment`) použijte parametr **-WhatIfResultFormat** . V příkazech programového objektu (`Get-Az*DeploymentWhatIf`) použijte parametr **ResultFormat** .

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

Po dokončení nasazení budete připraveni k otestování operace citlivostní zpracování. Tentokrát nasadíte [šablonu, která mění virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Chybí vám původní značky, podsíť se odebrala a předpona adresy se změnila.

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
$results = Get-AzResourceGroupDeploymentWhatIf `
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

Chcete-li před nasazením šablony zobrazit náhled změn, použijte parametr `-Confirm` switch s příkazem nasazení. Pokud se změní podle očekávání, potvrďte, že chcete nasazení dokončit.

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

- Pokud si všimnete špatných výsledků z verze Preview co-if, nahlaste prosím problémy na [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Postup nasazení šablon pomocí Azure PowerShell najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](deploy-powershell.md).
- Pokud chcete nasadit šablony s REST, přečtěte si téma [nasazení prostředků pomocí šablon Správce prostředků a Správce prostředků REST API](deploy-rest.md).
