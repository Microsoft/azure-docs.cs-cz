---
title: Použití Azure Premium Storage s SQL serverem | Dokumentace Microsoftu
description: Tento článek používá prostředky vytvořené pomocí modelu nasazení classic a poskytuje pokyny k použití Azure Premium Storage s SQL Server běžící na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.openlocfilehash: 252e4f9fe5ed6b4ff9997fc41c691636e6d002b3
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413534"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Použití Azure Premium Storage s SQL Serverem na virtuálních počítačích
## <a name="overview"></a>Přehled
[Azure Premium Storage](../premium-storage.md) je další generace služby úložiště, které poskytuje nízkou latenci a vysokou propustnost vstupně-výstupních operací. Je nejvhodnější pro klíče vstupně-výstupní operace náročné úlohy, jako je SQL Server na IaaS [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek poskytuje plánování a pokyny k migraci virtuálního počítače se systémem SQL Server chcete používat Premium Storage. Patří sem infrastruktury Azure (sítě, úložiště) a hostovaného virtuálního počítače Windows kroky. V příkladu v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zobrazí úplné komplexní kompletní migrace o tom, jak přesunout větší virtuální počítače, abyste mohli využívat vylepšené místní úložiště SSD pomocí prostředí PowerShell.

Je důležité pochopit,-celým procesem využívání Azure Premium Storage s SQL serverem na virtuálních počítačích IAAS. To zahrnuje:

* Identifikace požadavků pro použití služby Premium Storage.
* Příklady nasazení systému SQL Server na IaaS na Premium Storage pro nová nasazení.
* Příklady migrace stávajících nasazení samostatných serverů a nasazení s využitím SQL skupin dostupnosti Always On.
* Migrace možných přístupů.
* Příklad úplného začátku do konce ukazuje kroky Azure, Windows a SQL Server na migraci stávající implementaci Always On.

Další informace na pozadí v systému SQL Server na virtuálních počítačích Azure najdete v tématu [systému SQL Server ve službě Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **odborní recenzenti:** sleďů Vargas Luis Carlos, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Požadavky na úložiště úrovně Premium
Existuje několik předpokladů pro použití služby Premium Storage.

### <a name="machine-size"></a>Velikost počítače
Pro použití služby Premium Storage, budete muset použít řady DS Virtual Machines (VM). Pokud jste ve své cloudové službě před ještě počítačů řady DS, musíte odstranit existující virtuální počítač, zachovat připojených disků a pak vytvořit novou cloudovou službu před opětovným vytvořením virtuálního počítače jako velikost role DS *. Další informace o velikostech virtuálních počítačů najdete v tématu [virtuálního počítače a velikost cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Cloud Services
Při jejich vytváření v novou cloudovou službu, můžete použít pouze virtuální počítače DS * díky službě Premium Storage. Pokud používáte SQL serveru Always On v Azure, vždy na naslouchací proces odkazuje na adresu Azure interní nebo externí IP adresu nástroje pro vyrovnávání zatížení, který je spojen s cloudovou službou. Tento článek se zaměřuje na tom, jak migrovat při zachování dostupnosti v tomto scénáři.

> [!NOTE]
> Řady DS * musí být prvním virtuálním počítači, který je nasazený do nové cloudové služby.
>
>

### <a name="regional-vnets"></a>Regionální virtuální sítě
Pro virtuální počítače DS * je nutné nakonfigurovat Virtual Network (VNET) hostuje vaše virtuální počítače na místní. To "rozšiřuje" virtuální síť umožňuje větší virtuální počítače zřizované v jiných clusterech a povolit komunikaci mezi nimi. Na následujícím snímku obrazovky ukazuje zvýrazněného umístění oblastní virtuální sítě, zatímco první výsledek se zobrazí "zúžit" virtuální síť.

![RegionalVNET][1]

Může vyvolat lístek podpory Microsoftu a migrovat do oblastní virtuální sítě. Společnost Microsoft neposkytuje pak změnu. K dokončení migrace do regionálních virtuálních sítí, změňte vlastnost AffinityGroup v konfiguraci sítě. Exportujte konfiguraci sítě v prostředí PowerShell a potom změňte **AffinityGroup** vlastnost v **VirtualNetworkSite** element s **umístění** vlastnost. Zadejte `Location = XXXX` kde `XXXX` je určitá oblast Azure. Importujte novou konfiguraci.

Například vzhledem k tomu následující konfigurace virtuální sítě:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Pokud chcete přesunout to na regionální virtuální síť v oblasti západní Evropa, změňte konfiguraci následujícím způsobem:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Účty úložiště
Je potřeba vytvořit nový účet úložiště, který je nakonfigurovaný pro Premium Storage. Ale při použití VM řady DS * můžete připojit virtuální pevný disk společnosti z účtů Premium a Standard Storage, mějte na paměti, že použití služby Premium Storage je nastaven na účet úložiště, ne na jednotlivých virtuálních pevných disků. To může zvážit, pokud nechcete k umístění virtuálního pevného disku operačního systému do účtu Premium Storage.

Následující **New-AzureStorageAccountPowerShell** příkaz "Premium_LRS" **typ** vytvoří účet Premium Storage:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Nastavení mezipaměti virtuálních pevných disků
Hlavní rozdíl mezi vytváření disků, které jsou součástí účtu Premium Storage je nastavení mezipaměti disku. Pro disky objem dat serveru SQL se doporučuje se, že používáte "**ukládání do mezipaměti pro čtení**". Pro svazky s protokoly transakcí, disk mezipaměti by měla být nastavení "**žádný**". Tím se liší od doporučení pro účty úložiště úrovně Standard.

Jakmile byly připojeny virtuální pevné disky, nelze změnit nastavení mezipaměti. Bude potřeba odpojit a znovu ho připojte virtuální pevný disk s nastavením aktualizace mezipaměti.

### <a name="windows-storage-spaces"></a>Prostory úložiště Windows
Můžete použít [prostory úložiště Windows](https://technet.microsoft.com/library/hh831739.aspx) stejně jako u předchozí úložiště úrovně Standard, díky tomu můžete migrovat virtuální počítač, který již využívá prostory úložiště. V příkladu v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (kroky 9 a dopředu) ukazuje kód Powershellu k extrahování a import virtuálního počítače s více připojenými virtuálními pevnými disky.

Fondy úložiště byly použity s účtem úložiště Standard Azure pro zvýšení propustnosti a snižuje latenci. Hodnotu lze najít v testování fondy úložiště s Premium Storage pro nová nasazení, ale přidávají další složitosti pomocí nastavení úložiště.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak najít nich namapuje Azure virtuálních disků do fondů úložiště
Jako existují různé mezipaměti nastavení doporučení pro připojenými virtuálními pevnými disky, můžete se rozhodnout zkopírovat virtuální pevné disky do účtu Premium Storage. Ale když je znovu připojit k nových virtuálních počítačů řady DS, můžete potřebovat změnit nastavení mezipaměti. Je jednodušší použít Storage úrovně Premium, doporučuje se nastavení mezipaměti, když máte samostatný virtuální pevné disky pro SQL datové soubory a soubory protokolů (spíše než jeden virtuální pevný disk, který obsahuje oba).

> [!NOTE]
> Pokud máte soubory protokolu a data systému SQL Server na stejném svazku, vámi zvolené možnosti ukládání do mezipaměti závisí na vzory přístupu k vstupně-výstupních operací pro vaše databázové procesy využívající. Pouze testování předvést, která možnost ukládání do mezipaměti je nejvhodnější pro tento scénář.
>
>

Pokud používáte prostory úložiště Windows, které jsou tvořeny z více virtuálních pevných disků, musíme se podívat na původní skripty k identifikaci, který připojené virtuální pevné disky jsou však v konkrétní fondu, takže můžete nastavit nastavení mezipaměti na odpovídajícím způsobem pro každý disk.

Pokud původní skript Ukážeme vám, které mapují virtuálních pevných disků do fondu úložiště k dispozici, můžete použít následující postup k určení mapování disku/úložiště fondu.

U každého disku postupujte následovně:

1. Získání seznamu disky připojené k virtuálnímu počítači s **Get-AzureVM** příkaz:

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Poznámka: Diskname a logické jednotky.

    ![DisknameAndLUN][2]
3. Vzdálené plochy k virtuálnímu počítači. Pak přejděte na **Správa počítače** | **Správce zařízení** | **diskové jednotky**. Podívejte se na vlastnosti každého "Microsoft virtuálních disků.

    ![VirtualDiskProperties][3]
4. Číslo logické jednotky tady se odkaz na číslo logické jednotky, kterou zadáte při připojování virtuálního pevného disku k virtuálnímu počítači.
5. "Microsoft virtuální Disk" najdete **podrobnosti** kartu, pak v **vlastnost** seznamu, přejděte na **klíč ovladače**. V **hodnotu**, Poznámka **posun**, což je 0002 na následujícím snímku obrazovky. 0002 označuje PhysicalDisk2 odkazující na fondu úložiště.

    ![VirtualDiskPropertyDetails][4]
6. Pro každý fond úložiště a vypsat si přidružené disky:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Nyní můžete pomocí těchto informací k přidružení virtuální pevné disky připojené k fyzických disků do fondů úložiště.

Jakmile se virtuální pevné disky jsou namapovány na fyzické disky do fondů úložiště můžete odpojit a zkopírujte je do účtu Premium Storage, připojte je nastavení správné mezipaměti. Podívejte se na příklad v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroky 8 až 12. Tyto kroky ukazují, jak extrahovat konfiguraci disku připojeného k virtuálnímu počítači virtuální pevný disk do souboru CSV, zkopírovat virtuální pevné disky, změnit nastavení mezipaměti disku konfigurace a nakonec znovu nasadit virtuální počítač jako DS-series virtuálních počítačů s připojenými disky.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Šířku pásma úložiště virtuálního počítače a propustnost úložiště virtuálního pevného disku
Výkon úložiště závisí na velikosti virtuálního počítače DS * zadané a velikosti virtuálního pevného disku. Virtuální počítače mají různé limity pro počet virtuálních pevných disků, které je možné připojit a maximální šířka pásma (MB/s) podporují. Čísla konkrétní šířkou pásma, naleznete v tématu [virtuálního počítače a velikost cloudových služeb pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zvýšená vstupně-výstupních operací se vytvářejí s větší velikosti disku. Měli byste zvážit berete při rozhodování o vaši cestu migrace. Podrobnosti najdete [pro vstupně-výstupních operací a typech disků najdete v tabulce](../premium-storage.md#scalability-and-performance-targets).

Nakonec je třeba zvážit, že virtuální počítače mají jiný disk maximální šířky pásma, podporující pro všechny disky připojené. V případě velkého zatížení může saturate disku maximální šířka pásma dostupná pro velikost role tohoto virtuálního počítače. Třeba Standard_DS14 podporuje až 512 MB/s Proto se tři disky P30 může saturate šířky pásma disku virtuálního počítače. Ale v tomto příkladu může být překročen limit propustnost v závislosti na kombinaci pro čtení a zápisu s Iosem.

## <a name="new-deployments"></a>Nová nasazení
V následujících dvou částech ukazují, jak nasadit virtuální počítače s SQL serverem na Premium Storage. Jak jsme zmínili, nepotřebujete nutně umístit disk s operačním systémem na Premium storage. Můžete to provést, pokud je máte v úmyslu umístit všechny úlohy náročné na vstupně-výstupní operace na virtuálním pevném disku operačního systému.

První příklad ukazuje použití existující Image z Galerie Azure. Druhý příklad ukazuje, jak použít vlastní image virtuálního počítače, které máte v existující účet úložiště úrovně Standard.

> [!NOTE]
> Tyto příklady předpokládají, že jste již vytvořili oblastní virtuální sítě.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Vytvoření nového virtuálního počítače s Premium Storage pomocí Image z Galerie
Následující příklad ukazuje, jak umístit virtuální pevný disk operačního systému do služby premium storage a připojte virtuální pevné disky Premium Storage. Můžete však také umístit disk s operačním systémem v účtu úložiště úrovně Standard a pak připojte virtuální pevné disky, které se nacházejí v účtu Premium Storage. Je ukázán oba scénáře.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Vytvoření účtu služby Premium Storage
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Vytvořte novou Cloudovou službu
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Rezervace cloudové virtuální IP ADRESE služby (volitelné)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Krok 4: Vytvoření kontejneru virtuálního počítače
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: Umístění virtuálního pevného disku na Standard nebo Premium Storage
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


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Vytvoření nového virtuálního počítače používat Premium Storage s použitím vlastní image
Tento scénář předvádí, kde se nachází existující přizpůsobené Image, které se nacházejí v účtu úložiště úrovně Standard. Jak je uvedeno, pokud chcete umístit virtuální pevný disk operačního systému na Premium Storage, budete muset zkopírovat bitovou kopii, která existuje v účtu úložiště úrovně Standard a předtím, než je možné přenést do služby Premium Storage. Pokud máte image v místním, můžete také pomocí této metody můžete zkopírovat, který přímo do účtu Premium Storage.

#### <a name="step-1-create-storage-account"></a>Krok 1: Vytvoření účtu úložiště
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Krok 2 vytvořit Cloudovou službu
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Krok 3: Použití existujícího obrázku
Můžete použít stávající bitovou kopii. Nebo můžete [trvat image existujícího počítače](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si, že počítač, který jste image nemusí být počítač DS *. Jakmile budete mít image, následující kroky ukazují, jak a zkopírujte ho do účtu Premium Storage se **Start AzureStorageBlobCopy** rutinu Powershellu.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopírování objektů Blob mezi účty úložiště
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: Pravidelně kontrolovat stav kopírování:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Přidání bitové kopie disku na disk úložiště v rámci předplatného Azure
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Může se stát, že i v případě, že stav oznámí úspěšné dokončení, může stále dostanete chyba zapůjčení disku. V takovém případě Počkejte přibližně 10 minut.
>
>

#### <a name="step-7--build-the-vm"></a>Krok 7: Vytvoření virtuálního počítače
Tady jsou vytváření virtuálního počítače z image a připojení virtuální pevné disky úložiště dvě úrovně Premium:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Stávající nasazení, které nepoužívají skupin dostupnosti Always On
> [!NOTE]
> Stávající nasazení, nejprve najdete v článku [požadavky](#prerequisites-for-premium-storage) části tohoto článku.
>
>

Existují různé důležité informace týkající se nasazení SQL serveru, které nepoužívají skupin dostupnosti Always On a ty, které provést. Pokud nepoužíváte Always On a máte existující samostatný systém SQL Server, můžete upgradovat na Premium Storage s použitím nového účtu služby a úložiště cloudu. Zvažte následující možnosti:

* **Vytvoření nového virtuálního počítače SQL serveru**. Můžete vytvořit nový virtuální počítač serveru SQL, které používají účet Premium Storage, jak je uvedeno v nových nasazení. Potom zálohování a obnovení konfigurace a uživatelské databáze SQL serveru. Aplikace je potřeba aktualizovat tak, aby odkazovaly nový SQL Server, pokud ji je přistupováno interně nebo externě. Je třeba zkopírovat všechny objekty 'mimo db", jako kdyby jste dělali migrace systému SQL Server (SxS) vedle sebe. To zahrnuje objekty, jako jsou přihlašovací údaje, certifikáty a propojené servery.
* **Migrace stávajícího virtuálního počítače SQL serveru**. To vyžaduje, aby převodu virtuálního počítače SQL serveru do režimu offline a přenesou na novou cloudovou službu, která zahrnuje kopírování všech jeho připojenými virtuálními pevnými disky do účtu Premium Storage. Když virtuální počítač převede do režimu online, aplikace musí odkazovat název hostitele serveru jako před. Mějte na paměti, že velikost existujícího disku ovlivňuje výkonové charakteristiky. Například 400 GB disk získá zaokrouhluje P20. Pokud víte, že nechcete, aby tento výkon disku, může znovu vytvořte virtuální počítač jako virtuální počítač řady DS a připojit virtuální pevné disky Storage úrovně Premium specifikace velikosti a výkonu, které potřebujete. Potom můžete odpojit a znovu připojit soubory databáze SQL.

> [!NOTE]
> Při kopírování disků VHD, byste měli vědět o velikosti, v závislosti na velikosti znamená, jaký typ disku úložiště úrovně Premium spadají do, určuje specifikace výkon disku. Zaokrouhlí číslo Azure až na nejbližší velikost disku, takže pokud máte 400 GB disk, to se zaokrouhluje P20. V závislosti na vaší stávající vstupně-VÝSTUPNÍCH požadavků virtuálního pevného disku operačního systému nemusí to migrovat do účtu Premium Storage.
>
>

Pokud se SQL serveru přistupuje externě, virtuální IP adresa cloudové služby se změní. Budete také muset aktualizace koncových bodů, seznamy řízení přístupu a DNS nastavení.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Stávající nasazení, které používají skupiny dostupnosti Always On
> [!NOTE]
> Stávající nasazení, nejprve najdete v článku [požadavky](#prerequisites-for-premium-storage) části tohoto článku.
>
>

Zpočátku v této části podíváme na Always On interakci se sítí Azure. My pak rozdělit migrace ve dvou scénářích: migrace, kde můžete nějaké prostoje tolerovat a migrace, ve kterém musí dosáhnout minimálními prostoji.

V místním SQL serveru skupin dostupnosti Always On používat naslouchací proces v místním, které se zaregistruje virtuální název DNS spolu s IP adresu, která jsou sdílena mezi jeden nebo více serverů SQL. Pokud se klienti připojují jsou směrovány naslouchací proces IP na primární server SQL. Jedná se o server, který vlastní prostředek vždy na IP v daném čase.

![DeploymentsUseAlways na][6]

V Microsoft Azure může mít jenom jedna IP adresa přiřazená síťovému rozhraní ve virtuálním počítači tak, abyste dosáhli stejnou úroveň abstrakce, jako v místním prostředí, Azure využívá IP adresu, která je přiřazena interní/externí nástroje pro vyrovnávání zatížení (ILB/ELB). Prostředku IP adresy, jež jsou sdílena mezi servery je nastaven na stejnou IP Adresou jako ILB/ELB. To je publikována ve službě DNS a přenosy klienta ILB/ELB předána do repliky primárního serveru SQL Server. ILB/ELB ví, které SQL Server je primární, protože používá sondy testovat vždy na prostředku IP adresy. V předchozím příkladu sondy každý uzel, který má koncový bod odkazuje ELB/ILB, podle toho, co reaguje je teď primárním serverem SQL.

> [!NOTE]
> ILB a ELB jsou obě přiřazené k určité Azure se Cloudová služba, proto všechny migrace do cloudu v Azure pravděpodobně znamená, že se změní IP adresu nástroje pro vyrovnávání zatížení.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrace vždy na nasazení, která umožňuje výpadek
Existují dva strategie migrace vždy na nasazení, umožňující pro určitý výpadek:

1. **Přidání více sekundárních replik ve stávajícím vždy na clusteru**
2. **Migrovat do nového vždy na clusteru**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Přidání více sekundárních replik ve stávajícím vždy na clusteru
Chcete-li přidat další sekundární databáze skupiny dostupnosti Always On je jednou z možných strategií. Budete muset přidat do nové cloudové služby a aktualizovat naslouchací proces s novou IP adresu nástroje pro vyrovnávání zatížení.

##### <a name="points-of-downtime"></a>Body výpadku:
* Ověření clusteru.
* Testování vždy na převzetí služeb při selhání pro novou sekundární databáze.

Pokud používáte fondy úložišť systému Windows v rámci virtuálního počítače pro vyšší propustnost vstupně-výstupních operací, pak tyto pocházejí offline během úplného ověření clusteru. Ověřovací test je potřeba při přidání uzlů do clusteru. Čas potřebný ke spuštění testu se může lišit, a proto byste měli otestovat ve vašem reprezentativního testovacího prostředí a jak dlouho o tom, jak dlouho to trvá získání.

Čas, kde můžete provést ruční převzetí služeb při selhání a testování chaos na nově přidané uzly, aby byla funkce vysoká dostupnost AlwaysOn podle očekávání, měli byste zřídit.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Všechny instance systému SQL Server, kde se používají fondy úložiště by se měla zastavit před spuštěním ověření.
>
> ##### <a name="high-level-steps"></a>Postup vysoké úrovně
>

1. Vytvoření dvou nových serverů SQL v nové cloudové službě pomocí připojené služby Premium Storage.
2. Zkopírovat úplné zálohování a obnovení s **NORECOVERY**.
3. Zkopírovat "mimo uživatele DB" závislé objekty, jako jsou přihlášení atd.
4. Vytvořit nové nové nástroje pro vyrovnávání pro interní zatížení (ILB) nebo použijte externí Load Balancer (ELB) a potom nastavit koncové body s vyrovnáváním zatížení na obou uzlech nového.

   > [!NOTE]
   > Zkontrolujte, že všechny uzly mají správnou konfiguraci koncového bodu předtím, než budete pokračovat
   >
   >
5. Zastavte přístup uživatele nebo aplikaci SQL Server (Pokud používáte fondy úložišť).
6. Zastavte služby stroje SQL serveru na všech uzlech (Pokud používáte fondy úložišť).
7. Přidání nových uzlů do clusteru a spuštění úplného ověření.
8. Po úspěšném ověření spusťte všechny služby SQL Server.
9. Zálohování protokolů transakcí a obnovení uživatelské databáze.
10. Přidat nové uzly do skupiny dostupnosti Always On a umístěte replikace do **synchronní**.
11. Přidat prostředek IP adresy z nové cloudové služby ILB/ELB prostřednictvím prostředí PowerShell pro Always On na základě Multi-Site příkladu v [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). V clusteringu s Windows, nastavte **Možní vlastníci** z **IP adresu** prostředek, který se stará nové uzly. Najdete v části "Přidání prostředku IP adresy ve stejné podsíti" [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Převzetí služeb při selhání pro jeden z nových uzlů.
13. Vytvořit nové uzly partnery automatické převzetí služeb při selhání a testovací převzetí služeb při selhání.
14. Odeberte původní uzlů ze skupiny dostupnosti.

##### <a name="advantages"></a>Výhody
* Nový SQL servery můžou být testován (SQL Server a aplikace) předtím, než se přidají do Always On.
* Můžete změnit velikost virtuálního počítače a přizpůsobit úložiště přesných požadavcích. Nicméně by bylo výhodné ponechat stejné všechny cesty k souborům SQL.
* Můžete řídit při spuštění přenosu zálohy databáze do sekundárních replik. Tím se liší od používání Azure **Start AzureStorageBlobCopy** rutiny zkopírovat virtuální pevné disky, protože se jedná o asynchronní kopírování.

##### <a name="disadvantages"></a>Nevýhody
* Při použití fondů úložišť systému Windows, je během úplné ověření clusteru pro nové další uzly clusteru výpadek.
* V závislosti na verzi SQL serveru a stávající počet sekundárních replik nemusí být možné přidat více sekundárních replik bez odebrání existujícího sekundárních replik.
* Může dojít při nastavování sekundární databáze dlouhou dobu přenosu dat SQL.
* Existuje další náklady během migrace, dokud máte nové počítače, které běží paralelně.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrovat do nového vždy na clusteru
Jiné strategií je vytvořit úplně novou vždy na Cluster s úplně nové uzly v novou cloudovou službu a pak přesměrovat klienty a použijte ji.

##### <a name="points-of-downtime"></a>Body výpadku
Při přenosu aplikací a uživatelů na nový naslouchací proces Always On nedochází k výpadkům. Výpadek, závisí na:

* Čas potřebný k obnovení zálohy protokolu transakce konečné databází na nové servery.
* Čas potřebný k aktualizaci klienta aplikace, aby používaly nové stálé naslouchacího procesu.

##### <a name="advantages"></a>Výhody
* Skutečné produkční prostředí, SQL Server, můžete otestovat a změny sestavení operačního systému.
* Máte možnost přizpůsobit úložiště a potenciálně zmenšit velikost virtuálního počítače. To může způsobit snížení nákladů.
* Během tohoto procesu můžete aktualizovat sestavení systému SQL Server nebo verzi. Můžete také upgradovat operační systém.
* Předchozí vždy na clusteru může fungovat jako cíl solid vrácení zpět.

##### <a name="disadvantages"></a>Nevýhody
* Budete muset změnit název DNS naslouchacího procesu, pokud chcete, aby obě vždy na clustery s podporou současně. Tento postup přidá správu režii během migrace jako řetězce klientské aplikace musí odpovídat názvu nové naslouchací proces.
* Je nutné implementovat mechanismus synchronizace mezi těmito dvěma prostředími Novoroční co nejblíže minimalizujte požadavky poslední synchronizace před migrací.
* Během migrace se je dodatečné náklady při spuštění nového prostředí.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrace vždy na nasazení minimálními výpadky
Existují dva strategie migrace nasazení Always On pro minimálními prostoji:

1. **Využívat stávající sekundární: jedné lokalitě**
2. **Využívat stávající sekundární následující: Multi-Site**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Využívat stávající sekundární: jedné lokalitě
Jednou z možných strategií pro minimálními prostoji je využít existující cloud sekundární a odebrat ho z aktuálního cloudové služby. Potom zkopírujte virtuální pevné disky do nového účtu služby Premium Storage a vytvoření virtuálního počítače v nové cloudové službě. Pak aktualizujte naslouchací proces ve clustering a převzetí služeb při selhání.

##### <a name="points-of-downtime"></a>Body výpadku
* Při aktualizaci poslední uzel s koncovým bodem s vyrovnáváním zatížení nedochází k výpadkům.
* Vaše nové připojení klienta můžou být zpožděné v závislosti na konfiguraci klienta a DNS.
* Pokud se rozhodnete převést skupinu vždy na clusteru do režimu offline vyměnit IP adresy je další prostoje. Vyhnete použití nebo závislostí a možných vlastníků pro přidání prostředku IP adresy. Najdete v části "Přidání prostředku IP adresy ve stejné podsíti" [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Při přidání uzlu do zúčastnit se v jako vždy na převzetí služeb při selhání Partner, budete muset přidat koncový bod Azure s odkazem na zatížení vyrovnávat sady. Při spuštění **Add-AzureEndpoint** příkazu k tomu, aktuální počet připojení zůstat otevřít, ale nebudou moci být navázán, dokud se aktualizovala nástroje pro vyrovnávání zatížení nových připojení k naslouchacímu procesu. Při testování to byla zobrazena na posledních 90 120seconds, by měl být testován.
>
>

##### <a name="advantages"></a>Výhody
* Bez dalších nákladů, které vzniknou během migrace.
* 1: 1 migrace.
* Menší složitost.
* Umožňuje vyšší vstupně-výstupních operací z skladové položky Storage úrovně Premium. Pokud disky jsou odpojen od virtuálního počítače a zkopírován do novou cloudovou službu, 3. stran nástroj lze použít ke zvýšení velikosti virtuálního pevného disku, které poskytuje vyšší propustnost. Zvýšení velikosti virtuálního pevného disku, najdete v tomto [diskuzi na fóru](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nevýhody
* Během migrace je k dočasné ztrátě vysokou dostupnost a zotavení po Havárii.
* Toto je 1:1 migrace, budete muset použít minimální velikost virtuálního počítače, který podporuje váš Počet virtuálních pevných disků, takže nebudete moci downsize vašich virtuálních počítačů.
* V tomto scénáři byste použili Azure **Start AzureStorageBlobCopy** rutinu, která je asynchronní. Po dokončení kopírování neexistuje žádná smlouva SLA. Čas kopie se liší, přestože to závisí na čekání ve frontě, které také závisí na množství dat pro přenos. Kopírování času zvětší, když probíhá přenos do jiného datového centra Azure, který podporuje službu Premium Storage v jiné oblasti. Pokud máte pouze 2 uzly, zvažte v případě, že kopírování trvá déle než při testování možných zmírnění. To může zahrnovat následující nápady.
  * Před migrací dohodnutém výpadků přidáte dočasný uzel 3. systém SQL Server pro vysokou dostupnost.
  * Spustíte migraci mimo Azure plánované údržby.
  * Ujistěte se, že jste správně nakonfigurovali vaše kvorum clusteru.  

##### <a name="high-level-steps"></a>Postup vysoké úrovně
Tento dokument neukazuje kompletní kompletní příklad, ale [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) poskytuje podrobné informace, které můžete využít k této.

![MinimalDowntime][8]

* Shromáždit konfiguraci disku a odebrání uzlu (neodstraňujte připojenými virtuálními pevnými disky).
* Vytvoření účtu služby Premium Storage a zkopírovat virtuální pevné disky z účtu úložiště úrovně Standard
* Vytvořit novou cloudovou službu a opětovné nasazení virtuálního počítače SQL2 v dané cloudové službě. Vytvořte virtuální počítač pomocí zkopírovaného původního operačního systému virtuálního pevného disku a připojení zkopírovaný virtuální pevné disky.
* Konfigurace ILB / ELB a přidají koncové body.
* Naslouchací proces aktualizace buď:
  * Vždy ve skupině pro přepnutí do offline režimu a aktualizuje vždy na naslouchací proces s novou ILB / ELB IP adres.
  * Nebo přidání prostředku IP adresy z nové cloudové služby ILB/ELB prostřednictvím prostředí PowerShell do Windows clustering. Potom nastavte možných vlastníků prostředku IP adresy k uzlu migrované SQL2 a to jako závislost nebo. v názvu sítě. Najdete v části "Přidání prostředku IP adresy ve stejné podsíti" [příloha](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Zkontrolujte konfiguraci DNS/šíření klientům.
* Migrace virtuálního počítače SQL1 a projít kroky 2 – 4.
* Pokud používáte 5ii kroky, přidejte počítač SQL1 jako možného vlastníka pro přidání prostředku IP adresy
* Testovací převzetí služeb při selhání.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Využívat stávající sekundární následující: Multi-Site
Pokud máte uzly ve více než jednoho datového centra Azure (DC) nebo pokud máte hybridní prostředí, pak můžete konfiguraci Always On v tomto prostředí minimalizovat tak prostoje.

Tento přístup je změna synchronizace Always On na synchronní v místním nebo sekundární řadič domény Azure, a potom klepněte na převzetí služeb při selhání přes k danému serveru SQL. Poté zkopírovat virtuální pevné disky do účtu Premium Storage a znovu nasaďte tento počítač do nové cloudové služby. Aktualizujte naslouchací proces a navrácení služeb po obnovení.

##### <a name="points-of-downtime"></a>Body výpadku
Výpadek se skládá z doby převzetí služeb při selhání na alternativní řadiče domény a zpět. Také závisí na konfiguraci klienta/DNS a opětovné připojení vašeho klienta se může zpozdit.
Vezměte v úvahu následující příklad hybridní Always On konfigurace:

![MultiSite1][9]

##### <a name="advantages"></a>Výhody
* Můžete využít stávající infrastrukturu.
* Máte možnost předem nejdříve upgradujte Azure storage na řadiči domény zotavení po Havárii Azure.
* Můžete třeba překonfigurovat DC zotavení po Havárii Azure storage.
* Existuje minimálně dva převzetí služeb při selhání během migrace, s výjimkou testovacího převzetí služeb při selhání.
* Není potřeba přesunout data systému SQL Server pomocí zálohování a obnovení.

##### <a name="disadvantages"></a>Nevýhody
* V závislosti na klientský přístup k systému SQL Server může být vyšší latence při systém SQL Server běží v alternativní řadiče domény na aplikaci.
* Může být dlouhý čas kopírování virtuálních pevných disků Premium storage. To může ovlivnit vaše rozhodnutí, zda chcete zachovat uzlu ve skupině dostupnosti. Zvažte proto pro při protokolu náročné na pracovní zatížení jsou spuštěny během migrace je nutné použít, protože primární uzel musí zachovat nereplikovaných transakce v protokolu transakcí. Proto to může výrazně zvýší.
* V tomto scénáři byste použili Azure **Start AzureStorageBlobCopy** rutinu, která je asynchronní. Neexistuje žádná smlouva SLA při dokončení. Čas kopie se liší od, přestože to závisí na čekání ve frontě, také závisí na množství dat pro přenos. Proto ve vašem datovém centru 2. stačí jeden uzel, byste měli podniknout kroky pro zmírnění rizika v případě, že kopírování trvá déle než při testování. Tyto kroky pro zmírnění rizika patří následující návrhy:
  * Před migrací dohodnutém výpadků přidáte dočasný 2. uzel SQL pro vysokou dostupnost.
  * Spustíte migraci mimo Azure plánované údržby.
  * Ujistěte se, že jste správně nakonfigurovali vaše kvorum clusteru.

Tento scénář předpokládá, že můžete mít dokumentované vaší instalaci a vědět, jak je úložiště mapována, aby bylo možné provést změny nastavení mezipaměti optimální disku.

##### <a name="high-level-steps"></a>Postup vysoké úrovně
![Multisite2][10]

* Ujistěte se, že místní / řadiče domény v Azure do alternativního primárního serveru SQL a nastavte ji jiné automatické převzetí služeb při selhání partnera (AFP).
* Shromážděte informace o konfiguraci disku z SQL2 a odeberte uzel (neodstraňujte připojenými virtuálními pevnými disky).
* Vytvoření účtu služby Premium Storage a zkopírovat virtuální pevné disky z účtu úložiště úrovně Standard.
* Vytvořit novou cloudovou službu a vytvoření virtuálního počítače SQL2 s jeho bonusech úložné disky připojené.
* Konfigurace ILB / ELB a přidají koncové body.
* Aktualizujte vždy na naslouchací proces s novou ILB / ELB IP adres a test převzetí služeb při selhání.
* Zkontrolujte konfiguraci DNS.
* Změnit AFP SQL2, migrovat počítač SQL1 a projít kroky 2 – 5.
* Testovací převzetí služeb při selhání.
* Přepněte zpátky na počítač SQL1 a SQL2 AFP

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrace na Premium Storage nasazení ve více lokalitách vždy v clusteru
Zbývající část tohoto článku poskytuje podrobný příklad převodu Always On cluster více lokalit na Premium storage. Převede také naslouchací proces pomocí externí nástroj pro vyrovnávání zatížení (ELB) interní nástroj pro vyrovnávání zatížení (ILB).

### <a name="environment"></a>Prostředí
* Windows 2k 12 / SQL 2k 12
* 1 DB soubory na SP
* 2 x fondy úložiště na každý uzel

![Appendix1][11]

### <a name="vm"></a>VIRTUÁLNÍ POČÍTAČ:
V tomto příkladu budeme k předvedení přesunutí ze ELB pro ILB. ELB byla dostupná před ILB, takže to ukazuje, jak přepnout na ILB během migrace.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Před kroky: Připojte se k předplatnému
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Vytvoření nového účtu úložiště a cloudové služby
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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zvyšte povolených chyb pro prostředky <Optional>
Na některé prostředky, které patří do vaší skupiny dostupnosti Always On se vztahují omezení počtu chyb, které mohou nastat v období, ve kterém pokusí restartovat skupinu prostředků clusteru služby. Doporučuje se, že zvýšíte tím přitom jste se provede tento postup od Pokud to neuděláte ruční převzetí služeb při selhání a aktivaci převzetí služeb při selhání vypněte počítače, na kterých můžete získat blízko tento limit.

Bylo by vhodné dvojitá příspěvek selhání, chcete-li to provést v modulu Správce clusteru převzetí služeb při selhání přejděte do vlastností skupiny prostředků Always On:

![Appendix3][13]

Změňte maximální počet selhání na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Přidání IP adresu prostředku pro skupinu clusteru <Optional>
Pokud máte pouze jednu IP adresu pro skupinu clusteru a to je umístěno na podsíť cloud, mějte na paměti, pokud jste omylem převést do režimu offline všechny uzly clusteru v cloudu v této síti prostředku IP adresy clusteru a název sítě s clustery se moct do režimu online. V takovém případě brání aktualizace pro ostatní prostředky clusteru.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfigurace služby DNS
Implementace s hladkým přechodem závisí na tom, jak DNS se používá a je aktualizovat.
Always On je nainstalován, vytvoří skupinu prostředků clusteru Windows, pokud otevřete Správce clusteru převzetí služeb při selhání, zobrazí se, že minimálně má tři prostředky, které dokumentu odkazuje na dvě:

* Název virtuální sítě (VNN) – název DNS, který klienti připojit k, když se chce připojit k SQL serverům prostřednictvím Always On.
* Prostředek IP adresy – IP adresy, které jsou přidružené k VNN, může mít více než jeden a v konfiguraci ve více lokalitách mají jednotlivé lokality a podsítě IP adresu.

Při připojování k serveru SQL Server, SQL Server Client ovladač načte záznamy DNS přidružené naslouchací proces a pokusí se připojit k každý Always On přidružené IP adresy. Dále probereme některé faktory, které mohou mít vliv na to.

Počet souběžných záznamy DNS, které jsou přidružené naslouchací proces s názvem závisí nejen na počet IP adres přidruženy, ale "RegisterAllIpProviders'setting v pro prostředek Always ON VNN Clustering převzetí služeb při selhání.

Když nasadíte Always On v Azure existují různé kroky k vytvoření naslouchacího procesu a IP adresy, budete muset ručně konfigurovat 'RegisterAllIpProviders' na hodnotu 1, tím se liší v místním vždy na nasazení, kde je již nastaven na hodnotu 1.

Pokud 'RegisterAllIpProviders' na hodnotu 0, pak vám zobrazovat jenom jeden záznam DNS v DNS přidružené naslouchací proces:

![Appendix4][14]

Pokud 'RegisterAllIpProviders' je 1:

![Appendix5][15]

Následující kód vypíše VNN nastavení a nastaví ji za vás. Tato změna projevila budete muset VNN převést do režimu offline a znovu je zapnout zpět do online režimu. Tato akce trvá naslouchací proces offline způsobující přerušení připojení klienta.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

V pozdějším kroku migrace budete muset aktualizovat naslouchací proces Always On s aktualizovanou IP adresu, která odkazuje na nástroj pro vyrovnávání zatížení, to zahrnuje odstranění prostředku IP adresy a přidání služby. Po aktualizaci IP je potřeba zajistit aktualizovali jsme nové IP adresy v zóně DNS a, že klienti jsou aktualizaci jejich místní mezipaměť DNS.

Pokud vaši klienti nacházejí v různých síťových segmentů a odkazovat na jiný server DNS, je potřeba zvážit, co se stane o přenos zóny DNS během migrace, jak připojit aplikaci čas je omezená minimální doby přenosu zóny žádné nové IP adresy adresy pro naslouchací proces. Pokud jste v rámci omezení času tady, by měl popisují a otestovat vynucení přenosem přírůstkové zóny s týmy na jednom místě Windows a také umístit záznam hostitele DNS pro nižší Live TTL (Time To), takže klienti stahují. Další informace najdete v tématu [přírůstkové přenosy zóny](https://technet.microsoft.com/library/cc958973.aspx) a [Start DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Výchozí hodnota TTL pro DNS záznam, který je spojen s naslouchacím procesem v Always On v Azure je 1 200 sekund. Můžete chtít omezit to, pokud jste v části čas omezení při migraci zajistit, klienti aktualizovat svoje DNS aktualizované IP adresu pro naslouchací proces. Můžete zobrazit a upravit konfiguraci vypsání navýšení kapacity konfigurací VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> Čím nižší "HostRecordTTL" větší počet provoz DNS dochází.

##### <a name="client-application-settings"></a>Nastavení klienta aplikace
Pokud klientské aplikace SQL podporuje rozhraní .net 4.5 SQLClient a pak můžete použít "MULTISUBNETFAILOVER = TRUE" – klíčové slovo. Toto klíčové slovo bude použito, protože to umožňuje rychlejší připojení k SQL skupiny dostupnosti Always On během převzetí služeb při selhání. Vytvoří výčet prostřednictvím všechny IP adresy přidružené k naslouchání Always On paralelně a provede agresivnější rychlost opakovat připojení TCP při selhání.

Další informace o předchozích nastavení najdete v tématu [MultiSubnetFailover – klíčové slovo a související funkce](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Viz také [podpora klienta SqlClient pro vysokou dostupnost, zotavení po havárii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: Nastavení kvora clusteru
Jak chcete být pořizujících si aspoň jeden Server SQL dolů v čase, byste měli upravit nastavení kvora clusteru, pokud používáte soubor sdílenou složku s kopií clusteru (FSW) se dvěma uzly, měli byste nastavit povolit Většina uzlů a využívat dynamické hlasování kvora , umožňuje jeden uzel k zajištění stálého.

    Set-ClusterQuorum -NodeMajority  

Další informace o správě a konfiguraci kvora clusteru najdete v tématu [konfigurace a správa kvora v clusteru převzetí služeb při selhání ve Windows serveru 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Extrahování stávající koncové body a seznamy ACL
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Uložte následující text do souboru.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Změna režimů replikace a převzetí služeb při selhání partnerů
Pokud máte více než dvěma SQL servery, by měl změnit převzetí služeb při selhání jinou sekundární databáze v jiné řadiče domény nebo místní "Synchronní" a nastavte ji automatické převzetí služeb při selhání partnera (protokol AFP), jedná se proto udržovat vysokou dostupnost, zatímco provádíte změny. Můžete to provést prostřednictvím TSQL z upravit ale SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Odebrání sekundárního virtuálního počítače z cloudové služby
By měl být plánování migrace do cloudu sekundárního uzlu nejprve. Pokud tento uzel je aktuálně primárním, by mělo zahájit ruční převzetí služeb při selhání.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Změna nastavení v souboru CSV mezipaměti disku a uložit
Pro datové svazky ty je třeba nastavit na jen pro čtení.

Tlog určené svazky ty by měla být nastavena na hodnotu NONE.

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



Můžete zkontrolovat stav kopírování virtuálních pevných disků do účtu Premium Storage:

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

Počkejte, dokud všechny tyto informace jsou zaznamenány jako úspěch.

Informace pro jednotlivé objekty BLOB:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Krok 11: Registr operačního systému disku
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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Importujte sekundární do nové cloudové služby
Následující kód používá také možnost přidání Tady můžete import počítače a použít retainable virtuálních IP adres.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: Vytvoření ILB na nové Svc cloudu, přidat zatížení vyrovnávat koncových bodů a seznamy ACL
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

#### <a name="step-14-update-always-on"></a>Krok 14: Aktualizujte vždy na
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

Teď odeberte starý cloudovou službu IP adresu.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: Kontrola aktualizací DNS
By měl nyní zkontrolujte servery DNS ve vašich sítích klienta systému SQL Server a ujistěte se, clustering přidal záznam další hostitele pro přidání IP adresu. Pokud tyto servery DNS neaktualizovali, zvažte vynucení přenos zóny DNS a ujistěte se, že klienti v existuje podsíť budou moct přeložit na obě vždy na IP adresy, jedná se proto není potřeba počkat automatickou replikaci DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: Změna konfigurace vždy na
V tomto okamžiku je počkat sekundární tohoto uzlu, která byla migrována do plně opakovanou synchronizaci s místním uzlem a přejděte do uzlu synchronní replikace a nastavte ji protokol AFP.  

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Změna nastavení v souboru CSV mezipaměti disku a uložit
Pro datové svazky je třeba nastavit nastavení mezipaměti na jen pro čtení.

Nastavení mezipaměti tlog určené svazky, by měla být nastavena na hodnotu NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Vytvoření nového účtu úložiště nezávisle pro sekundární uzel
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


Můžete zkontrolovat stav kopírování virtuálního pevného disku pro všechny virtuální pevné disky: ForEach ($disk v $diskobjects) {$lun = $disk. Logická jednotka $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Jmenovka_disku $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Počkejte, dokud všechny tyto informace jsou zaznamenány jako úspěch.

Informace pro jednotlivé objekty BLOB:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Krok 21: Registr operačního systému disku
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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Přidání Load Balanced koncových bodů a seznamy ACL
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
Počkejte migrované uzel k synchronizaci s místní vždy na uzlu. Umístěte ho do režimu synchronní replikace a počkejte, dokud se synchronizují. Pak převzetí služeb při selhání z místního na prvním uzlu migrovat, což je protokol AFP. Jakmile, který pracoval, změňte poslední uzel migrované na protokol AFP.

By měl test převzetí služeb při selhání mezi všemi uzly a spustit testy chaos a zajistit převzetí služeb při selhání fungovat stejně jako očekávané a v včas manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Mělo vrátit nastavení kvora clusteru / hodnotu TTL pro DNS a převzetí služeb při selhání Pntrs / nastavení synchronizace
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Přidání prostředku IP adresy ve stejné podsíti
Pokud máte jenom dva servery SQL a chcete migrovat na novou cloudovou službu, ale chtějí, aby ve stejné podsíti, je možné vyhnete se tak naslouchací proces do offline režimu odstranit původní vždy na IP adresu a přidat nové IP adresy. Pokud provádíte migraci virtuálních počítačů do jiné podsítě, nepotřebujete k tomu je síť další clusteru, která odkazuje na této podsíti.

Po zařazení nahoru migrovaná sekundární a přidá nový prostředek IP adresu pro novou cloudovou službu před převzetí služeb při selhání existující primární, byste měli provést tyto kroky v rámci Správce clusteru převzetí služeb při selhání:

Pokud chcete přidat IP adresu, najdete v článku [příloha](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), kroku 14.

1. Aktuální IP adresu prostředku změňte možných vlastníků "Existující primární SQL Server", v tomto příkladu "dansqlams4":

    ![Appendix13][23]
2. Pro nový prostředek, IP adresa změňte možných vlastníků "Migrováno sekundární SQL Server", v tomto příkladu "dansqlams5":

    ![Appendix14][24]
3. Jakmile je tato možnost nastavená, můžete převzetí služeb při selhání a po migraci oznámení o poslední uzel mezi možnými vlastníky by měl být upraven tak, že tento uzel se přidá jako možného vlastníka:

    ![Appendix15][25]

## <a name="additional-resources"></a>Další zdroje informací:
* [Azure Premium Storage](../premium-storage.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
