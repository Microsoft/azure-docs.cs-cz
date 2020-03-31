---
title: Použití úložiště Azure Premium se serverem SQL Server | Dokumenty společnosti Microsoft
description: Tento článek používá prostředky vytvořené pomocí klasického modelu nasazení a poskytuje pokyny k používání azure úložiště s SQL Server běží na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 479f9abc667e20a136da5f6231e78a1e4052f087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965671"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Použití Azure Premium Storage s SQL Serverem na virtuálních počítačích

## <a name="overview"></a>Přehled

[Azure premium SSD je](../disks-types.md) další generace úložiště, které poskytuje nízkou latenci a vysokou propustnost vi. Funguje nejlépe pro klíčové úlohy náročné na vi, jako je například SQL Server na [virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/)IaaS .

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek obsahuje plánování a pokyny pro migraci virtuálního počítače se systémem SQL Server pro použití úložiště Premium. To zahrnuje infrastrukturu Azure (sítě, úložiště) a kroky hostovaného virtuálního počítače s Windows. Příklad v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) ukazuje úplné komplexní ukončení migrace, jak přesunout větší virtuální chodů využít výhod vylepšené místní úložiště SSD s PowerShell.

Je důležité pochopit proces komplexnívyužití azure premium storage s SQL Server na virtuálních počítačích IAAS. To zahrnuje:

* Identifikace předpokladů pro použití úložiště Premium.
* Příklady nasazení SQL Serveru na IaaS do úložiště Premium pro nová nasazení.
* Příklady migrace existujících nasazení, samostatných serverů i nasazení pomocí skupin y dostupnosti SQL Always On.
* Možné migrační přístupy.
* Úplný příklad dokončení azure, windows a sql server kroky pro migraci existující vždy na implementaci.

Další základní informace o SQL Serveru ve virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Technické recenzenti:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Předpoklady pro prémiové úložiště

Existuje několik předpokladů pro používání úložiště Premium.

### <a name="machine-size"></a>Velikost stroje

Pro použití úložiště Premium je třeba použít virtuální počítače řady DS (VM). Pokud jste dříve ve své cloudové službě nepoužívali počítače řady DS, musíte odstranit existující virtuální počítač, zachovat připojené disky a pak vytvořit novou cloudovou službu před opětovným vytvořením virtuálního počítače jako velikosti role DS*. Další informace o velikostech virtuálních strojů najdete v [tématu Velikosti virtuálních strojů a cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Cloud Services

Virtuální aplikace DS* s úložištěm Premium můžete používat jenom v případě, že jsou vytvořeny v nové cloudové službě. Pokud používáte SQL Server always on v Azure, vždy na schytá odkazuje na azure interní nebo externí vyrovnávání zatížení IP adresu, která je přidružena ke cloudové službě. Tento článek se zaměřuje na způsob migrace při zachování dostupnosti v tomto scénáři.

> [!NOTE]
> Řada DS* musí být první virtuální virtuální ms nasazený do nové cloudové služby.
>
>

### <a name="regional-vnets"></a>Místní virtuální sítě

Pro virtuální počítače DS* je nutné nakonfigurovat virtuální síť (VNET) hostující vaše virtuální počítače tak, aby byly místní. To "rozšiřuje" virtuální sítě je umožnit větší virtuální chod, které mají být zřízeny v jiných clusterech a povolit komunikaci mezi nimi. Na následujícím snímku obrazovky zvýrazněné umístění zobrazuje regionální VNET, zatímco první výsledek ukazuje "úzký" Virtuální síť.

![RegionálníVirtuální síť pro místní rozvoj][1]

Můžete zvýšit lístek podpory Microsoftu pro migraci do místní virtuální sítě. Microsoft pak provede změnu. Chcete-li dokončit migraci na regionální virtuální sítě, změňte vlastnost AffinityGroup v konfiguraci sítě. Nejprve exportujte konfiguraci sítě v prostředí PowerShell a potom nahraďte vlastnost **AffinityGroup** v elementu **VirtualNetworkSite** vlastností **Location.** Určete, `Location = XXXX` kde `XXXX` je oblast Azure. Pak importujte novou konfiguraci.

Například s ohledem na následující konfiguraci virtuální sítě:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Chcete-li tuto síť přesunout do regionální virtuální sítě v západní Evropě, změňte konfiguraci na následující:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Účty úložiště

Musíte vytvořit nový účet úložiště, který je nakonfigurovaný pro úložiště Premium. Všimněte si, že použití úložiště Premium je nastaveno na účtu úložiště, nikoli na jednotlivých virtuálních počítačích, ale při použití virtuálního počítače řady DS* můžete připojit virtuální disky z účtů Premium a Standard Storage. Můžete to zvážit, pokud nechcete umístit virtuální pevný disk operačního systému na účet úložiště Premium.

Následující příkaz **New-AzureStorageAccountPowerShell** s **typem** "Premium_LRS" vytvoří účet úložiště Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Nastavení mezipaměti virtuálních disponálů

Hlavní rozdíl mezi vytvářením disků, které jsou součástí účtu úložiště Premium, je nastavení mezipaměti disku. Pro disky svazku dat serveru SQL Server doporučujeme použít '**Ukládání do mezipaměti**'. U svazků transakčního protokolu by mělo být nastavení mezipaměti disku nastaveno na**hodnotu**Žádný. To se liší od doporučení pro účty standard storage.

Po připojení virtuálních discích nelze změnit nastavení mezipaměti. Budete muset odpojit a znovu připojit virtuální pevný disk s aktualizovaným nastavením mezipaměti.

### <a name="windows-storage-spaces"></a>Prostory pro úložiště systému Windows

[Prostory úložiště Windows](https://technet.microsoft.com/library/hh831739.aspx) můžete používat stejně jako u předchozího standardního úložiště, což vám umožní migrovat virtuální hok, který už využívá prostory úložiště. Příklad v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 a vpřed) ukazuje kód Powershell extrahovat a importovat virtuální počítač s více připojených Virtuální počítač.

Fondy úložiště se používaly se standardním účtem úložiště Azure ke zvýšení propustnosti a snížení latence. Můžete najít hodnotu v testování fondů úložiště s úložištěm Premium pro nová nasazení, ale přidávají další složitost s nastavením úložiště.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak zjistit, které virtuální disky Azure jsou mapovány do fondů úložišť

Vzhledem k tomu, že existují různá doporučení pro nastavení mezipaměti pro připojené virtuální řadiče, můžete se rozhodnout zkopírovat virtuální řadiče do účtu úložiště Premium. Když je však znovu připojíte k novému virtuálnímu počítače řady DS, bude pravděpodobně nutné změnit nastavení mezipaměti. Je jednodušší použít nastavení mezipaměti doporučené úložiště Premium Storage, pokud máte samostatné virtuální pevné disky pro datové soubory SQL a soubory protokolu (spíše než jeden virtuální pevný disk, který obsahuje obojí).

> [!NOTE]
> Pokud máte sql server data a soubory protokolu na stejném svazku, možnost ukládání do mezipaměti, kterou zvolíte, závisí na vzorcích přístupu videa pro úlohy databáze. Pouze testování může prokázat, která možnost ukládání do mezipaměti je nejvhodnější pro tento scénář.
>
>

Pokud však používáte prostory úložiště systému Windows, které se skládají z více virtuálních pevných disků, musíte se podívat na původní skripty, abyste zjistili, které připojené virtuální pevné disky jsou v jakém konkrétním fondu, takže můžete nastavit nastavení mezipaměti odpovídajícím způsobem pro každý disk.

Pokud nemáte k dispozici původní skript, který vám ukáže, které virtuální pevné disky jsou mapovány do fondu úložiště, můžete k určení mapování fondu disků nebo úložišť použít následující kroky.

Pro každý disk použijte následující kroky:

1. Získejte seznam disků připojených k virtuálnímu počítači pomocí příkazu **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Všimněte si názvu DiskName a logické jednotky.

    ![Název_diskuAndlun][2]
1. Vzdálená plocha do virtuálního počítače. Potom přejděte na**diskové jednotky****správce zařízení pro** |  **správu** | počítače . Podívejte se na vlastnosti každého z 'Microsoft Virtuální disky'

    ![Vlastnosti virtualdisků][3]
1. Číslo logické jednotky je odkazem na číslo logické jednotky, které zadáte při připojování virtuálního pevného disku k virtuálnímu virtuálnímu účtu.
1. Pro 'Microsoft Virtuální disk' přejděte na **kartu Podrobnosti,** pak v seznamu **vlastností,** přejděte na **klíč řidiče**. V **hodnotě**, **poznamenejte**si posun , který je 0002 v následujícím snímku obrazovky. 0002 označuje PhysicalDisk2, který odkazuje fond úložiště.

    ![VirtualDiskPropertyDetails][4]
1. Pro každý fond úložiště vypíšete přidružené disky:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nyní můžete tyto informace použít k přidružení připojených virtuálních disků k fyzickým diskům ve fondech úložiště.

Po namapování virtuálních pevných disků na fyzické disky ve fondech úložiště je potom můžete odpojit a zkopírovat do účtu úložiště Premium a připojit je se správným nastavením mezipaměti. Viz příklad v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroky 8 až 12. Tyto kroky ukazují, jak extrahovat konfiguraci virtuálního pevného disku připojeného k virtuálnímu počítači do souboru CSV, zkopírovat virtuální pevné disky, změnit nastavení mezipaměti konfigurace disku a nakonec znovu nasadit virtuální počítač jako virtuální počítač řady DS se všemi připojenými disky.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Šířka pásma úložiště virtuálního zařízení a propustnost úložiště virtuálního pevného disku

Velikost výkonu úložiště závisí na zadané velikosti virtuálního počítače DS* a velikosti virtuálního pevného disku. Virtuální chody mají různé povolenky pro počet virtuálních disponiál, které mohou být připojeny a maximální šířku pásma, které podporují (MB/s). Konkrétní čísla šířky pásma najdete v [tématu Velikosti virtuálních strojů a cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zvýšené viposlužby jsou dosaženy s větší velikosti disků. Měli byste to zvážit, když přemýšlíte o cestě migrace. Podrobnosti [naleznete v tabulce pro viops a typy disků](../disks-types.md#premium-ssd).

Nakonec zvažte, že virtuální počítače mají různé maximální šířky pásma disku, které podporují pro všechny připojené disky. Při vysokém zatížení můžete nasytit maximální šířku pásma disku, která je k dispozici pro tuto velikost role virtuálního počítače. Například Standard_DS14 podporuje až 512 MB/s; proto se třemi disky P30 můžete nasytit šířku pásma disku virtuálního počítače. Ale v tomto příkladu může být překročen limit propustnost v závislosti na kombinaci čtení a zápisu iOs.

## <a name="new-deployments"></a>Nová nasazení

Další dvě části ukazují, jak můžete nasadit virtuální chod SQL Server do úložiště Premium. Jak již bylo zmíněno dříve, nemusíte nutně umístit disk operačního systému do úložiště Premium. Můžete to provést, pokud máte v úmyslu umístit všechny intenzivní úlohy vi na virtuální mhd operačního systému.

První příklad ukazuje využití existujících ibiobrázků Galerie Azure. Druhý příklad ukazuje, jak používat vlastní image virtuálního aplikace, které máte v existujícím účtu úložiště Standard.

> [!NOTE]
> Tyto příklady předpokládají, že jste již vytvořili místní virtuální síť.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Vytvoření nového virtuálního virtuálního zařízení s úložištěm Premium s obrázkem galerie

Následující příklad ukazuje, jak umístit virtuální disk OS na úložiště premium a připojit virtuální disky úložiště Premium. Disk operačního systému však můžete také umístit do účtu standard storage a pak připojit virtuální disky, které jsou umístěny v účtu úložiště Premium. Oba scénáře jsou demonstrovány.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Vytvoření účtu úložiště premium

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Vytvoření nové cloudové služby

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Rezervace služby Cloud Service VIP (volitelné)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Krok 4: Vytvoření kontejneru virtuálního řezu

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: Umístění virtuálního pevného disku operačního systému na standardní nebo prémiové úložiště

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Krok 6: Vytvoření virtuálního mísy

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Vytvoření nového virtuálního virtuálního zařízení pro použití úložiště Premium s vlastní bitovou kopií

Tento scénář ukazuje, kde máte existující vlastní image, které jsou umístěny v účtu standard úložiště. Jak již bylo zmíněno, pokud chcete umístit virtuální disk OS na úložiště Premium, musíte zkopírovat obrázek, který existuje v účtu standard storage, a přenést je do úložiště Premium, než ho můžete použít. Pokud máte image v místním prostředí, můžete také tuto metodu zkopírovat přímo do účtu úložiště Premium.

#### <a name="step-1-create-storage-account"></a>Krok 1: Vytvoření účtu úložiště

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Krok 2 Vytvoření cloudové služby

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Krok 3: Použití existujícího obrázku

Můžete použít existující obrázek. Nebo můžete [pokonat snímek existujícího počítače](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si, že zařízení, které si obrázek nemusí být DS * stroj. Jakmile budete mít image, následující kroky ukazují, jak ji zkopírovat do účtu úložiště Premium pomocí **příkazu Start-AzureStorageBlobCopy** PowerShell.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopírování objektů Blob mezi účty úložiště

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: Pravidelně kontrolujte stav kopírování:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Přidání bitového disku do úložiště disků Azure v předplatném

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Můžete zjistit, že i když zprávy o stavu jako úspěch, stále můžete získat chybu zapůjčení disku. V tomto případě počkejte asi 10 minut.

#### <a name="step-7--build-the-vm"></a>Krok 7: Sestavení virtuálního virtuálního mísa

Tady vytváříte virtuální počítač z obrázku a připojujete dva virtuální počítače s úložištěm Premium:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Existující nasazení, která nepoužívají skupiny dostupnosti always on

> [!NOTE]
> Existující nasazení, nejprve naleznete [požadavky](#prerequisites-for-premium-storage) části tohoto článku.

Existují různé aspekty pro nasazení serveru SQL Server, které nepoužívají vždy na dostupnost skupiny a ty, které dělají. Pokud nepoužíváte vždy zapnutý a máte existující samostatný SQL Server, můžete upgradovat na úložiště Premium pomocí nové cloudové služby a účtu úložiště. Zvažte následující možnosti:

* **Vytvořte nový virtuální modul SQL Server**. Můžete vytvořit nový virtuální modul SQL Server, který používá účet úložiště Premium, jak je popsáno v nové nasazení. Potom zálohujte a obnovte konfiguraci serveru SQL Server a uživatelské databáze. Aplikace musí být aktualizována tak, aby odkazovala na nový SQL Server, pokud je přístupný interně nebo externě. Budete muset zkopírovat všechny 'mimo db' objekty, jako byste dělali side by side (SxS) SQL Server migrace. To zahrnuje objekty, jako jsou přihlášení, certifikáty a propojené servery.
* **Migrujte existující virtuální modul SQL Server**. To vyžaduje převedení virtuálního virtuálního soudu SQL Server offline a jeho přenos do nové cloudové služby, která zahrnuje kopírování všech připojených virtuálních discích do účtu úložiště Premium. Když virtuální ho dispozice konzumuje, aplikace odkazuje na název hostitele serveru jako dříve. Uvědomte si, že velikost existujícího disku ovlivňuje charakteristiky výkonu. Například disk o velikosti 400 GB se zaokrouhlí na p20. Pokud víte, že nepotřebujete, že výkon disku, pak můžete znovu vytvořit virtuální počítače jako virtuální počítače řady DS a připojit virtuální pevné disky úložiště Premium storage specifikace velikosti a výkonu, které požadujete. Pak můžete odpojit a znovu připojit soubory SQL DB.

> [!NOTE]
> Při kopírování disků VHD byste měli vědět o velikosti, v závislosti na velikosti znamená, jaký typ disku úložiště Premium spadají do, to určuje specifikaci výkonu disku. Azure zaokrouhlí na nejbližší velikost disku, takže pokud máte disk 400 GB, zaokrouhlí se nahoru na P20. V závislosti na vašich stávajících požadavcích na vo vsystému VHD nemusí být nutné migrovat do účtu úložiště Premium.

Pokud je váš SQL Server přístupný externě, změní se virtuální ip služba cloudové služby. Musíte také aktualizovat koncové body, alokace a nastavení DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Existující nasazení, která používají skupiny dostupnosti always on

> [!NOTE]
> Existující nasazení, nejprve naleznete [požadavky](#prerequisites-for-premium-storage) části tohoto článku.

Zpočátku v této části se podíváme na to, jak vždy zapnuto spolupracuje s Azure Networking. Migraci pak rozložíme do dvou scénářů: migrace, kde lze tolerovat určité prostoje, a migrace, kde je nutné dosáhnout minimálních prostojů.

Místní skupiny SQL Server Always On Availability používají místní listener, který registruje virtuální název DNS spolu s IP adresou sdílenou mezi jedním nebo více servery SQL. Když se klienti připojují, jsou směrovány přes IP naslouchací proces na primární SQL Server. Toto je server, který v té době vlastní prostředek ALWAYS On IP.

![NasazeníUseAlways On][6]

V Microsoft Azure můžete mít jenom jednu IP adresu přiřazenou k nic na virtuálním počítači, takže k dosažení stejné vrstvy abstrakce jako místní Azure využívá IP adresu, která je přiřazená interním/externím vykladačům zatížení (ILB/ELB). Prostředek IP, který je sdílen mezi servery, je nastaven na stejnou adresu IP jako ILB/ELB. To je publikováno v DNS a provoz klienta je předán prostřednictvím ILB/ELB do primární repliky serveru SQL Server. ILB/ELB ví, který SQL Server je primární, protože používá sondy k proprávě prostředku IP always on. V předchozím příkladu sonduje každý uzel, který má koncový bod odkazuje ELB/ILB, podle toho, co odpoví, je primární SQL Server.

> [!NOTE]
> ILB a ELB jsou přiřazeny k určité cloudové službě Azure, proto jakákoli migrace cloudu v Azure s největší pravděpodobností znamená, že se změní IP nástroj pro vyrovnávání zatížení.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrace vždy při nasazeních, která mohou umožnit určité prostoje

Existují dvě strategie migrace vždy na nasazení, které umožňují některé prostoje:

1. **Přidání dalších sekundárních replik do existujícího clusteru Vždy v clusteru**
2. **Migrace do nového clusteru vždy zapnuto**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Přidání dalších sekundárních replik do existujícího clusteru vždy v clusteru

Jednou ze strategií je přidat další sekundární informace do skupiny vždy na dostupnosti. Musíte je přidat do nové cloudové služby a aktualizovat naslouchací proces s novou IP službou vyrovnávání zatížení.

##### <a name="points-of-downtime"></a>Body prostoje:

* Ověření clusteru.
* Testování vždy při převzetí služeb při selhání pro nové sekundární.

Pokud používáte fondy úložiště Windows v rámci virtuálního virtuálního zařízení pro vyšší propustnost vi, pak se přepnou do režimu offline během ověření celého clusteru. Ověřovací test je vyžadován při přidání uzlů do clusteru. Čas potřebný ke spuštění testu se může lišit, takže byste měli otestovat v prostředí reprezentativního testu, abyste získali přibližný čas, jak dlouho to trvá.

Měli byste zřídit čas, kdy můžete provádět ruční převzetí služeb při selhání a chaos testování na nově přidané uzly k zajištění vždy na vysoké dostupnosti funkce podle očekávání.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Měli byste zastavit všechny instance serveru SQL Server, kde fondy úložiště se používají před spuštěním ověření.
>
> ##### <a name="high-level-steps"></a>Základní kroky
>

1. Vytvořte dva nové SQL servery v nové cloudové službě s připojeným úložištěm Premium.
2. Zkopírujte přes úplné zálohy a obnovení pomocí **funkce NORECOVERY**.
3. Kopírování přes 'mimo uživatele DB' závislé objekty, jako jsou přihlášení atd.
4. Vytvořte nový interní vyrovnávání zatížení (ILB) nebo použijte externí vyrovnávání zatížení (ELB) a nastavte koncové body s vyrovnáváním zatížení na obou nových uzlech.

   > [!NOTE]
   > Zkontrolujte, zda všechny uzly mají správnou konfiguraci koncového bodu, než budete pokračovat
   >
   >
5. Zastavte přístup uživatelů nebo aplikací k serveru SQL (pokud používáte fondy úložiště).
6. Zastavte služby SQL Server Engine Services ve všech uzlech (pokud používáte fondy úložiště).
7. Přidejte nové uzly do clusteru a spusťte úplné ověření.
8. Po úspěšném ověření spusťte všechny služby SQL Server Services.
9. Zálohování protokolů transakcí a obnovení uživatelských databází.
10. Přidejte nové uzly do skupiny dostupnosti vždy a umístěte replikaci do **synchronní**.
11. Přidejte prostředek IP adresy nové cloudové služby ILB/ELB prostřednictvím prostředí PowerShell pro vždy zapnuto na základě příkladu více webů v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). V clusteringu systému Windows nastavte **možné vlastníky** prostředku **adresy IP** na nové staré uzly. Viz část "Přidání prostředku adresy IP ve stejné podsíti" v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Převzetí služeb při selhání do jednoho z nových uzlů.
13. Vytvořte nové uzly Auto Failover Partners a otestujte převzetí služeb při selhání.
14. Odeberte původní uzly ze skupiny dostupnosti.

##### <a name="advantages"></a>Výhody

* Nové servery SQL lze testovat (SQL Server a aplikace) před jejich přidáním do always on.
* Můžete změnit velikost virtuálního počítače a přizpůsobit úložiště podle přesných požadavků. Bylo by však výhodné zachovat všechny cesty souborů SQL stejné.
* Můžete určit, kdy je spuštěn přenos záloh DB do sekundárních replik. To se liší od použití příkazu Azure **Start-AzureStorageBlobCopy** ke kopírování virtuálních discích, protože se jedná o asynchronní kopii.

##### <a name="disadvantages"></a>Nevýhody

* Při použití fondů úložiště systému Windows dochází k prostojům clusteru během ověření celého clusteru pro nové další uzly.
* V závislosti na verzi serveru SQL A existujícípočet sekundárních replik, nemusí být možné přidat další sekundární repliky bez odebrání existující sekundární.
* Při nastavování sekundárních dat může být dlouhá doba přenosu dat SQL.
* Během migrace se při požívají dodatečné náklady, zatímco máte nové počítače spuštěné paralelně.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrace do nového clusteru vždy zapnuto

Další strategií je vytvořit zcela nový vždy zapnutý cluster se zbrusu novými uzly v nové cloudové službě a pak přesměrovat klienty, aby ji používali.

##### <a name="points-of-downtime"></a>Body prostoje

Při přenosu aplikací a uživatelů do nového naslouchací hoschu Always On dochází k prostojům. Prostoje závisí na:

* Doba, která byla doba obnovení konečné transakce protokolu zálohy do databází na nových serverech.
* Čas, který trval a aktualizace klientských aplikací použít nový vždy na naslouchací proces.

##### <a name="advantages"></a>Výhody

* Můžete otestovat skutečné produkční prostředí, SQL Server a změny sestavení operačního systému.
* Máte možnost přizpůsobit úložiště a potenciálně snížit velikost virtuálního počítače. To by mohlo vést ke snížení nákladů.
* Během tohoto procesu můžete aktualizovat sestavení nebo verzi serveru SQL Server. Můžete také upgradovat operační systém.
* Předchozí vždy na clusteru může fungovat jako cíl vrácení zpět.

##### <a name="disadvantages"></a>Nevýhody

* Je třeba změnit název DNS naslouchací proces, pokud chcete, aby oba vždy na clustery běží současně. Tím se přidá režie správy během migrace jako řetězce klientské aplikace musí odrážet nový název listener.
* Je nutné implementovat mechanismus synchronizace mezi dvěma prostředími, aby byly co nejblíže k minimalizaci konečných požadavků na synchronizaci před migrací.
* Během migrace jsou přidány další náklady, zatímco máte spuštěné nové prostředí.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrace vždy při nasazení pro minimální prostoje

Existují dvě strategie pro migraci vždy na nasazení pro minimální prostoje:

1. **Využití existující sekundární: Jedno-site**
2. **Využití existujících sekundárních replik: Více lokalit**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Využijte stávající sekundární: Single-Site

Jednou ze strategií pro minimální prostoje je vzít existující sekundární cloud a odebrat ho z aktuální cloudové služby. Pak zkopírujte virtuální diody do nového účtu úložiště Premium a vytvořte virtuální virtuální město v nové cloudové službě. Pak aktualizujte naslouchací proces v clustering u převzetí služeb při selhání.

##### <a name="points-of-downtime"></a>Body prostoje

* Je prostoje při aktualizaci konečného uzlu s vyrovnáváním zatížení koncového bodu.
* Opětovné připojení klienta může být zpožděno v závislosti na konfiguraci klienta/DNS.
* Pokud se rozhodnete převést skupinu Always On Cluster do offline a vyměnit IP adresy, dojde k dalším výpadkům. Tomu se můžete vyhnout pomocí závislosti NEBO a možných vlastníků pro přidaný prostředek adresy IP. Viz část "Přidání prostředku adresy IP ve stejné podsíti" v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Pokud chcete, aby se přidaný uzel účastnil jako vždy na převzetí služeb při selhání partnera, musíte přidat koncový bod Azure s odkazem na sadu vyrovnávání zatížení. Při spuštění příkazu **Add-AzureEndpoint** k tomu aktuální připojení zůstat otevřené, ale nová připojení k naslouchací proces nelze navázat, dokud se aktualizuje vyrovnávání zatížení. Při testování to bylo vidět na poslední 90-120sekund, to by mělo být testováno.

##### <a name="advantages"></a>Výhody

* Žádné další náklady vzniklé během migrace.
* Migrace 1:1.
* Snížená složitost.
* Umožňuje zvýšení viops z výhod úložiště Premium. Když jsou disky odpojeny od virtuálního počítače a zkopírovány do nové cloudové služby, nástroj třetí strany lze použít ke zvýšení velikosti virtuálního pevného disku, který poskytuje vyšší propustnosti. Informace o zvýšení velikosti virtuálního pevného disku naleznete v [této diskusní mši na fóru](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nevýhody

* Během migrace dochází k dočasné ztrátě HA a ZOTAVENÍ po havárii.
* Vzhledem k tomu, že se jedná o migraci 1:1, musíte použít minimální velikost virtuálního počítače, která podporuje váš počet virtuálních discích, takže možná nebudete moct zvětšit velikost virtuálních počítačů.
* Tento scénář by použít příkaz Azure **Start-AzureStorageBlobCopy,** který je asynchronní. Po dokončení kopírování neexistuje žádná sla. Čas kopií se liší, zatímco to závisí na čekání ve frontě závisí také na množství dat k přenosu. Doba kopírování se zvyšuje, pokud se přenos přesouvá do jiného datového centra Azure, které podporuje úložiště Premium v jiné oblasti. Pokud máte pouze 2 uzly, zvažte možné zmírnění v případě, že kopie trvá déle než v testování. To by mohlo zahrnovat následující nápady.
  * Přidejte dočasný třetí uzel SQL Server pro HA před migrací s dohodnutými prostoji.
  * Spusťte migraci mimo naplánovanou údržbu Azure.
  * Ujistěte se, že jste správně nakonfigurovali kvorum clusteru.  

##### <a name="high-level-steps"></a>Základní kroky

Tento dokument neukazuje úplný příklad od konce [Appendix](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) do konce, vdodatku však obsahuje podrobnosti, které lze využít k provedení tohoto.

![Minimální doba prostoje][8]

* Shromážděte konfiguraci disku a odeberte uzel (neodstraňujte připojené virtuální disky).
* Vytvoření účtu úložiště Premium a kopírování virtuálních dispozičních zařízení z účtu standardního úložiště
* Vytvořte novou cloudovou službu a znovu nasaďte virtuální modul SQL2 v této cloudové službě. Vytvořte virtuální virtuální ms pomocí zkopírovaného původního virtuálního pevného disku operačního systému a připojeníkopím zkopírovaných virtuálních pevných disků.
* Nakonfigurujte ILB / ELB a přidejte koncové body.
* Aktualizovat posluchače buď:
  * Přepneme always on group do ústrojí a aktualizujeme always on listener s novou IP adresou ILB / ELB.
  * Nebo přidání ip adresy prostředku nové cloudové služby ILB/ELB prostřednictvím prostředí PowerShell do clusteringu Windows. Potom nastavte možné vlastníky prostředku IP adresy na migrovaný uzel SQL2 a nastavte tuto závislost jako OR v síťovém názvu. Viz část "Přidání prostředku adresy IP ve stejné podsíti" v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Zkontrolujte konfiguraci/šíření DNS klientům.
* Migrujte virtuální ms SQL1 a projděte si kroky 2 – 4.
* Pokud používáte kroky 5ii, přidejte SQL1 jako možného vlastníka pro přidaný prostředek adresy IP
* Otestujte převzetí služeb při selhání.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Využijte stávající sekundární repliky: Multi-Site

Pokud máte uzly ve více než jednom datovém centru Azure (DC) nebo pokud máte hybridní prostředí, můžete použít vždy zapnuté konfigurace v tomto prostředí k minimalizaci prostojů.

Přístup je změnit vždy na synchronizaci synchronní pro místní nebo sekundární Azure DC a pak převzetí služeb při selhání na tento SQL Server. Pak zkopírujte virtuální disponály do účtu úložiště Premium a znovu nasadit počítač do nové cloudové služby. Aktualizujte naslouchací proces a potom navrácení služeb po selhání.

##### <a name="points-of-downtime"></a>Body prostoje

Prostoje se skládá z doby převzetí služeb při selhání na alternativní dc a zpět. Závisí také na konfiguraci klienta/DNS a opětovné připojení klienta může být zpožděno.
Vezměme si následující příklad hybridní konfigurace always on:

![Vícewebů1][9]

##### <a name="advantages"></a>Výhody

* Můžete využít stávající infrastrukturu.
* Máte možnost předinopletovat úložiště Azure na DR Azure DC jako první.
* Úložiště DR Azure DC lze překonfigurovat.
* Během migrace existují minimálně dvě převzetí služeb při selhání, s výjimkou převzetí služeb při selhání testu.
* Není nutné přesunout data serveru SQL Server se zálohováním a obnovením.

##### <a name="disadvantages"></a>Nevýhody

* V závislosti na přístupu klienta k SQL Server může být zvýšena latence při sql server běží v alternativní řadič domény k aplikaci.
* Doba kopírování virtuálních discích do úložiště Premium může být dlouhá. To může ovlivnit vaše rozhodnutí o tom, zda zachovat uzel ve skupině dostupnosti. Zvažte to, když je během migrace spuštěno pracovní zatížení náročné na protokol, protože primární uzel musí zachovat nereplikované transakce v transakčním protokolu. Proto by to mohlo výrazně růst.
* Tento scénář by použít příkaz Azure **Start-AzureStorageBlobCopy,** který je asynchronní. Po dokončení není žádná sla. Čas kopií se liší, zatímco to závisí na čekání ve frontě, závisí také na množství dat k přenosu. Proto máte pouze jeden uzel ve druhém datovém centru, měli byste provést kroky zmírnění v případě, že kopie trvá déle než v testování. Tyto kroky zmírnění zahrnují následující nápady:
  * Přidejte dočasný druhý uzel SQL pro HA před migrací s dohodnutými prostoji.
  * Spusťte migraci mimo naplánovanou údržbu Azure.
  * Ujistěte se, že jste správně nakonfigurovali kvorum clusteru.

Tento scénář předpokládá, že jste zdokumentovali instalaci a věděli, jak je úložiště mapováno, abyste mohli provést změny pro optimální nastavení mezipaměti disku.

##### <a name="high-level-steps"></a>Základní kroky

![Více pracem2][10]

* Vytvořte místní / alternativní Řadič domény Azure primární SQL Server a z něj udělejte jiného partnera pro automatické převzetí služeb při selhání (AFP).
* Shromážděte informace o konfiguraci disku z SQL2 a odeberte uzel (neodstraňujte připojené virtuální disky).
* Vytvořte si účet úložiště Premium a zkopírujte virtuální řadiče z účtu standardního úložiště.
* Vytvořte novou cloudovou službu a vytvořte virtuální modul SQL2 s připojenými disky úložiště Premiums.
* Nakonfigurujte ILB / ELB a přidejte koncové body.
* Aktualizujte vždy na schylení s novou IP adresu ILB / ELB a test převzetí služeb při selhání.
* Zkontrolujte konfiguraci DNS.
* Změňte AFP na SQL2 a pak migrovat SQL1 a projít kroky 2 – 5.
* Otestujte převzetí služeb při selhání.
* Přepnutí AFP zpět na SQL1 a SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrace vícepracového webu vždy v clusteru do úložiště Premium

Zbývající část tohoto článku obsahuje podrobný příklad převodu clusteru always on na více lokalit na úložiště Premium. Také převede naslouchací proces z použití externího nástrojpro vyrovnávání zatížení (ELB) na interní nástroj pro vyrovnávání zatížení (ILB).

### <a name="environment"></a>Prostředí

* Windows 2k12 / SQL 2k12
* 1 DB soubory na SP
* 2 x fondy úložiště na uzel

![Dodatek1][11]

### <a name="vm"></a>Vm:

V tomto příkladu budeme demonstrovat přechod z ELB na ILB. ELB byla k dispozici před ILB, takže to ukazuje, jak přepnout na ILB během migrace.

![Dodatek2][12]

### <a name="pre-steps-connect-to-subscription"></a>Předběžné kroky: Připojení k předplatnému

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Vytvoření nového účtu úložiště a cloudové služby

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zvýšení povolených chyb \<u zdrojů Volitelné>

U určitých prostředků, které patří do skupiny dostupnosti vždy na, existují omezení počtu selhání, ke kterým může v období dojít, kdy se clusterová služba pokusí restartovat skupinu prostředků. Doporučujese zvýšit to, zatímco procházíte tímto postupem, protože pokud nemáte ručně převzetí služeb při selhání a spoušť převzetí služeb při selhání vypnutím počítače se můžete dostat blízko k tomuto limitu.

Bylo by rozumné zdvojnásobit příspěvek na selhání, chcete-li to provést ve Správci clusteru s podporou převzetí služeb při selhání, přejděte na vlastnosti skupiny prostředků Always On:

![Dodatek 3][13]

Změňte maximální počet selhání na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Přidání prostředku adresy \<IP pro volitelnou> skupiny clusterů

Pokud máte pouze jednu adresu IP pro skupinu clusterů a je zarovnána s podsítí cloudu, dejte si pozor, pokud omylem přepnete offline všechny uzly clusteru v cloudu v této síti, pak prostředek IP clusteru a síťový název clusteru nebudou moci přejít do režimu online. V takovém případě zabrání aktualizace jiných prostředků clusteru.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfigurace DNS

Implementace plynulého přechodu závisí na tom, jak je služba DNS využívána a aktualizována.
Při instalaci vždy zapnuto vytvoří skupinu prostředků clusteru Windows, pokud otevřete Správce clusteru s podporou převzetí služeb při selhání, zjistíte, že má minimálně tři prostředky, dva, na které dokument odkazuje, jsou:

* Název virtuální sítě (VNN) – název DNS, ke kterému se klienti připojují, když se chtějí připojit k serverům SQL prostřednictvím always on.
* IP adresa - IP adresa, která je přidružena k VNN, můžete mít více než jednu a v konfiguraci více pracovišti máte IP adresu pro každý web/podsíť.

Při připojování k serveru SQL Server ovladač klienta serveru SQL Server načte záznamy DNS přidružené k naslouchacímu procesu a pokusí se připojit ke každé přidružené adrese IP vždy zapnuté. Dále diskutujeme o některých faktorech, které to mohou ovlivnit.

Počet souběžných záznamů DNS, které jsou přidruženy k názvu posluchače, závisí nejen na počtu přidružených adres IP, ale také na nastavení RegisterAllIpProviders v clusteringu s podporou převzetí služeb při selhání pro prostředek Always ON VNN.

Při nasazení always on v Azure existují různé kroky k vytvoření posluchače a IP adresy, budete muset ručně nakonfigurovat "RegisterAllIpProviders" na 1, to se liší od místní ho nasazení vždy na místě, kde je již nastavena na 1.

Pokud 'RegisterAllIpProviders' je 0, pak se zobrazí pouze jeden záznam DNS v DNS spojené s naslouchací proces:

![Dodatek4][14]

Pokud 'RegisterAllIpProviders' je 1:

![Dodatek 5][15]

Níže uvedený kód vypíše nastavení VNN a nastaví jej za vás. Aby se změna projevila, je třeba převést vnn offline a vrátit ji zpět do režimu online. To přepne naslouchací proces offline způsobuje narušení připojení klienta.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

V pozdějšíkrok migrace, je třeba aktualizovat vždy na naslouchací proces s aktualizovanou IP adresu, která odkazuje na nástroj pro vyrovnávání zatížení, to zahrnuje odebrání a přidání prostředku IP adresy. Po aktualizaci protokolu IP je třeba zajistit, aby byla nová adresa IP aktualizována v zóně DNS a aby klienti aktualizovali místní mezipaměť DNS.

Pokud jsou klienti umístěni v jiném segmentu sítě a odkazují na jiný server DNS, je třeba zvážit, co se stane s přenosem zóny DNS během migrace, protože doba opětovného připojení aplikace je omezena alespoň časem přenosu zóny jakékoli nové IP adresy adresy pro posluchače. Pokud jste v časové omezení zde, měli byste diskutovat a otestovat vynucení přírůstkové zóny přenosu s týmy systému Windows a také umístit záznam hostitele DNS na nižší Čas na live (TTL), aby klienti aktualizovat. Další informace naleznete [v tématu Přírůstkové zónové přenosy](https://technet.microsoft.com/library/cc958973.aspx) a [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Ve výchozím nastavení TTL pro záznam DNS, který je přidružen k naslouchací proces v vždy zapnuto v Azure je 1200 sekund. Můžete chtít snížit, pokud jste pod časovým omezením během migrace zajistit, že klienti aktualizovat své DNS s aktualizovanou IP adresu pro naslouchací proces. Konfiguraci můžete zobrazit a upravit tak, že vynesete konfiguraci VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Čím nižší je 'HostRecordTTL', vyšší množství provozu DNS dochází.

##### <a name="client-application-settings"></a>Nastavení klientské aplikace

Pokud vaše klientská aplikace SQL podporuje sqlclient .NET 4.5, můžete použít klíčové slovo MULTISUBNETFAILOVER=TRUE. Toto klíčové slovo by měla být použita, protože umožňuje rychlejší připojení k SQL Always On Availability Group během převzetí služeb při selhání. Vyjmenovává všechny adresy IP přidružené k naslouchací proces Always On paralelně a provádí agresivnější rychlost opakování připojení TCP během převzetí služeb při selhání.

Další informace o předchozích nastaveních naleznete v [tématu Klíčové slovo MultiSubnetFailover a přidružené funkce](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Viz také [podpora sqlclient pro vysokou dostupnost, zotavení po havárii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: Nastavení kvora clusteru

Vzhledem k tomu, že budete mít alespoň jeden SQL Server dolů najednou, měli byste upravit nastavení kvora clusteru, pokud používáte SVIDentní obrázek sdílení souborů (FSW) se dvěma uzly, měli byste nastavit kvorum povolit většinu uzlu a využít dynamické hlasování , což umožňuje, aby jeden uzel zůstal stát.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Další informace o správě a konfiguraci kvora clusteru naleznete [v tématu Konfigurace a správa kvora v clusteru s podporou převzetí služeb při selhání systému Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Extrahovat existující koncové body a aloky

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Uložte tento text do souboru.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Změna partnerů s podporou převzetí služeb při selhání a režimů replikace

Pokud máte více než dva servery SQL, měli byste změnit převzetí služeb při selhání jiné sekundární v jiném řadiči domény nebo v místním na "Synchronní" a učinit z něj partnera pro automatické převzetí služeb při selhání (AFP), je to tak, že budete udržovat HA při provádění změn. Můžete to udělat prostřednictvím TSQL upravit i když SSMS:

![Dodatek6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Odebrání sekundárního virtuálního virtuálního zařízení z cloudové služby

Měli byste nejprve plánovat migraci sekundárního uzlu v cloudu. Pokud je tento uzel aktuálně primární, měli byste zahájit ruční převzetí služeb při selhání.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Změna nastavení mezipaměti disku v souboru CSV a uložení

Pro svazky dat by měly být nastaveny na POUZE PRO ČTENÍ.

Pro svazky TLOG by měly být nastaveny na NONE.

![Dodatek 7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: Kopírování virtuálních pevných disků

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Stav kopírování virtuálních disponů můžete zkontrolovat na účet úložiště Premium:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Dodatek8][18]

Počkejte, až všechny tyto jsou zaznamenány jako úspěch.

Informace pro jednotlivé objekty BLOB:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Krok 11: Registrace disku operačního systému

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Import sekundární do nové cloudové služby

Níže uvedený kód také používá přidanou možnost zde můžete importovat stroj a použít zachycovatelný VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: Vytvoření ILB na novém cloudu Svc, přidání koncových bodů s vyrovnáváním zatížení a aklů

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Krok 14: Aktualizace je stále zapnutá

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Dodatek9][19]

Nyní odeberte starou ip adresu cloudové služby.

![Dodatek 10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: Kontrola aktualizací DNS

Nyní byste měli zkontrolovat servery DNS v klientských sítích serveru SQL Server a ujistit se, že clustering přidal další záznam hostitele pro přidanou adresu IP. Pokud se tyto servery DNS neaktualizovaly, zvažte vynucení přenosu zóny DNS a ujistěte se, že klienti v této podsíti jsou schopni vyřešit obě adresy ALWAYS On IP, je to proto, že nemusíte čekat na automatickou replikaci DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: Překonfigurovat vždy zapnutý

V tomto okamžiku čekáte na sekundární uzel, který byl migrován plně znovu synchronizovat s místním uzlem a přepnout do uzlu synchronní replikace a učinit z něj AFP.  

#### <a name="step-17-migrate-second-node"></a>Krok 17: Migrace druhého uzlu

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Změna nastavení ukládání disku do mezipaměti v souboru CSV a uložení

U datových svazků by nastavení mezipaměti měla být nastavena na hodnotu READONLY.

U svazků TLOG by nastavení mezipaměti mělo být nastaveno na HODNOTU NONE.

![Dodatek11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Vytvoření nového účtu nezávislého úložiště pro sekundární uzel

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Krok 20: Kopírování virtuálních pevných disků

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Stav kopírování v pevném rozlišení můžete zkontrolovat pro všechny virtuální disky:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Dodatek 12][22]

Počkejte, až všechny tyto jsou zaznamenány jako úspěch.

Informace pro jednotlivé objekty BLOB:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Krok 21: Registrace disku operačního systému

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Přidání koncových bodů a aklů s vyrovnáváním zatížení

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Krok 23: Test převzetí služeb při selhání

Počkejte, až se migrovaný uzel synchronizuje s místním uzly Always On. Umístěte jej do režimu synchronní replikace a počkejte, až bude synchronizován. Pak převzetí služeb při selhání z místního do prvního uzlu migrované, což je AFP. Jakmile to funguje, změňte poslední migrovaný uzel na AFP.

Měli byste otestovat převzetí služeb při selhání mezi všemi uzly a spustit testy chaosu, abyste zajistili, že převzetí služeb při selhání funguje podle očekávání a v včasném panství.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Vrácení nastavení kvora clusteru / DNS TTL / Failover Pntrs / Nastavení synchronizace

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Přidání prostředku adresy IP do stejné podsítě

Pokud máte pouze dva servery SQL a chcete je migrovat do nové cloudové služby, ale chcete je zachovat ve stejné podsíti, můžete se vyhnout tomu, abyste posluchače přeřadili do offline, abyste odstranili původní adresu ALWAYS On IP a přidali novou adresu IP. Pokud migrujete virtuální chod do jiné podsítě, není nutné to provést, protože existuje další síť clusteru, která odkazuje na tuto podsíť.

Po vyvolání migrované sekundární a přidal i v nové IP adresu prostředku pro novou cloudovou službu před převzetí služeb při selhání existující primární, měli byste provést tyto kroky v rámci správce převzetí služeb při selhání clusteru:

Chcete-li přidat adresu IP, naleznete v dodatku, krok 14.

1. Pro aktuální prostředek IP adresy změňte možného vlastníka na Existující primární SQL Server v příkladu "dansqlams4":

    ![Dodatek 13][23]
2. U nového prostředku adresy IP změňte možného vlastníka na "Migrovaný sekundární SQL Server" v příkladu dansqlams5:

    ![Dodatek 14][24]
3. Jakmile je tato možnost nastavena, můžete převzetí služeb při selhání a při migraci posledního uzlu je třeba upravit možné vlastníky tak, aby uzel byl přidán jako možný vlastník:

    ![Dodatek 15][25]

## <a name="additional-resources"></a>Další zdroje

* [Úložiště Azure Premium](../disks-types.md)
* [Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server ve virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
