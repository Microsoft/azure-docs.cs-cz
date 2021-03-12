---
title: Běžné chyby při migraci z modelu Classic na Azure Resource Manager
description: Tento článek je katalogem nejčastějších chyb a rizik během migrace prostředků IaaS ze správy služeb Azure na Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 6d803d1a66c069f5eb42deead453a8526577f76b
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615206"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>Chyby, které běžně nastávají při migraci z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).

Tento článek obsahuje katalog nejběžnějších chyb a omezení rizik při migraci prostředků IaaS z modelu nasazení Azure Classic do zásobníku Azure Resource Manageru.


## <a name="list-of-errors"></a>Seznam chyb

| Text chyby | Omezení rizik |
| --- | --- |
| Vnitřní chyba serveru |V některých případech se jedná o přechodnou chybu, která zmizí při opakování pokusu. Pokud k chybě dochází i nadále, [kontaktujte podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md), protože je potřeba prošetřit protokoly platformy. <br><br> **POZNÁMKA:** Jakmile incident začne sledovat tým podpory, nepokoušejte se sami o omezení rizik. Mohlo by to mít nežádoucí důsledky pro vaše prostředí. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože jde o nasazení PaaS (webová role/role pracovního procesu). |K tomu dochází v případě, že nasazení obsahuje webovou roli nebo roli pracovního procesu. Protože se migrace podporuje pouze u virtuálních počítačů, odeberte z nasazení webovou roli nebo roli pracovního procesu a zkuste migrovat znovu. |
| Nasazení šablony {název_šablony} selhalo. CorrelationId={guid} |V back-endu služby migrace používáme k vytváření prostředků v zásobníku Azure Resource Manageru šablony Azure Resource Manageru. Vzhledem k tomu, že šablony jsou idempotentní, obvykle můžete bez obav opakovat operaci migrace a obejít tuto chybu. Pokud k této chybě dochází i nadále, [kontaktujte podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) a sdělte jim hodnotu CorrelationId. <br><br> **POZNÁMKA:** Jakmile incident začne sledovat tým podpory, nepokoušejte se sami o omezení rizik. Mohlo by to mít nežádoucí důsledky pro vaše prostředí. |
| Virtuální síť {název_virtuální_sítě} neexistuje. |K tomu může dojít v případě, že jste virtuální síť vytvořili na novém webu Azure Portal. Název skutečné virtuální sítě má tvar „Group * \<VNET name>“ |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, které se v Azure Resource Manageru nepodporuje. Před pokračováním v migraci se doporučuje toto rozšíření odinstalovat z virtuálního počítače. |Přípona XML, jako je například BGInfo 1. \* nejsou podporovány v Azure Resource Manager. Proto tato rozšíření není možné migrovat. Pokud tato rozšíření zůstanou nainstalována na virtuálním počítači, před dokončením migrace se automaticky odinstalují. |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření VMSnapshot/VMSnapshotLinux, jehož migrace se aktuálně nepodporuje. Odinstalujte ho z virtuálního počítače a po dokončení migrace ho přidejte zpět pomocí Azure Resource Manageru. |Jedná se o scénář, kde je virtuální počítač nakonfigurovaný pro službu Azure Backup. Vzhledem k tomu, že se jedná o nepodporovaný scénář, postupujte prosím podle pokynů v https://aka.ms/vmbackupmigration |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, jehož stav se nehlásí z virtuálního počítače. Virtuální počítač proto není možné migrovat. Ujistěte se, že se stav rozšíření hlásí, nebo rozšíření odinstalujte z virtuálního počítače a opakujte migraci. <br><br> Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, které hlásí stav obslužné rutiny: {stav_obslužné_rutiny}. Virtuální počítač proto není možné migrovat. Ujistěte se, že se stav obslužné rutiny rozšíření hlásí jako {stav_obslužné_rutiny}, nebo rozšíření odinstalujte z virtuálního počítače a opakujte migraci. <br><br> Agent virtuálního počítače pro virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} hlásí celkový stav agenta jako Není připraveno. Virtuální počítač proto možná nepůjde migrovat, i když bude mít rozšíření, které migrovat jde. Ujistěte se, že agent virtuálního počítače hlásí celkový stav agenta jako Připraveno. Informace najdete v tématu https://aka.ms/classiciaasmigrationfaqs . |Agent hosta Azure a rozšíření virtuálního počítače pro zadání svého stavu potřebují odchozí internetový přístup k účtu úložiště virtuálního počítače. Mezi běžné příčiny chyby stavu patří: <li> Skupina zabezpečení sítě, která blokuje odchozí přístup k internetu <li> Pokud virtuální síť funguje na místních serverech DNS a dojde ke ztrátě připojení DNS <br><br> Pokud se bude i nadále zobrazovat nepodporovaný stav, můžete odinstalováním rozšíření tuto kontrolu přeskočit a pokračovat v migraci. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože má více skupin dostupnosti. |Aktuálně je možné migrovat pouze hostované služby s 1 nebo žádnou skupinou dostupnosti. Pokud chcete tento problém obejít, přesuňte další skupiny dostupnosti a virtuální počítače v těchto skupinách dostupnosti do jiné hostované služby. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože jsou v něm virtuální počítače, které nejsou součástí skupiny dostupnosti, přestože je tato skupina v hostované službě. |Alternativním řešením pro tento scénář je buď přesun všech virtuálních počítačů do jedné skupiny dostupnosti, nebo odebrání všech virtuálních počítačů ze skupiny dostupnosti v hostované službě. |
| Účet úložiště, hostovaná služba nebo virtuální síť {název_virtuální_sítě} se právě migruje a proto nejde změnit. |K této chybě dochází v případě, že se dokončila operace „Příprava“ migrace prostředku a aktivuje se operace, která by provedla změnu tohoto prostředku. Kvůli zámku v rovině správy po operaci „Příprava“ jsou všechny změny prostředku blokovány. Pokud chcete odemknout rovinu správy, můžete migraci dokončit spuštěním operace „Potvrzení“ migrace nebo vrátit zpět operaci „Příprava“ spuštěním operace „Přerušení“ migrace. |
| Migrace se pro hostovanou službu {název_hostované_služby} nepovoluje, protože má virtuální počítač {název_virtuálního_počítače} ve stavu: Neznámý stav role. Migrace se povoluje, jenom když je virtuální počítač v jednom z následujících stavů: Spuštěno, Zastaveno, Zastaveno (přidělení zrušeno). |Virtuální počítač může proběhnout přechodem stavu, který se obvykle stává během operace aktualizace na hostované službě, jako je restartování, instalace rozšíření atd. Než se pokusíte migrovat, doporučuje se, aby se operace aktualizace na hostované službě dokončila. |
| Nasazení {název_nasazení} v hostované službě {název_hostované_služby} obsahuje virtuální počítač {název_virtuálního_počítače} s datovým diskem {název_datového_disku}, u kterého fyzická velikost jeho objektu blob {velikost_objektu_blob_VHD_pro_datový_disk} bajtů neodpovídá logické velikosti datového disku virtuálního počítače, která je {velikost_datového_disku_zadaná_v_rozhraní_API_virtuálního_počítače} bajtů. Migrace bude pokračovat bez určení velikosti datového disku pro virtuální počítač Azure Resource Manageru. | K této chybě dochází v případě, že jste změnili velikost objektu blob VHD, aniž byste aktualizovali velikost v modelu rozhraní API virtuálního počítače. Podrobné kroky pro zmírnění rizika jsou uvedené [níže](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Došlo k výjimce úložiště při ověřování datového disku {název_datového_disku} s odkazem na médium {identifikátor_URI_datového_disku} pro virtuální počítač {název_virtuálního_počítače} v cloudové službě {název_cloudové_služby}. Ujistěte se, že je pro tento virtuální počítač dostupný odkaz na médium VHD. | K této chybě může dojít, pokud byly disky virtuálního počítače odstraněny, nebo pokud už nejsou dostupné. Ujistěte se, že disky pro virtuální počítač existují.|
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje disk s odkazem na médium {identifikátor_URI_VHD} s názvem objektu blob {název_objektu_blob_VHD}, který se v Azure Resource Manageru nepodporuje. | K této chybě dochází, když název objektu blob obsahuje znak „/“, který se aktuálně v poskytovateli výpočetních prostředků nepodporuje. |
| Migrace se pro nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepovoluje, protože není v místním oboru. Pokud chcete \/ Toto nasazení přesunout do regionálního rozsahu, přečtěte si prosím https:/aka.MS/regionalscope. | V roce 2014 byl pro Azure oznámen přesun síťových prostředků z oboru na úrovni serveru do místního oboru. Další podrobnosti najdete v tématu [https://aka.ms/regionalscope](https://aka.ms/regionalscope) . K této chybě dochází v případě, že u migrovaného nasazení neproběhla operace aktualizace, která by ho automaticky přesunula do místního oboru. Nejlepším řešením je přidat koncový bod do virtuálního počítače nebo datový disk k virtuálnímu počítači a pak zkusit migraci znovu. <br> Přečtěte si, [jak nastavit koncové body na klasickém virtuálním počítači v Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) nebo [připojit datový disk k virtuálnímu počítači vytvořenému pomocí modelu nasazení Classic](./linux/attach-disk-portal.md) .|
| Migrace se u Virtual Network {VNet-Name} nepodporuje, protože obsahuje nasazení PaaS bez brány. | K této chybě dochází, pokud máte nasazení bez brány PaaS, například Application Gateway nebo API Management služby, které jsou připojeny k Virtual Network.|


## <a name="detailed-mitigations"></a>Podrobné způsoby zmírnění rizik

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtuální počítač s datovými diskem, u kterého fyzická velikost jeho objektu blob v bajtech neodpovídá logické velikosti datového disku virtuálního počítače v bajtech.

K tomu dochází, když je možné, že logická velikost datového disku není synchronizovaná se skutečnou velikostí objektu blob VHD. Můžete to snadno ověřit pomocí následujících příkazů:

#### <a name="verifying-the-issue"></a>Ověření problému

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Zmírnění problému

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Přesun virtuálního počítače do jiného předplatného po dokončení migrace

Po dokončení procesu migrace možná budete chtít přesunout virtuální počítač do jiného předplatného. Pokud však na virtuálním počítači máte tajný klíč nebo certifikát, který odkazuje na prostředek Key Vault, přesun se aktuálně nepodporuje. Následující pokyny vám umožní tento problém obejít. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)