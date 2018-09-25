# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Běžné chyby při migraci z modelu Classic na Azure Resource Manager
Tento článek obsahuje katalog nejběžnějších chyb a omezení rizik při migraci prostředků IaaS z modelu nasazení Azure Classic do zásobníku Azure Resource Manageru.

## <a name="list-of-errors"></a>Seznam chyb
| Text chyby | Omezení rizik |
| --- | --- |
| Vnitřní chyba serveru |V některých případech se jedná o přechodnou chybu, která zmizí při opakování pokusu. Pokud k chybě dochází i nadále, [kontaktujte podporu Azure](../articles/azure-supportability/how-to-create-azure-support-request.md), protože je potřeba prošetřit protokoly platformy. <br><br> **POZNÁMKA:** Jakmile incident začne sledovat tým podpory, nepokoušejte se sami o omezení rizik. Mohlo by to mít nežádoucí důsledky pro vaše prostředí. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože jde o nasazení PaaS (webová role/role pracovního procesu). |K tomu dochází v případě, že nasazení obsahuje webovou roli nebo roli pracovního procesu. Protože se migrace podporuje pouze u virtuálních počítačů, odeberte z nasazení webovou roli nebo roli pracovního procesu a zkuste migrovat znovu. |
| Nasazení šablony {název_šablony} selhalo. CorrelationId={guid} |V back-endu služby migrace používáme k vytváření prostředků v zásobníku Azure Resource Manageru šablony Azure Resource Manageru. Vzhledem k tomu, že šablony jsou idempotentní, obvykle můžete bez obav opakovat operaci migrace a obejít tuto chybu. Pokud k této chybě dochází i nadále, [kontaktujte podporu Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) a sdělte jim hodnotu CorrelationId. <br><br> **POZNÁMKA:** Jakmile incident začne sledovat tým podpory, nepokoušejte se sami o omezení rizik. Mohlo by to mít nežádoucí důsledky pro vaše prostředí. |
| Virtuální síť {název_virtuální_sítě} neexistuje. |K tomu může dojít v případě, že jste virtuální síť vytvořili na novém webu Azure Portal. Název skutečné virtuální sítě má tvar „Group * <VNET name>“ |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, které se v Azure Resource Manageru nepodporuje. Před pokračováním v migraci se doporučuje toto rozšíření odinstalovat z virtuálního počítače. |Rozšíření XML, jako je například BGInfo 1.*, nejsou podporována v Azure Resource Manageru. Proto tato rozšíření není možné migrovat. Pokud tato rozšíření zůstanou nainstalována na virtuálním počítači, před dokončením migrace se automaticky odinstalují. |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření VMSnapshot/VMSnapshotLinux, jehož migrace se aktuálně nepodporuje. Odinstalujte ho z virtuálního počítače a po dokončení migrace ho přidejte zpět pomocí Azure Resource Manageru. |Jedná se o scénář, kde je virtuální počítač nakonfigurovaný pro službu Azure Backup. Protože aktuálně se jedná o nepodporovaný scénář, postupujte prosím podle alternativní řešení na https://aka.ms/vmbackupmigration |
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, jehož stav se nehlásí z virtuálního počítače. Virtuální počítač proto není možné migrovat. Ujistěte se, že se stav rozšíření hlásí, nebo rozšíření odinstalujte z virtuálního počítače a opakujte migraci. <br><br> Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje rozšíření {název_rozšíření}, které hlásí stav obslužné rutiny: {stav_obslužné_rutiny}. Virtuální počítač proto není možné migrovat. Ujistěte se, že se stav obslužné rutiny rozšíření hlásí jako {stav_obslužné_rutiny}, nebo rozšíření odinstalujte z virtuálního počítače a opakujte migraci. <br><br> Agent virtuálního počítače pro virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} hlásí celkový stav agenta jako Není připraveno. Virtuální počítač proto možná nepůjde migrovat, i když bude mít rozšíření, které migrovat jde. Ujistěte se, že agent virtuálního počítače hlásí celkový stav agenta jako Připraveno. Odkazovat na https://aka.ms/classiciaasmigrationfaqs. |Agent hosta Azure a rozšíření virtuálního počítače pro zadání svého stavu potřebují odchozí internetový přístup k účtu úložiště virtuálního počítače. Mezi běžné příčiny chyby stavu patří: <li> Skupina zabezpečení sítě, která blokuje odchozí přístup k internetu <li> Situace, kdy má virtuální síť místní servery DNS a dojde ke ztrátě připojení k DNS <br><br> Pokud se bude i nadále zobrazovat nepodporovaný stav, můžete odinstalováním rozšíření tuto kontrolu přeskočit a pokračovat v migraci. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože má více skupin dostupnosti. |Aktuálně je možné migrovat pouze hostované služby s 1 nebo žádnou skupinou dostupnosti. Pokud chcete tento problém obejít, přesuňte dodatečné skupiny dostupnosti a virtuální počítače v těchto skupinách dostupnosti do jiné hostované služby. |
| Migrace se u nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepodporuje, protože jsou v něm virtuální počítače, které nejsou součástí skupiny dostupnosti, přestože je tato skupina v hostované službě. |Alternativním řešením pro tento scénář je buď přesun všech virtuálních počítačů do jedné skupiny dostupnosti, nebo odebrání všech virtuálních počítačů ze skupiny dostupnosti v hostované službě. |
| Účet úložiště, hostovaná služba nebo virtuální síť {název_virtuální_sítě} se právě migruje a proto nejde změnit. |K této chybě dochází v případě, že se dokončila operace „Příprava“ migrace prostředku a aktivuje se operace, která by provedla změnu tohoto prostředku. Kvůli zámku v rovině správy po operaci „Příprava“ jsou všechny změny prostředku blokovány. Pokud chcete odemknout rovinu správy, můžete migraci dokončit spuštěním operace „Potvrzení“ migrace nebo vrátit zpět operaci „Příprava“ spuštěním operace „Přerušení“ migrace. |
| Migrace se pro hostovanou službu {název_hostované_služby} nepovoluje, protože má virtuální počítač {název_virtuálního_počítače} ve stavu: Neznámý stav role. Migrace se povoluje, jenom když je virtuální počítač v jednom z následujících stavů: Spuštěno, Zastaveno, Zastaveno (přidělení zrušeno). |Virtuální počítač může procházet přechodem stavu. K tomu obvykle dochází, když v hostované službě probíhá operace aktualizace, jako je restartování, instalace rozšíření atd. Doporučuje se před pokusem o migraci dokončit operaci aktualizace v hostované službě. |
| Nasazení {název_nasazení} v hostované službě {název_hostované_služby} obsahuje virtuální počítač {název_virtuálního_počítače} s datovým diskem {název_datového_disku}, u kterého fyzická velikost jeho objektu blob {velikost_objektu_blob_VHD_pro_datový_disk} bajtů neodpovídá logické velikosti datového disku virtuálního počítače, která je {velikost_datového_disku_zadaná_v_rozhraní_API_virtuálního_počítače} bajtů. Migrace bude pokračovat bez určení velikosti datového disku pro virtuální počítač Azure Resource Manageru. | K této chybě dochází v případě, že jste změnili velikost objektu blob VHD, aniž byste aktualizovali velikost v modelu rozhraní API virtuálního počítače. Podrobné kroky pro zmírnění rizika jsou uvedené [níže](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Došlo k výjimce úložiště při ověřování datového disku {název_datového_disku} s odkazem na médium {identifikátor_URI_datového_disku} pro virtuální počítač {název_virtuálního_počítače} v cloudové službě {název_cloudové_služby}. Ujistěte se, že je pro tento virtuální počítač dostupný odkaz na médium VHD. | K této chybě může dojít, pokud byly disky virtuálního počítače odstraněny, nebo pokud už nejsou dostupné. Ujistěte se, že disky pro virtuální počítač existují.|
| Virtuální počítač {název_virtuálního_počítače} v hostované službě {název_hostované_služby} obsahuje disk s odkazem na médium {identifikátor_URI_VHD} s názvem objektu blob {název_objektu_blob_VHD}, který se v Azure Resource Manageru nepodporuje. | K této chybě dochází, když název objektu blob obsahuje znak „/“, který se aktuálně v poskytovateli výpočetních prostředků nepodporuje. |
| Migrace se pro nasazení {název_nasazení} v hostované službě {název_hostované_služby} nepovoluje, protože není v místním oboru. Najdete http://aka.ms/regionalscope o přesunutí tohoto nasazení do místního oboru. | V roce 2014 byl pro Azure oznámen přesun síťových prostředků z oboru na úrovni serveru do místního oboru. V tématu [http://aka.ms/regionalscope] Další podrobnosti (http://aka.ms/regionalscope). K této chybě dochází v případě, že u migrovaného nasazení neproběhla operace aktualizace, která by ho automaticky přesunula do místního oboru. Nejlepším alternativním řešením je přidat do virtuálního počítače koncový bod nebo datový disk a potom opakovat migraci. <br> Viz [Postup nastavení koncových bodů na klasickém virtuálním počítači s Windows v Azure](../articles/virtual-machines/windows/classic/setup-endpoints.md#create-an-endpoint) nebo [Připojení datového disku k virtuálnímu počítači s Windows vytvořenému pomocí modelu nasazení Classic](../articles/virtual-machines/windows/classic/attach-disk.md).|
| Migrace se u virtuální sítě {název virtuální sítě} nepodporuje, protože obsahuje nasazení PaaS jiného typu než brány. | K této chybě dochází v případě, že máte nasazení PaaS jiného typu než brány, jako je například služba Application Gateway nebo rozhraní API správy služeb, které jsou připojené k virtuální síti.|


## <a name="detailed-mitigations"></a>Podrobné způsoby zmírnění rizik

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtuální počítač s datovými diskem, u kterého fyzická velikost jeho objektu blob v bajtech neodpovídá logické velikosti datového disku virtuálního počítače v bajtech.

K tomu dochází, když je možné, že logická velikost datového disku není synchronizovaná se skutečnou velikostí objektu blob VHD. Můžete to snadno ověřit pomocí následujících příkazů:

#### <a name="verifying-the-issue"></a>Ověření problému

```PowerShell
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
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

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

```PowerShell
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

Po dokončení procesu migrace možná budete chtít přesunout virtuální počítač do jiného předplatného. Pokud však na virtuálním počítači máte tajný klíč nebo certifikát, který odkazuje na prostředek Key Vault, přesun se aktuálně nepodporuje. Pomocí následujících pokynů můžete tento problém obejít. 

#### <a name="powershell"></a>PowerShell
```powershell
$vm = Get-AzureRmVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzureRmVMSecret -VM $vm
Update-AzureRmVM -ResourceGroupName "MyRG" -VM $vm
```
#### <a name="azure-cli"></a>Azure CLI

```bash
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```
