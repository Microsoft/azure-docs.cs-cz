---
title: Použití Azure Premium Storage s SQL Server | Microsoft Docs
description: Tento článek používá prostředky vytvořené pomocí modelu nasazení Classic a poskytuje pokyny k používání služby Azure Premium Storage s SQL Server běžícími na Azure Virtual Machines.
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
ms.openlocfilehash: ca11fce252192cbf8e5f0bc2cfb5fcd38f5d4443
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84020876"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Použití Azure Premium Storage s SQL Serverem na virtuálních počítačích

## <a name="overview"></a>Přehled

[Azure Premium SSD](../disks-types.md) je novou generací úložiště, která poskytuje nízkou latenci a vysokou propustnost v/v. Funguje nejlépe pro úlohy náročné na vstupně-výstupní operace, například SQL Server v [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek popisuje plánování a pokyny k migraci virtuálního počítače se spuštěným SQL Server pro použití Premium Storage. To zahrnuje postupy infrastruktury Azure (sítě, úložiště) a hostovaného virtuálního počítače s Windows. V příkladu v [příloze](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) se zobrazuje kompletní ucelená migrace, jak přesunout větší virtuální počítače, aby bylo možné využít vylepšené místní úložiště SSD pomocí PowerShellu.

Je důležité pochopit, jak se v rámci procesu Azure Premium Storage využíváme SQL Server na virtuálních počítačích s IAAS. Sem patří:

* Identifikace požadavků, které se mají použít Premium Storage.
* Příklady nasazení SQL Server v IaaS pro Premium Storage pro nová nasazení.
* Příklady migrace stávajících nasazení, samostatné servery a nasazení s využitím skupin dostupnosti Always On SQL serveru.
* Možné přístupy k migraci.
* Úplný kompletní příklad ukazující kroky pro Azure, Windows a SQL Server pro migraci existující integrované implementace Always On.

Další informace o SQL Server ve službě Azure Virtual Machines najdete v tématu [SQL Server v azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Autor:** **Techničtí kontroloři** Daniel Sol: Luis Carlos Vargas sleď, Sanjay Mishra, Pravin Mital, Juergen Tomáš, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Předpoklady pro Premium Storage

Existuje několik požadavků na použití Premium Storage.

### <a name="machine-size"></a>Velikost počítače

Pro použití Premium Storage musíte použít Virtual Machines DS (VM Series). Pokud jste v cloudové službě předtím nepoužívali počítače řady DS, musíte odstranit existující virtuální počítač, zachovat připojené disky a pak vytvořit novou cloudovou službu před tím, než znovu vytvoříte virtuální počítač jako velikost role DS *. Další informace o velikostech virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů a cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Cloud Services

Při vytváření v nové cloudové službě můžete použít jenom virtuální počítače DS * s Premium Storage. Pokud používáte službu SQL Server Always On v Azure, služba Always On Listener odkazuje na interní nebo Load Balancer externí IP adresu Azure, která je přidružená ke cloudové službě. Tento článek se zaměřuje na migraci a zachování dostupnosti v tomto scénáři.

> [!NOTE]
> Řada DS * musí být prvním virtuálním počítačem, který je nasazený do nové cloudové služby.
>
>

### <a name="regional-vnets"></a>Oblastní virtuální sítě

Pro virtuální počítače DS * musíte nakonfigurovat Virtual Network (VNET) hostující vaše virtuální počítače, aby byly regionální. Tato "rozšiřující" virtuální síť umožňuje zřídit větší virtuální počítače v jiných clusterech a umožňovat komunikaci mezi nimi. Na následujícím snímku obrazovky se zvýrazněné umístění zobrazuje jako oblastní virtuální sítě, zatímco první výsledek zobrazuje "úzký" virtuální síť.

![RegionalVNET][1]

Můžete vyvolat lístek podpory Microsoftu pro migraci na oblastní virtuální síť. Microsoft pak provede změnu. Pokud chcete dokončit migraci do regionálního virtuální sítě, změňte vlastnost AffinityGroup v konfiguraci sítě. Napřed exportujte konfiguraci sítě v PowerShellu a pak v elementu **VirtualNetworkSite** nahraďte vlastnost **AffinityGroup** vlastností **Location** . Určete `Location = XXXX` , kde `XXXX` je oblast Azure. Pak importujte novou konfiguraci.

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

Pokud ho chcete přesunout do oblastní virtuální sítě v Západní Evropa, změňte konfiguraci na následující:
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

Musíte vytvořit nový účet úložiště, který je nakonfigurovaný pro Premium Storage. Všimněte si, že použití Premium Storage je nastavené na účet úložiště, ne na jednotlivé virtuální pevné disky, ale při použití virtuálního počítače DS * Series můžete z účtů úložiště úrovně Premium a Standard připojit virtuální pevný disk. Můžete to vzít v úvahu, pokud nechcete umístit virtuální pevný disk operačního systému na účet Premium Storage.

Následující příkaz **New-AzureStorageAccountPowerShell** s **typem** "Premium_LRS" vytvoří účet Premium Storage:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Nastavení mezipaměti VHD

Hlavním rozdílem mezi vytvářením disků, které jsou součástí Premium Storage účtu, je nastavení diskové mezipaměti. U SQL Serverch disků s datovým svazkem se doporučuje používat**ukládání do mezipaměti pro čtení**. U svazků protokolu transakcí by mělo být nastavení mezipaměti disku nastaveno na**hodnotu žádné**. To se liší od doporučení pro účty úložiště úrovně Standard.

Po připojení virtuálních pevných disků se nastavení mezipaměti nedá změnit. Je nutné odpojit a znovu připojit virtuální pevný disk s aktualizovaným nastavením mezipaměti.

### <a name="windows-storage-spaces"></a>Prostory úložiště Windows

Můžete použít [prostory úložiště Windows](https://technet.microsoft.com/library/hh831739.aspx) stejně jako u předchozích standardních úložišť. to vám umožní migrovat virtuální počítač, který už využívá prostory úložiště. Příklad v [příloze](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 a přeposlání) ukazuje kód PowerShellu k extrakci a importu virtuálního počítače s několika připojenými virtuálními pevnými disky.

Fondy úložiště se používaly se standardním účtem služby Azure Storage ke zvýšení propustnosti a snížení latence. Můžete najít hodnotu v testování fondů úložiště pomocí Premium Storage pro nová nasazení, ale přidávají další složitost s nastavením úložiště.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak zjistit, které virtuální disky Azure se mapují na fondy úložiště

V případě, že jsou k připojeným virtuálním pevným diskům k dispozici různá nastavení mezipaměti, můžete se rozhodnout zkopírovat virtuální pevné disky na účet Premium Storage. Když je však znovu připojíte k novému virtuálnímu počítači DS Series, může být nutné změnit nastavení mezipaměti. Je jednodušší použít Premium Storage doporučená nastavení mezipaměti, když máte samostatné virtuální pevné disky pro datové soubory SQL a soubory protokolu (nikoli jeden VHD, který obsahuje obojí).

> [!NOTE]
> Pokud máte SQL Server dat a souborů protokolů na stejném svazku, možnost ukládání do mezipaměti závisí na vzorech přístupu v/v pro vaše databázové úlohy. Pouze testování může ukázat, která možnost ukládání do mezipaměti je pro tento scénář nejvhodnější.
>
>

Pokud ale používáte prostory úložiště Windows, které jsou tvořené několika virtuálními pevnými disky, potřebujete se podívat na původní skripty a zjistit, které připojené virtuální pevné disky jsou v konkrétním fondu, takže můžete nastavení mezipaměti nastavit pro každý disk odpovídajícím způsobem.

Pokud nemáte k dispozici původní skript, který vám ukáže, které virtuální pevné disky se mapují na fond úložiště, můžete pomocí následujících kroků určit mapování disku nebo fondu úložiště.

Pro každý disk použijte následující postup:

1. Získat seznam disků připojených k virtuálnímu počítači pomocí příkazu **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Poznamenejte si jednotky pevného disku a LUN.

    ![DisknameAndLUN][2]
1. Vzdálená plocha do virtuálního počítače. Pak přejdete do **správy počítače**  |  **Device Manager**  |  **diskové jednotky**. Podívejte se na vlastnosti každého z virtuálních disků Microsoft.

    ![VirtualDiskProperties][3]
1. Číslo logické jednotky (LUN): tady je odkaz na číslo logické jednotky, kterou zadáte při připojování virtuálního pevného disku k virtuálnímu počítači.
1. Pro virtuální disk Microsoft přejdete na kartu **Podrobnosti** a pak v seznamu **vlastností** přejdete na **klíč ovladače**. V **hodnotě**si všimněte **posunu**, který je 0,002 na následujícím snímku obrazovky. 0,002 označuje PhysicalDisk2, na které fond úložiště odkazuje.

    ![VirtualDiskPropertyDetails][4]
1. Pro každý fond úložiště vypsat přidružené disky:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Teď můžete tyto informace použít k přidružení připojených virtuálních pevných disků k fyzickým diskům ve fondech úložiště.

Jakmile namapujete virtuální pevné disky na fyzické disky ve fondech úložiště, můžete je pak odpojit a zkopírovat na účet Premium Storage a pak je připojit ke správnému nastavení mezipaměti. Podívejte se na příklad v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroky 8 až 12. Tyto kroky ukazují, jak extrahovat konfiguraci disku VHD připojeného k virtuálnímu počítači do souboru CSV, zkopírovat virtuální pevné disky, změnit nastavení mezipaměti konfigurace disku a nakonec znovu nasadit virtuální počítač jako virtuální počítač řady DS se všemi připojenými disky.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Propustnost úložiště virtuálních počítačů a propustnost úložiště VHD

Velikost výkonu úložiště závisí na zadané velikosti virtuálního počítače DS * a velikosti VHD. Virtuální počítače mají různé odměny za počet virtuálních pevných disků, které se dají připojit, a maximální šířku pásma, kterou podporují (MB/s). Konkrétní čísla šířky pásma najdete v tématu [velikosti virtuálních počítačů a cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zvýšení IOPS se dosáhne větší velikosti disků. Měli byste to vzít v úvahu, až si myslíte o cestě migrace. Podrobnosti najdete v [tabulce pro vstupně-výstupní operace a typy disků](../disks-types.md#premium-ssd).

Nakonec Vezměte v úvahu, že virtuální počítače mají různou maximální šířku pásma, kterou podporují pro všechny připojené disky. Při vysokém zatížení byste mohli zvýšit maximální šířku pásma dostupnou pro danou velikost role virtuálního počítače. Například Standard_DS14 podporuje až 512 MB/s. Proto se třemi disky P30 byste mohli navýšit šířku pásma virtuálního počítače na disku. V tomto příkladu by se ale překročil limit propustnosti v závislosti na kombinaci se systémem IOs pro čtení a zápis.

## <a name="new-deployments"></a>Nová nasazení

Následující dvě části ukazují, jak můžete nasadit SQL Server virtuálních počítačů do Premium Storage. Jak už bylo zmíněno dříve, nemusíte nutně umístit disk s operačním systémem do služby Premium Storage. To se může stát, pokud hodláte na virtuální pevný disk s operačním systémem umístit jakékoli náročné vstupně-výstupní úlohy.

První příklad ukazuje použití existujících imagí Galerie Azure. Druhý příklad ukazuje, jak použít vlastní image virtuálního počítače, kterou máte v existujícím účtu úložiště Standard.

> [!NOTE]
> V těchto příkladech se předpokládá, že jste už vytvořili regionální virtuální síť.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Vytvoření nového virtuálního počítače s využitím Premium Storage s imagí galerie

Následující příklad ukazuje, jak umístit virtuální pevný disk s operačním systémem do úložiště Premium a připojit Premium Storage VHD. Disk s operačním systémem ale taky můžete umístit do účtu úložiště úrovně Standard a pak připojit virtuální pevné disky, které se nacházejí v účtu Premium Storage. Oba scénáře jsou znázorněny.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: vytvoření účtu Premium Storage

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: vytvoření nové cloudové služby

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: rezervování virtuální IP adresy cloudové služby (volitelné)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Krok 4: vytvoření kontejneru virtuálních počítačů

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: umístění virtuálního pevného disku s operačním systémem na Standard nebo Premium Storage

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Krok 6: Vytvoření virtuálního počítače

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
#Note the size specified '-DiskSizeInGB 1023', this attaches 2 x P30 Premium Storage Disk Type
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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Vytvoření nového virtuálního počítače pro použití Premium Storage s vlastní imagí

Tento scénář ukazuje, kde máte existující vlastní image, které se nacházejí v účtu úložiště úrovně Standard. Jak bylo zmíněno, pokud chcete umístit virtuální pevný disk s operačním systémem na Premium Storage musíte zkopírovat image, která existuje v účtu úložiště Standard, a přenést je do Premium Storage, aby ji bylo možné použít. Pokud máte místní image, můžete tuto metodu použít také ke zkopírování přímo na účet Premium Storage.

#### <a name="step-1-create-storage-account"></a>Krok 1: vytvoření účtu úložiště

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

#### <a name="step-3-use-existing-image"></a>Krok 3: použití existující image

Můžete použít existující bitovou kopii. Případně můžete [získat obrázek existujícího počítače](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si, že počítač, na který jste image, nemusí být DS * Machine. Až budete mít image, následující kroky ukazují, jak je zkopírovat do účtu Premium Storage pomocí rutiny **Start-AzureStorageBlobCopy** prostředí PowerShell rutiny.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: kopírování objektu BLOB mezi účty úložiště

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: pravidelné kontroly stavu kopírování:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Přidání disku s obrázkem do úložiště disků Azure v předplatném

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Může se stát, že i když zpráva o stavu bude úspěšná, může se i přesto zobrazit chyba zapůjčení disku. V takovém případě počkejte asi 10 minut.

#### <a name="step-7--build-the-vm"></a>Krok 7: Sestavte virtuální počítač

Tady vytváříte virtuální počítač z image a připojujete dva Premium Storage VHD:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Existující nasazení, která nepoužívají skupiny dostupnosti Always On

> [!NOTE]
> U stávajících nasazení si nejdřív přečtěte část [požadavky](#prerequisites-for-premium-storage) tohoto článku.

K dispozici jsou různé požadavky na nasazení SQL Server, které nepoužívají skupiny dostupnosti Always On a ty, které dělají. Pokud nepoužíváte Always On a máte stávající samostatné SQL Server, můžete upgradovat na Premium Storage pomocí nové cloudové služby a účtu úložiště. Vezměte v úvahu následující možnosti:

* **Vytvořte nový virtuální počítač SQL Server**. Můžete vytvořit nový virtuální počítač SQL Server, který používá Premium Storage účet, jak je popsáno v nových nasazeních. Pak zálohujte a obnovte konfiguraci SQL Server a uživatelských databází. Aplikaci je třeba aktualizovat, aby odkazovala na nové SQL Server, pokud se k ní přistupuje interně nebo externě. Museli byste zkopírovat všechny objekty ' out z databáze ', jako kdyby jste prováděli souběžnou migraci SQL Server migrace. To zahrnuje objekty, jako jsou přihlašovací údaje, certifikáty a propojené servery.
* **Migrujte existující virtuální počítač SQL Server**. To vyžaduje přepnutí SQL Server virtuálního počítače do režimu offline a jeho převádění do nové cloudové služby, která zahrnuje kopírování všech připojených virtuálních pevných disků do Premium Storage účtu. Když je virtuální počítač online, aplikace odkazuje na název hostitele serveru jako předtím. Počítejte s tím, že velikost stávajícího disku má vliv na výkonnostní charakteristiky. Například disk 400 GB se zaokrouhluje na P20. Pokud víte, že nepotřebujete výkon disku, pak můžete virtuální počítač znovu vytvořit jako virtuální počítač řady DS a připojit Premium Storage VHD požadované specifikace velikosti a výkonu. Pak můžete soubory databáze SQL odpojit a znovu připojit.

> [!NOTE]
> Při kopírování disků VHD byste měli znát velikost, a to v závislosti na velikosti znamená to, jaký Premium Storage typ disku patří do. tím se určuje specifikace výkonu disku. Azure zaokrouhlí na nejbližší velikost disku, takže pokud máte disk s 400 GB, zaokrouhlí se na P20. V závislosti na vašich stávajících vstupně-výstupních požadavcích na virtuální pevný disk operačního systému je možné, že ho nebudete muset migrovat na účet Premium Storage.

Pokud je k SQL Server přistup externě, pak se virtuální IP adresa cloudové služby změní. Také je potřeba aktualizovat koncové body, seznamy ACL a nastavení DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Existující nasazení, která používají skupiny dostupnosti Always On

> [!NOTE]
> U stávajících nasazení si nejdřív přečtěte část [požadavky](#prerequisites-for-premium-storage) tohoto článku.

Zpočátku v této části se podíváme na to, jak vždycky interaktivně komunikuje s Azure Networking. Migrace se pak dělí do dvou scénářů: migrace, u kterých je možné nějaké výpadky tolerovat, a migrace, u kterých je potřeba dosáhnout minimálního výpadku.

Místní SQL Server skupiny dostupnosti Always On používají místní naslouchací proces, který zaregistruje virtuální název DNS spolu s IP adresou, která je sdílená mezi jedním nebo více servery SQL. Pokud se klienti připojují, jsou směrováni přes IP adresu naslouchacího procesu na primární SQL Server. Jedná se o server, který je v daném okamžiku vlastníkem prostředku trvalého protokolu IP.

![DeploymentsUseAlways na][6]

V Microsoft Azure může být na virtuálním počítači přiřazená jenom jedna IP adresa, takže pokud chcete dosáhnout stejné vrstvy abstrakce jako v místním prostředí, Azure použije IP adresu, která je přiřazená interním/externím nástrojům pro vyrovnávání zatížení (interního nástroje/ELB). Prostředek IP, který je sdílen mezi servery, je nastaven na stejnou IP adresu jako interního nástroje/ELB. Tato databáze je publikována v DNS a přenos klientů se předává prostřednictvím interního nástroje/ELB do primární repliky SQL Server. INTERNÍHO nástroje/ELB ví, který SQL Server je primární, protože používá sondy k testování prostředku trvalé IP adresy. V předchozím příkladu se testuje každý uzel, který má koncový bod, na který odkazuje ELB/interního nástroje, podle toho, která odpověď je primární SQL Server.

> [!NOTE]
> INTERNÍHO nástroje a ELB se přiřazují ke konkrétní cloudové službě Azure, takže jakákoli migrace do cloudu v Azure pravděpodobně znamená, že se změní Load Balancer IP adresa.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrace vždy na nasazení, která můžou způsobit nějaké výpadky

Existují dvě strategie migrace vždy v nasazeních, která umožňují určité výpadky:

1. **Přidat další sekundární repliky do existujícího clusteru Always On**
2. **Migrace na nový cluster Always On**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Přidání dalších sekundárních replik do existujícího clusteru Always On

Jednou z strategií je přidání dalších sekundárních skupin do skupiny dostupnosti Always On. Je nutné je přidat do nové cloudové služby a aktualizovat naslouchací proces pomocí nové IP adresy nástroje pro vyrovnávání zatížení.

##### <a name="points-of-downtime"></a>Body prostojů:

* Ověření clusteru.
* Testování vždy u převzetí služeb při selhání pro nové sekundární počítače.

Pokud na virtuálním počítači používáte fondy úložiště Windows pro vyšší propustnost vstupně-výstupních operací, tyto funkce jsou během úplného ověření clusteru offline. Ověřovací test je vyžadován při přidávání uzlů do clusteru. Čas potřebný ke spuštění testu se může lišit, takže byste ho měli otestovat v reprezentativním testovacím prostředí a získat tak přibližnou dobu trvání.

Měli byste zřídit čas, ve kterém můžete na nově přidaných uzlech provádět ruční a chaos testování, abyste měli jistotu, že funkce vysoké dostupnosti budou vždycky fungovat podle očekávání.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Před spuštěním ověřování byste měli zastavit všechny instance SQL Server, kde se používají fondy úložiště.
>
> ##### <a name="high-level-steps"></a>Základní kroky
>

1. V nové cloudové službě vytvořte dva nové servery SQL s připojenými Premium Storage.
2. Kopírování přes úplné zálohování a obnovení pomocí **NORECOVERY**.
3. Kopírovat přes "objekty závislé na uživateli", například přihlašovací údaje atd.
4. Vytvořte novou interní Load Balancer (interního nástroje) nebo použijte externí Load Balancer (ELB) a pak nastavte koncové body s vyrovnáváním zatížení na obou nových uzlech.

   > [!NOTE]
   > Před pokračováním ověřte, že všechny uzly mají správnou konfiguraci koncového bodu.
   >
   >
5. Zastavte přístup uživatele nebo aplikace k SQL Server (Pokud používáte fondy úložišť).
6. Zastavte služby SQL Server Engine na všech uzlech (Pokud používáte fondy úložišť).
7. Přidejte nové uzly do clusteru a spusťte úplné ověřování.
8. Po úspěšném ověření spusťte všechny SQL Server služby.
9. Zálohování protokolů transakcí a obnovení databází uživatelů.
10. Přidejte nové uzly do skupiny dostupnosti Always On a replikaci umístěte do **synchronního**umístění.
11. Přidejte prostředek IP adresy nové cloudové služby interního nástroje/ELB prostřednictvím PowerShellu, aby se Always vycházela z příkladu s více lokalitami v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). V části clustering systému Windows nastavte **možné vlastníky** prostředku **IP adresy** na nové uzly staré. Viz část Přidání prostředku IP adresy do stejné podsítě v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Převzetí služeb při selhání jednoho z nových uzlů.
13. Nastavte nové uzly na automatické převzetí služeb při selhání a testovací převzetí služeb při selhání.
14. Odeberte původní uzly ze skupiny dostupnosti.

##### <a name="advantages"></a>Výhody

* Nové servery SQL je možné testovat (SQL Server a aplikace) před jejich přidáním na Always On.
* Velikost virtuálního počítače můžete změnit a přizpůsobit úložiště podle svých požadavků. Je ale vhodné zachovat všechny cesty souborů SQL stejné.
* Můžete určit, kdy se má spustit přenos záloh databáze do sekundárních replik. To se liší od použití Azure **Start-AzureStorageBlobCopy** rutiny ke kopírování VHD, protože se jedná o asynchronní kopii.

##### <a name="disadvantages"></a>Nevýhody

* Při použití fondů úložiště Windows dojde během úplného ověřování clusteru pro nové další uzly k výpadkům clusteru.
* V závislosti na verzi SQL Server a stávajícím počtu sekundárních replik se možná nebudete moct přidat další sekundární repliky, aniž byste museli odebírat stávající sekundární.
* Při nastavování sekundárních dat může být dlouhý čas přenosu dat SQL.
* V průběhu migrace se účtují další náklady, zatímco jsou spuštěné nové počítače paralelně.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrace na nový cluster Always On

Další strategií je vytvořit úplně novou značku clusteru s novými uzly v nové cloudové službě a potom klienty přesměrovat na použití.

##### <a name="points-of-downtime"></a>Body prostojů

Při přenosu aplikací a uživatelů do nového naslouchacího procesu Always On dojde k výpadkům. Výpadek závisí na:

* Doba potřebná k obnovení záloh protokolu finální transakce do databází na nových serverech.
* Čas potřebný k aktualizaci klientských aplikací na použití nového naslouchacího procesu Always On

##### <a name="advantages"></a>Výhody

* Můžete testovat skutečné provozní prostředí, SQL Server a změny sestavení operačního systému.
* Máte možnost přizpůsobit úložiště a potenciálně snížit velikost virtuálního počítače. Výsledkem může být snížení nákladů.
* Během tohoto procesu můžete aktualizovat SQL Server Build nebo verzi. Můžete také upgradovat operační systém.
* Předchozí cluster Always On může fungovat jako cíl pevného vrácení zpět.

##### <a name="disadvantages"></a>Nevýhody

* Je potřeba změnit název DNS naslouchacího procesu, pokud chcete, aby současně běžely současně současně v clusterech. To přináší režijní náklady na správu během migrace jako řetězce klientské aplikace musí odrážet název nového naslouchacího procesu.
* Před migrací musíte implementovat mechanismus synchronizace mezi těmito dvěma prostředími, aby bylo možné co nejblíže minimalizovat požadavky na konečnou synchronizaci.
* Během migrace se za běhu přidávají náklady, i když je spuštěno nové prostředí.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrace vždy na nasazení za účelem minimálního výpadku

Existují dvě strategie migrace vždy na nasazení za účelem minimálního výpadku:

1. **Použít existující sekundární: jeden server**
2. **Využít existující sekundární repliky: více lokalit**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Využijte existující sekundární: jeden web.

Jednou z strategií pro minimální prostoje je převzít existující sekundární Cloud a odebrat ho z aktuální cloudové služby. Pak zkopírujte virtuální pevné disky do nového účtu Premium Storage a vytvořte virtuální počítač v nové cloudové službě. Pak aktualizujte naslouchací proces při clusteringu a převzetí služeb při selhání.

##### <a name="points-of-downtime"></a>Body prostojů

* Když aktualizujete poslední uzel s koncovým bodem s vyrovnáváním zatížení, dojde k výpadkům.
* Opětovné připojení klienta se může zpozdit v závislosti na konfiguraci klienta nebo DNS.
* Pokud se rozhodnete, že chcete oddělit IP adresy, musíte se rozhodnout, že se má skupina clusteru Always On přepnout do režimu offline. K tomu se můžete vyhnout použitím závislosti nebo a možných vlastníků pro přidaný prostředek IP adresy. Viz část Přidání prostředku IP adresy do stejné podsítě v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Pokud chcete, aby byl přidaný uzel partake jako vždy na partnerském serveru pro převzetí služeb při selhání, musíte přidat Koncový bod Azure s odkazem na skupinu s vyrovnáváním zatížení. Když spustíte příkaz **Add-AzureEndpoint** , zůstanou současná připojení otevřená, ale nová připojení k naslouchacímu procesu se nebudou moct navázat, dokud se nástroj pro vyrovnávání zatížení neaktualizuje. V testování se jednalo o posledních 90 – 120seconds. to by mělo být testováno.

##### <a name="advantages"></a>Výhody

* Během migrace se neúčtují žádné další náklady.
* Migrace 1:1.
* Snížení složitosti.
* Umožňuje zvýšit IOPS z Premium Storage SKU. Když se disky odpojí z virtuálního počítače a zkopírují se do nové cloudové služby, můžete použít nástroj třetí strany k zvýšení velikosti virtuálního pevného disku, která poskytuje vyšší propustnost. Pro zvýšení velikosti VHD si přečtěte tuto [diskuzi o fórech](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nevýhody

* Během migrace dojde k dočasné ztrátě HA a zotavení po havárii.
* Vzhledem k tomu, že je to 1:1 migrace, musíte použít minimální velikost virtuálního počítače, která podporuje váš počet virtuálních pevných disků, takže možná nebudete moct klidnějších virtuální počítače.
* Tento scénář by používal Azure **Start-AzureStorageBlobCopy** rutiny, který je asynchronní. Při dokončování kopírování neexistuje žádná smlouva SLA. Doba kopírování se liší, zatímco to závisí na čekání ve frontě závisí také na množství dat, která se mají přenést. Čas kopírování se zvyšuje, pokud přenos probíhá na jiné datové centrum Azure, které podporuje Premium Storage v jiné oblasti. Pokud máte jenom dva uzly, zvažte možné zmírnění pro případ, že kopírování trvá déle než při testování. To může zahrnovat následující nápady.
  * Před migrací s dohodnutým prostojem přidejte dočasný SQL Server uzel pro HA.
  * Spusťte migraci mimo plánovanou údržbu Azure.
  * Ujistěte se, že jste správně nakonfigurovali kvorum clusteru.  

##### <a name="high-level-steps"></a>Základní kroky

Tento dokument neukazuje kompletní příklad úplného ukončení, ale v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) najdete podrobnosti, které je možné využít k tomu.

![MinimalDowntime][8]

* Shromážděte konfiguraci disku a odeberte uzel (neodstraňujte připojené virtuální pevné disky).
* Vytvoření účtu Premium Storage a zkopírování virtuálních pevných disků ze standardního účtu úložiště
* Vytvořte novou cloudovou službu a znovu nasaďte virtuální počítač SQL2 do této cloudové služby. Vytvořte virtuální počítač pomocí zkopírovaného původního virtuálního pevného disku s operačním systémem a připojte zkopírované virtuální pevné disky.
* Nakonfigurujte interního nástroje/ELB a přidejte koncové body.
* Aktualizujte naslouchací proces podle těchto:
  * Převzetí skupiny Always On v režimu offline a aktualizace naslouchacího procesu Always On s novou IP adresou interního nástroje/ELB.
  * Nebo přidejte prostředek IP adresy nové cloudové služby interního nástroje/ELB prostřednictvím PowerShellu do clusteringu Windows. Potom nastavte možné vlastníky prostředku IP adresy na migrovaný uzel, SQL2 a nastavte tuto hodnotu jako závislost v názvu sítě. Viz část Přidání prostředku IP adresy do stejné podsítě v [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Ověřte konfiguraci nebo šíření DNS do klientů.
* Migrujte virtuální počítač SQL1 a Projděte kroky 2 – 4.
* Pokud používáte kroky 5ii, přidejte SQL1 jako možného vlastníka prostředku přidané IP adresy.
* Testovací převzetí služeb při selhání.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Využijte existující sekundární repliky: Multi-Site

Pokud máte uzly ve více než jednom datacentru Azure (DC) nebo máte hybridní prostředí, můžete k minimalizaci výpadků použít konfiguraci Always On v tomto prostředí.

Je potřeba změnit synchronizaci Always On na synchronní pro místní nebo sekundární řadič domény Azure a pak převzít služby při selhání z tohoto SQL Server. Pak zkopírujte virtuální pevné disky do účtu Premium Storage a znovu ho nasaďte do nové cloudové služby. Aktualizujte naslouchací proces a pak navrácení služeb po obnovení.

##### <a name="points-of-downtime"></a>Body prostojů

Výpadek se skládá z doby převzetí služeb při selhání alternativního řadiče domény a zpět. Záleží taky na konfiguraci klienta nebo DNS a může dojít ke zpoždění opětovného připojení klienta.
Vezměte v úvahu následující příklad hybridní konfigurace Always On:

![MultiSite1][9]

##### <a name="advantages"></a>Výhody

* Můžete využít stávající infrastrukturu.
* Nejdřív máte možnost předem upgradovat službu Azure Storage na řadiči pro zotavení po havárii Azure.
* Je možné překonfigurovat úložiště s ŘADIČEm zotavení po havárii Azure.
* Během migrace nastane minimálně dvě převzetí služeb při selhání bez testovacího převzetí služeb při selhání.
* Nemusíte přesouvat SQL Server data pomocí zálohování a obnovení.

##### <a name="disadvantages"></a>Nevýhody

* V závislosti na přístupu klienta k SQL Server může dojít ke zvýšené latenci při SQL Server spuštění v alternativním řadiči domény pro aplikaci.
* Doba kopírování VHD do Premium Storage by mohla být dlouhá. To může mít vliv na vaše rozhodnutí o tom, jestli se má uzel ve skupině dostupnosti zachovat. Vezměte v úvahu, pokud jsou při migraci vyžadovány pracovní zatížení náročné na protokol, protože primární uzel musí uchovávat nereplikované transakce v transakčním protokolu. Proto to může výrazně růst.
* Tento scénář by používal Azure **Start-AzureStorageBlobCopy** rutiny, který je asynchronní. Neexistuje žádná smlouva SLA k dokončení. Čas kopírování se liší, zatímco tato doba závisí na čekání ve frontě, závisí taky na množství dat, která se mají přenést. Proto máte jenom jeden uzel ve svém druhé datovém centru, měli byste vzít v úvahu kroky zmírnění v případě, že kopie trvá déle než při testování. Tyto kroky pro zmírnění rizik zahrnují následující nápady:
  * Před migrací s dohodnutým prostojem přidejte dočasný uzel SQL pro HA.
  * Spusťte migraci mimo plánovanou údržbu Azure.
  * Ujistěte se, že jste správně nakonfigurovali kvorum clusteru.

V tomto scénáři se předpokládá, že jste si nastavili instalaci a víte, jak je úložiště namapované, aby bylo možné provádět změny nastavení optimální mezipaměti disku.

##### <a name="high-level-steps"></a>Základní kroky

![Multisite2][10]

* Místní nebo alternativní řadič domény Azure nastavte na primární SQL Server a nastavte ho jako jiného partnera automatického převzetí služeb při selhání (AFP).
* Shromážděte informace o konfiguraci disku z SQL2 a odeberte uzel (neodstraňujte připojené virtuální pevné disky).
* Vytvořte účet Premium Storage a zkopírujte virtuální pevné disky ze standardního účtu úložiště.
* Vytvořte novou cloudovou službu a vytvořte virtuální počítač SQL2 s připojenými disky úložiště Premium.
* Nakonfigurujte interního nástroje/ELB a přidejte koncové body.
* Aktualizujte naslouchací proces Always On novou IP adresou interního nástroje/ELB a testovací převzetí služeb při selhání.
* Ověřte konfiguraci DNS.
* Změňte protokol AFP na SQL2 a pak migrujte SQL1 a Projděte kroky 2 – 5.
* Testovací převzetí služeb při selhání.
* Přepnout protokol AFP zpátky na SQL1 a SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Příloha: migrace clusteru s více lokalitami vždy na Premium Storage

Zbývající část tohoto článku poskytuje podrobný příklad převodu clusteru s více lokalitami na Premium Storage. Také převede naslouchací proces z použití externího nástroje pro vyrovnávání zatížení (ELB) do interního nástroje pro vyrovnávání zatížení (interního nástroje).

### <a name="environment"></a>Prostředí

* Windows 2k12/SQL 2k12
* 1 soubory databáze v aktualizaci SP
* 2 × fondy úložiště na jeden uzel

![Appendix1][11]

### <a name="vm"></a>SÍŤ

V tomto příkladu provedeme přechod z ELB na interního nástroje. ELB byl k dispozici před interního nástroje, takže to ukazuje, jak během migrace přejít na interního nástroje.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Předchozí kroky: připojení k předplatnému

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: vytvoření nového účtu úložiště a cloudové služby

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: zvýšení povolených selhání prostředků\<Optional>

U některých prostředků, které patří do vaší skupiny dostupnosti Always On, se omezuje počet selhání, ke kterým může dojít v určité době, kdy se Clusterová služba pokusí restartovat skupinu prostředků. Doporučuje se to zvýšit, i když procházíte tímto postupem, protože Pokud neprovedete ruční převzetí služeb při selhání a aktivaci převzetí služeb při selhání vypnutím počítačů, můžete toto omezení dosáhnout blízko.

Mělo by se považovat za nedodržení tohoto limitu, pokud to chcete udělat v Správce clusteru s podporou převzetí služeb při selhání, přejít do vlastností skupiny prostředků Always On:

![Appendix3][13]

Změňte maximální počet selhání na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Přidání prostředku IP adresy pro skupinu clusteru\<Optional>

Pokud pro skupinu clusteru máte jenom jednu IP adresu a ta je zarovnaná na podsíť cloudu, mějte na pozor, že pokud omylem převezmete všechny uzly clusteru v cloudu v této síti, nebudete moct přejít do režimu online. V takovém případě zabrání aktualizace jiných prostředků clusteru.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfigurace DNS

Implementace hladkého přechodu závisí na tom, jak se služba DNS používá a aktualizuje.
Pokud je funkce Always On nainstalována, vytvoří skupinu prostředků clusteru se systémem Windows. Pokud otevřete Správce clusteru s podporou převzetí služeb při selhání, uvidíte, že má minimálně tři prostředky, a to dvě, na které dokument odkazuje:

* Virtual Network název (VNN) – název DNS, ke kterému se klienti připojují, když se chtějí připojit k SQL serverům přes Always On.
* Prostředek IP adresy – IP adresa, která je přidružená k VNN, může mít víc než jednu a v konfiguraci ve více lokalitách máte IP adresu na lokalitu nebo podsíť.

Při připojování k SQL Server načte ovladač klienta SQL Server záznamy DNS přidružené k naslouchacímu procesu a pokusí se připojit ke všem přidruženým IP adresám vždycky. V dalším kroku probereme několik faktorů, které mohou mít vliv na to.

Počet souběžných záznamů DNS, které jsou přidruženy k názvu naslouchacího procesu, závisí nejen na počtu přidružených IP adres, ale v clusteru RegisterAllIpProviders'setting v clusteringu s podporou převzetí služeb při selhání pro prostředek Always ON VNN.

Když nasadíte Always On v Azure, jsou k dispozici různé kroky pro vytvoření naslouchacího procesu a IP adresy. musíte ručně nakonfigurovat ' RegisterAllIpProviders ' na hodnotu 1, to se liší od místního nasazení Always On, pokud je již nastaveno na hodnotu 1.

Pokud je hodnota ' RegisterAllIpProviders ' 0, zobrazí se pouze jeden záznam DNS v DNS přidružený k Naslouchacímu procesu:

![Appendix4][14]

Pokud je ' RegisterAllIpProviders ' 1:

![Appendix5][15]

Následující kód vypíše nastavení VNN a nastaví ho za vás. Aby se změna projevila, musíte převést VNN do offline režimu a znovu ho převést do režimu online. To povede k tomu, že naslouchací proces bude v režimu offline způsobovat přerušení připojení klienta.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

V pozdějším kroku migrace je potřeba aktualizovat naslouchací proces Always On s aktualizovanou IP adresou, která odkazuje na nástroj pro vyrovnávání zatížení, zahrnuje odebrání a přidání prostředku IP adresy. Po aktualizaci IP je potřeba zajistit, aby se nová IP adresa aktualizovala v zóně DNS a aby klienti aktualizovali místní mezipaměť DNS.

Pokud se klienti nacházejí v jiném segmentu sítě a odkazují na jiný server DNS, musíte zvážit, co se stane s přenosem zóny DNS během migrace, protože čas opětovného připojení aplikace se omezuje aspoň na dobu přenosu zóny pro všechny nové IP adresy pro naslouchací proces. Pokud je tady omezení času, měli byste diskutovat a otestovat vynucení přírůstkového přenosu zóny s Windows teams a také umístit záznam hostitele DNS na hodnotu TTL (Time to Live), aby se klienti mohli aktualizovat. Další informace najdete v tématu o [přírůstkových zónových přenosech](https://technet.microsoft.com/library/cc958973.aspx) a [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Ve výchozím nastavení je hodnota TTL pro záznam DNS, která je přidružená k Naslouchacímu programu v rámci služby Always On Azure, 1200 sekund. Tuto dobu můžete chtít snížit, pokud během migrace v rámci časového omezení nechcete zajistit, aby klienti aktualizovali DNS pomocí aktualizované IP adresy pro naslouchací proces. Konfiguraci můžete zobrazit a upravit pomocí výpisu konfigurace VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Čím menší je "HostRecordTTL", dojde k vyššímu množství provozu DNS.

##### <a name="client-application-settings"></a>Nastavení klientské aplikace

Pokud vaše klientská aplikace SQL podporuje .NET 4,5 SQLClient, můžete použít klíčové slovo "MULTISUBNETFAILOVER = TRUE". Toto klíčové slovo by mělo být použito, protože umožňuje rychlejší připojení ke skupině dostupnosti Always On SQL během převzetí služeb při selhání. Vyčísluje všechny IP adresy přidružené k souběžnému naslouchacího procesu paralelně a při převzetí služeb při selhání provede výkonnější připojení TCP.

Další informace o předchozím nastaveních najdete v tématu [klíčové slovo MultiSubnetFailover a související funkce](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Viz také [Podpora SqlClient pro vysokou dostupnost a zotavení po havárii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: nastavení kvora clusteru

Vzhledem k tomu, že se chystáte alespoň jednu SQL Server mimo provoz, měli byste změnit nastavení kvora clusteru, pokud používáte určující sdílenou složku (FSW) se dvěma uzly, měli byste nastavit kvorum tak, aby bylo možné většinu uzlů využít a využívat dynamická hlasování, což umožňuje, aby jeden uzel zůstal zachovný.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Další informace o správě a konfiguraci kvora clusteru najdete v tématu [Konfigurace a Správa kvora v clusteru s podporou převzetí služeb při selhání s Windows serverem 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: extrakce existujících koncových bodů a seznamů ACL

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Uloží tento text do souboru.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Změna partnerů pro převzetí služeb při selhání a replikační režimy

Pokud máte více než dva servery SQL Server, měli byste změnit převzetí služeb při selhání jiné sekundární operace v jiném řadiči domény nebo v místním počítači na hodnotu synchronní a nastavit pro něj tohoto partnera pro automatické převzetí služeb při selhání (AFP). tím zajistíte, že zachováte HA, zatímco provádíte změny. Můžete to provést prostřednictvím TSQL změny, i když SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: odebrání sekundárního virtuálního počítače z cloudové služby

Nejdřív byste měli naplánovat migraci sekundárního uzlu cloudu. Pokud je tento uzel aktuálně primární, měli byste iniciovat ruční převzetí služeb při selhání.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Změna nastavení ukládání do mezipaměti disku v souboru CSV a uložení

U datových svazků by měly být nastavené na jen pro čtení.

Pro TLOG svazky by měly být nastavené na žádné.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: zkopírování virtuálních pevných disků

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


Stav kopírování VHD můžete kontrolovat na účet Premium Storage:

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

![Appendix8][18]

Počkejte, až budou všechny tyto záznamy zaznamenány jako úspěšné.

Informace o jednotlivých objektech blob:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Krok 11: Registrace disku s operačním systémem

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: import sekundární do nové cloudové služby

Níže uvedený kód používá také možnost Přidat, můžete importovat počítač a použít zachovávajíelné virtuální IP adresy.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: vytvoření interního nástroje na novém cloudu SVC, přidání koncových bodů a seznamů ACL s vyrovnáváním zatížení

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

#### <a name="step-14-update-always-on"></a>Krok 14: aktualizace Always On

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

![Appendix9][19]

Teď odeberte starou IP adresu cloudové služby.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: ověření aktualizace DNS

Nyní byste měli zkontrolovat servery DNS v klientských sítích SQL Server a zajistěte, aby clustering přidal další záznam hostitele pro přidanou IP adresu. Pokud se tyto servery DNS neaktualizovaly, zvažte vynucení přenosu zóny DNS a zajistěte, aby klienti v podsíti mohli překládat na IP adresy vždy, takže nemusíte čekat na automatickou replikaci DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: překonfigurování Always On

V tuto chvíli počkáte na sekundární uzel, který se migruje, aby se úplně znovu synchronizoval s místním uzlem a aby se přepnul na uzel synchronní replikace a vytvořil pro něj protokol AFP.  

#### <a name="step-17-migrate-second-node"></a>Krok 17: migrace druhého uzlu

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Změna nastavení ukládání do mezipaměti disku v souboru CSV a uložení

V případě datových svazků by měla být nastavení mezipaměti nastavena na jen pro čtení.

U TLOG svazků by měla být nastavení mezipaměti nastavená na NONE (žádné).

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: vytvoření nového nezávislého účtu úložiště pro sekundární uzel

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

#### <a name="step-20-copy-vhds"></a>Krok 20: zkopírování virtuálních pevných disků

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

Můžete kontrolovat stav kopírování VHD pro všechny virtuální pevné disky:

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

![Appendix12][22]

Počkejte, až budou všechny tyto záznamy zaznamenány jako úspěšné.

Informace o jednotlivých objektech blob:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Krok 21: Registrace disku s operačním systémem

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Přidání koncových bodů a seznamů ACL s vyrovnáváním zatížení

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

#### <a name="step-23-test-failover"></a>Krok 23: testovací převzetí služeb při selhání

Počkejte, až se migrovaný uzel synchronizuje s místním uzlem Always On. Umístěte ho do režimu synchronní replikace a počkejte, než se synchronizuje. Pak dojde k převzetí služeb při selhání z místního počítače na první migrovaný uzel, což je protokol AFP. Jakmile to fungovalo, změňte poslední migrovaný uzel na protokol AFP.

Měli byste otestovat převzetí služeb při selhání mezi všemi uzly a spustit i přes chaos testy, abyste zajistili, že převzetí služeb při selhání funguje podle očekávání a včas Manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: umístění back-Pntrs nastavení kvora clusteru/nastavení DNS TTL/převzetí služeb při selhání/synchronizace

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Přidává se prostředek IP adresy ve stejné podsíti.

Pokud máte jenom dva servery SQL a chcete je migrovat do nové cloudové služby, ale chcete je zachovat ve stejné podsíti, můžete se vyhnout přepnutí tohoto naslouchacího procesu do režimu offline, aby se odstranila původní trvalá IP adresa a přidala se nová IP adresa. Pokud migrujete virtuální počítače do jiné podsítě, nemusíte to dělat, protože existuje další síť s clustery, která odkazuje na tuto podsíť.

Po zaregistrování migrované sekundární a přidané do nového prostředku IP adresy pro novou cloudovou službu před převzetím služeb při selhání stávající primární databáze byste měli provést tyto kroky v rámci clusteru Správce převzetí služeb při selhání:

Informace o přidání do IP adresy najdete v dodatku, krok 14.

1. V případě aktuálního prostředku IP adresy změňte možného vlastníka na stávající primární SQL Server, v příkladu ' dansqlams4 ':

    ![Appendix13][23]
2. V případě nového prostředku IP adresy změňte možného vlastníka na migrované sekundární SQL Server, v příkladu ' dansqlams5 ':

    ![Appendix14][24]
3. Jakmile nastavíte tuto možnost, můžete převzetí služeb při selhání a při migraci posledního uzlu upravit možné vlastníky, aby byl uzel přidaný jako možný vlastník:

    ![Appendix15][25]

## <a name="additional-resources"></a>Další zdroje

* [Premium Storage Azure](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

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
