---
title: Správa přiřazení pomocí PowerShellu
description: Naučte se spravovat přiřazení podrobných plánů pomocí oficiálního modulu PowerShellu pro Azure modrotisky, AZ. detail.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232889"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Správa přiřazení pomocí PowerShellu

Přiřazení podrobného plánu je možné spravovat pomocí modulu **AZ. detail** Azure PowerShell Module. Modul podporuje načítání, vytváření, aktualizaci a odebírání přiřazení. Modul může také načíst podrobnosti o stávajících definicích podrobného plánu. Tento článek popisuje, jak nainstalovat modul a začít ho používat.

## <a name="add-the-azblueprint-module"></a>Přidání modulu AZ. detail Module

Aby bylo možné Azure PowerShell spravovat přiřazení podrobných plánů, modul musí být přidán. Tento modul se dá použít s místně nainstalovaným PowerShellem, s [Azure Cloud Shell](https://shell.azure.com), nebo s [imagí Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure modrotisky vyžaduje následující software:

- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Nainstalovat modul

Modul modrotisky pro PowerShell je **AZ. detail**.

1. Na příkazovém řádku PowerShellu **pro správu** spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Pokud je už nainstalovaná **AZ. Accounts** , může být nutné použít `-AllowClobber` k vynucení instalace.

1. Ověřte, že je modul naimportovaný a má správnou verzi (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Získat definice podrobného plánu

Prvním krokem pro práci s přiřazením je často získání odkazu na definici podrobného plánu.
`Get-AzBlueprint` Rutina získá jednu nebo více definic podrobného plánu. Rutina může získat definice podrobného plánu ze skupiny pro `-ManagementGroupId {mgId}` správu s nebo s `-SubscriptionId {subId}`předplatným. Parametr **Name** získá definici podrobného plánu, ale musí být použit s **ManagementGroupId** nebo **SubscriptionId**. **Verzi** lze použít s **názvem** , aby byla lépe explicitní o tom, která definice podrobného plánu se vrátí. Místo **verze**se v přepínači `-LatestPublished` přesměruje naposledy publikovaná verze.

Následující příklad používá `Get-AzBlueprint` k získání všech verzí definice podrobného plánu s názvem "101-modrotisky-definition-Subscription" z konkrétního předplatného `{subId}`reprezentovaného jako:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Ukázkový výstup pro definici podrobného plánu s více verzemi vypadá takto:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

[Parametry](../concepts/parameters.md#blueprint-parameters) podrobného plánu v definici podrobného plánu lze rozšířit tak, aby poskytovaly Další informace.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Získat přiřazení podrobného plánu

Pokud přiřazení podrobného plánu už existuje, můžete na něj získat odkaz pomocí `Get-AzBlueprintAssignment` rutiny. Rutina přijímá **SubscriptionId** a **název** jako volitelné parametry. Pokud není zadané **SubscriptionId** , použije se aktuální kontext předplatného.

Následující příklad používá `Get-AzBlueprintAssignment` k získání jednoho přiřazení podrobného plánu s názvem "přiřazení-zámek-Resource-Groups" z konkrétního předplatného reprezentovaného jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Ukázkový výstup přiřazení podrobného plánu vypadá takto:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Vytvořit přiřazení podrobného plánu

Pokud přiřazení podrobného plánu ještě neexistuje, můžete ho vytvořit pomocí `New-AzBlueprintAssignment` rutiny. Tato rutina používá následující parametry:

- **Název** požadovanou
  - Určuje název přiřazení podrobného plánu.
  - Musí být jedinečné a v **SubscriptionId** již neexistují.
- Podrobný **plán** požadovanou
  - Určuje definici podrobného plánu, která se má přiřadit.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu.
- **Umístění** požadovanou
  - Určuje oblast pro objekt nasazení spravované identity a předplatného přiřazený systémem, který se má vytvořit v.
- **Předplatné** volitelné
  - Určuje předplatné, na které se přiřazení nasazuje.
  - Pokud není zadaný, použije se výchozí kontext aktuálního předplatného.
- **Zamknout** volitelné
  - Definuje [uzamykání prostředků](../concepts/resource-locking.md) podrobného plánu, které se má použít pro nasazené prostředky.
  - Podporované možnosti: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Pokud není zadaný, použije se výchozí hodnota _none_ .
- **SystemAssignedIdentity** volitelné
  - Tuto možnost vyberte, pokud chcete vytvořit spravovanou identitu přiřazenou systémem pro přiřazení a nasadit prostředky.
  - Výchozí hodnota pro sadu parametrů identity
  - Nejde použít s **UserAssignedIdentity**
- **UserAssignedIdentity** volitelné
  - Určuje uživatelem přiřazenou identitu, která se má použít pro přiřazení a nasazení prostředků.
  - Část sady parametrů identity
  - Nejde použít s **SystemAssignedIdentity**
- **Parametr** volitelné
  - [Zatřiďovací tabulka](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) u přiřazení podrobného plánu
  - Výchozí hodnota pro dynamický parametr je vlastnost **DefaultValue** v definici.
  - Pokud parametr není zadaný a nemá hodnotu **DefaultValue**, parametr není nepovinný.

    > [!NOTE]
    > **Parametr** nepodporuje secureStrings.

- **ResourceGroupParameter** volitelné
  - [Zatřiďovací tabulka](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý zástupný symbol pro všechny skupiny prostředků bude mít páry klíč/hodnota pro dynamické nastavování **názvu** nebo **umístění** v rámci artefaktu skupiny prostředků.
  - Pokud není zadaný parametr skupiny prostředků a nemá žádnou hodnotu **DefaultValue**, parametr skupiny prostředků není nepovinný.

Následující příklad vytvoří nové přiřazení verze "1,1" definice podrobného plánu "My-detail", která byla načtena s `Get-AzBlueprint`, nastaví spravovanou identitu a umístění objektu přiřazení na hodnotu "westus2", zamkne prostředky pomocí _AllResourcesReadOnly_ a nastaví zatřiďovací tabulky pro **parametr** i **ResourceGroupParameter** pro konkrétní předplatné reprezentované jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Ukázkový výstup pro vytvoření přiřazení podrobného plánu vypadá takto:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Aktualizovat přiřazení podrobného plánu

Někdy je potřeba aktualizovat přiřazení podrobného plánu, který už je vytvořený. `Set-AzBlueprintAssignment` Rutina tuto akci zpracuje. Rutina přebírá většinu stejných parametrů, které `New-AzBlueprintAssignment` provádí rutina, a umožňuje tak, aby se aktualizovala veškerá nastavení na přiřazení. Výjimkou jsou _název_, podrobný _plán_a _SubscriptionId_. Aktualizují se jenom zadané hodnoty.

Informace o tom, co se stane při aktualizaci přiřazení podrobného plánu, najdete v tématu [pravidla pro aktualizaci přiřazení](./update-existing-assignments.md#rules-for-updating-assignments).

- **Název** požadovanou
  - Určuje název přiřazení podrobného plánu, který se má aktualizovat.
  - Slouží k vyhledání přiřazení, které se má aktualizovat, nikoli Změna přiřazení.
- Podrobný **plán** požadovanou
  - Určuje definici podrobného plánu přiřazení podrobného plánu.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu.
  - Slouží k vyhledání přiřazení, které se má aktualizovat, nikoli Změna přiřazení.
- **Umístění** volitelné
  - Určuje oblast pro objekt nasazení spravované identity a předplatného přiřazený systémem, který se má vytvořit v.
- **Předplatné** volitelné
  - Určuje předplatné, na které se přiřazení nasazuje.
  - Pokud není zadaný, použije se výchozí kontext aktuálního předplatného.
  - Slouží k vyhledání přiřazení, které se má aktualizovat, nikoli Změna přiřazení.
- **Zamknout** volitelné
  - Definuje [uzamykání prostředků](../concepts/resource-locking.md) podrobného plánu, které se má použít pro nasazené prostředky.
  - Podporované možnosti: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** volitelné
  - Tuto možnost vyberte, pokud chcete vytvořit spravovanou identitu přiřazenou systémem pro přiřazení a nasadit prostředky.
  - Výchozí hodnota pro sadu parametrů identity
  - Nejde použít s **UserAssignedIdentity**
- **UserAssignedIdentity** volitelné
  - Určuje uživatelem přiřazenou identitu, která se má použít pro přiřazení a nasazení prostředků.
  - Část sady parametrů identity
  - Nejde použít s **SystemAssignedIdentity**
- **Parametr** volitelné
  - [Zatřiďovací tabulka](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) u přiřazení podrobného plánu
  - Výchozí hodnota pro dynamický parametr je vlastnost **DefaultValue** v definici.
  - Pokud parametr není zadaný a nemá hodnotu **DefaultValue**, parametr není nepovinný.

    > [!NOTE]
    > **Parametr** nepodporuje secureStrings.

- **ResourceGroupParameter** volitelné
  - [Zatřiďovací tabulka](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý zástupný symbol pro všechny skupiny prostředků bude mít páry klíč/hodnota pro dynamické nastavování **názvu** nebo **umístění** v rámci artefaktu skupiny prostředků.
  - Pokud není zadaný parametr skupiny prostředků a nemá žádnou hodnotu **DefaultValue**, parametr skupiny prostředků není nepovinný.

Následující příklad aktualizuje přiřazení verze "1,1" definice podrobného plánu "My-detailed", která byla načtena `Get-AzBlueprint` se změnou režimu zámku:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Ukázkový výstup pro vytvoření přiřazení podrobného plánu vypadá takto:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Odebrat přiřazení podrobného plánu

Když je čas na odebrání přiřazení podrobného plánu, `Remove-AzBlueprintAssignment` rutina tuto akci zpracuje. Rutina přebírá buď **název** , nebo **položky InputObject** , který určuje, které přiřazení podrobného plánu se má odebrat. **SubscriptionId** je _povinné_ a musí být zadáno ve všech případech.

Následující příklad načte existující přiřazení podrobného plánu s `Get-AzBlueprintAssignment` a pak ho odebere z konkrétního předplatného, které je reprezentované jako: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Příklad kódu na konci

Když propojíte všechny kroky společně, následující příklad získá definici podrobného plánu a pak vytvoří, aktualizuje a odebere přiřazení podrobného plánu v rámci konkrétního `{subId}`předplatného reprezentovaného jako:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [životním cyklu](../concepts/lifecycle.md)podrobného plánu.
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)