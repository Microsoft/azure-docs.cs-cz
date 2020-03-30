---
title: Správa přiřazení pomocí PowerShellu
description: Zjistěte, jak spravovat přiřazení podrobného plánu pomocí oficiálního modulu Azure Blueprints PowerShell, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: b16cf887ba8bfc51616839db5f4af87944ec686d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247397"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Správa přiřazení pomocí PowerShellu

Přiřazení podrobného plánu lze spravovat pomocí modulu **Az.Blueprint** Azure PowerShell. Modul podporuje načítání, vytváření, aktualizaci a odebírání přiřazení. Modul můžete také načíst podrobnosti o existující chod definice. Tento článek popisuje, jak nainstalovat modul a začít jej používat.

## <a name="add-the-azblueprint-module"></a>Přidání modulu Az.Blueprint

Chcete-li povolit Azure PowerShell pro správu přiřazení podrobného plánu, musí být přidán modul. Tento modul se dá používat s místně nainstalovaným PowerShellem, s [Azure Cloud Shell](https://shell.azure.com)nebo s image Azure [PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure Blueprints vyžaduje následující software:

- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalace modulu

Modul Podrobné plány pro prostředí PowerShell je **Az.Blueprint**.

1. Z výzvy prostředí PowerShell **pro správu** spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Pokud je **již nainstalována aplikace Az.Accounts,** může být nutné instalaci použít `-AllowClobber` k vynucení.

1. Ověřte, zda byl modul importován a zda je správnou verzí (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Získat definice podrobných plánů

Prvním krokem k práci s přiřazením je často získání odkazu na definici podrobného plánu.
Rutina `Get-AzBlueprint` získá jednu nebo více definic podrobného plánu. Rutina můžete získat podrobné plány definice `-ManagementGroupId {mgId}` ze skupiny `-SubscriptionId {subId}`pro správu s nebo předplatné s . Name **Parametr** získá definici podrobného plánu, ale musí být použit s **ManagementGroupId** nebo **SubscriptionId**. **Verze** lze použít s **Name** být více explicitní o definici podrobného plánu je vrácena. Namísto **version**, `-LatestPublished` přepínač chytne naposledy publikovanou verzi.

Následující příklad `Get-AzBlueprint` používá k získání všech verzí definice podrobného plánu s názvem "101-blueprints-definition-subscription" z konkrétního předplatného reprezentovaného jako `{subId}`:

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

[Parametry podrobného plánu](../concepts/parameters.md#blueprint-parameters) v definici podrobného plánu lze rozbalit a poskytnout další informace.

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

## <a name="get-blueprint-assignments"></a>Získání přiřazení podrobného plánu

Pokud přiřazení podrobného plánu již existuje, můžete získat `Get-AzBlueprintAssignment` odkaz na něj s rutinou. Rutina trvá **SubscriptionId** a **Name** jako volitelné parametry. Pokud **SubscriptionId** není zadán, použije se aktuální kontext předplatného.

Následující příklad `Get-AzBlueprintAssignment` používá k získání jednoho podrobného plánu přiřazení s názvem Assignment-lock-resource-groups' z konkrétního předplatného reprezentovaného jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Ukázkový výstup pro přiřazení podrobného plánu vypadá takto:

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

## <a name="create-blueprint-assignments"></a>Vytvoření přiřazení podrobného plánu

Pokud přiřazení podrobného plánu ještě neexistuje, můžete `New-AzBlueprintAssignment` jej vytvořit pomocí rutiny. Tato rutina používá následující parametry:

- **Jméno** [povinné]
  - Určuje název přiřazení podrobného plánu.
  - Musí být jedinečný a ještě neexistuje v **SubscriptionId**
- **Plán** [povinné]
  - Určuje definici podrobného plánu, kterou chcete přiřadit.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu
- **Umístění** [povinné]
  - Určuje oblast pro systémově přiřazený objekt pro nasazení spravované identity a předplatného, který má být vytvořen.
- **Předplatné** (volitelné)
  - Určuje předplatné, do které je přiřazení nasazeno.
  - Pokud není k dispozici, výchozí aktuální kontext předplatného
- **Zámek** (volitelně)
  - Definuje [uzamčení prostředků podrobného plánu,](../concepts/resource-locking.md) které se má použít pro nasazené prostředky.
  - Podporované možnosti: _Žádné_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Pokud není k dispozici, _None_ výchozí hodnota
- **SystemAssignedIdentity** (volitelné)
  - Výběrem vyberte, chcete-li pro přiřazení vytvořit spravovanou identitu přiřazenou systémem a nasadit prostředky.
  - Výchozí nastavení parametru "identity"
  - Nelze použít s **UserAssignedIdentity**
- **UserAssignedIdentity** (volitelné)
  - Určuje uživatelem přiřazenou spravovanou identitu, která má být použita pro přiřazení a nasazení prostředků.
  - Část sady parametrů "identity"
  - Nelze použít s **SystemAssignedIdentity**
- **Parametr** (nepovinné)
  - Tabulka [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) při přiřazení podrobného plánu
  - Výchozí hodnota dynamického parametru je **defaultValue** v definici.
  - Pokud parametr není k dispozici a nemá **žádnou výchozí hodnotu**, parametr není volitelný

    > [!NOTE]
    > **Parametr** nepodporuje secureStrings.

- **ResourceGroupParameter** (volitelné)
  - [Tabulka hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý zástupný symbol artefaktů skupiny prostředků má páry klíč/hodnota pro dynamické nastavení **název** a **umístění** na artefaktu skupiny prostředků
  - Pokud není k dispozici parametr skupiny prostředků a nemá **žádnou hodnotu defaultValue**, není parametr skupiny prostředků volitelný.
- **AssignmentFile** (nepovinné)
  - Cesta k reprezentaci souboru JSON přiřazení podrobného plánu
  - Tento parametr je součástí sady parametrů prostředí PowerShell, která zahrnuje pouze **Name**, **Blueprint**a **SubscriptionId**a společné parametry.

### <a name="example-1-provide-parameters"></a>Příklad 1: Zadejte parametry

Následující příklad vytvoří nové přiřazení verze '1.1' definice podrobného plánu 'my-blueprint' načtené `Get-AzBlueprint`s , nastaví spravované identity a umístění objektu přiřazení na 'westus2', uzamkne prostředky s _AllResourcesReadOnly_a nastaví tabulky hash pro **parametr** a **ResourceGroupParameter** na konkrétní předplatné reprezentované jako `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Ukázkový výstup pro vytvoření přiřazení podrobného plánu vypadá takto:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Příklad 2: Použití souboru definice přiřazení JSON

Následující příklad vytvoří téměř stejné přiřazení jako [příklad 1](#example-1-provide-parameters).
Místo předávání parametrů rutině příklad ukazuje použití souboru definice přiřazení JSON a parametru **AssignmentFile.** Navíc **excludedPrincipals** vlastnost je nakonfigurován jako součást **zámky**. Neexistuje parametr Prostředí PowerShell pro **vyloučené principals** a vlastnost lze nakonfigurovat pouze nastavením prostřednictvím souboru definice přiřazení JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Příklad souboru definice přiřazení JSON pro uživatelem přiřazenou spravovanou identitu najdete v textu požadavku v [příkladu: Přiřazení s uživatelem přiřazenou spravovanou identitou](/rest/api/blueprints/assignments/createorupdate#examples) pro rozhraní REST API.

## <a name="update-blueprint-assignments"></a>Aktualizovat přiřazení podrobného plánu

Někdy je nutné aktualizovat přiřazení podrobného plánu, který již byl vytvořen. Rutina `Set-AzBlueprintAssignment` zpracovává tuto akci. Rutina přebírá většinu stejných parametrů jako `New-AzBlueprintAssignment` rutina, což umožňuje aktualizaci všeho, co bylo nastaveno v přiřazení. Výjimky jsou _Název_, _Blueprint_a _SubscriptionId_. Aktualizovány jsou pouze uvedené hodnoty.

Informace o tom, co se stane při aktualizaci přiřazení podrobného plánu, naleznete [v tématu pravidla pro aktualizaci přiřazení](./update-existing-assignments.md#rules-for-updating-assignments).

- **Jméno** [povinné]
  - Určuje název přiřazení podrobného plánu, které má být aktualizováno.
  - Slouží k vyhledání přiřazení k aktualizaci, nikoli ke změně přiřazení.
- **Plán** [povinné]
  - Určuje definici podrobného plánu přiřazení podrobného plánu.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu
  - Slouží k vyhledání přiřazení k aktualizaci, nikoli ke změně přiřazení.
- **Umístění** (volitelné)
  - Určuje oblast pro systémově přiřazený objekt pro nasazení spravované identity a předplatného, který má být vytvořen.
- **Předplatné** (volitelné)
  - Určuje předplatné, do které je přiřazení nasazeno.
  - Pokud není k dispozici, výchozí aktuální kontext předplatného
  - Slouží k vyhledání přiřazení k aktualizaci, nikoli ke změně přiřazení.
- **Zámek** (volitelně)
  - Definuje [uzamčení prostředků podrobného plánu,](../concepts/resource-locking.md) které se má použít pro nasazené prostředky.
  - Podporované možnosti: _Žádné_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (volitelné)
  - Výběrem vyberte, chcete-li pro přiřazení vytvořit spravovanou identitu přiřazenou systémem a nasadit prostředky.
  - Výchozí nastavení parametru "identity"
  - Nelze použít s **UserAssignedIdentity**
- **UserAssignedIdentity** (volitelné)
  - Určuje uživatelem přiřazenou spravovanou identitu, která má být použita pro přiřazení a nasazení prostředků.
  - Část sady parametrů "identity"
  - Nelze použít s **SystemAssignedIdentity**
- **Parametr** (nepovinné)
  - Tabulka [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) párů klíč/hodnota pro nastavení [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) při přiřazení podrobného plánu
  - Výchozí hodnota dynamického parametru je **defaultValue** v definici.
  - Pokud parametr není k dispozici a nemá **žádnou výchozí hodnotu**, parametr není volitelný

    > [!NOTE]
    > **Parametr** nepodporuje secureStrings.

- **ResourceGroupParameter** (volitelné)
  - [Tabulka hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktů skupiny prostředků
  - Každý zástupný symbol artefaktů skupiny prostředků má páry klíč/hodnota pro dynamické nastavení **název** a **umístění** na artefaktu skupiny prostředků
  - Pokud není k dispozici parametr skupiny prostředků a nemá **žádnou hodnotu defaultValue**, není parametr skupiny prostředků volitelný.

Následující příklad aktualizuje přiřazení verze '1.1' definice podrobného plánu 'my-blueprint' načtené `Get-AzBlueprint` změnou režimu uzamčení:

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

## <a name="remove-blueprint-assignments"></a>Odebrání přiřazení podrobného plánu

Když je čas pro přiřazení podrobného plánu, `Remove-AzBlueprintAssignment` které mají být odebrány, rutina zpracovává tuto akci. Rutina trvá **buď Name** nebo **InputObject** určit, které přiřazení podrobného plánu odebrat. **SubscriptionId** je _vyžadováno_ a musí být poskytnuto ve všech případech.

Následující příklad načte existující přiřazení `Get-AzBlueprintAssignment` podrobného plánu s a potom `{subId}`jej odebere z konkrétní ho předplatného reprezentovaného jako :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Příklad kódu od konce

Sdružující všechny kroky dohromady, následující příklad získá definici podrobného plánu, pak vytvoří, `{subId}`aktualizuje a odebere přiřazení podrobného plánu v konkrétním předplatném reprezentovaném jako :

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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

- Přečtěte si o [životním cyklu podrobného plánu](../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../concepts/resource-locking.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).