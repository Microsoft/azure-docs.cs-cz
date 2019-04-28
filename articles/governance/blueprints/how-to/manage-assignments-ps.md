---
title: Jak spravovat přiřazení pomocí Powershellu
description: Zjistěte, jak spravovat přiřazení podrobného plánu se oficiální plány modul Azure PowerShell, Az.Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682828"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Jak spravovat přiřazení pomocí Powershellu

Přiřazení podrobného plánu se dá spravovat **Az.Blueprint** modulu Azure PowerShell. Modul podporuje načítání, vytváření, aktualizace nebo odebrání přiřazení. Modul můžete také načíst podrobnosti pro existující definice podrobného plánu. Tento článek popisuje postup instalace modulu a začít ji používat.

## <a name="add-the-azblueprint-module"></a>Přidejte modul Az.Blueprint

Pokud chcete povolit Azure Powershellu ke správě přiřazení podrobného plánu, je nutné přidat modul. Tento modul je možné místně nainstalovaný PowerShell s [Azure Cloud Shell](https://shell.azure.com), nebo se [image Dockeru Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Základní požadavky

Plány Azure modul vyžaduje následující software:

- Prostředí Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalace modulu

Modul plány pro prostředí PowerShell je **Az.Blueprint**.

1. Ze **správu** příkazový řádek Powershellu, spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Pokud **Az.Accounts** je už nainstalovaná, může být potřeba použít `-AllowClobber` chcete vynutit instalaci.

1. Ověřte, že v modulu byla naimportována a správnou verzi (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Získat definice podrobného plánu

Prvním krokem při práci s přiřazení je často získání odkazu na definici podrobného plánu.
`Get-AzBlueprint` Rutina načte jeden nebo více definice podrobného plánu. Rutinu můžete získat definice podrobného plánu ze skupiny pro správu s `-ManagementGroupId {mgId}` nebo předplatné s `-SubscriptionId {subId}`. **Název** parametr získá definice podrobného plánu, ale musí být použit s **ManagementGroupId** nebo **SubscriptionId**. **Verze** jde použít s **název** více explicitně, o kterou definici podrobného plánu se vrátí. Místo **verze**, přepínač `-LatestPublished` získá naposledy publikované verze.

Následující příklad používá `Get-AzBlueprint` zobrazíte všechny verze definice podrobného plánu s názvem "101 plány definice předplatnými" z konkrétní předplatné, které reprezentují `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Příklad výstupu pro definici podrobného plánu s více verzemi vypadá takto:

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

[Podrobný plán parametry](../concepts/parameters.md#blueprint-parameters) na podrobný plán se rozšířit definice k poskytnutí dalších informací.

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

Pokud přiřazení podrobného plánu už existuje, můžete získat na ni se odkaz `Get-AzBlueprintAssignment` rutiny. Přijímá rutina **SubscriptionId** a **název** jako volitelné parametry. Pokud **SubscriptionId** není zadán, slouží k nastavení aktuálního kontextu předplatného.

Následující příklad používá `Get-AzBlueprintAssignment` získat jeden plán přiřazení z konkrétní předplatné reprezentovaná jako s názvem "Přiřazení – lock-resource-groups" `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Příklad výstupu pro přiřazení podrobného plánu vypadá takto:

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

Pokud přiřazení podrobného plánu ještě neexistuje, můžete ho vytvořit `New-AzBlueprintAssignment` rutiny. Tato rutina používá následující parametry:

- **Název** [povinné]
  - Určuje název přiřazení podrobného plánu
  - Musí být jedinečný a není již existují v **ID předplatného**
- **Podrobný plán** [povinné]
  - Určuje definici podrobného plánu přiřazení
  - Použití `Get-AzBlueprint` k získání odkazu na objekt
- **Umístění** [povinné]
  - Určuje oblast pro systém přiřadil spravované identity a předplatné nasazení objektu bude vytvořena ve
- **Předplatné** (volitelné)
  - Určuje předplatné, které přiřazení je nasazený do
  - Pokud není zadaný, použije se výchozí nastavení aktuálního kontextu předplatného
- **Zámek** (volitelné)
  - Definuje [podrobný plán uzamčení prostředků](../concepts/resource-locking.md) pro nasazených prostředků
  - Podporované možnosti: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Pokud není zadaný, použije se výchozí _None_
- **SystemAssignedIdentity** (volitelné)
  - Vyberte systém přiřadil spravovanou identitu pro přiřazení vytvoření a nasazení prostředků
  - Výchozí nastavení pro sadu parametrů "identity"
  - Nelze použít s **UserAssignedIdentity**
- **UserAssignedIdentity** (volitelné)
  - Určuje uživatelsky přiřazené spravovaná identita má použít pro přiřazení a k nasazení prostředků
  - Součástí sady "identity"
  - Nelze použít s **SystemAssignedIdentity**
- **Parametr** (volitelné)
  - A [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) na přiřazení podrobného plánu
  - Výchozí pro dynamický parametr je **defaultValue** v definici
  - Pokud parametr není k dispozici a nemá žádné **defaultValue**, není-li volitelný parametr

    > [!NOTE]
    > **Parametr** secureStrings nepodporuje.

- **ResourceGroupParameter** (volitelné)
  - A [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý prostředek skupiny artefaktů zástupný symbol bude mít páry klíč/hodnota pro dynamicky nastavení **název** a/nebo **umístění** na tento artefakt skupiny prostředků
  - Pokud parametr skupiny prostředků není k dispozici a nemá žádné **defaultValue**, není-li volitelný parametr skupiny prostředků

Následující příklad vytvoří nové přiřazení verze "1.1" definition 'my podrobného plánu' plán pomocí `Get-AzBlueprint`, nastaví spravované identity a přiřazení umístění objektu pro "westus2", uzamkne prostředky s  _AllResourcesReadOnly_a nastaví zatřiďovacích tabulek pro obě **parametr** a **ResourceGroupParameter** na konkrétní předplatné, které jsou reprezentovány jako `{subId}`:

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

Příklad výstupu pro vytváření přiřazení podrobného plánu vypadá takto:

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

Někdy je potřeba aktualizovat přiřazení podrobného plánu, který již byl vytvořen. `Set-AzBlueprintAssignment` Rutina zpracovává tuto akci. Většina stejné parametry přijímá rutina, která `New-AzBlueprintAssignment` rutina nemá povolení cokoli, co byl nastaven na přiřazení aktualizovat. Výjimky jsou _název_, _podrobného plánu_, a _SubscriptionId_. Aktualizují se jenom hodnoty, které jsou k dispozici.

Vysvětlení, co se stane při aktualizaci přiřazení podrobného plánu, najdete v tématu [pravidla aktualizace přiřazení](./update-existing-assignments.md#rules-for-updating-assignments).

- **Název** [povinné]
  - Určuje název přiřazení podrobného plánu aktualizace
  - Používaná k nalezení přiřazení aktualizovat, nechcete změnit přiřazení
- **Podrobný plán** [povinné]
  - Určuje definici podrobného plánu přiřazení podrobného plánu
  - Použití `Get-AzBlueprint` k získání odkazu na objekt
  - Používaná k nalezení přiřazení aktualizovat, nechcete změnit přiřazení
- **Umístění** (volitelné)
  - Určuje oblast pro systém přiřadil spravované identity a předplatné nasazení objektu bude vytvořena ve
- **Předplatné** (volitelné)
  - Určuje předplatné, které přiřazení je nasazený do
  - Pokud není zadaný, použije se výchozí nastavení aktuálního kontextu předplatného
  - Používaná k nalezení přiřazení aktualizovat, nechcete změnit přiřazení
- **Zámek** (volitelné)
  - Definuje [podrobný plán uzamčení prostředků](../concepts/resource-locking.md) pro nasazených prostředků
  - Podporované možnosti: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (volitelné)
  - Vyberte systém přiřadil spravovanou identitu pro přiřazení vytvoření a nasazení prostředků
  - Výchozí nastavení pro sadu parametrů "identity"
  - Nelze použít s **UserAssignedIdentity**
- **UserAssignedIdentity** (volitelné)
  - Určuje uživatelsky přiřazené spravovaná identita má použít pro přiřazení a k nasazení prostředků
  - Součástí sady "identity"
  - Nelze použít s **SystemAssignedIdentity**
- **Parametr** (volitelné)
  - A [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) na přiřazení podrobného plánu
  - Výchozí pro dynamický parametr je **defaultValue** v definici
  - Pokud parametr není k dispozici a nemá žádné **defaultValue**, není-li volitelný parametr

    > [!NOTE]
    > **Parametr** secureStrings nepodporuje.

- **ResourceGroupParameter** (volitelné)
  - A [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý prostředek skupiny artefaktů zástupný symbol bude mít páry klíč/hodnota pro dynamicky nastavení **název** a/nebo **umístění** na tento artefakt skupiny prostředků
  - Pokud parametr skupiny prostředků není k dispozici a nemá žádné **defaultValue**, není-li volitelný parametr skupiny prostředků

Následující příklad aktualizuje přiřazení verze "1.1" definice podrobného plánu 'my podrobného plánu' pomocí `Get-AzBlueprint` tak, že změníte režim uzamčení:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Příklad výstupu pro vytváření přiřazení podrobného plánu vypadá takto:

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

Až nastane čas pro přiřazení podrobného plánu, která se má odebrat, `Remove-AzBlueprintAssignment` rutina zpracovává tuto akci. Rutina přebírá buď **název** nebo **InputObject** k určení podrobný plán přiřazení k odebrání. **SubscriptionId** je _požadované_ a ve všech případech musí být zadaná.

Následující příklad načte existující přiřazení podrobného plánu se `Get-AzBlueprintAssignment` a odstraní ji z konkrétní předplatné, které jsou reprezentovány jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Příklad kódu začátku do konce

Spojuje všechny kroky v následujícím příkladu získá definice podrobného plánu, pak vytvoří, aktualizuje a odebere přiřazení podrobného plánu v konkrétní předplatné, které jsou reprezentovány jako `{subId}`:

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

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)