---
title: Přesunutí prostředků do různých oblastí pomocí prostředí PowerShell v nástroji Azure Resource stěhovací
description: Naučte se přesouvat prostředky v různých oblastech pomocí prostředí PowerShell v nástroji Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89670400"
---
# <a name="move-resources-across-regions-in-powershell"></a>Přesunutí prostředků mezi oblasti v PowerShellu

Přečtěte si, jak přesunout prostředky Azure do jiné oblasti pomocí prostředí PowerShell v nástroji Azure Resource stěhovací. 

> [!NOTE]
> Prostředek Azure Resource stěhovací je momentálně ve verzi Preview.



## <a name="before-you-start"></a>Než začnete

- Vaše předplatné Azure by mělo mít přístup k dodávání prostředku a Vy byste k tomuto předplatnému měli mít oprávnění [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatelů](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) .
- Resource Track nesleduje změny a upgrady, proto proveďte potřebné změny prostředků, než je začnete přesouvat.
- Když přesunete prostředky pomocí PowerShellu, nemůžete aktuálně upravovat žádná nastavení cílové oblasti. Tato nastavení upravte přímo na portálu.
- Když přidáte prostředky do kolekce přesunutí, při přípravě na jejich přesun do jiné oblasti se metadata o přesunu ukládají do skupiny prostředků, která se pro účel vytvořila. V současné době se tato skupina prostředků může nacházet ve Východní USA 2 nebo Severní Evropa oblastech. Prostředky Azure je možné přesouvat mezi všemi veřejnými oblastmi, které používají metadata přítomná v některé z těchto oblastí.

## <a name="sample-values"></a>Ukázkové hodnoty

Tyto hodnoty používáme ve skriptových příkladech:

**Nastavení** | **Hodnota** 
--- | ---
ID předplatného | ID předplatného
Umístění služby Resource stěhovací | USA – východ 2
Skupina prostředků metadat | RegionMoveRG-centralus-westcentralus
Umístění skupiny prostředků metadat | USA – východ 2
Přesunout název kolekce | Movecollection – centralus – westcentralus
Zdrojová oblast |  centralus
Zdrojová skupina prostředků | PSDemoRM
Cílová oblast | Středozápadní USA
Cílová skupina prostředků | PSDemoRMtgt
Virtuální počítač, který se má přesunout | PSDemoVM

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Vytvoření skupiny prostředků pro metadata

Vytvořte skupinu prostředků, která bude uchovávat metadata kolekce přesunu a informace o konfiguraci.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Očekávaný výstup**:

![Výstupní text po vytvoření skupiny prostředků](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Zaregistrujte poskytovatele prostředků Microsoft. migrujte, aby bylo možné vytvořit prostředek Movecollection.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Vytvoření kolekce Move

Objekt Movecollection ukládá metadata a konfigurační informace o prostředcích, které chcete přesunout.

- Aby objekt Movecollection mohl získat přístup k předplatnému, ve kterém se nachází služba Azure Resource stěhovací, potřebuje [spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve označovanou jako služba zjišťování spravované služby (MSI)), která je pro předplatné důvěryhodná.
- Identitě je přiřazená role přispěvatel nebo správce přístupu uživatele pro zdrojové předplatné.
- K přiřazení role k identitě potřebujete roli v rámci předplatného (například vlastník nebo správce přístupu uživatelů) s těmito oprávněními:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft. Authorization/roleAssignments/DELETE


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Očekávaný výstup**:

![Výstupní text po vytvoření kolekce Move](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Přiřazení spravované identity 

Nastavte ID předplatného, načtěte instanční objekt objektu Movecollection a přiřaďte k němu role Azure.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>Přidat prostředky do kolekce přesunů

Načtěte ID zdrojového prostředku, který chcete přesunout. Pak ho přidejte do kolekce přesunů.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Očekávaný výstup**

![Výstupní text po načtení ID prostředku](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Očekávaný výstup** 
 ![ Výstup textu po přidání prostředku](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Vyřešit závislosti

Ověřte prostředky, které jste přidali, a vyřešte všechny závislosti na jiných prostředcích.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Výstup, pokud existují závislosti**

Pokud má prostředek v kolekci Move závislosti na jiných prostředcích, zpráva o chybě se vystavila.

![Výstupní text po kontrole závislostí](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Načtení závislostí

Pokud má prostředek, který chcete přesunout, závislosti na jiných prostředcích, můžete načíst informace o chybějících závislostech.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Očekávaný výstup**

![Výstup textu po načtení závislostí](./media/move-region-powershell/missing-dependencies.png)

V tomto příkladu se identifikují dvě závislosti jako chybějící:

- Zdrojová skupina prostředků: PSDemoRM
- Síťová karta na virtuálním počítači: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Přidat závislosti do kolekce


Pomocí ID prostředků, které jste načetli, identifikujte názvy chybějících prostředků a přidejte je do kolekce Move.

### <a name="add-the-nic"></a>Přidat síťovou kartu

Načtěte název a typ síťové karty a přidejte je do kolekce.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**Očekávaný výstup**

![Výstup textu po načtení síťového rozhraní](./media/move-region-powershell/output-retrieve-nic.png)

Nyní přidejte síťovou kartu do kolekce Move. Tento příklad poskytuje stejný název jako cílový síťový adaptér. Zachovat nastavení a velikost písmen v souladu s.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Očekávaný výstup**

![Výstupní text po přidání síťového rozhraní do kolekce](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Přidat zdrojovou skupinu prostředků

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Očekávaný výstup**

![Výstupní text po přidání skupiny prostředků do kolekce](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Znovu kontrolovat závislosti

Vraťte se znovu pro chybějící závislosti.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Výstup**

Máme další chybějící závislosti.

![Výstup textu po kontrole pro závislosti znovu](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Načíst další závislosti

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Očekávaný výstup**


![Výstup textu po načtení dalších závislostí](./media/move-region-powershell/additional-missing-dependencies.png)

V tomto příkladu jsou tři další závislosti identifikovány jako chybějící:

- Veřejná IP adresa: psdemovm-IP
- Virtuální síť Azure: psdemorm-VNet
- Skupina zabezpečení sítě (NSG): psdemovm-NSG

## <a name="add-additional-dependencies"></a>Přidat další závislosti

1. [Podle pokynů](#add-dependencies-to-collection) přidejte tyto prostředky do kolekce přesunutí.
2. Po přidání prostředků znovu ověřte, dokud nebudou přidány všechny závislosti.


## <a name="prepare-and-move-the-source-resource-group"></a>Příprava a přesun zdrojové skupiny prostředků

Ve zdrojové oblasti si provedete přípravu prostředků před jejich přesunutím. Proces přípravy závisí na prostředcích, které přesouváte. Například pro přípravu bezstavových prostředků může dopravovat a exportovat šablonu Azure Resource Manager (ARM). Nebo pro stavové prostředky může být replikace vypnutá. 

Než budete moct připravit zdrojové prostředky, musíte připravit a přesunout zdrojovou skupinu prostředků.

### <a name="prepare"></a>Příprava

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**Očekávaný výstup**

![Výstupní text po přípravě zdrojové skupiny prostředků](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Zahájit přesun

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Očekávaný výstup**

![Výstupní text po potvrzení skupiny zdrojových prostředků](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Příprava prostředků

Po přesunutí zdrojové skupiny prostředků do cílové oblasti načtěte seznam prostředků v kolekci přesunů a připravte se na jejich přesunutí.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Očekávaný výstup**

![Výstup textu po načtení prostředků v kolekci](./media/move-region-powershell/collection-resources.png)

Nyní Připravte prostředky.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Alternativně můžete místo názvu použít k přípravě a přesunutí prostředků pomocí ID prostředku:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Očekávaný výstup**

![Výstup textu po přípravě prostředků v kolekci](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Zahájit přesun prostředku

Po přípravě prostředků zahajte přesun.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Očekávaný výstup** 
 ![ Výstupní text po zahájení přesunu prostředku](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Zahodit nebo potvrdit přesun

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: při testování můžete zrušit jeho přesunutí a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se daný prostředek vrátí do stavu *zahájení přesunu čeká na vyřízení*. V případě potřeby pak můžete znovu zahájit přesun.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu *čeká na odstranění zdroje*a Vy se můžete rozhodnout, jestli ho chcete odstranit.

### <a name="discard"></a>Zahodit

Zrušení přesunutí:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Očekávaný výstup** 
 ![ Výstupní text po zrušení přesunutí](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Potvrzení

Potvrzení přesunu:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Očekávaný výstup**

![Výstupní text po potvrzení přesunu](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Odstranit zdrojové prostředky

Po potvrzení přesunu a ověření, že prostředky v cílové oblasti fungují podle očekávání, můžete zdrojové prostředky odstranit v [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [pomocí PowerShellu](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)nebo rozhraní příkazového [řádku Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Další kroky

[Naučte se, jak](remove-move-resources.md) odebrat prostředky z kolekce přesunutí.