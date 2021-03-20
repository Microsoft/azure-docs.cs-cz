---
title: Migrace virtuálních počítačů VMware do Azure pomocí šifrování na straně serveru (SSE) a klíčů spravovaných zákazníkem (CMK) pomocí migrace serveru Azure Migrate
description: Naučte se migrovat virtuální počítače VMware do Azure pomocí šifrování na straně serveru (SSE) a klíčů spravovaných zákazníkem (CMK) pomocí migrace Azure Migrate serveru.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751083"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrace virtuálních počítačů VMware na virtuální počítače Azure s povoleným šifrováním na straně serveru a zákaznickými klíči

Tento článek popisuje, jak migrovat virtuální počítače VMware do virtuálních počítačů Azure pomocí disků šifrovaných pomocí šifrování na straně serveru (SSE) pomocí klíčů spravovaných zákazníkem (CMK), a to pomocí Azure Migrate migrace serveru (bez agenta).

Prostředí portálu pro migraci Azure Migrate serveru umožňuje [migrovat virtuální počítače VMware do Azure s replikací bez agenta.](tutorial-migrate-vmware.md) Prostředí portálu aktuálně nenabízí možnost zapnout SSE s CMK pro vaše replikované disky v Azure. Možnost zapnout SSE s CMK pro replikované disky je aktuálně dostupná jenom prostřednictvím REST API. V tomto článku se dozvíte, jak vytvořit a nasadit [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) pro replikaci virtuálního počítače VMware a konfiguraci replikovaných disků v Azure tak, aby používala SSE s CMK.

Příklady v tomto článku se používají [Azure PowerShell](/powershell/azure/new-azureps-module-az) k provedení úloh potřebných k vytvoření a nasazení Správce prostředků šablony.

[Přečtěte si další informace](../virtual-machines/disk-encryption.md) o šifrování na straně serveru (SSE) se spravovanými klíči Customer (CMK) pro spravované disky.

## <a name="prerequisites"></a>Předpoklady

- [Přečtěte si kurz](tutorial-migrate-vmware.md) migrace virtuálních počítačů VMware do Azure s replikací bez agentů, abyste pochopili požadavky na nástroj.
- [Postupujte podle těchto pokynů](./create-manage-projects.md) a vytvořte projekt Azure Migrate a přidejte do projektu nástroj pro **migraci serveru Azure Migrate: Server** .
- [Podle těchto pokynů](how-to-set-up-appliance-vmware.md) nastavte zařízení Azure Migrate pro VMware v místním prostředí a dokončete zjišťování.

## <a name="prepare-for-replication"></a>Příprava na replikaci

Po dokončení zjišťování virtuálních počítačů se na dlaždici zjištěné servery na dlaždici migrace serveru zobrazí počet virtuálních počítačů VMware zjištěných zařízením.

Než budete moct začít replikovat virtuální počítače, je potřeba připravit infrastrukturu replikace.

1. Vytvoří instanci Service Bus v cílové oblasti. Service Bus používá místní zařízení Azure Migrate ke komunikaci se službou pro migraci serveru za účelem koordinace replikace a migrace.
2. Vytvořte účet úložiště pro přenos protokolů operací z replikace.
3. Vytvořte účet úložiště, do kterého Azure Migrate zařízení nahrává data replikace.
4. Vytvořte Key Vault a nakonfigurujte Key Vault, abyste mohli spravovat tokeny sdíleného přístupového podpisu pro přístup k objektu BLOB v účtech úložiště vytvořených v kroku 3 a 4.
5. Vygenerujte token sdíleného přístupového podpisu pro službu Service Bus vytvořenou v kroku 1 a vytvořte tajný klíč pro token ve Key Vault vytvořeném v předchozím kroku.
6. Vytvořte zásadu přístupu Key Vault, která poskytne místní zařízení Azure Migrate (pomocí aplikace AAD pro zařízení) a přístup k Key Vault službě migrace serveru.
7. Vytvořte zásadu replikace a nakonfigurujte službu migrace serveru s podrobnostmi o infrastruktuře replikace vytvořené v předchozím kroku.

Infrastruktura replikace se musí vytvořit v cílové oblasti Azure pro migraci a v cílovém předplatném Azure, na které se virtuální počítače migrují.

Prostředí portálu pro migraci serveru zjednodušuje přípravu infrastruktury replikace tím, že je automaticky provede při první replikaci virtuálního počítače v projektu. V tomto článku předpokládáme, že už máte replikovaný jeden nebo víc virtuálních počítačů s využitím portálu a že už je vytvořená infrastruktura replikace. Podíváme se na to, jak zjistit podrobnosti o stávající infrastruktuře replikace a jak tyto podrobnosti použít jako vstupy do šablony Správce prostředků, která se použije k nastavení replikace pomocí CMK.

### <a name="identifying-replication-infrastructure-components"></a>Určení součástí infrastruktury replikace

1. Na Azure Portal otevřete stránku skupiny prostředků a vyberte skupinu prostředků, ve které byl vytvořen Azure Migrate projekt.
2. V nabídce vlevo vyberte **nasazení** a vyhledejte název nasazení začínající řetězcem *"Microsoft. MigrateV2. VMwareV2EnableMigrate"*. Zobrazí se seznam šablon Správce prostředků vytvořených prostředím portálu pro nastavení replikace pro virtuální počítače v tomto projektu. Stáhneme jednu takovou šablonu a použijeme ji jako základ pro přípravu šablony pro replikaci pomocí CMK.
3. Pokud chcete stáhnout šablonu, vyberte libovolné nasazení, které odpovídá vzoru řetězce v předchozím kroku > v nabídce vlevo vyberte **šablonu** > v horní nabídce klikněte na **Stáhnout** . Uložit template.jsdo souboru lokálně. Tento soubor šablony budete upravovat v posledním kroku.

## <a name="create-a-disk-encryption-set"></a>Vytvoření sady šifrování disků

Objekty pro nastavení šifrování disku – mapování Managed Disks na Key Vault obsahující CMK, který se má použít pro SSE. K replikaci virtuálních počítačů pomocí CMK vytvoříte sadu pro šifrování disků a předáte ji jako vstup do operace replikace.

Podle [následujícího příkladu vytvořte](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) pomocí Azure PowerShell sadu šifrování disku. Ujistěte se, že je v cílovém předplatném, na které jsou virtuální počítače migrovány, vytvořená sada Disk Encryption, a v cílové oblasti Azure pro migraci.

Sada šifrování disků se dá nakonfigurovat tak, aby se pomocí klíče spravovaného zákazníkem a klíče platformy zašifroval spravované disky, nebo pro dvojité šifrování. Pokud chcete použít možnost dvojité šifrování v klidovém formátu, nakonfigurujte sadu šifrování disku, jak je popsáno [zde](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

V příkladu zobrazeném níže je sada Disk Encryption nakonfigurovaná tak, aby používala klíč spravovaný zákazníkem.

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

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Získat podrobnosti o virtuálním počítači VMware pro migraci

V tomto kroku použijete Azure PowerShell k získání podrobných informací o virtuálním počítači, který je potřeba migrovat. Tyto podrobnosti budou použity k vytvoření šablony Správce prostředků pro replikaci. Konkrétně jsou to tyto dvě vlastnosti zájmu:

- ID prostředku počítače pro zjištěné virtuální počítače.
- Seznam disků pro virtuální počítač a jejich identifikátory disku.

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

Zkopírujte hodnotu identifikátoru řetězce, který odpovídá Azure Migratemu zařízení, prostřednictvím kterého se virtuální počítač objevil. V příkladu uvedeném výše je identifikátor ID *"/Subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* .

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

Zkopírujte hodnoty ResourceId, Name a UUID disku pro počítač, který chcete migrovat.
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

- Otevřete soubor šablony Správce prostředků, který jste si stáhli v kroku **identifikující infrastrukturu replikace** v libovolném editoru.
- Odebere z šablony všechny definice prostředků kromě prostředků, které jsou typu *Microsoft. RecoveryServices/trezors/replicationFabrics/replicationProtectionContainers/replicationMigrationItems* .
- Pokud existuje více definic prostředků výše uvedeného typu, odeberte všechny kromě jednoho. Odeberte všechny definice vlastností **dependsOn** z definice prostředků.
- Na konci tohoto kroku byste měli mít soubor, který vypadá podobně jako v následujícím příkladu a má stejnou sadu vlastností.

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

- V definici prostředků upravte vlastnost **název** . Nahraďte řetězec, který následuje za posledním znakem "/" ve vlastnosti Name s hodnotou *$Machine. Název*(z předchozího kroku).
- Změňte hodnotu vlastnosti **. providerSpecificDetails. vmwareMachineId** s hodnotou *$Machine. ResourceId*(z předchozího kroku).
- Nastavte hodnoty pro **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** na ID cílové skupiny prostředků, ID prostředku cílové virtuální sítě a název cílové podsítě.
- Pro použití Zvýhodněné hybridní využití Azure pro tento virtuální počítač nastavte hodnotu **LicenseType** na "windowsserver". Pokud tento virtuální počítač nemá nárok na Zvýhodněné hybridní využití Azure, nastavte hodnotu **LicenseType** na NoLicenseType.
- Změňte hodnotu vlastnosti **targetVmName** na požadovaný název virtuálního počítače Azure pro migrovaný virtuální počítač.
- Volitelně přidejte vlastnost s názvem **targetVmSize** pod vlastnost **targetVmName** . Nastavte hodnotu vlastnosti **targetVmSize** na požadovanou velikost virtuálního počítače Azure pro migrovaný virtuální počítač.
- Vlastnost **disksToInclude** je seznam vstupů disku pro replikaci s každou položkou seznamu, která představuje jeden místní disk. Vytvořte tolik položek seznamu jako počet disků na místním virtuálním počítači. Nahraďte vlastnost **diskId** v položce seznamu identifikátorem UUID disků identifikovaných v předchozím kroku. Nastavte hodnotu **isOSDisk** na "true" pro disk s operačním systémem virtuálního počítače a "false" pro všechny ostatní disky. **LogStorageAccountId** a vlastnosti **logStorageAccountSasSecretName** ponechte beze změny. Nastavte hodnotu **diskType** na typ spravovaného disku Azure (*Standard_LRS, Premium_LRS, StandardSSD_LRS*), který se má použít pro disk. Pro disky, které je potřeba zašifrovat pomocí CMK přidejte vlastnost s názvem **diskEncryptionSetId** a nastavte hodnotu na ID prostředku pro vytvořenou sadu šifrování disku (**$des. ID**) v kroku *Vytvoření šifrovacího disku sady*
- Uložte upravený soubor šablony. V příkladu výše vypadá upravený soubor šablony následujícím způsobem:

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

Nyní můžete nasadit upravenou šablonu Správce prostředků do skupiny prostředků projektu pro nastavení replikace pro virtuální počítač. Naučte se [nasadit prostředek pomocí Azure Resource Manager šablon a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

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