---
title: Nasazení šablony what-if (Náhled)
description: Zjistěte, jaké změny se stane s vašimi prostředky před nasazením šablony Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jgao
ms.openlocfilehash: b8e94d0b4f364e2873dfc21792a67f11c33483bf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010184"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operace pro nasazení šablony ARM (preview)

Před nasazením šablony Správce prostředků Azure (ARM) můžete chtít zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operace co by se vám umožní zobrazit, jak se změní prostředky, pokud nasadíte šablonu. Operace "Pokud" neprovede žádné změny existujících prostředků. Místo toho předpovídá změny, pokud je nasazena zadaná šablona.

> [!NOTE]
> Citlivostní operace je aktuálně ve verzi preview. Jako verze preview mohou výsledky někdy ukázat, že se prostředek změní, když se ve skutečnosti nedojde k žádné změně. Pracujeme na snížení těchto problémů, ale potřebujeme vaši pomoc. Tyto problémy [https://aka.ms/whatifissues](https://aka.ms/whatifissues)nahlaste na adrese .

Funkci "Co se ještě chcete" použít s příkazy Prostředí PowerShell nebo s operacemi rozhraní REST API.

## <a name="install-powershell-module"></a>Instalace modulu PowerShell

Pokud chcete v PowerShellu použít what-if, nainstalujte verzi preview modulu Az.Resources z galerie PowerShellu.

### <a name="install-preview-version"></a>Instalace verze náhledu

Chcete-li nainstalovat modul náhledu, použijte:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Odinstalovat alfa verzi

Pokud jste dříve nainstalovali alfa verzi modulu what-if, odinstalujte tento modul. Alfa verze byla dostupná pouze uživatelům, kteří se zaregistrovali k předběžné verzi. Pokud jste tento náhled nenainstalovali, můžete tuto část přeskočit.

1. Spusťte PowerShell jako správce.
1. Zkontrolujte nainstalované verze modulu Az.Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Pokud máte nainstalovanou verzi s číslem verze ve formátu **2.x.x-alpha**, odinstalujte tuto verzi.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Zrušení registrace citlivostního úložiště, které jste použili k instalaci náhledu.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Jste připraveni použít what-if.

## <a name="see-results"></a>Zobrazit výsledky

V Prostředí PowerShell obsahuje výstup barevně odlišené výsledky, které vám pomohou zobrazit různé typy změn.

![Správce prostředků šablony nasazení co-if operace fullresourcepayload a typy změn](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

## <a name="what-if-commands"></a>Co-if příkazy

Pro operaci What-if můžete použít azure prostředí nebo rozhraní AZURE REST API.

### <a name="azure-powershell"></a>Azure PowerShell

Chcete-li zobrazit náhled změn před nasazením `-Whatif` šablony, přidejte parametr switch do příkazu nasazení.

* `New-AzResourceGroupDeployment -Whatif`pro nasazení skupin y prostředků
* `New-AzSubscriptionDeployment -Whatif`a `New-AzDeployment -Whatif` pro nasazení na úrovni předplatného

Nebo můžete použít `-Confirm` parametr switch k zobrazení náhledu změn a zobrazí se výzva k pokračování v nasazení.

* `New-AzResourceGroupDeployment -Confirm`pro nasazení skupin y prostředků
* `New-AzSubscriptionDeployment -Confirm`a `New-AzDeployment -Confirm` pro nasazení na úrovni předplatného

Předchozí příkazy vrátí souhrn textu, který můžete ručně zkontrolovat. Chcete-li získat objekt, který můžete programově zkontrolovat změny, použijte:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`pro nasazení skupin y prostředků
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`nebo `$results = Get-AzDeploymentWhatIfResult` pro nasazení na úrovni předplatného

### <a name="azure-rest-api"></a>Azure REST API

Pro rozhraní REST API použijte:

* [Nasazení – co když pro](/rest/api/resources/deployments/whatif) nasazení skupiny prostředků
* [Nasazení – co když na subscription obor](/rest/api/resources/deployments/whatifatsubscriptionscope) pro nasazení na úrovni předplatného

## <a name="change-types"></a>Změnit typy

Operace IF uvádí šest různých typů změn:

- **Vytvořit**: Prostředek v současné době neexistuje, ale je definován v šabloně. Zdroj bude vytvořen.

- **Odstranit**: Tento typ změny platí pouze při použití [úplného režimu](deployment-modes.md) pro nasazení. Prostředek existuje, ale není definován v šabloně. V režimu dokončení bude prostředek odstraněn. Do tohoto typu změny jsou zahrnuty pouze prostředky, které [podporují úplné odstranění režimu.](complete-mode-deletion.md)

- **Ignorovat**: Prostředek existuje, ale není definován v šabloně. Prostředek nebude nasazen nebo změněn.

- **NoChange**: Prostředek existuje a je definován v šabloně. Prostředek bude znovu nasazen, ale vlastnosti prostředku se nezmění. Tento typ změny je vrácen, `FullResourcePayloads`pokud je [funkce ResultFormat](#result-format) nastavena na hodnotu , což je výchozí hodnota.

- **Změnit**: Prostředek existuje a je definován v šabloně. Prostředek bude znovu nasazen a vlastnosti prostředku se změní. Tento typ změny je vrácen, `FullResourcePayloads`pokud je [funkce ResultFormat](#result-format) nastavena na hodnotu , což je výchozí hodnota.

- **Nasazení**: Prostředek existuje a je definován v šabloně. Prostředek bude znovu nasazen. Vlastnosti prostředku se mohou nebo nemusí změnit. Operace vrátí tento typ změny, pokud nemá dostatek informací k určení, zda se změní všechny vlastnosti. Tato podmínka se zobrazí pouze v `ResourceIdOnly`případě, že je funkce [ResultFormat](#result-format) nastavena na .

## <a name="result-format"></a>Formát výsledků

Můžete řídit úroveň podrobností, která je vrácena o předpovídané změny. V příkazech nasazení`New-Az*Deployment`( ) použijte parametr **-WhatIfResultFormat.** V příkazech programového`Get-Az*DeploymentWhatIf`objektu ( ) použijte parametr **ResultFormat.**

Nastavte parametr format na **FullResourcePayloads,** abyste získali seznam prostředků, které se změní, a podrobnosti o vlastnostech, které se změní. Nastavte parametr format na **ResourceIdOnly** získat seznam prostředků, které se změní. Výchozí hodnota je **FullResourcePayloads**.  

Následující výsledky ukazují dva různé výstupní formáty:

- Úplné datové části prostředků

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

## <a name="run-what-if-operation"></a>Spustit operaci "Pokud o něco"

### <a name="set-up-environment"></a>Nastavení prostředí

Chcete-li zjistit, jak co-li funguje, pojďme spouštět některé testy. Nejprve nasadit [šablonu, která vytvoří virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Tuto virtuální síť použijete k testování toho, jak jsou změny hlášeny what-if.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modifikace testu

Po dokončení nasazení jste připraveni otestovat operaci co-if. Tentokrát nasadit [šablonu, která změní virtuální síť](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Chybí jedna z původních značek, byla odebrána podsíť a předpona adresy byla změněna.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Výstup "pokud", který se podobá:

![Výstup operace nasazení šablony Resource Manageru](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

Všimněte si v horní části výstupu, že barvy jsou definovány k označení typu změn.

V dolní části výstupu se zobrazí značka Owner byla odstraněna. Předpona adresy byla změněna z 10.0.0.0/16 na 10.0.0.0/15. Podsíť s názvem podsíť001 byla odstraněna. Nezapomeňte, že tyto změny nebyly ve skutečnosti nasazeny. Zobrazí se náhled změn, ke kterým dojde, pokud nasadíte šablonu.

Některé vlastnosti, které jsou uvedeny jako odstraněné, se ve skutečnosti nezmění. Vlastnosti mohou být nesprávně hlášeny jako odstraněné, pokud nejsou v šabloně, ale jsou automaticky nastaveny během nasazení jako výchozí hodnoty. Tento výsledek je považován za "šum" v citlivostní odpovědi. Konečný nasazený prostředek bude mít nastavené hodnoty pro vlastnosti. Jako operace co-if zraje, tyto vlastnosti budou odfiltrovány z výsledku.

## <a name="programmatically-evaluate-what-if-results"></a>Programově vyhodnocovat výsledky " co-li

Nyní programově vyhodnotíme výsledky "pokud" nastavením příkazu na proměnnou.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Můžete zobrazit souhrn jednotlivých změn.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Potvrzení odstranění

Operace if if podporuje použití [režimu nasazení](deployment-modes.md). Pokud je nastaven a do režimu dokončení, prostředky, které nejsou v šabloně jsou odstraněny. Následující příklad nasadí [šablonu, která nemá žádné prostředky definované](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) v režimu dokončení.

Chcete-li zobrazit náhled změn před `-Confirm` nasazením šablony, použijte parametr switch s příkazem nasazení. Pokud jsou změny podle očekávání, potvrďte, že chcete nasazení dokončit.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Vzhledem k tomu, že v šabloně nejsou definovány žádné prostředky a režim nasazení je nastaven na dokončení, bude virtuální síť odstraněna.

![Dokončení režimu nasazení výstupu operace správce prostředků správce prostředků dokončení](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

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

Zobrazí očekávané změny a můžete potvrdit, že chcete nasazení spustit.

## <a name="next-steps"></a>Další kroky

- Pokud zaznamenáte nesprávné výsledky z předběžné verze what-if, nahlaste problémy na adrese [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Pokud chcete nasadit šablony s Azure PowerShellem, [přečtěte si tématu Nasazení prostředků pomocí šablon ARM a Azure PowerShellu](deploy-powershell.md).
- Pokud chcete nasadit šablony s REST, [přečtěte si informace o nasazení prostředků pomocí šablon ARM a rozhraní REST API správce prostředků](deploy-rest.md).
