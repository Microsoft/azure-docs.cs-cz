---
title: Použití Azure Premium Storage s SQL serverem | Microsoft Docs
description: Tento článek používá prostředky, které jsou vytvořené pomocí modelu nasazení classic a poskytuje pokyny k používání Azure Premium Storage s SQL Server běžící na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: 3d3fdd8865a293c5e2f0df6a97910ac8e2a07d4c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400609"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Použití Azure Premium Storage s SQL Serverem na virtuálních počítačích
## <a name="overview"></a>Přehled
[Azure Premium Storage](../premium-storage.md) je nová generace úložiště, které zajišťuje nízkou latencí a vysokou propustnost vstupně-výstupní operace. Je nejvhodnější pro klíče vstupně-výstupní operace náročné úlohy, jako je SQL Server na IaaS [virtuální počítače](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek obsahuje plánování a pokyny k migraci virtuálního počítače se systémem SQL Server používat úložiště úrovně Premium. To zahrnuje infrastrukturu Azure (sítě, úložiště) a hostovaný virtuální počítač s Windows kroky. V příkladu v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) ukazuje úplné komplexní migrace koncová jak přesunout větší virtuální počítače využívat vylepšené místní úložiště SSD pomocí prostředí PowerShell.

Je důležité si uvědomit, proces začátku do konce využitím Azure Premium Storage s SQL serverem na virtuální počítače IAAS. To zahrnuje následující:

* Identifikace nezbytné požadavky pro použití úložiště Premium.
* Příklady nasazení systému SQL Server na IaaS do úložiště úrovně Premium pro nová nasazení.
* Příklady migraci existujícího nasazení samostatných serverů a nasazení pomocí SQL skupin dostupnosti Always On.
* Migrace možných přístupů.
* Příklad úplného začátku do konce zobrazující Azure, Windows a SQL Server kroky pro migraci stávající implementaci Always On.

Další informace pozadí na serveru SQL Server v Azure Virtual Machines najdete v tématu [systému SQL Server v Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** ADAM Sol **odborní recenzenti:** Leoš Carlos Vargas sleďů, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Předpoklady pro Storage úrovně Premium
Existuje několik předpokladů pro použití služby Premium Storage.

### <a name="machine-size"></a>Velikost počítače
Pro použití služby Premium Storage, budete muset použít řady DS virtuální počítače (VM). Pokud jste nepoužili řady DS počítače v cloudové službě před, musí odstranit existující virtuální počítač, zachovat připojené disky a poté vytvořit novou cloudovou službu před opětovným vytvořením virtuálního počítače jako velikost role DS *. Další informace o velikosti virtuálních počítačů najdete v tématu [virtuálního počítače a Cloud velikost služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Cloud Services
Při jejich vytváření v novou cloudovou službu, můžete použít pouze virtuální počítače DS * Storage úrovně Premium. Pokud používáte SQL serveru Always On v Azure, vždy na naslouchací proces odkazuje na adresu Azure interní nebo externí IP nástroje pro vyrovnávání zatížení, která souvisí s cloudovou službou. Tento článek se zaměřuje na tom, jak migrovat při zachování dostupnosti v tomto scénáři.

> [!NOTE]
> Řady DS * musí být první virtuální počítač, který je nasazen na novou Cloudovou službu.
>
>

### <a name="regional-vnets"></a>Virtuální místní sítě
Pro virtuální počítače DS * je nutné nakonfigurovat virtuální síť (VNET) hostování virtuálních počítačů jako místní. To "rozšiřuje" virtuální sítě je umožnit větší virtuální počítače v jiných clusterech zřídit a povolit komunikaci mezi nimi. Na následujícím snímku obrazovky ukazuje zvýrazněné umístění regionální virtuální sítě, zatímco první výsledek ukazuje "úzké" virtuální sítě.

![RegionalVNET][1]

Můžete zvýšit lístek podpory Microsoft migrovat do oblastní virtuální sítě. Společnost Microsoft neposkytuje pak změnu. K dokončení migrace na regionální virtuální sítě, změňte vlastnost AffinityGroup v konfiguraci sítě. Nejprve exportovat konfiguraci sítě v prostředí PowerShell a potom můžete nahradit **AffinityGroup** vlastnost **VirtualNetworkSite** element s **umístění** vlastnost. Zadejte `Location = XXXX` kde `XXXX` je oblast Azure. Pak importujte novou konfiguraci.

Například vzhledem k tomu následující konfiguraci virtuální sítě:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

To lze přesunout do oblastní virtuální síť v oblasti západní Evropa, změňte konfiguraci na následující:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Účty úložiště
Budete muset vytvořit nový účet úložiště, který je nakonfigurován pro Storage úrovně Premium. Všimněte si, že použití úložiště Premium je nastavený na účet úložiště, nikoli na jednotlivé virtuální pevné disky, ale při použití Virtuálního řady DS * z účtů Premium a Standard Storage můžete připojit virtuální pevný disk na. To může zvažte, pokud nechcete umístit virtuální pevný disk operačního systému k účtu úložiště Premium.

Následující **New-AzureStorageAccountPowerShell** s "Premium_LRS" **typ** vytvoří účet úložiště Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Nastavení mezipaměti virtuálních pevných disků
Hlavní rozdíl mezi vytváření disků, které jsou součástí účtu úložiště Premium je nastavená disková mezipaměť. Pro svazek dat systému SQL Server disků se doporučuje použít '**ukládání do mezipaměti pro čtení**'. Pro svazky protokolu transakcí, disku mezipaměti nastavení by mělo být '**žádné**'. To se liší od doporučení pro účty úložiště Standard Storage.

Jakmile byly připojeny virtuální pevné disky, nelze změnit nastavení mezipaměti. Potřebujete by se odpojíte a znovu připojíte virtuální pevný disk s nastavením aktualizované mezipaměti.

### <a name="windows-storage-spaces"></a>Prostory úložiště ve Windows
Můžete použít [prostory úložiště ve Windows](https://technet.microsoft.com/library/hh831739.aspx) jako u předchozí standardního úložiště, to umožňuje migrovat virtuální počítač, který je již využívá prostory úložiště. V příkladu v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 a předat dál) ukazuje kód prostředí Powershell k extrahování a import virtuálního počítače s více připojených virtuálních pevných disků.

Fondy úložiště používaly s účtem úložiště Azure. standardní zvýšit propustnost a snižování latence. Je možné, hodnota při testování fondy úložiště Storage úrovně Premium pro nová nasazení, ale zvyšují zvýšenou složitostí při instalaci úložiště.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Postup nalezení které mapy Azure virtuálních disků do fondů úložiště
Jako existují různé mezipaměti nastavení doporučení pro virtuální pevné disky připojené, můžete se rozhodnout pro kopírování virtuálních pevných disků do účtu Storage úrovně Premium. Když je připojte nový řady DS virtuálních počítačů, může však muset změnit nastavení ukládání do mezipaměti. Je jednodušší použít úložiště Premium doporučené nastavení mezipaměti, pokud máte samostatný virtuální pevné disky pro SQL datové soubory a soubory protokolů (spíše než jeden virtuální pevný disk obsahující oba).

> [!NOTE]
> Pokud máte soubory protokolu a data systému SQL Server na stejném svazku, možnost ukládání do mezipaměti, který zvolíte, závisí na vzory přístupu k vstupně-výstupní operace pro zatížení databáze. Pouze testování můžete ukazují, které možnost ukládání do mezipaměti je nejvhodnější pro tento scénář.
>
>

Pokud používáte prostory úložiště systému Windows, které se skládají z více virtuálních pevných disků, budete muset podívejte se na původní skripty k identifikaci, který je připojen virtuální pevné disky jsou však v jaké konkrétní fondu, takže pak můžete nastavit nastavení ukládání do mezipaměti odpovídajícím způsobem pro každý disk.

Pokud nemáte původní skript tak, aby zobrazovalo který mapovat virtuální pevné disky do fondu úložiště k dispozici, můžete použít následující kroky k určení mapování disku/úložiště fondu.

Pro každý disk použijte následující postup:

1. Získání seznamu disky připojené k virtuálnímu počítači s **Get-AzureVM** příkaz:

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Poznamenejte si Diskname a logické jednotky.

    ![DisknameAndLUN][2]
3. Vzdálená plocha do virtuálních počítačů. Pak přejděte na **Správa počítače** | **Správce zařízení** | **diskové jednotky**. Podívejte se na vlastnosti 'Microsoft virtuální disky.

    ![VirtualDiskProperties][3]
4. Číslo logické jednotky je odkaz na číslo logické jednotky, které určíte při připojení k virtuálnímu počítači virtuální pevný disk.
5. Pro "Microsoft virtuální Disk, přejděte na **podrobnosti** kartě, potom v **vlastnost** seznamu, přejděte na **klíč ovladače**. V **hodnotu**, Poznámka **posun**, což je 0002 na následujícím snímku obrazovky. 0002 označuje PhysicalDisk2, který odkazuje na fondu úložiště.

    ![VirtualDiskPropertyDetails][4]
6. Pro každý fond úložiště dump out přidružené disky:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Teď můžete použít tyto informace k přidružení připojit virtuální pevné disky na fyzických disků ve fondu úložiště.

Jakmile virtuální pevné disky jsou namapovány na fyzických disků ve fondech úložiště, potom můžete odpojit a zkopírujte je přes do účtu Storage úrovně Premium, připojte je k mezipaměti správné nastavení. Podívejte se na příklad v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroky 8 až 12. Tyto kroky ukazují, jak extrahovat konfiguraci disků virtuálních počítačů připojený virtuální pevný disk do souboru CSV, zkopírujte virtuální pevné disky, změnit nastavení konfigurace mezipaměti na disku a nakonec znovu nasadit virtuální počítač jako řady DS virtuálních počítačů s všech připojených discích.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Šířka pásma úložiště virtuálních počítačů a propustnost úložiště virtuálního pevného disku
Výkon úložiště závisí na velikosti DS * virtuálního počítače zadaný a velikosti virtuálního pevného disku. Virtuální počítače mají různé příspěvky pro počet virtuálních pevných disků, které je možné připojit a maximální šířka pásma, které podporují (MB/s). Čísla konkrétní šířky pásma, najdete v části [virtuálního počítače a Cloud velikost služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Při větší velikosti disků jsou dosáhnout vyšší IOPS. Byste měli zvážit při úvahách o vaši cestu migrace. Podrobnosti najdete [IOPS a typy disků, najdete v tabulce](../premium-storage.md#scalability-and-performance-targets).

Nakonec zvažte, že virtuální počítače mají jiný disk maximální šířek pásma, které podporují pro všechny disky připojené. Vysoké zátěži může vyčerpat šířku maximální disku pásma, které jsou k dispozici pro velikost role tohoto virtuálního počítače. Například Standard_DS14 podporuje až 512 MB/s Proto se tři disky P30 vám může vyčerpat šířku pásma disku virtuálního počítače. Ale v tomto příkladu může být překročen limit propustnost v závislosti na směs ke čtení a zápisu IOs.

## <a name="new-deployments"></a>Nová nasazení
V následujících dvou částech ukazují, jak můžete nasadit virtuální počítače SQL serveru do úložiště úrovně Premium. Jak je uvedeno nahoře, není nutné nutně umístit disk operačního systému do úložiště úrovně Premium. Můžete to udělat, pokud se hodláte umístit všechny zatížení s intenzivním vstupně-výstupní operace na virtuálním pevném disku operačního systému.

V prvním příkladu ukazuje použití existující obrázky v galerii Azure. Druhý příklad ukazuje, jak používat vlastní image virtuálního počítače, který máte ve stávající účet úložiště Standard storage.

> [!NOTE]
> Tyto příklady předpokládejme, že jste již vytvořili oblastní virtuální sítě.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Vytvořit nový virtuální počítač s Storage úrovně Premium s bitovou kopií Galerie
Následující příklad ukazuje, jak umístit do úložiště úrovně premium virtuální pevný disk operačního systému a připojit virtuální pevné disky úložiště Premium. Můžete však také umístit disk operačního systému v standardní účet úložiště a pak připojte virtuální pevné disky, které jsou umístěny v účtu Storage úrovně Premium. Je ukázán oba scénáře.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Vytvoření účtu služby Storage úrovně Premium
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Vytvořte novou Cloudovou službu
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Rezervovat VIP cloudové služby (volitelné)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Krok 4: Vytvořte kontejner virtuálních počítačů
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: Umístění virtuálního pevného disku operačního systému na Standard nebo Premium Storage
    #NOTE: Set up subscription and default storage account which is used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Krok 6: Vytvoření virtuálního počítače
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

    #create new Avaiability Set
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


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Vytvořit nový virtuální počítač pro použití s vlastní image Storage úrovně Premium
Tento scénář předvádí, kdy máte existující přizpůsobené bitové kopie, které jsou umístěny ve standardní účet úložiště. Jak je uvedeno, pokud chcete umístit virtuální pevný disk operačního systému na Storage úrovně Premium musíte zkopírovat bitovou kopii, která existuje v účet standardního úložiště a před použitím přenést do úložiště úrovně Premium. Pokud máte bitovou kopii na místě, můžete také použít tuto metodu, zkopírujte přímo do účtu Storage úrovně Premium.

#### <a name="step-1-create-storage-account"></a>Krok 1: Vytvoření účtu úložiště
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Krok 2 Vytvoření cloudové služby
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Krok 3: Použití existující bitová kopie
Můžete použít stávající image. Nebo můžete [trvat image existujícího počítače](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si na počítač, který je obrázek nemá být DS * počítače. Až budete mít bitovou kopii, následující kroky ukazují, jak ho zkopírujte do účet úložiště Premium se **Start-AzureStorageBlobCopy** prostředí PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopírování objektu Blob mezi účty úložiště
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: Pravidelné kontroly stavu kopie:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Přidejte bitové kopie disku na disk úložiště v rámci předplatného Azure
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Můžete zjistit, že i když stav hlásí úspěšné dokončení, může stále dostanete zapůjčení chyba disku. V takovém případě Počkejte přibližně 10 minut.
>
>

#### <a name="step-7--build-the-vm"></a>Krok 7: Vytvoření virtuálního počítače
Zde vytváříte virtuální počítač z bitové kopie a připojení dvě disků VHD úložiště Premium:

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

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Existující nasazení, které nepoužívají skupin dostupnosti Always On
> [!NOTE]
> Existující nasazení, nejprve najdete v článku [požadavky](#prerequisites-for-premium-storage) tohoto článku.
>
>

Existují různé aspekty pro nasazení systému SQL Server, které nepoužívají skupin dostupnosti Always On a ty, které provádějí. Pokud nepoužíváte Always On a máte existující samostatný systém SQL Server, můžete pomocí nového účtu služby a úložiště cloudu upgradovat do úložiště úrovně Premium. Zvažte následující možnosti:

* **Vytvoření nového virtuálního počítače SQL serveru**. Můžete vytvořit nový virtuální počítač SQL Server, který používá účet úložiště Premium, jak je uvedeno v nová nasazení. Zálohování a obnovení konfigurace a uživatele databáze SQL Server. Aplikace je třeba aktualizovat tak, aby odkazovaly nový Server SQL, pokud ji je přistupuje interně nebo externě. Museli byste se zkopírovat všechny objekty 'z databáze, jako když jste dělali migrace systému SQL Server (SxS) vedle sebe. To zahrnuje objekty, jako je například přihlášení, certifikáty a propojené servery.
* **Migrovat existující virtuální počítač serveru SQL**. To vyžaduje převádět virtuální počítač SQL Server do režimu offline a pak ho přenosu do novou cloudovou službu, což zahrnuje kopírování všechny své virtuální pevné disky připojené k účtu úložiště Premium. Při přechodu do režimu online virtuálního počítače, aplikace odkazuje na název hostitele serveru jako před. Upozorňujeme, že velikost stávající disk, ovlivňuje výkonové charakteristiky. Například 400 GB místa na disku získá zaokrouhlené nahoru P20. Pokud víte, že výkon disku nevyžadují, může znovu vytvořte virtuální počítač jako virtuální počítač řady DS a připojte virtuální pevné disky úložiště Premium specifikace velikosti a výkonu, které vyžadujete. Pak můžete odpojit a znovu připojit soubory databáze SQL.

> [!NOTE]
> Při kopírování disků VHD, byste měli vědět o velikosti, v závislosti na velikosti znamená, jaký typ disku úložiště Premium se spadají do, tato hodnota určuje specifikace výkon disku. Zaokrouhlí číslo Azure až po nejbližší velikost disku, takže pokud máte 400 GB místa na disku, to je zaokrouhlené nahoru P20. V závislosti na vaší stávající vstupně-VÝSTUPNÍMI požadavky virtuálního pevného disku operačního systému nemusí budete muset migrovat tato na účet úložiště Premium.
>
>

Pokud SQL Server pracuje externě, cloudové služby virtuální IP adresa se změní. Také musíte aktualizovat koncové body, seznamy řízení přístupu a DNS nastavení.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Existující nasazení, které používají skupiny dostupnosti Always On
> [!NOTE]
> Existující nasazení, nejprve najdete v článku [požadavky](#prerequisites-for-premium-storage) tohoto článku.
>
>

Nejprve v této části podíváme na jak Always On komunikuje s sítí Azure. Jsme potom rozčlenit migrací ve dvou scénářích: migrace, kde musí dosáhnout minimální dobou výpadku a migrace, kde lze tolerovat výpadky.

Použít místní SQL Server skupin dostupnosti Always On naslouchací proces místní které zaregistruje virtuální název DNS společně s IP adresu, která jsou sdílena mezi jeden nebo více serverů SQL. Pokud se klienti připojují směrování v naslouchací proces IP na primární server SQL. Je to server, který je vlastníkem prostředku vždy na IP v daném čase.

![DeploymentsUseAlways na][6]

Ve službě Microsoft Azure může mít pouze jednu IP adresu, které jsou přiřazené k síťovému adaptéru ve virtuálním počítači takže k dosažení stejnou úroveň abstrakce jako místní, Azure využívá IP adresu, která je přiřazena k interní/externí služby Vyrovnávání zatížení (ILB/ELB). Prostředek IP, která je sdílena mezi servery je nastaven na stejnou IP Adresou jako ILB režim Manageout. To je publikován v DNS a přenosy klientů je předána ILB režim Manageout do repliky primárního serveru SQL. ILB režim Manageout ví, což systému SQL Server je primární, protože ho používá sondy testovat prostředek vždy na IP. V předchozím příkladu ho sondy každý uzel, který má koncový bod odkazuje ELB/ILB, podle toho, která odpovídá je primární Server SQL.

> [!NOTE]
> ILB a ELB jsou obě přiřazené k určité Azure se Cloudová služba, proto žádné migrace do cloudu Azure pravděpodobně znamená, že změní IP nástroje pro vyrovnávání zatížení.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrace vždy na nasazení, která můžete povolit výpadky
Existují dvě strategie migrace vždy na nasazení, které umožňují některé výpadek:

1. **Přidejte další sekundární repliky do existujícího vždy na clusteru**
2. **Migrovat do nového vždy na clusteru**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Přidejte další sekundární repliky do existujícího vždy na clusteru
Jednou z možných strategií je přidání více sekundárních do vždy na skupiny dostupnosti. Budete muset přidat do novou cloudovou službu a aktualizovat naslouchací proces s novou IP nástroje pro vyrovnávání zatížení.

##### <a name="points-of-downtime"></a>Body výpadek:
* Ověření clusteru.
* Testování vždy na převzetí služeb při selhání pro novou sekundární repliky.

Pokud používáte fondy úložišť systému Windows v rámci virtuálního počítače pro vyšší propustnost vstupně-výstupní operace, pak režimu tyto prostředky jsou během úplného ověření clusteru. Ověřovací test je potřeba při přidání uzlů do clusteru. Čas potřebný k spuštění testu se může lišit, a proto byste měli otestovat ve vašem prostředí reprezentativního testovacího získat Přibližná doba o tom, jak dlouho to trvá.

By měl zřídit čas, kde můžete provádět ruční převzetí služeb při selhání a chaos testování na nově přidaných uzlech, aby bylo vždy na vysokou dostupnost funkcí podle očekávání.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Všechny instance systému SQL Server, kdy se používá fondy úložiště by se měla zastavit před spuštěním ověření.
>
> ##### <a name="high-level-steps"></a>Postup vysoké úrovně
>

1. Vytvoření dvou nových serverů SQL v novou cloudovou službu s připojeným úložištěm Premium.
2. Zkopírujte přes úplné zálohování a obnovení pomocí **NORECOVERY**.
3. Zkopírujte přes "mimo uživatelem DB" závislé objekty, jako je například přihlášení atd.
4. Vytvořit nový nové vyrovnávání pro interní zatížení (ILB) nebo použijte externí zatížení vyrovnávání (ELB) a potom nastavit koncové body skupinu s vyrovnáváním zatížení v obou uzlech nového.

   > [!NOTE]
   > Zkontrolujte, jestli že všechny uzly mají správnou konfiguraci koncového bodu, než budete pokračovat
   >
   >
5. Zastavte uživatele nebo aplikace přístup k systému SQL Server (Pokud používáte fondy úložišť).
6. Zastavte služby stroje SQL serveru na všech uzlech (Pokud používáte fondy úložišť).
7. Přidáte nové uzly clusteru a spuštění úplného ověření.
8. Po úspěšném ověření spusťte všechny služby SQL Server.
9. Protokoly transakcí zálohování a obnovení uživatelských databází.
10. Přidání nových uzlů do vždy na skupiny dostupnosti a umístěte replikace do **Synchronous**.
11. Přidat prostředek IP adresy z nové cloudové služby ILB/ELB pomocí prostředí PowerShell pro Always On podle příkladu více lokalit [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). V systému vytváření clusterů systému Windows, nastavte **Možní vlastníci** z **IP adresu** prostředek pro nové uzly starý. Najdete v části "Přidání prostředek IP adresy ve stejné podsíti, [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Převzetí služeb při selhání s jedním z nových uzlů.
13. Zkontrolujte nové uzly, automatické převzetí služeb při selhání partnery a testovací převzetí služeb při selhání.
14. Odeberte původní uzly ze skupiny dostupnosti.

##### <a name="advantages"></a>Výhody
* Nové servery SQL Server může být testována (SQL Server a aplikace), než budou přidány do Always On.
* Můžete změnit velikost virtuálního počítače a přizpůsobit úložiště do požadavků na přesný. Ale by bylo vhodné zachovat všechny cesty k souborům SQL stejné.
* Můžete ovládat, kdy je spuštěna přenos záloh databáze na sekundárních replikách. Tím se liší od použití Azure **Start-AzureStorageBlobCopy** příkaz zkopírovat virtuální pevné disky, protože se jedná o asynchronní kopírování.

##### <a name="disadvantages"></a>Nevýhody
* Pokud používáte fondy úložišť systému Windows, je během úplného ověření clusteru pro nové další uzly clusteru výpadku.
* V závislosti na verze serveru SQL a stávající počet sekundárních replikách nemusí být možné přidat další sekundární repliky bez odebrání stávající sekundární repliky.
* Může dojít při nastavení sekundárních dlouhá doba přenosu dat SQL.
* Není další náklady během migrace, když máte nové počítače, které běží paralelně.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrovat do nového vždy na clusteru
Další strategie je vytvoření nové vždy na clusteru s nové uzly v novou cloudovou službu a pak přesměrovat klienty pro použití.

##### <a name="points-of-downtime"></a>Body výpadku
Při přenosu aplikace a uživatele pro nového naslouchací proces Always On dojde k výpadku. Výpadek, závisí na:

* Čas potřebný k obnovení zálohování konečné transakčního protokolu databáze na nové servery.
* Čas potřebný k aktualizaci klientským aplikacím používat nový Always On naslouchací proces.

##### <a name="advantages"></a>Výhody
* Skutečné provozním prostředí, SQL Server, můžete otestovat a změny sestavení operačního systému.
* Máte možnost, chcete-li přizpůsobit úložiště a potenciálně snížili velikost virtuálního počítače. To může mít za následek snížení nákladů.
* Během tohoto procesu můžete aktualizovat sestavení systému SQL Server nebo verze. Rovněž lze upgradovat operační systém.
* Předchozí vždy na clusteru může fungovat jako cíl plnou vrácení zpět.

##### <a name="disadvantages"></a>Nevýhody
* Budete muset změnit název DNS naslouchacího procesu, pokud chcete, aby obě Always On clustery se systémem současně. Tento postup přidá správy režii během migrace jako nový název naslouchacího procesu musí použit řetězců klientské aplikace.
* Je nutné implementovat synchronizační mechanismus mezi těmito dvěma prostředími k jejich jak nejblíže k minimalizaci konečná synchronizace požadavky před migrací.
* Během migrace se se přidá náklady při novém prostředí spuštěna.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrace vždy na nasazení pro minimální dobou výpadku
Pro minimální dobou výpadku existují dvě strategie migrace nasazení Always On:

1. **Využívat stávající sekundární: jedné lokalitě**
2. **Využívat stávající sekundární následující: více lokalit**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Využívat stávající sekundární: jedné lokalitě
Jednou z možných strategií pro minimální dobou výpadku je využít existující cloudu sekundární a odebrat z aktuální cloudové služby. Potom zkopírovat virtuální pevné disky pro nový účet úložiště Premium a vytvoření virtuálního počítače v rámci nové cloudové služby. Aktualizujte naslouchací proces ve clustering a převzetí služeb při selhání.

##### <a name="points-of-downtime"></a>Body výpadku
* Při aktualizaci poslední uzel s vyrovnáváním zatížení se koncový bod dojde k výpadku.
* Vaše opětovné připojení klienta může být zpoždění v závislosti na konfiguraci klienta a DNS.
* Pokud se rozhodnete převést do offline režimu skupinu vždy na clusteru vyměnit IP adresy je další výpadku. To se můžete vyhnout pomocí služby nebo závislostí a možných vlastníků pro přidání prostředků IP adresu. Najdete v části "Přidání prostředek IP adresy ve stejné podsíti, [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Když chcete přidané uzel zúčastnit se v jako vždy na převzetí služeb při selhání Partner, je nutné přidat koncový bod Azure s odkazem na zatížení vyrovnáváním sady. Při spuštění **přidat AzureEndpoint** příkaz k tomu, otevřete aktuální připojení zůstat, ale nejsou schopná navázat, dokud nástroj pro vyrovnávání zatížení byl aktualizován. nová připojení k naslouchací proces. Při testování to bylo nalezeno na posledních 90 120seconds, by měla být testována.
>
>

##### <a name="advantages"></a>Výhody
* Žádné další náklady vzniklé během migrace.
* 1: 1 migrace.
* Menší složitost.
* Umožňuje vyšší IOPS z SKU úložiště Premium. Pokud jsou disky odpojit od virtuálního počítače a zkopírovat do nové cloudové služby, 3. stran nástroje lze zvýšit velikost virtuálního pevného disku, která poskytuje vyšší propustnost. Zvýšení velikosti virtuálního pevného disku, najdete [diskuzi na fóru](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nevýhody
* Při migraci dojde k dočasné ztrátě HA a zotavení po Havárii.
* Protože se jedná o migraci 1:1, budete muset použít minimální velikost virtuálního počítače, který podporuje počtu virtuální pevné disky, takže nebudete moci downsize virtuální počítače.
* Tento scénář využije Azure **Start-AzureStorageBlobCopy** příkaz, který je asynchronní. Neexistuje žádná smlouva SLA na dokončení kopírování. Čas kopií se liší, když to závisí na čekání ve frontě, které také závisí na množství dat pro přenos. Kopírování času zvyšuje, pokud přenos má jiné Azure datového centra, který podporuje službu Premium Storage v jiné oblasti. Pokud máte právě 2 uzly, vezměte v úvahu možné snížení rizika v případě, že bude pro kopii trvá déle než při testování. To může zahrnovat následující návrhy.
  * Přidáte dočasný uzel systému SQL Server 3. pro HA před migrací odsouhlaseného výpadku.
  * Spusťte migraci mimo Azure plánované údržby.
  * Zkontrolujte, zda že jste správně nakonfigurovali vaší kvorum clusteru.  

##### <a name="high-level-steps"></a>Postup vysoké úrovně
Tento dokument nepředvádí úplný a provést tak kompletní příklad, ale [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) poskytuje podrobné informace, které můžete využít postupu.

![MinimalDowntime][8]

* Shromáždit konfiguraci disku a odebrat uzel (neodstraňujte připojené virtuální pevné disky).
* Vytvořit účet úložiště Premium a zkopírovat virtuální pevné disky z účet standardního úložiště
* Vytvořte novou cloudovou službu a znovu nasaďte SQL2 virtuálního počítače v dané cloudové služby. Vytvořte virtuální počítač pomocí zkopírovaného původního operačního systému virtuálního pevného disku a připojení zkopírovaný virtuální pevné disky.
* Konfigurace ILB / ELB a přidat koncové body.
* Buď aktualizujte naslouchací proces:
  * Přepnutím do režimu offline vždy na skupinu a aktualizace vždy na naslouchací proces s novou ILB / ELB IP adres.
  * Nebo přidání prostředek IP adresy z nové cloudové služby ILB/ELB pomocí prostředí PowerShell do clusteringu Windows. Potom nastavte možných vlastníků prostředku IP adresu do uzlu migrované SQL2 a to jako závislosti nebo v názvu sítě. Najdete v části "Přidání prostředek IP adresy ve stejné podsíti, [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Zkontrolujte konfiguraci DNS nebo šíření klientům.
* Migrujte virtuální počítač SQL1 a projít kroky 2 – 4.
* Pokud používáte 5ii kroky, přidejte počítač SQL1 jako možného vlastníka pro přidání prostředek IP adresy
* Testovací převzetí služeb při selhání.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Využívat stávající sekundární následující: Multi-Site
Pokud máte uzly ve více než jeden datové centrum Azure (DC) nebo pokud máte hybridní prostředí, potom můžete konfigurace Always On v tomto prostředí minimalizovat prostoje.

Přístup je změnit synchronizace Always On na Synchronous místní nebo sekundární řadič domény Azure, a potom klepněte na převzetí služeb při selhání přes k tomuto serveru SQL. Potom zkopírovat virtuální pevné disky na účet úložiště Premium a znovu nasaďte tento počítač do novou cloudovou službu. Aktualizujte naslouchací proces a pak navrácení služeb po obnovení.

##### <a name="points-of-downtime"></a>Body výpadku
Výpadek se skládá z čas potřebný k převzetí služeb při selhání alternativní řadič domény a zpět. Také závisí na konfiguraci klienta a DNS a může být zpoždění vaší opětovné připojení klienta.
Podívejte se na následující příklad konfigurace hybridní Always On:

![MultiSite1][9]

##### <a name="advantages"></a>Výhody
* Můžete využít stávající infrastrukturu.
* Máte možnost předem napřed upgradovat službu Azure storage na řadiči domény Azure zotavení po Havárii.
* Můžete třeba změnit konfiguraci úložiště Azure pro zotavení po Havárii řadiče domény.
* Během migrace, s výjimkou testovací převzetí služeb při selhání není k dispozici minimálně dvě převzetí služeb při selhání.
* Není nutné pro přesun dat systému SQL Server pomocí zálohování a obnovení.

##### <a name="disadvantages"></a>Nevýhody
* V závislosti na klientský přístup k systému SQL Server je možné zvýší latence při systém SQL Server běží v alternativní řadič domény do aplikace.
* Čas kopírování virtuálních pevných disků do úložiště úrovně Premium může trvat dlouho. To může mít vliv na vaše rozhodnutí, zda chcete zachovat uzlu ve skupině dostupnosti. Zvažte proto pro při protokolu náročné pracovním zatížením běží během migrace je nutné, protože primárním uzlu, který má k udržování nereplikovaných transakce v protokolu transakcí. Proto to může výrazně zvýší.
* Tento scénář využije Azure **Start-AzureStorageBlobCopy** příkaz, který je asynchronní. Neexistuje žádná smlouva SLA na dokončení. Čas kopií se liší, když to závisí na čekání ve frontě, je také závisí na množství dat pro přenos. Proto máte jenom jeden uzel v 2. datové centrum, můžete provést kroky zmírňující rizika v případě, že bude pro kopii trvá déle než při testování. Tyto kroky zmírňující rizika zahrnují následující návrhy:
  * Přidáte dočasný uzel SQL 2. pro HA před migrací odsouhlaseného výpadku.
  * Spusťte migraci mimo Azure plánované údržby.
  * Zkontrolujte, zda že jste správně nakonfigurovali vaší kvorum clusteru.

Tento scénář předpokládá, že budete mít zdokumentovaný instalaci vaší a vědět, jak je namapovaný úložiště, aby bylo možné změny pro disk optimální nastavení mezipaměti.

##### <a name="high-level-steps"></a>Postup vysoké úrovně
![Multisite2][10]

* Zkontrolujte místní / alternativní řadič domény Azure primární Server SQL a nastavte jej Další automatické převzetí služeb při selhání partnera (AFP).
* Shromáždit informace o konfiguraci disků ze SQL2 a odebrat uzel (neodstraňujte připojené virtuální pevné disky).
* Vytvořit účet úložiště Premium a zkopírovat virtuální pevné disky z účet standardního úložiště.
* Vytvořte novou cloudovou službu a vytvoření virtuálního počítače SQL2 s jeho úložiště prémií disky připojené.
* Konfigurace ILB / ELB a přidat koncové body.
* Aktualizovat vždy na naslouchací proces s novou ILB / ELB IP adresu a testovací převzetí služeb při selhání.
* Zkontrolujte konfiguraci DNS.
* Změňte AFP SQL2, migrovat SQL1 a projít kroky 2 až 5.
* Testovací převzetí služeb při selhání.
* Přepněte AFP zpět na počítač SQL1 a SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrace na Storage úrovně Premium nasazení ve více lokalitách vždy v clusteru
Zbývající část tohoto článku poskytuje podrobný příklad převodu vždy na cluster více lokalit do úložiště úrovně Premium. Převede také naslouchací proces pomocí externím vyrovnáváním zatížení (ELB) vyrovnávání interní zatížení (ILB).

### <a name="environment"></a>Prostředí
* Windows 2k12 / SQL 2k12
* 1 DB soubory na SP
* 2 x fondy úložiště na každý uzel

![Appendix1][11]

### <a name="vm"></a>VM:
V tomto příkladu přidáme k předvedení přesunutí ze systému Windows externí nástroje pro ILB. Režim Manageout nebylo k dispozici před ILB, takže to ukazuje, jak přepnout na ILB během migrace.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Kroky před: Připojení k předplatnému
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Vytvořte nový účet úložiště a cloudové služby
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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zvýšit povolených selhání na prostředky <Optional>
Na některé prostředky, které patří do vaší vždy na skupiny dostupnosti neexistují omezení na tom, kolik chyby, ke kterým může dojít v době, kde se Clusterová služba pokusí o restartování skupině prostředků. Doporučuje se, že to zvýšíte a přitom jste se s návodem tento postup od Pokud to neuděláte ruční převzetí služeb při selhání a aktivační události převzetí služeb při selhání pomocí vypnutí počítače, které vám brzy bude dosaženo tohoto limitu.

Je třeba ji zkontrolovat zdvojnásobit příspěvek na selhání, k tomu ve Správci clusteru převzetí služeb při selhání přejděte do vlastností skupiny prostředků Always On:

![Appendix3][13]

Změňte maximální počet selhání na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Přidání IP adresu prostředku pro skupinu clusteru <Optional>
Pokud máte pouze jednu IP adresu pro skupinu clusteru a to je zarovnán do cloudu podsítě, mějte na paměti, pokud jste omylem převést do režimu offline všechny uzly clusteru v cloudu na tuto síť pak prostředek IP clusteru a název sítě s clustery nejsou možné do režimu online. V takovém případě brání aktualizací k jiným prostředkům clusteru.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfigurace serveru DNS
Implementace s hladkým přechodem závisí na tom, jak se DNS využité a aktualizovat.
Když Always On je nainstalován, vytvoří skupinu prostředků clusteru Windows otevřete Správce clusteru převzetí služeb při selhání, uvidíte, že minimálně má tři zdroje, které dokumentu odkazuje na dva jsou:

* Názvu virtuální sítě (VNN) – název DNS, který se klienti připojují, když chce připojit k SQL serverům prostřednictvím Always On.
* Prostředek IP adresa – IP adresa, které přidružené VNN, můžete mít více než jednu a v konfiguraci ve více lokalitách mají IP adresu pro lokality a podsítě.

Při připojování k systému SQL Server, SQL Server Client ovladač načte záznamy DNS, který je přidružený naslouchací proces a pokusí se připojit k každý Always On související IP adresu. V dalším kroku probereme některé faktory, které mohou mít vliv na to.

Počet souběžných záznamy DNS, které jsou spojeny s názvem naslouchací proces závisí nejen na počet IP adresám, ale ' RegisterAllIpProviders'setting clusteringu pro převzetí služeb při selhání pro prostředek Always ON VNN.

Když nasadíte Always On v Azure existují různé kroky, které vytvořit naslouchací proces a IP adresy, budete muset ručně nakonfigurovat 'RegisterAllIpProviders' na hodnotu 1, to se liší na místní vždy na nasazení, kde je již nastaven na hodnotu 1.

Pokud 'RegisterAllIpProviders' 0, pak zobrazí pouze jeden záznam DNS ve službě DNS přidružený naslouchací proces:

![Appendix4][14]

Je-li 'RegisterAllIpProviders' 1:

![Appendix5][15]

Následující kód výpisy paměti se nastavení VNN a nastaví ho za vás. Aby se změna projevila musíte do offline režimu VNN a zapnout ho zpátky do online režimu. Tato akce trvá naslouchací proces offline což způsobilo přerušení připojení klienta.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

V pozdější fázi migrace je potřeba aktualizovat naslouchací proces Always On s aktualizované IP adresu, která odkazuje na službu Vyrovnávání zatížení, to zahrnuje odebrání prostředků IP adresy a přidání. Po aktualizaci IP je potřeba zajistit novou IP adresu byl aktualizován v zóně DNS a že klienti jsou aktualizace své místní mezipaměti DNS.

Pokud vaši klienti nacházejí v různých síťových segmentu a odkazovat na jiný server DNS, je potřeba zvážit, co se stane o přenos zóny DNS během migrace, jak znovu připojit aplikace čas je omezené alespoň zóny přenosu době všechny nové adresy IP adresy pro naslouchací proces. Pokud jste v části času omezení tady, by měl zabývat a otestovat vynucení přenos zóny přírůstkové s týmům Windows a taky záznam hostitele DNS pro nižší TTL Time To Live (), takže klienti aktualizovat. Další informace najdete v tématu [přírůstkové přenosy zóny](https://technet.microsoft.com/library/cc958973.aspx) a [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Ve výchozím nastavení TTL pro DNS záznam, který je přidružený naslouchací proces ve Always On v Azure je 1 200 sekund. Můžete chtít omezit to, pokud jste v části času omezení během migrace zajistit klienti aktualizaci jejich služby DNS s aktualizovanou adresou IP pro naslouchací proces. Můžete zobrazit a upravit konfiguraci vypsání se konfigurace VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> Čím nižší 'HostRecordTTL' vyšší množství přenosy DNS nastane.

##### <a name="client-application-settings"></a>Nastavení aplikace klienta
Pokud klientské aplikace SQL podporuje rozhraní .net 4.5 SQLClient a pak můžete použít "MULTISUBNETFAILOVER = TRUE" – klíčové slovo. This – klíčové slovo bude použito, protože umožňuje rychlejší připojení k SQL vždy na skupiny dostupnosti během převzetí služeb při selhání. Vytvoří výčet prostřednictvím všechny IP adresy přidružené k naslouchání Always On paralelně a provede agresivnější rychlost opakování připojení TCP během převzetí služeb.

Další informace o předchozí nastavení najdete v tématu [MultiSubnetFailover – klíčové slovo a související funkce](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Viz také [SqlClient podporu pro vysokou dostupnost a zotavení po havárii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: Nastavení kvora clusteru
Jak chcete trvá na nejméně jednom serveru SQL dolů v čase, změňte nastavení kvora clusteru, pokud soubor sdílené složky s kopií clusteru (FSW) s dvěma uzly, měli byste nastavit pro povolení Většina uzlů a využívat dynamické hlasování kvora , aby vám umožnil jednoho uzlu kvůli zachování stálého.

    Set-ClusterQuorum -NodeMajority  

Další informace o správě a nastavení kvora clusteru najdete v tématu [konfigurace a správa kvora v clusteru převzetí služeb při selhání ve Windows serveru 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Extrahování stávající koncové body a seznamy řízení přístupu
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Uložte tento text do souboru.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Změnit režimy replikace a převzetí služeb při selhání partnery
Pokud máte více než dva servery SQL, by měl změnit převzetí služeb při selhání jinou sekundární v jiném řadiče domény nebo místní 'Synchronous' a nastavit jej jako automatické převzetí služeb při selhání partnera (AFP), to je tak spravovat HA a přitom provádíte změny. Můžete to provést prostřednictvím TSQL systému změnit, když aplikace SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Odeberte sekundární virtuální počítač z cloudové služby
By měl být plánování nejdřív přenést sekundární uzel cloudu. Pokud tento uzel je aktuálně primární, by měla iniciovat ruční převzetí služeb při selhání.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Změňte disku ukládání do mezipaměti nastavení v souboru CSV a uložit
Pro datové svazky tyto musí být nastavená na hodnotu READONLY.

Svazky protokolu tyto by měla být nastavena na hodnotu NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: Kopírování virtuálních pevných disků
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



Můžete zkontrolovat stav kopírování virtuálních pevných disků k účtu úložiště Premium:

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

![Appendix8][18]

Počkejte, dokud všechny tyto se zaznamenávají jako úspěšné.

Informace pro jednotlivé objekty BLOB:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Krok 11: Registrace operačního systému disku
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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Importujte sekundární do novou cloudovou službu
Následující kód také používá možnosti přidané sem můžete import počítače a použít retainable VIP.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: Vytvoření ILB na nové cloudové Svc zatížit vyrovnáváním koncových bodů a seznamy řízení přístupu
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

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Krok 14: Aktualizace vždy na
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Nyní odeberte starý cloudové služby IP adresu.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: Kontrola aktualizací DNS
Nyní zkontrolujte servery DNS na vaše klientské sítě systému SQL Server a ujistěte se, že clustering přidal záznam navíc hostitele pro přidání IP adresu. Pokud tyto servery DNS nebyly aktualizovány, zvažte vynucení přenos zóny DNS a ujistěte se, že klienti v existuje podsíť jsou schopni vyřešit obě vždy na IP adresy, to je proto není potřeba čekat na automatické replikaci DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: Překonfigurujte vždy na
V tomto okamžiku je počkat sekundární tento uzel, která byla migrována plně poté proveďte opakovanou synchronizaci s místními uzlem a přepněte do uzlu synchronní replikace a změňte ji AFP.  

#### <a name="step-17-migrate-second-node"></a>Krok 17: Migrace druhého uzlu
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
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Změňte disku ukládání do mezipaměti nastavení v souboru CSV a uložit
Pro datové svazky by měl nastavení ukládání do mezipaměti nastavit na hodnotu READONLY.

Nastavení ukládání do mezipaměti svazky protokolu by měla být nastavena na hodnotu NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Vytvoření nového účtu nezávislé úložiště pro sekundární uzel
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

#### <a name="step-20-copy-vhds"></a>Krok 20: Kopírování virtuálních pevných disků
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

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Můžete zkontrolovat stav kopie virtuálního pevného disku pro všechny virtuální pevné disky: příkazu ForEach ($disk v $diskobjects) {$lun = $disk. Logická jednotka $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Jmenovka_disku $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Počkejte, dokud všechny tyto se zaznamenávají jako úspěšné.

Informace pro jednotlivé objekty BLOB:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Krok 21: Registrace operačního systému disku
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

    #Join to existing Avaiability Set

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Přidejte zatížení vyrovnáváním koncových bodů a seznamy řízení přístupu
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Krok 23: Test převzetí služeb při selhání
Počkejte, než pro migrované uzel k synchronizaci s místními vždy na uzlu. Umístěte ji do režimu synchronní replikace a počkejte, dokud je synchronizován. Následně převzetí služeb při selhání z místního na prvním uzlu migrován, který je AFP. Jakmile který pracoval, změňte na AFP poslední migrované uzel.

Doporučujeme testovací převzetí služeb při selhání mezi všemi uzly a spustit v případě chaos testy, aby fungoval převzetí služeb při selhání jako očekáváno a včas manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Vrátit zpět nastavení kvora clusteru nebo DNS TTL nebo Pntrs převzetí služeb při selhání nebo nastavení synchronizace
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Přidání prostředku IP adresy ve stejné podsíti
Pokud máte jenom dva servery SQL a chcete migrovat na novou cloudovou službu, ale chcete zachovat je ve stejné podsíti, se můžete vyhnout trvá naslouchací proces do offline režimu odstranit původní vždy na IP adresu a přidat nové IP adresy. Pokud provádíte migraci virtuálních počítačů k jiné podsíti, není nutné k tomu, jak je síť další clusteru, který odkazuje na této podsíti.

Jakmile mají zapínají migrované sekundární a přidat nový prostředek IP adresu pro novou cloudovou službu před převzetí služeb při selhání existující primární, můžete provést tyto kroky v rámci Správce clusteru převzetí služeb při selhání:

Chcete-li přidat IP adresu, přečtěte si téma [příloha](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), krok 14.

1. Pro aktuální prostředek IP adresu změna možných vlastníků "Existující primární SQL Server", v příkladu 'dansqlams4':

    ![Appendix13][23]
2. U nového prostředku IP adresu změna možných vlastníků 'Migrováno sekundární SQL Server", v příkladu 'dansqlams5':

    ![Appendix14][24]
3. Toto nastavení můžete převzetí služeb při selhání, a při migraci poslední uzel mezi možnými vlastníky by měl být upraven tak, že uzel je přidán jako možného vlastníka:

    ![Appendix15][25]

## <a name="additional-resources"></a>Další zdroje informací:
* [Azure Premium Storage](../premium-storage.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [Systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
