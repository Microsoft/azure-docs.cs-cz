---
title: Linuxových výpočetních virtuálních počítačů v clusteru služby HPC Pack | Microsoft Docs
description: Postup vytvoření a používání clusteru HPC Pack v Azure pro Linux s vysokým výkonem úloh (prostředí HPC)
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841689"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Začínáme s výpočetními uzly Linuxu v clusteru HPC Pack v Azure
Nastavení [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) clusteru v Azure, který obsahuje hlavního uzlu se systémem Windows Server a několika výpočetních uzlech systémem podporované distribuce systému Linux. Prozkoumejte možnosti pro přesun dat mezi uzly Linux a Windows hlavnímu uzlu clusteru. Zjistěte, jak k odesílání úloh Linux HPC do clusteru.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Následující diagram znázorňuje na vysoké úrovni clusteru HPC Pack vytváření a práci s.

![Cluster HPC Pack s uzly Linux][scenario]

Další možnosti spuštění úloh HPC pro Linux v Azure najdete v tématu [technických prostředcích pro batch a vysoce výkonné výpočty](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Nasazení clusteru HPC Pack s Linux výpočetních uzlů
Tento článek ukazuje, že jste dvě možnosti pro nasazení clusteru HPC Pack v Azure s Linuxem výpočetních uzlů. Obě metody k vytvoření hlavního uzlu použít image pořízenou prostřednictvím Marketplace systému Windows Server s HPC Pack. 

* **Šablona Azure Resource Manageru** -použít šablonu z Azure Marketplace nebo šabloně pro rychlý start od komunity, k automatizovanému vytvoření clusteru v modelu nasazení Resource Manager. Například [clusteru HPC Pack pro Linux úlohy](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) šablony v Azure Marketplace vytvoří kompletní infrastruktura clusteru HPC Pack pro Linux HPC úlohy.
* **Skript prostředí PowerShell** -použití [skript nasazení Microsoft HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) k automatizaci nasazení clusterů v modelu nasazení classic. Tento skript prostředí Azure PowerShell používá bitovou kopii prostředí HPC Pack virtuálních počítačů v Azure Marketplace pro rychlé nasazení a poskytuje komplexní sadu parametrů konfigurace pro nasazení systému Linux výpočetních uzlů.

Další informace o možnostech nasazení clusteru HPC Pack v Azure najdete v tématu [clusteru možnosti k vytváření a správě vysoce výkonné výpočty (HPC) v Azure pomocí sady Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Požadavky
* **Předplatné Azure** -předplatné můžete použít ve službě Azure globální nebo Azure China. Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) si během několika minut.
* **Kvóta jader** -může být potřeba zvýšit kvótu jádra, obzvláště pokud se rozhodnete nasadit několik uzlů clusteru s vícejádrovými velikosti virtuálních počítačů. Chcete-li zvýšit kvótu, otevřete žádosti o podporu online zákazníka zdarma.
* **Linuxových distribucích** -aktuálně HPC Pack podporuje následující Linuxových distribucích pro výpočetní uzly. Můžete použít Marketplace verze těchto distribuce, pokud jsou k dispozici, nebo zadat vlastní.
  
  * **Na základě centOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, verze 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 pro prostředí HPC, SLES 12 pro HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Pro použití sítě Azure RDMA s jedním z velikosti virtuálních počítačů podporuje RDMA, zadejte SUSE Linux Enterprise Server 12 HPC nebo na základě CentOS HPC bitovou kopii z Azure Marketplace. Další informace najdete v tématu [vysokovýkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Další požadavky na nasazení clusteru pomocí skriptu prostředí HPC Pack IaaS nasazení:

* **Klientský počítač** -potřebujete počítač klienta se systémem Windows pro spuštění skriptu nasazení clusteru.
* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skript nasazení HPC Pack IaaS** – stáhněte a rozbalte nejnovější verzi skript z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verze skriptu můžete zkontrolovat spuštěním `.\New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verzi 4.4.1 nebo později skript.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Možnost nasazení 1. Pomocí šablony Resource Manageru
1. Přejděte na [clusteru HPC Pack pro Linux úlohy](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) šablony Azure Marketplace, a klikněte na **nasadit**.
2. Na portálu Azure, zkontrolujte informace a pak klikněte na tlačítko **vytvořit**.
   
    ![Vytvoření portálu][portal]
3. Na **Základy** okno, zadejte název clusteru, který také názvy hlavního uzlu virtuálního počítače. Můžete vybrat existující skupinu prostředků nebo vytvořte skupinu pro nasazení v umístění, které je k dispozici. Umístění má vliv na dostupnost určité velikosti virtuálních počítačů a dalším službám Azure (viz [produkty podle oblasti](https://azure.microsoft.com/regions/services/)).
4. Na **hlavního uzlu nastavení** okně pro první nasazení, můžete obvykle přijmout výchozí nastavení. 
   
   > [!NOTE]
   > **Adresu URL skriptu po konfiguraci** je volitelné nastavení k určení veřejně dostupné skript prostředí Windows PowerShell, který chcete spustit z hlavního uzlu virtuálního počítače, jakmile je spuštěna. 
   > 
   > 
5. Na **výpočetní uzel nastavení** okně vyberte pojmenování vzor pro uzly, počet a velikost uzlů a distribuci systému Linux k nasazení.
6. Na **nastavení infrastruktury** okno, zadejte názvy pro virtuální sítě a služby Active Directory domény, domény a přihlašovací údaje Správce virtuálních počítačů a vzoru pro pojmenovávání pro účty úložiště.
   
   > [!NOTE]
   > HPC Pack používá domény služby Active Directory k ověřování uživatelů clusteru. 
   > 
   > 
7. Po spuštění ověřovacích testů a přečtěte si podmínky použití, klikněte na tlačítko **nákupu**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Možnost nasazení 2. Pomocí tohoto skriptu nasazení IaaS
Toto jsou další požadavky na nasazení clusteru pomocí skriptu prostředí HPC Pack IaaS nasazení:

* **Klientský počítač** -potřebujete počítač klienta se systémem Windows pro spuštění skriptu nasazení clusteru.
* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skript nasazení HPC Pack IaaS** – stáhněte a rozbalte nejnovější verzi skript z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verze skriptu můžete zkontrolovat spuštěním `.\New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verzi 4.4.1 nebo později skript.

**Konfigurační soubor XML.**

Skript nasazení HPC Pack IaaS používá k popisu clusteru HPC konfigurační soubor XML jako vstup. Následující vzorový konfigurační soubor Určuje malé cluster obsahuje hlavnímu uzlu HPC Pack a dvě velikost A7 CentOS 7.0 Linux výpočetních uzlů. 

Upravte soubor podle potřeby pro vaše prostředí a konfiguraci požadovaných clusteru a uložte s názvem, jako je například HPCDemoConfig.xml. Například musíte zadat název vašeho odběru a název účtu úložiště jedinečný a cloudových název služby. Kromě toho můžete vybrat jiný obrázek Linux podporované pro výpočetní uzly. Další informace o elementy v konfiguračním souboru, najdete v souboru Manual.rtf ve složce skriptu a [vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Pro spuštění skriptu nasazení HPC Pack IaaS**

1. Otevřete prostředí Windows PowerShell v klientském počítači jako správce.
2. Změnit adresář na složku, kde je skript nainstalovaný (E:\IaaSClusterScript v tomto příkladu).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Spusťte následující příkaz k nasazení clusteru HPC Pack. Tento příklad předpokládá, že konfigurační soubor nachází v E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Protože **AdminPassword** není zadané v předchozí příkaz, zobrazí se výzva k zadání hesla pro uživatele *MyAdminName*.
   
    b. Chcete-li ověřit konfigurační soubor pak spustí skript. Může trvat až několik minut v závislosti na síťové připojení.
   
    ![Ověření][validate]
   
    c. Po ověření úspěšně, skript zobrazí seznam prostředků clusteru k vytvoření. Zadejte *Y* pokračujte.
   
    ![Zdroje a prostředky][resources]
   
    d. Skript spustí nasazení clusteru HPC Pack a dokončení konfigurace bez další ruční kroky. Tento skript můžete spustit několik minut.
   
    ![Nasazení][deploy]
   
   > [!NOTE]
   > V tomto příkladu skript generuje soubor protokolu automaticky od **- LogFile** není zadán parametr. Protokoly nezapisují v reálném čase, ale se shromažďují na konci ověření a nasazení. Pokud proces prostředí PowerShell je zastavena, když je skript spuštěn, některé protokoly jsou ztraceny.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Připojení k hlavnímu uzlu
Po nasazení clusteru HPC Pack v Azure, [připojit pomocí vzdálené plochy](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k hlavnímu uzlu virtuálních počítačů využívající tuto doménu můžete zadané údaje po nasazení clusteru (například *hpc\\clusteradmin*). Spravujete z hlavního uzlu clusteru.

Z hlavního uzlu spusťte Správce clusteru HPC a zkontrolujte stav clusteru HPC Pack. Můžete spravovat a monitorování Linuxových výpočetních uzlů stejným způsobem jako pracujete s Windows výpočetních uzlů. Například zobrazí Linuxových uzlů uvedené v **Správa prostředků** (tyto uzly jsou nasazeny pomocí **LinuxNode** šablony).

![Uzel správy][management]

Zobrazí také Linux uzlů **Heat mapa** zobrazení.

![Heat mapa][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Jak přesunout data v clusteru s uzly Linux
Máte několik možností pro přesun dat mezi uzly Linux a Windows hlavnímu uzlu clusteru. Tady jsou tři běžné metody další podrobně popsány v následujících částech:

* **Azure File** -zpřístupní spravované sdílení souborů SMB pro ukládají datové soubory v úložišti Azure. Uzly Windows a Linux uzly můžete připojit Azure sdílené složky jako jednotky nebo složku ve stejnou dobu, i v případě, že nasazené v různých virtuálních sítích.
* **Sdílet hlavního uzlu SMB** -připojí standardní sdílené složky systému Windows z hlavního uzlu na Linuxových uzlů.
* **Server systému souborů NFS uzlu HEAD** -poskytuje řešení sdílení souborů pro smíšená prostředí systému Windows a Linux.

### <a name="azure-file-storage"></a>Úložiště Azure File
[Azure File](https://azure.microsoft.com/services/storage/files/) služby zpřístupní sdílené složky, které používají standardní protokol SMB 2.1. Virtuální počítače Azure a cloudových služeb můžou sdílet souborová data mezi komponentami aplikace přes sdílené složky a místní aplikace můžou k souborovým datům ve sdílené složce prostřednictvím rozhraní API úložiště File. 

Podrobné pokyny k vytvoření Azure sdílené složky a připojte ho z hlavního uzlu, najdete v části [Začínáme s Azure File storage ve Windows](../../../storage/files/storage-how-to-use-files-windows.md). Připojit sdílenou složku Azure File na Linuxových uzlů, najdete v části [postup používání Azure File storage s Linuxem](../../../storage/files/storage-how-to-use-files-linux.md). Nastavení trvalých připojení, naleznete v tématu [Persisting připojení k Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

V následujícím příkladu vytvoření účtu úložiště Azure sdílené složky. Chcete-li připojit sdílenou složku z hlavního uzlu, otevřete příkazový řádek a zadejte následující příkazy:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

V tomto příkladu allvhdsje je váš název účtu úložiště, storageaccountkey je klíč účtu úložiště a rdma je název sdílené složky Azure File. Sdílenou složku Azure File je připojit jako Z: z hlavního uzlu.

Chcete-li připojit sdílenou složku Azure File na uzlech Linux, spusťte **clusrun** příkaz z hlavního uzlu. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  je užitečným nástrojem HPC Pack provádět úlohy správy ve více uzlech. (Viz také [Clusrun pro Linuxové uzly](#Clusrun-for-Linux-nodes) v tomto článku.)

Otevřete okno prostředí Windows PowerShell a zadejte následující příkazy:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

První příkaz vytvoří složku s názvem /rdma ve všech uzlech v LinuxNodes skupiny. V druhém příkazu připojí allvhdsjw.file.core.windows.net/rdma sdílenou složku Azure File do složky /rdma s dir a souborů režimu bits nastavený na 777. V druhém příkazu allvhdsje je váš název účtu úložiště a storageaccountkey je klíč účtu úložiště.

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená, že "," (čárku) se část příkazu.
> 
> 

### <a name="head-node-share"></a>Sdílené složky hlavního uzlu
Alternativně připojte sdílenou složku hlavního uzlu na Linuxových uzlů. Sdílené složky poskytuje nejjednodušší způsob, jak sdílet soubory, ale hlavního uzlu a všech uzlech Linux musí být nasazeny ve stejné virtuální síti. Tady jsou kroky.

1. Vytvořte složku z hlavního uzlu a sdílet ho pro všechny uživatele s oprávněními pro čtení a zápisu. Například sdílet D:\OpenFOAM z hlavního uzlu jako \\CentOS7RDMA HN\OpenFOAM. Zde CentOS7RDMA HN je název hostitele hlavního uzlu.
   
    ![Oprávnění ke sdílené složce][fileshareperms]
   
    ![Sdílení souborů][filesharing]
2. Otevřete okno prostředí Windows PowerShell a spusťte následující příkazy:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

První příkaz vytvoří složku s názvem /openfoam ve všech uzlech v LinuxNodes skupiny. V druhém příkazu připojí //CentOS7RDMA-HN/OpenFOAM sdílené složky do složky s dir a souborů režimu bits nastavený na 777. Uživatelské jméno a heslo v příkazu musí být uživatelské jméno a heslo uživatele z hlavního uzlu clusteru. (Viz [přidat nebo odebrat uživatele clusteru](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená, že "," (čárku) se část příkazu.
> 
> 

### <a name="nfs-server"></a>Server systému souborů NFS
Služby systému souborů NFS můžete sdílet a migrace souborů mezi počítači s operačním systémem Windows Server 2012 pomocí protokolu SMB a počítačů se systémem Linux pomocí protokolu NFS. Server systému souborů NFS a všechny ostatní uzly mají být nasazeny ve stejné virtuální síti. Poskytuje lepší kompatibilitu s Linux uzly ve srovnání s sdílené složce SMB. Například podporuje odkazy souboru.

1. K instalaci a nastavení serveru NFS, postupujte podle kroků v [Server pro systém první sdílení souborů pro kompletní](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Můžete například vytvořte sdílené složky NFS s názvem systému souborů nfs s následujícími vlastnostmi:
   
    ![Autorizace systému souborů NFS][nfsauth]
   
    ![Oprávnění k sdíleným složkám NFS][nfsshare]
   
    ![Oprávnění NTFS pro systém souborů NFS][nfsperm]
   
    ![Vlastnosti správy systému souborů NFS][nfsmanage]
2. Otevřete okno prostředí Windows PowerShell a spusťte následující příkazy:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   První příkaz vytvoří složku s názvem /nfsshared ve všech uzlech v LinuxNodes skupiny. V druhém příkazu připojí CentOS7RDMA HN sdílené složky NFS: nebo systému souborů nfs na složku. Zde CentOS7RDMA HN: systém souborů nfs je složka vzdálené sdílené složky NFS.

## <a name="how-to-submit-jobs"></a>Postup odeslání úlohy
K odesílání úloh do clusteru HPC Pack několika způsoby:

* Správce clusteru HPC nebo grafického uživatelského rozhraní Správce úloh HPC
* Webového portálu HPC
* REST API

Odesílání úloh do clusteru v Azure pomocí nástroje HPC Pack grafickým uživatelským rozhraním a webového portálu HPC jsou stejné jako u Windows výpočetních uzlů. V tématu [Správce úloh HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) a [postup odesílání úloh z klientského počítače k místní](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Odesílání úloh přes REST API, najdete v tématu [vytváření a odesílání úloh pomocí rozhraní API REST v Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). K odesílání úloh z klienta systému Linux, se také podívat na vzorku Python v [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun pro Linuxové uzly
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) nástroj lze použít ke spuštění příkazů na Linuxových uzlů buď prostřednictvím příkazového řádku nebo Správce clusteru HPC. Tady jsou některé základní příklady.

* Zobrazit aktuální uživatelská jména na všech uzlech v clusteru.
  
    ```command
    clusrun whoami
    ```
* Nainstalujte **gdb** ladicí nástroj s **yum** na všechny uzly v linuxnodes skupiny a pak restartujte uzly po 10 minutách.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Vytvořit skript prostředí zobrazení všechna čísla 1 až 10 pro jednu sekundu v každém Linux uzlu v clusteru, spusťte ji a zobrazit výstup z uzlů okamžitě.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Možná budete muset použít určité řídicí znaky v **clusrun** příkazy. Jak je znázorněno v tomto příkladu, použijte ^ v příkazovém řádku, abyste se vyhnuli ">" symbol.
> 
> 

## <a name="next-steps"></a>Další postup
* Zkuste škálování na větší počet uzlů clusteru, nebo zkuste spustit úlohu Linux v clusteru. Příklad, naleznete v části [spustit NAMD pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](hpcpack-cluster-namd.md).
* Zkuste cluster s [virtuální počítače podporuje RDMA, náročné](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ke spuštění úlohy MPI. Příklad, naleznete v části [spustit OpenFOAM pomocí sady Microsoft HPC Pack na Linux RDMA cluster v Azure](hpcpack-cluster-openfoam.md).
* Pokud vás zajímá při práci s Linux uzly v clusteru HPC Pack služby místně, najdete v článku [TechNet pokyny](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
