---
title: Migrace virtuálních počítačů VMware do Azure (bez agenta) – PowerShell
description: Naučte se spouštět migraci virtuálních počítačů VMware bez agenta pomocí Azure Migrate prostřednictvím PowerShellu.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 006b2838a4e593397f8968e53ba2364d16753a40
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547056"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrace virtuálních počítačů VMware do Azure (bez agenta) – PowerShell

V tomto článku se dozvíte, jak migrovat zjištěné virtuální počítače VMware s metodou bez agentů pomocí Azure PowerShell pro [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Načte zjištěné virtuální počítače VMware v projektu Azure Migrate.
> * Zahajte replikaci virtuálních počítačů.
> * Aktualizuje vlastnosti pro replikované virtuální počítače.
> * Monitorujte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci virtuálního počítače.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="1-prerequisites"></a>1. požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. Dokončete [kurz: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru](tutorial-discover-vmware.md) pro přípravu Azure a VMware pro migraci.
1. Dokončete [kurz: vyhodnoťte virtuální počítače VMware pro migraci na virtuální počítače Azure](./tutorial-assess-vmware-azure-vm.md) před jejich migrací do Azure.
1. [Instalace modulu AZ PowerShell Module](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Instalace modulu Azure Migrate PowerShellu

Modul Azure Migrate PowerShell je k dispozici ve verzi Preview. Modul PowerShell budete muset nainstalovat pomocí následujícího příkazu.

```azurepowershell-interactive
Install-Module -Name Az.Migrate
```

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Přihlaste se k předplatnému Microsoft Azure

Přihlaste se ke svému předplatnému Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```azurepowershell
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Vyberte své předplatné Azure.

Pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) Získejte seznam předplatných Azure, ke kterým máte přístup. Pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) vyberte předplatné Azure, které má váš Azure Migrate projekt.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. načtěte Azure Migrate projekt.

Azure Migrate projekt se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnotili nebo migrujete.
V projektu můžete sledovat zjištěné prostředky, orchestrovat posouzení a provádět migrace.

V rámci požadavků byste už vytvořili Azure Migrate projekt. K načtení podrobností o Azure Migrate projektu použijte rutinu [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) . Je nutné zadat název Azure Migrate projektu ( `Name` ) a název skupiny prostředků projektu Azure Migrate ( `ResourceGroupName` ).

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. načtení zjištěných virtuálních počítačů v projektu Azure Migrate

Azure Migrate používá odlehčené [Azure Migrate zařízení](migrate-appliance-architecture.md). Jako součást požadavků jste nasadili Azure Migrate zařízení jako virtuální počítač VMware.

Pokud chcete načíst konkrétní virtuální počítač VMware v projektu Azure Migrate, zadejte název Azure Migrate projektu ( `ProjectName` ), skupinu prostředků Azure Migrate projektu ( `ResourceGroupName` ) a název virtuálního počítače ( `DisplayName` ).

> [!IMPORTANT]
> **Hodnota parametru název virtuálního počítače ( `DisplayName` ) rozlišuje velká a malá písmena**.

```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM

# View discovered server details
Write-Output $DiscoveredServer
```

Tento virtuální počítač migrujeme jako součást tohoto kurzu.

Všechny virtuální počítače VMware můžete také načíst v Azure Migrate projektu pomocí parametrů **ProjectName** a **ResourceGroupName** .

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Pokud máte v Azure Migrate projektu více zařízení, můžete k načtení všech virtuálních počítačů zjištěných pomocí konkrétního Azure Migrate zařízení **použít parametry** **ProjectName**, **ResourceGroupName** a promítnout.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. inicializace infrastruktury replikace

[Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) využívá více prostředků Azure pro migraci virtuálních počítačů. Migrace serveru zřídí následující prostředky ve stejné skupině prostředků jako projekt.

- **Service Bus**: Migrace serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na disky spravované replikou.
- **Trezor klíčů**: zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci.

Před replikací prvního virtuálního počítače v projektu Azure Migrate spusťte následující skript, který zřídí infrastrukturu replikace. Tento skript zřídí a nakonfiguruje výše uvedené prostředky, abyste mohli začít migrovat vaše virtuální počítače VMware.

> [!NOTE]
> Jeden Azure Migrate projekt podporuje migrace pouze do jedné oblasti Azure. Po spuštění tohoto skriptu nemůžete změnit cílovou oblast, do které chcete migrovat virtuální počítače VMware.
> `Initialize-AzMigrateReplicationInfrastructure`Pokud nakonfigurujete nové zařízení v Azure Migrate projektu, budete muset skript spustit.

V tomto článku inicializujeme infrastrukturu replikace, abychom mohli migrovat naše virtuální počítače do `Central US` oblasti. Soubor si můžete [Stáhnout](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) z úložiště GitHubu nebo ho spustit pomocí následujícího fragmentu kódu.

```azurepowershell-interactive
# Download the script from Azure Migrate GitHub repository
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion CentralUS
```

## <a name="7-replicate-vms"></a>7. replikace virtuálních počítačů

Po dokončení zjišťování a inicializaci infrastruktury replikace můžete zahájit replikaci virtuálních počítačů VMware do Azure. Můžete spustit až 300 replikace současně.

Vlastnosti replikace můžete zadat následujícím způsobem.

- **Cílové předplatné a skupina prostředků** – zadejte předplatné a skupinu prostředků, do kterých se má virtuální počítač migrovat, a to zadáním ID skupiny prostředků pomocí `TargetResourceGroupId` parametru.
- **Cílová virtuální síť a podsíť** – zadejte ID Virtual Network Azure a název podsítě, do které se má virtuální počítač migrovat, pomocí `TargetNetworkId` parametrů a v `TargetSubnetName` uvedeném pořadí.
- **Název cílového virtuálního počítače** – zadejte název virtuálního počítače Azure, který se má vytvořit, pomocí `TargetVMName` parametru.
- **Velikost cílového virtuálního počítače** – určete velikost virtuálního počítače Azure, která se má použít pro virtuální počítač pro replikaci pomocí `TargetVMSize` parametru. Pokud například chcete migrovat virtuální počítač na D2_v2 virtuálního počítače v Azure, zadejte hodnotu `TargetVMSize` jako "Standard_D2_v2".
- **Licence** – Chcete-li použít zvýhodněné hybridní využití Azure pro počítače s Windows serverem, které jsou pokryté s aktivními předplatnými Software Assurance nebo Windows Server, zadejte hodnotu `LicenseType` parametru jako "windowsserver". V opačném případě zadejte hodnotu `LicenseType` parametru jako "NoLicenseType".
- **Disk s operačním** systémem – zadejte jedinečný identifikátor disku, který obsahuje zaváděcí program pro spouštění a instalaci operačního systému. ID disku, který se má použít, je vlastnost jedinečného identifikátoru (UUID) pro disk načtený pomocí `Get-AzMigrateServer` rutiny.
- **Typ disku** – zadejte hodnotu `DiskType` parametru následujícím způsobem.
    - Chcete-li použít disky spravované na úrovni Premium, zadejte jako hodnotu parametru "Premium_LRS" `DiskType` .
    - Chcete-li použít standardní disky SSD, zadejte jako hodnotu parametru "StandardSSD_LRS" `DiskType` .
    - Chcete-li použít standardní disky HDD, zadejte jako hodnotu parametru "Standard_LRS" `DiskType` .
- **Redundance infrastruktury** – zadejte následující možnost redundance infrastruktury.
    - Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti. Pokud chcete použít zóny dostupnosti, zadejte hodnotu zóny dostupnosti pro `TargetAvailabilityZone` parametr.
    - Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít. Chcete-li použít skupinu dostupnosti, zadejte ID skupiny dostupnosti pro `TargetAvailabilitySet` parametr.

### <a name="replicate-vms-with-all-disks"></a>Replikace virtuálních počítačů se všemi disky

V tomto kurzu budeme replikovat všechny disky zjištěného virtuálního počítače a zadat nový název virtuálního počítače v Azure. Určíme první disk zjištěného serveru jako disk s operačním systémem a migrujete všechny disky jako HDD úrovně Standard. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replikace virtuálních počítačů pomocí vybraných disků

Můžete také selektivně replikovat disky zjištěného virtuálního počítače pomocí rutiny [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) a poskytnout je jako vstup do parametru **DiskToInclude** v rutině [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) . Pomocí `New-AzMigrateDiskMapping` rutiny můžete také určit různé typy cílových disků pro jednotlivé disky, které se mají replikovat.

Zadejte hodnoty pro následující parametry `New-AzMigrateDiskMapping` rutiny.

- **DiskId** – zadejte jedinečný identifikátor disku, který se má migrovat. ID disku, který se má použít, je vlastnost jedinečného identifikátoru (UUID) pro disk načtený pomocí `Get-AzMigrateServer` rutiny.
- **IsOSDisk** – zadejte hodnotu true, pokud je disk, který se má migrovat, disk s operačním systémem virtuálního počítače, jinak false.
- **DiskType** – zadejte typ disku, který se má používat v Azure.

V následujícím příkladu provedeme replikaci pouze dvou disků zjištěného virtuálního počítače. Určíme disk s operačním systémem a pro každý disk, který se má replikovat, použijeme jiné typy disků. Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. monitorování replikace

Replikace probíhá takto:

- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data disku ze snímku se replikují na disky spravované replikou v Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.

Pomocí rutiny [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) Sledujte stav replikace.

> [!NOTE]
> ID zjištěného virtuálního počítače a replikace jsou dva různé jedinečné identifikátory. Oba tyto identifikátory lze použít k načtení podrobností o replikačním serveru.

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Monitorování replikace pomocí zjištěného identifikátoru virtuálního počítače

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Monitorování replikace pomocí identifikátoru replikačního virtuálního počítače

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

Ve výstupu můžete sledovat **stav migrace** a vlastnosti **popisu stavu migrace** .

- V případě počáteční replikace budou hodnoty vlastnosti Popis **stavu migrace** a **popisu stavu migrace** v `InitialSeedingInProgress` `Initial replication` uvedeném pořadí.
- Během rozdílové replikace budou hodnoty vlastností **stav** migrace a **Popis stavu migrace** v `Replicating` `Ready to migrate` uvedeném pořadí.
- Po dokončení migrace budou hodnoty vlastnosti **stav migrace** a **Popis stavu migrace** `Migration succeeded` a v `Migrated` uvedeném pořadí.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Podrobnosti o průběhu replikace získáte spuštěním následující rutiny.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

Průběh počáteční replikace můžete sledovat pomocí **procenta počátečního plnění v procentech** ve výstupu.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Replikace probíhá takto:

- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data disku ze snímku se replikují na disky spravované replikou v Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.

## <a name="9-retrieve-the-status-of-a-job"></a>9. načtení stavu úlohy

Stav úlohy můžete sledovat pomocí rutiny [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) .

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. aktualizace vlastností replikačního virtuálního počítače

[Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) umožňuje změnit vlastnosti cíle, jako je název, velikost, skupina prostředků, konfigurace síťových adaptérů atd., a to pro replikační virtuální počítač.

Následující vlastnosti lze pro virtuální počítač aktualizovat.

- **Název virtuálního počítače** – zadejte název virtuálního počítače Azure, který se má vytvořit, pomocí parametru **TargetVMName** .
- **Velikost virtuálního počítače** – zadejte velikost virtuálního počítače Azure, která se má použít pro virtuální počítač pro replikaci pomocí parametru **TargetVMSize** . Pokud třeba chcete migrovat virtuální počítač na D2_v2 virtuálního počítače v Azure, zadejte hodnotu pro **TargetVMSize** jako `Standard_D2_v2` .
- **Virtual Network** – zadejte ID Virtual Network Azure, na které se má virtuální počítač migrovat, pomocí parametru **TargetNetworkId** .
- **Skupina prostředků** – zadejte ID skupiny prostředků, do které se má virtuální počítač migrovat, zadáním ID skupiny prostředků pomocí parametru **TargetResourceGroupId** .
- **Síťové rozhraní** – konfiguraci síťových adaptérů lze zadat pomocí rutiny [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) . Objekt je pak předán vstupu do parametru **NicToUpdate** v rutině [set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) .

    - **Změna přidělování IP** adres – Chcete-li zadat statickou IP adresu pro síťový adaptér, zadejte adresu IPv4, která se má použít jako statická IP adresa virtuálního počítače pomocí parametru **TargetNicIP** . Pokud chcete dynamicky přiřadit IP adresu pro síťové rozhraní, zadejte `auto` hodnotu parametru **TargetNicIP** .
    - Použijte hodnoty `Primary` , `Secondary` nebo `DoNotCreate` pro parametr **TargetNicSelectionType** určete, jestli se má síťové rozhraní primární, sekundární nebo nemá vytvořit na migrovaném virtuálním počítači. Jako primární síťová karta pro virtuální počítač lze zadat pouze jednu síťovou kartu.
    - Pokud chcete vytvořit primární síťovou kartu, budete taky muset zadat jiné síťové adaptéry, které se mají nastavit jako sekundární, nebo se na migrovaném virtuálním počítači nevytvoří.
    - Chcete-li změnit podsíť pro síťové rozhraní, zadejte název podsítě pomocí parametru **TargetNicSubnet** .

 - **Zóna dostupnosti** – pro použití zón dostupnosti zadejte hodnotu zóny dostupnosti pro parametr **TargetAvailabilityZone** .
 - **Nastavení dostupnosti** – pro použití skupiny dostupnosti zadejte ID skupiny dostupnosti pro parametr **TargetAvailabilitySet** .

`Get-AzMigrateServerReplication`Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

V následujícím příkladu aktualizujeme konfiguraci síťových adaptérů tak, že první síťové rozhraní provedeme jako primární a přiřadíte mu statickou IP adresu. druhou síťovou kartu zahodíme pro migraci a aktualizujeme název a velikost cílového virtuálního počítače.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

Můžete také zobrazit seznam všech replikačních serverů v projektu Azure Migrate a pak pomocí identifikátoru replikačního virtuálního počítače aktualizovat vlastnosti virtuálního počítače.

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

## <a name="11-run-a-test-migration"></a>11. spuštění testovací migrace

Když se spustí rozdílová replikace, můžete před spuštěním úplné migrace do Azure spustit testovou migraci pro virtuální počítače. Důrazně doporučujeme, abyste před migrací pro každý počítač provedli migraci alespoň jednou. Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání. Test migrace nemá vliv na místní počítač, který zůstává v provozu a pokračuje v replikaci.
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Vyberte Virtual Network Azure, která se má použít pro testování, zadáním ID virtuální sítě pomocí parametru **TestNetworkID** .

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Po dokončení testování vyčistěte testovací migraci pomocí rutiny [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) . Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete replikační Server migrovat pomocí následující rutiny. Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

Pokud nechcete zdrojový server vypnout, použijte parametr **TurnOffSourceServer** .

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. dokončení migrace

1. Po dokončení migrace zastavte replikaci pro místní počítač a vyčištění informací o stavu replikace pro virtuální počítač pomocí následující rutiny. Rutina vrátí úlohu, kterou je možné sledovat pro monitorování stavu operace.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Nainstalujte agenta pro [Linux](../virtual-machines/extensions/agent-linux.md) do migrovaných počítačů, pokud má počítač Linux OS. Během migrace automaticky nainstalujete agenta virtuálního počítače pro virtuální počítače s Windows.
1. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
1. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
1. Vyjmutí provozu do migrované instance virtuálního počítače Azure
1. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
1. Odeberte místní virtuální počítače ze záloh.
1. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure.

## <a name="14-post-migration-best-practices"></a>14. osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Přečtěte si další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Přečtěte si další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Odblokujte a omezte přístup k příchozímu provozu pomocí [správy v čase Azure Security Center](../security-center/security-center-just-in-time.md).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
    - Nasaďte službu [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), která bude monitorovat využití prostředků a útratu.
