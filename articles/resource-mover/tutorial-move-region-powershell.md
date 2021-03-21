---
title: Přesunutí prostředků do různých oblastí pomocí prostředí PowerShell v nástroji Azure Resource stěhovací
description: Naučte se přesouvat prostředky v různých oblastech pomocí prostředí PowerShell v nástroji Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584304"
---
# <a name="move-resources-across-regions-in-powershell"></a>Přesunutí prostředků mezi oblasti v PowerShellu

Tento článek vysvětluje, jak přesunout prostředky Azure do jiné oblasti Azure pomocí PowerShellu v nástroji [Azure Resource stěhovací](overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte požadavky a požadavky.
> * Nastavte kolekci přesunů.
> * Přidejte prostředky do kolekce přesunů a vyřešte závislosti.
> * Připravte a přesuňte zdrojovou skupinu prostředků. 
> * Příprava a přesun dalších prostředků.
> * Rozhodněte, zda chcete přesun zrušit nebo potvrdit. 
> * Volitelně můžete po přesunutí odebrat prostředky ve zdrojové oblasti.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Předpoklady
**Požadavek** | **Popis**
--- | ---
**Oprávnění předplatného** | Ověřte, že máte přístup *vlastníka* k předplatnému obsahující prostředky, které chcete přesunout.<br/><br/> **Proč potřebuji přístup k vlastníkovi?** Při prvním přidání prostředku pro konkrétní dvojici zdroje a cíle v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná. Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel nebo správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
**Podpora Resource stěhovací** | [Zkontrolujte](common-questions.md) podporované oblasti a další časté otázky.
**Podpora virtuálních počítačů** |  Ověřte, že všechny virtuální počítače, které chcete přesunout, jsou podporované.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.<br/><br/> – Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Podpora SQL** | Pokud chcete přesunout prostředky SQL, Projděte si [seznam požadavků SQL](tutorial-move-region-sql.md#check-sql-requirements).
**Cílové předplatné** | Aby bylo možné vytvořit prostředky, které přesouváte v cílové oblasti, musí předplatné v cílové oblasti mít dostatečnou kvótu. Pokud nemá kvótu, [požádejte o další omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Poplatky za cílovou oblast** | Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.

### <a name="review-powershell-requirements"></a>Kontrola požadavků PowerShellu

Většina operací přesunutí prostředků je stejná, ať už pomocí Azure Portal nebo PowerShellu, s několika výjimkami.

**Operace** | **PowerShell** | **Azure Portal**
--- | --- | ---
**Vytvoření kolekce Move** | Kolekce přesunů (seznam všech prostředků, které přesouváte), se vytvoří automaticky. Požadovaná oprávnění identity se přiřadí do back-endu portálu. | Rutiny PowerShellu použijete k těmto akcím:<br/><br/> – Vytvořte skupinu prostředků pro kolekci přesunutí a určete její umístění.<br/><br/> – Přiřaďte kolekci spravovanou identitu.<br/><br/> -Přidat prostředky do kolekce.
**Odebrat kolekci Move** | Kolekci přesunů nelze přímo odebrat na portálu. | Chcete-li odebrat kolekci přesunutí, použijte rutinu prostředí PowerShell.
**Operace přesunu prostředků**<br/><br/> (Připravte, iniciovat přesun, potvrzení atd.).| Jednotlivé kroky při automatickém ověřování pomocí programu Resource stěhovací | Rutiny PowerShellu pro:<br/><br/> 1) ověřte závislosti.<br/><br/> 2) proveďte přesun.
**Odstranit zdrojové prostředky** | Přímo na portálu Resource stěhovací. | Rutiny prostředí PowerShell na úrovni prostředků typu.


### <a name="sample-values"></a>Ukázkové hodnoty

Tyto hodnoty používáme ve skriptových příkladech:

**Nastavení** | **Hodnota** 
--- | ---
ID předplatného | ID předplatného
Zdrojová oblast |  USA – střed
Cílová oblast | USA – středozápad
Skupina prostředků (uchovává metadata pro Move Collection) | RG-Movecollection-demoRMS
Přesunout název kolekce | PS-centralus-westcentralus-demoRMS
Skupina prostředků (zdrojová oblast) | PSDemoRM 
Skupina prostředků (cílová oblast) | PSDemoRM – cíl
Umístění služby přesunutí prostředku | USA – východ 2
IdentityType | SystemAssigned
Virtuální počítač, který se má přesunout | PSDemoVM


## <a name="sign-into-azure"></a>Přihlášení do Azure

Přihlaste se k předplatnému Azure pomocí rutiny Connect-AzAccount:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Nastavení kolekce pro přesun

Objekt Movecollection ukládá metadata a konfigurační informace o prostředcích, které chcete přesunout. Chcete-li nastavit kolekci přesunutí, proveďte následující akce:

- Vytvořte skupinu prostředků pro kolekci Move.
- Zaregistrujte poskytovatele služby pro odběr, aby bylo možné vytvořit prostředek Movecollection.
- Vytvořte objekt Movecollection se spravovanou identitou. Aby objekt Movecollection měl přístup k předplatnému, ve kterém se nachází služba Resource stěhovací, potřebuje [spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve označovanou jako identita spravované služby (MSI)), která je pro předplatné důvěryhodná.
- Udělte pro spravovanou identitu přístup k předplatnému Resource stěhovací.

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro informace o metadatech a konfiguraci kolekce, a to následujícím způsobem:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Výstup**:

![Výstupní text po vytvoření skupiny prostředků](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

1. Zaregistrujte poskytovatele prostředků Microsoft. migrujte, aby se mohl vytvořit prostředek Movecollection, a to takto:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Vytvoření objektu Movecollection

Vytvořte objekt Movecollection a k němu přiřaďte spravovanou identitu následujícím způsobem: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Výstup**:

![Výstupní text po vytvoření kolekce Move](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Udělení přístupu spravované identitě

Následujícím způsobem udělte spravované identitě přístup k předplatnému prostředku stěhovací společnosti. Musíte být vlastníkem předplatného.

1. Načte podrobnosti o identitě z objektu Movecollection.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Přiřaďte k identitě požadované role, aby mohl Azure Resource stěhovací získat přístup k vašemu předplatnému, aby bylo možné přesouvat prostředky.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Přidat prostředky do kolekce přesunů

Načtěte ID pro existující zdrojové prostředky, které chcete přesunout. Vytvořte objekt nastavení cílového prostředku a pak přidejte prostředky do kolekce přesunů.

> [!NOTE]
> Prostředky přidané do kolekce přesunů musí být ve stejném předplatném, ale můžou být v různých skupinách prostředků.

Přidejte prostředky následujícím způsobem:

1. Získat ID zdrojového prostředku:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Výstup**

    ![Výstupní text po načtení ID prostředku](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Vytvořte objekt nastavení cílového prostředku podle prostředku, který přesouváte. V našem případě se jedná o virtuální počítač.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Nastavte typ prostředku a název cílového prostředku pro daný objekt.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > Náš cílový virtuální počítač má stejný název jako virtuální počítač ve zdrojové oblasti. Můžete zvolit jiný název.

4. Přidejte zdrojové prostředky do kolekce přesunů pomocí objektu ID prostředku a cílového objektu nastavení, který jste načetli nebo vytvořili.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Výstup** ![ Výstup textu po přidání prostředku](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Ověřit a přidat závislosti

Ověřte, zda prostředky, které jste přidali, mají jakékoli závislosti na jiných prostředcích a přidejte je podle potřeby. 

1. Ověřte závislosti následujícím způsobem:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Výstup (pokud existují závislosti)**

    ![Výstupní text po ověření závislostí](./media/tutorial-move-region-powershell/dependency-output.png)

2. Chybějící závislosti v identitě:

    - Načtení seznamu všech chybějících závislostí:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Výstup** ![ Výstup textu po načtení seznamu všech závislostí](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Chcete-li načíst pouze závislosti první úrovně (přímé závislosti pro prostředek):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Výstup** ![ Výstup textu po načtení seznamu závislostí první úrovně](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Pokud chcete přidat nedokončené chybějící závislosti, podle pokynů uvedených výše [přidejte prostředky do kolekce přesunů](#add-resources-to-the-move-collection)a znovu je ověřte, dokud neexistují žádné nedokončené prostředky.

> [!NOTE]
> Pokud z nějakého důvodu chcete odebrat prostředky z kolekce prostředků, postupujte podle pokynů v [tomto článku](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Přidat zdrojovou skupinu prostředků

Přidejte zdrojovou skupinu prostředků obsahující prostředky, které chcete přesunout do kolekce přesunů.

1. Načte ID skupiny prostředků.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Výstup** ![ Výstupní text po načtení ID zdrojové skupiny prostředků](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Používáme skupinu prostředků, která už je v cílové oblasti.

 
2. Pomocí načteného ID přidejte skupinu prostředků do kolekce.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Výstup** ![ Výstupní text po přidání zdrojové skupiny prostředků do kolekce Move](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Ověřte závislosti a ujistěte se, že jste po přidání skupiny prostředků nezmeškali žádné změny.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Vidíte, že neexistují žádné nedokončené závislosti.

    **Výstup** ![ Výstupní text po kontrole závislostí](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Příprava prostředků

Před přesunem je obvykle potřeba připravit prostředky ve zdrojové oblasti. Například:

- K přesunu bezstavových prostředků, jako jsou virtuální sítě Azure, síťové adaptéry, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě, může být potřeba Exportovat šablonu Azure Resource Manager.
- K přesunutí stavových prostředků, jako jsou virtuální počítače Azure a databáze SQL, možná budete muset spustit replikaci prostředků ze zdrojové do cílové oblasti.

V tomto kurzu, protože přesouváme virtuální počítače, potřebujeme připravit zdrojovou skupinu prostředků a pak zahájit a potvrdit svůj přesun, než můžeme začít připravovat virtuální počítače.

> [!NOTE]
> Pokud máte existující cílovou skupinu prostředků, můžete přesunout změny pro zdrojovou skupinu prostředků přímo a Přeskočit fáze připravit a zahájit přesun.

  
### <a name="prepare-the-source-resource-group"></a>Příprava zdrojové skupiny prostředků:

1. Připravte skupinu prostředků:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Výstup**

    ![Výstupní text po přípravě zdrojové skupiny prostředků](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Iniciuje přesun zdrojové skupiny prostředků.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Výstupní text po zahájení přesunu zdrojové skupiny prostředků](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Potvrďte přesun zdrojové skupiny prostředků.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Výstup**

    ![Výstup textu po potvrzení zdrojové skupiny prostředků](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Příprava prostředků virtuálního počítače

Po přípravě a přesunutí zdrojové skupiny prostředků můžeme připravit prostředky virtuálního počítače pro přesun.

1. Před přípravou prostředků virtuálního počítače ověřte závislosti.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Výstup**

    ![Výstupní text po ověření virtuálního počítače před jeho připravení](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Získejte závislé prostředky, které je potřeba připravit spolu s virtuálním počítačem.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Výstup**

    ![Výstupní text po načtení závislých prostředků virtuálního počítače](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Zahajte proces přípravy všech závislých prostředků.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Výstup**

    ![Výstup textu po initating přípravě všech prostředků](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > ID zdrojového prostředku můžete místo názvu prostředku zadat jako vstupní parametry pro rutinu Prepare a také v rutinách zahájit přesun a potvrzení. Uděláte to tak, že spustíte:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Iniciování přesunu prostředků virtuálních počítačů

1. Ověřte, že prostředky virtuálních počítačů jsou v *nevyřízeném stavu čekání na přesunutí* :

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Výstup**

    ![Výstupní text po kontrole stavu iniciace](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Zahájit přesun:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Výstup**

    ![Výstupní text po zahájení přesunu prostředků](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: při testování můžete zrušit jeho přesunutí a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se daný prostředek vrátí do stavu *zahájení přesunu čeká na vyřízení*. V případě potřeby pak můžete znovu zahájit přesun.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu *čeká na odstranění zdroje* a Vy se můžete rozhodnout, jestli ho chcete odstranit.

### <a name="discard-the-move"></a>Zrušit přesun

Zrušení přesunutí:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Výstup**

![Výstupní text po zrušení přesunutí](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Potvrdit přesun

1. Potvrďte přesunutí následujícím způsobem:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Výstup**

    ![Výstupní text po potvrzení přesunu](./media/tutorial-move-region-powershell/commit-move.png)

2. Ověřte, že všechny prostředky byly přesunuty do cílové oblasti:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Všechny prostředky jsou nyní ve stavu *odstranění zdroje, který čeká na vyřízení* v cílové oblasti.

## <a name="delete-source-resources"></a>Odstranit zdrojové prostředky

Po potvrzení přesunu a ověření, že prostředky v cílové oblasti fungují podle očekávání, můžete odstranit všechny zdrojové prostředky v [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [pomocí PowerShellu](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)nebo rozhraní příkazového [řádku Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Virtuální počítače Azure se přesunuly do jiné oblasti Azure pomocí PowerShellu.
> * Přesunuly se prostředky přidružené k virtuálním počítačům do jiné oblasti.

Nyní se snažíte přesunout virtuální počítače Azure pomocí portálu.

> [!div class="nextstepaction"]
> [Přesunutí virtuálních počítačů Azure na portálu](./tutorial-move-region-virtual-machines.md)


