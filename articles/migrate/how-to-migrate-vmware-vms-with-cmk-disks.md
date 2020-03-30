---
title: Migrace virtuálních počítačů VMware do Azure pomocí šifrování na straně serveru (SSE) a klíčů spravovaných zákazníky (CMK) pomocí migrace migrace serveru Azure
description: Zjistěte, jak migrovat virtuální počítače VMware do Azure pomocí šifrování na straně serveru (SSE) a klíčů spravovaných zákazníkem (CMK) pomocí migrace migrace serveru Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269481"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrace virtuálních počítačů VMware do virtuálních počítačů Azure s povoleným šifrováním na straně serveru a klíči spravovanými zákazníky

Tento článek popisuje, jak migrovat virtuální počítače VMware do virtuálních počítačů Azure s disky šifrovanými pomocí šifrování na straně serveru (SSE) s klíči spravovanými zákazníky (CMK) pomocí migrace migrace serveru Azure (replikace bez agentů).

Prostředí migrace portálu migrace serveru Azure umožňuje [migrovat virtuální počítače VMware do Azure s replikací bez agenta.](tutorial-migrate-vmware.md) Prostředí portálu aktuálně nenabízí možnost zapnout SSE s CMK pro replikované disky v Azure. Možnost zapnout SSE s CMK pro replikované disky je aktuálně k dispozici pouze prostřednictvím rozhraní REST API. V tomto článku uvidíte, jak vytvořit a nasadit [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) replikovat virtuální počítač VMware a nakonfigurovat replikované disky v Azure pro použití SSE s CMK.

Příklady v tomto článku používají [Azure PowerShell](/powershell/azure/new-azureps-module-az) k provádění úloh potřebných k vytvoření a nasazení šablony Správce prostředků.

[Další informace](../virtual-machines/windows/disk-encryption.md) o šifrování na straně serveru (SSE) s klíči spravovanými zákazníky (CMK) pro spravované disky.

## <a name="prerequisites"></a>Požadavky

- [Projděte si kurz](tutorial-migrate-vmware.md) o migraci virtuálních počítačů VMware do Azure s replikací bez agentů, abyste pochopili požadavky na nástroje.
- [Podle těchto pokynů](how-to-add-tool-first-time.md) vytvořte projekt Migrace Azure a přidejte do projektu nástroj **Migrace: Server Azure.**
- [Podle těchto pokynů](how-to-set-up-appliance-vmware.md) nastavte zařízení Azure Migrate pro VMware v místním prostředí a úplné zjišťování.

## <a name="prepare-for-replication"></a>Příprava na replikaci

Po dokončení zjišťování virtuálních zařízení se na řádku Zjištěné servery na dlaždici Migrace serveru zobrazí počet virtuálních počítačích VMware zjištěných zařízením.

Než začnete replikovat virtuální počítače, musí být připravena infrastruktura replikace.

1. Vytvořte instanci služby Service Bus v cílové oblasti. Service Bus používá místní zařízení Azure Migrate ke komunikaci se službou Migrace serveru ke koordinaci replikace a migrace.
2. Vytvořte účet úložiště pro přenos protokolů operací z replikace.
3. Vytvořte účet úložiště, do kterého zařízení Azure Migrate nahraje replikační data.
4. Vytvořte trezor klíčů a nakonfigurujte trezor klíčů pro správu tokenů sdílených přístupových podpisů pro přístup k objektům blob na účtech úložiště vytvořených v kroku 3 a 4.
5. Vygenerujte token sdíleného přístupového podpisu pro sběrnici služby vytvořenou v kroku 1 a vytvořte tajný klíč pro token v trezoru klíčů vytvořeném v předchozím kroku.
6. Vytvořte zásady přístupu trezoru klíčů, které místnímu zařízení Azure Migrate (pomocí aplikace AAD zařízení) a službě migrace serveru poskytují přístup k trezoru klíčů.
7. Vytvořte zásady replikace a nakonfigurujte službu Migrace serveru s podrobnostmi o infrastruktuře replikace vytvořené v předchozím kroku.

Infrastruktura replikace musí být vytvořená v cílové oblasti Azure pro migraci a v cílovém předplatném Azure, do kterého se virtuální počítače migrují.

Prostředí portálu migrace serveru zjednodušuje přípravu infrastruktury replikace tím, že to automaticky dělá za vás, když replikujete virtuální hod poprvé v projektu. V tomto článku budeme předpokládat, že jste již replikovali jeden nebo více virtuálních klíčů pomocí prostředí portálu a že infrastruktura replikace je již vytvořená. Podíváme se, jak zjistit podrobnosti o existující infrastruktury replikace a jak použít tyto podrobnosti jako vstupy do šablony Správce prostředků, který se použije k nastavení replikace s CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identifikace součástí infrastruktury replikace

1. Na webu Azure Portal přejděte na stránku skupiny prostředků a vyberte skupinu prostředků, ve které byl vytvořen projekt Migrace Azure.
2. V levé nabídce **vyberte Možnost Nasazení** a vyhledejte název nasazení začínající řetězcem *Microsoft.MigrateV2.VMwareV2EnableMigrate*. Zobrazí se seznam šablon Správce prostředků vytvořených prostředím portálu k nastavení replikace pro virtuální počítače v tomto projektu. Stáhneme jednu takovou šablonu a použijeme ji jako základ pro přípravu šablony pro replikaci s CMK.
3. Chcete-li šablonu stáhnout, vyberte libovolné nasazení odpovídající řetězci v předchozím kroku > v levé nabídce vyberte **šablonu** > Z horní nabídky klepněte na **tlačítko Stáhnout.** Uložte soubor template.json místně. Tento soubor šablony upravíte v posledním kroku.

## <a name="create-a-disk-encryption-set"></a>Vytvoření sady šifrování disku

Objekt sady šifrování disku mapuje spravované disky na trezor klíčů, který obsahuje sadu CMK pro sazbu. Chcete-li replikovat virtuální počítače s CMK, vytvoříte sadu šifrování disku a předáte ji jako vstup do operace replikace.

Podle příkladu [zde](../virtual-machines/windows/disk-encryption.md#powershell) vytvořte sadu šifrování disku pomocí Azure PowerShellu. Ujistěte se, že sada šifrování disku se vytvoří v cílovém předplatném, do kterého se migrují virtuální počítače, a v cílové oblasti Azure pro migraci.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Získání podrobností o virtuálním mísu VMware k migraci

V tomto kroku použijete Azure PowerShell k získání podrobností o virtuálním počítači, který je potřeba migrovat. Tyto podrobnosti budou použity k vytvoření šablony Správce prostředků pro replikaci. Konkrétně jsou to dvě vlastnosti zájmu:

- ID prostředku počítače pro zjištěné virtuální počítače.
- Seznam disků pro virtuální počítače a jejich identifikátory disků.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Zkopírujte hodnotu řetězce SiteId odpovídající zařízení Azure Migrate, které je zjištěna prostřednictvím virtuálního počítače. Ve výše uvedeném příkladu je SiteId *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Zkopírujte hodnoty ResourceId, name a disk uuid pro zařízení, které má být migrováno.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Vytvořit šablonu Správce prostředků pro replikaci

- Otevřete soubor šablony Resource Manager, který jste stáhli v kroku **Identifikace součástí infrastruktury replikace** v editoru podle vašeho výběru.
- Odeberte ze šablony všechny definice prostředků s výjimkou prostředků, které jsou typu *Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems.*
- Pokud existuje více definic prostředků výše uvedeného typu, odeberte všechny kromě jednoho. Odeberte všechny definice vlastností **dependsOn** z definice prostředku.
- Na konci tohoto kroku byste měli mít soubor, který vypadá jako příklad níže a má stejnou sadu vlastností.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Upravte vlastnost **name** v definici prostředku. Nahraďte řetězec, který následuje za poslední "/" ve vlastnosti name hodnotou *$machine. Název*( z předchozího kroku).
- Změňte hodnotu **vlastnosti properties.providerSpecificDetails.vmwareMachineId** s hodnotou *$machine. ResourceId*( z předchozího kroku).
- Nastavte hodnoty pro **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** na ID cílové skupiny prostředků, ID cílové virtuální sítě a název cílové podsítě.
- Nastavte hodnotu **licenseType** na "WindowsServer" pro použití hybridní výhody Azure pro tento virtuální počítač. Pokud tento virtuální počítač nemá nárok na hybridní výhody Azure, nastavte hodnotu **licenseType** na NoLicenseType.
- Změňte hodnotu vlastnosti **targetVmName** na požadovaný název virtuálního počítače Azure pro migrovaný virtuální počítač.
- Volitelně přidat vlastnost s názvem **targetVmSize** pod **vlastnost targetVmName.** Nastavte hodnotu vlastnosti **targetVmSize** na požadovanou velikost virtuálního počítače Azure pro migrovaný virtuální počítač.
- Vlastnost **disksToInclude** je seznam diskových vstupů pro replikaci s každou položkou seznamu představující jeden místní disk. Vytvořte tolik položek seznamu jako počet disků na místním virtuálním počítači. Nahraďte vlastnost **diskId** v položce seznamu na uuid disků identifikovaných v předchozím kroku. Nastavte hodnotu **isOSDisk** na hodnotu "true" pro disk operačního systému virtuálního počítače a "false" pro všechny ostatní disky. Ponechte vlastnosti **logStorageAccountId** a **logStorageAccountSasSecretName** beze změny. Nastavte hodnotu **diskType** na typ spravovaného disku Azure *(Standard_LRS, Premium_LRS, StandardSSD_LRS)* pro použití pro disk. Pro disky, které je třeba zašifrovat pomocí CMK, přidejte vlastnost s názvem **diskEncryptionSetId** a nastavte hodnotu na ID prostředku vytvořené šifrovací sady disku(**$des. Id**) v kroku *Vytvořit sadu šifrování disku*
- Uložte upravený soubor šablony. Ve výše uvedeném příkladu vypadá upravený soubor šablony takto:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Nastavení replikace

Nyní můžete nasadit upravenou šablonu Správce prostředků do skupiny zdrojů projektu a nastavit replikaci pro virtuální počítače. Zjistěte, jak [nasadit prostředky pomocí šablon Azure Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Další kroky

[Sledujte stav replikace](tutorial-migrate-vmware.md#track-and-monitor) prostřednictvím prostředí portálu a proveďte testovací migrace a migraci.
