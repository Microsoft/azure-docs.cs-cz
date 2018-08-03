---
title: Linuxový výpočetní výkon virtuálních počítačů v clusteru HPC Pack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a použít clusteru HPC Pack v Azure pro Linux úlohy vysokovýkonného výpočetního (prostředí HPC)
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
ms.openlocfilehash: 2d4091d8ad6a778405ee6bb916c399e0b144f21d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441523"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Začínáme s výpočetními uzly Linuxu v clusteru HPC Pack v Azure
Nastavení [sady Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) clusteru v Azure, která obsahuje hlavního uzlu se systémem Windows Server a některé výpočetní uzly, které běží podporované distribuce systému Linux. Prozkoumejte možnosti služby pro přesun dat mezi uzly s Linuxem a k hlavnímu uzlu clusteru Windows. Zjistěte, jak odesílat úlohy HPC pro Linux do clusteru.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Následující diagram ukazuje na vysoké úrovni clusteru HPC Pack vytváření a používání.

![HPC Pack clusteru s Linuxovými uzly][scenario]

Další možnosti pro spouštění úloh HPC pro Linux v Azure najdete v tématu [technických prostředcích pro batch a prostředí HPC](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Nasazení clusteru HPC Pack s Linuxovými výpočetními uzly
Tento článek ukazuje, že jste dvě možnosti, jak nasadit cluster prostředí HPC Pack v Azure s Linuxem výpočetních uzlů. Obě metody používají Marketplace image Windows serveru se sadou HPC Pack pro vytvoření hlavního uzlu. 

* **Šablona Azure Resource Manageru** -použít šablonu z Azure Marketplace, nebo od komunity, šablony rychlý start k automatizaci vytváření clusteru v modelu nasazení Resource Manager. Například [clusteru HPC Pack pro Linuxové úlohy](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) šablony na webu Azure Marketplace vytvoří kompletní infrastrukturu clusteru prostředí HPC Pack pro HPC pro Linux úlohy.
* **Skript prostředí PowerShell** – použití [skriptem nasazení IaaS sady Microsoft HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) k automatizaci nasazení celého clusteru v modelu nasazení classic. Skript Azure Powershellu používá image virtuálního počítače HPC Pack na webu Azure Marketplace pro rychlé nasazení a poskytuje komplexní sadu parametrů konfigurace nasazení výpočetních uzlů s Linuxem.

Další informace o možnostech nasazení clusteru HPC Pack v Azure najdete v tématu [možnosti, jak vytvářet a spravovat vysokovýkonným výpočetním prostředím (HPC) clusteru v Azure pomocí sady Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Požadavky
* **Předplatné Azure** -uživatelům pomocí odběru ve službě Azure Global nebo Azure China. Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.
* **Kvóta pro jádra** – možná muset zvýšit kvótu jader, zejména v případě, že budete chtít nasadit několik uzlů clusteru s vícejádrovými velikosti virtuálních počítačů. Pokud chcete zvýšit kvótu, otevřete žádosti o podporu online zákazníků bez poplatků.
* **Linuxové distribuce** – aktuálně sady HPC Pack podporuje následující Linuxových distribucí pro výpočetní uzly. Můžete využít ve verzi Marketplace tyto distribucí, pokud je k dispozici nebo zadat vlastní.
  
  * **Založené na centOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 pro prostředí HPC, SLES 12 pro prostředí HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Pokud chcete použít síť Azure RDMA s jedním velikosti virtuálních počítačů s podporou RDMA, určete bitovou kopii operačního systému SUSE Linux Enterprise Server 12 HPC nebo založené na CentOS HPC na Azure Marketplace. Další informace najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Další požadavky na nasazení clusteru s použitím skriptem nasazení IaaS sady HPC Pack:

* **Klientský počítač** -je nutné počítače klienta se systémem Windows pro spuštění skriptu pro nasazení clusteru.
* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skriptem nasazení IaaS sady HPC Pack** – stažení a rozbalení nejnovější verzi skriptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verze tohoto skriptu můžete zkontrolovat spuštěním `.\New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verze 4.4.1 nebo novější tohoto skriptu.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Možnost nasazení 1. Pomocí šablony Resource Manageru
1. Přejděte [clusteru HPC Pack pro Linuxové úlohy](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) šablony na webu Azure Marketplace, a klikněte na **nasadit**.
1. Na webu Azure Portal, projděte si informace a pak klikněte na tlačítko **vytvořit**.
   
    ![Vytvoření portálu][portal]
1. Na **Základy** okně zadejte název clusteru také názvy virtuálních počítačů k hlavnímu uzlu. Můžete vybrat existující skupinu prostředků nebo vytvořte skupinu pro nasazení do umístění, které je k dispozici. Umístění má vliv na dostupnost určité velikosti virtuálních počítačů a dalším službám Azure (viz [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/)).
1. Na **hlavního uzlu nastavení** okně první nasazení, můžete obvykle přijmout výchozí nastavení. 
   
   > [!NOTE]
   > **Adresa URL skriptu po konfiguraci** je volitelné nastavení, a určete veřejně k dispozici skript prostředí Windows PowerShell, který chcete spustit hlavního uzlu virtuálního počítače po jeho spuštění. 
   > 
   > 
1. Na **výpočetní uzel nastavení** okně vyberte názvy vzorku pro uzly, počet a velikost uzlů a Linuxovou distribuci k nasazení.
1. Na **nastavení infrastruktury** okno, zadejte názvy pro virtuální sítě a služby Active Directory domény, domény a přihlašovací údaje Správce virtuálního počítače a vzoru pro pojmenovávání pro účty úložiště.
   
   > [!NOTE]
   > HPC Pack používá domény služby Active Directory k ověřování uživatelů clusteru. 
   > 
   > 
1. Po spuštění ověřovacích testů a přečíst podmínky použití, klikněte na tlačítko **nákupní**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Možnost nasazení 2. Použití skriptem nasazení IaaS sady
Tady jsou další požadavky na nasazení clusteru s použitím skriptem nasazení IaaS sady HPC Pack:

* **Klientský počítač** -je nutné počítače klienta se systémem Windows pro spuštění skriptu pro nasazení clusteru.
* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skriptem nasazení IaaS sady HPC Pack** – stažení a rozbalení nejnovější verzi skriptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verze tohoto skriptu můžete zkontrolovat spuštěním `.\New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verze 4.4.1 nebo novější tohoto skriptu.

**Konfigurační soubor XML.**

Skriptem nasazení IaaS sady HPC Pack konfigurační soubor XML používá jako vstup pro popis clusteru prostředí HPC. Následující ukázkový soubor konfigurace určuje malý cluster, který se skládá z hlavní uzel HPC Pack a dvou výpočetních uzlů A7 CentOS 7.0 Linux velikost. 

Podle potřeby pro vaše prostředí a konfigurace požadované clusteru, upravte soubor a uložte ho s názvem, jako je například HPCDemoConfig.xml. Například musíte zadat název vašeho odběru a jedinečným názvem účtu úložiště a název služby v cloudu. Kromě toho můžete zvolit jiné podporované image Linuxu pro výpočetní uzly. Další informace o prvcích v konfiguračním souboru, naleznete v souboru Manual.rtf ve složce script a [vytvoření clusteru HPC se skriptem nasazení IaaS sady HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

**Ke spuštění skriptem nasazení IaaS sady HPC Pack**

1. Otevřete prostředí Windows PowerShell v klientském počítači jako správce.
1. Změnit adresář na složku, kde je skript nainstalovat (E:\IaaSClusterScript v tomto příkladu).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
1. Spusťte následující příkaz k nasazení clusteru HPC Pack. Tento příklad předpokládá, že konfigurační soubor umístěný v E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Vzhledem k tomu, **AdminPassword** není zadán v předchozím příkazu, zobrazí se výzva k zadání hesla pro uživatele *MyAdminName*.
   
    b. Skript potom začne ověření konfiguračního souboru. To může trvat až několik minut v závislosti na připojení k síti.
   
    ![Ověření][validate]
   
    c. Po ověření předat, skript zobrazí prostředky clusteru k vytvoření. Zadejte *Y* pokračujte.
   
    ![Zdroje a prostředky][resources]
   
    d. Tento skript k nasazení clusteru HPC Pack při spuštění a dokončení konfigurace bez další ruční kroky. Skript můžete spustit několik minut.
   
    ![Nasazení][deploy]
   
   > [!NOTE]
   > V tomto příkladu skript generuje soubor protokolu automaticky od té doby **- LogFile** parametr není zadán. Protokoly nejsou zapsány v reálném čase, ale jsou shromažďovány na konci ověření a nasazení. Pokud PowerShell proces se ukončí, když je spuštěný skript, některé protokoly budou ztraceny.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Připojení k hlavnímu uzlu
Po nasazení clusteru HPC Pack v Azure, [připojit pomocí vzdálené plochy](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k hlavnímu uzlu virtuálního počítače pomocí domény přihlašovací údaje můžete Pokud po nasazení clusteru (například *hpc\\clusteradmin*). Správa clusteru z hlavního uzlu.

Hlavního uzlu spusťte Správce clusterů HPC a zkontrolujte stav clusteru HPC Pack. Můžete spravovat a monitorování Linuxových výpočetních uzlů stejným způsobem jako při práci s Windows výpočetních uzlů. Například se zobrazí uzly s Linuxem podle **správy prostředků** (nasazení se používají tyto uzly **LinuxNode** šablony).

![Uzel správy][management]

Zobrazí také uzly s Linuxem v **Heat mapa** zobrazení.

![Heat mapa][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Jak přesunout data v clusteru s Linuxovými uzly
Máte několik možností pro přesun dat mezi uzly s Linuxem a k hlavnímu uzlu clusteru Windows. Tady jsou tři běžné metody, je popsáno podrobněji v následujících částech:

* **Azure File** – poskytuje spravované sdílené složky protokolu SMB pro ukládání dat souborů ve službě Azure storage. Windows uzly a uzly s Linuxem můžete připojit sdílené složky Azure jako jednotky nebo složka ve stejnou dobu, i v případě, kde jsou nasazeny v různých virtuálních sítích.
* **Hlavní uzel SMB sdílet** -připojí standardní sdílenou složku Windows hlavního uzlu na uzly s Linuxem.
* **Server systému souborů NFS hlavní uzel** – poskytuje řešení sdílení souborů pro hybridní prostředí s Windows a Linux.

### <a name="azure-file-storage"></a>Azure File storage
[Azure File](https://azure.microsoft.com/services/storage/files/) služba zpřístupňuje sdílené složky, které používají standardní protokol SMB 2.1. Virtuální počítače Azure a cloudové služby můžou sdílet souborová data mezi komponentami aplikace přes sdílené složky a místní aplikace můžou k souborovým datům ve sdílené složce přes API úložiště souborů. 

Podrobný postup vytvoření sdílené složky Azure a připojit ji hlavního uzlu, naleznete v tématu [Začínáme s Azure File storage ve Windows](../../../storage/files/storage-how-to-use-files-windows.md). Připojení sdílené složky Azure v uzlech systému Linux, najdete v článku [postupy používání Azure File storage s Linuxem](../../../storage/files/storage-how-to-use-files-linux.md). Nastavení trvalých připojení, naleznete v tématu [Persisting připojení k Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

V následujícím příkladu vytvoření sdílené složky Azure v účtu úložiště. Připojte sdílenou složku hlavního uzlu, otevřete příkazový řádek a zadejte následující příkazy:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

V tomto příkladu allvhdsje je název vašeho účtu úložiště, storageaccountkey je klíč účtu úložiště a rdma představuje název sdílené složky Azure File. Sdílené složky Azure je připojený jako Z: hlavního uzlu.

Chcete-li připojení sdílené složky Azure v uzlech systému Linux, spusťte **clusrun** příkaz hlavního uzlu. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  je užitečný nástroj sady HPC Pack provádět úlohy správy ve více uzlech. (Viz také [Clusrun pro uzly s Linuxem](#Clusrun-for-Linux-nodes) v tomto článku.)

Otevřete okno prostředí Windows PowerShell a zadejte následující příkazy:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

První příkaz vytvoří složku s názvem /rdma na všechny uzly ve skupině LinuxNodes. Druhý příkaz slouží pro připojení allvhdsjw.file.core.windows.net/rdma sdílené složky Azure File do složky /rdma s dir a souborů režimu bity nastaveny na 777. Druhý příkaz allvhdsje je název vašeho účtu úložiště a storageaccountkey je klíč účtu úložiště.

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená, že "," (znak čárky) je součástí příkazu.
> 
> 

### <a name="head-node-share"></a>Hlavní uzel sdílené složky
Můžete také připojte sdílenou složku k hlavnímu uzlu na uzly s Linuxem. Sdílenou složku poskytuje nejjednodušší způsob, jak sdílet soubory, ale hlavní uzel a všechny uzly s Linuxem musí být nasazený ve stejné virtuální síti. Tady jsou kroky.

1. Vytvořte složku na hlavní uzel a sdílet pro všechny uživatele s oprávněními pro čtení a zápisu. Například sdílet D:\OpenFOAM hlavního uzlu jako \\CentOS7RDMA HN\OpenFOAM. Tady je CentOS7RDMA HN názvu hostitele k hlavnímu uzlu.
   
    ![Oprávnění ke sdílené složce][fileshareperms]
   
    ![Sdílení souborů][filesharing]
1. Otevřete okno Windows Powershellu a spusťte následující příkazy:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

První příkaz vytvoří složku s názvem /openfoam na všechny uzly ve skupině LinuxNodes. Druhý příkaz slouží pro připojení sdílené složky //CentOS7RDMA-HN/OpenFOAM do složky s dir a souborů režimu bity nastaveny na 777. Uživatelské jméno a heslo v příkazu by měl být uživatelské jméno a heslo uživatele hlavního uzlu clusteru. (Viz [přidat nebo odebrat uživatele clusteru](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená, že "," (znak čárky) je součástí příkazu.
> 
> 

### <a name="nfs-server"></a>Server systému souborů NFS
Systém souborů NFS služba vám umožňuje sdílet a migrace souborů mezi počítači s operačním systémem Windows Server 2012 pomocí protokolu SMB a počítačů se systémem Linux pomocí protokolu NFS. Server systému souborů NFS a všechny ostatní uzly mají být nasazeny ve stejné virtuální síti. Poskytuje lepší kompatibilitu s Linuxovými uzly ve srovnání s sdílené složce SMB. Například nepodporuje odkazy na soubory.

1. Instalace a nastavení serveru systému souborů NFS, postupujte podle kroků v [Server pro první sdíleného síťového umístění systému začátku do konce](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Například vytvořte sdílené složky NFS s názvem systému souborů nfs s následujícími vlastnostmi:
   
    ![Autorizace systému souborů NFS][nfsauth]
   
    ![Oprávnění sdílených složek NFS][nfsshare]
   
    ![Oprávnění NTFS pro systém souborů NFS][nfsperm]
   
    ![Vlastnosti správy systému souborů NFS][nfsmanage]
1. Otevřete okno Windows Powershellu a spusťte následující příkazy:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   První příkaz vytvoří složku s názvem /nfsshared na všechny uzly ve skupině LinuxNodes. Druhý příkaz slouží pro připojení sdílené složky NFS CentOS7RDMA HN: / systému souborů nfs na složku. Tady CentOS7RDMA HN: / systému souborů nfs je Vzdálená cesta do sdílené složky systému souborů NFS.

## <a name="how-to-submit-jobs"></a>Postup odeslání úlohy
Existuje několik způsobů, jak odesílat úlohy do clusteru HPC Pack:

* Správce clusterů HPC nebo grafické uživatelské rozhraní Správce úloh HPC
* Webového portálu HPC
* REST API

Odeslání úlohy do clusteru v Azure pomocí nástroje pro prostředí HPC Pack grafickým uživatelským rozhraním a webového portálu HPC jsou stejné jako u Windows výpočetních uzlů. Zobrazit [Správce úloh HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) a [postup odesílání úloh z klientských počítačů v místním](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Chcete-li odesílat úlohy prostřednictvím rozhraní REST API, přečtěte si [vytváření a odesílání úloh pomocí rozhraní REST API v Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Odesílání úloh z klienta systému Linux, také naleznete ukázku Pythonu v [sady HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun pro uzly s Linuxem
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) nástroj můžete použít ke spuštění příkazů na uzly s Linuxem prostřednictvím příkazového řádku nebo Správce clusterů HPC. Toto jsou některé základní příklady.

* Zobrazit jména aktuálních uživatelů na všech uzlech v clusteru.
  
    ```command
    clusrun whoami
    ```
* Instalace **gdb** ladicího programu nástroje s **yumu** na všech uzlech linuxnodes skupině a restartujte uzly po 10 minutách.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Vytvořit skript prostředí zobrazení jednotlivých čísel 1 až 10 jedné sekundy na všech uzlech Linuxu v clusteru, spusťte ji a okamžitě zobrazit výstup z uzlů.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Možná budete muset použít některé řídicí znaky v **clusrun** příkazy. Jak je znázorněno v tomto příkladu, použít ^ v příkazovém řádku k uvození ">" symbol.
> 
> 

## <a name="next-steps"></a>Další postup
* Vyzkoušejte vertikálním navýšení kapacity clusteru většímu počtu uzlů, nebo spuštění úlohy Linuxu v clusteru. Příklad najdete v tématu [spuštění NAMD pomocí sady Microsoft HPC Pack v Linuxu výpočetních uzlech v Azure](hpcpack-cluster-namd.md).
* Zkuste cluster s [virtuální počítače s podporou RDMA, jsou náročné na výpočetní](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ke spouštění úloh MPI. Příklad najdete v tématu [spuštění OpenFOAM se sadou Microsoft HPC Pack na Linux RDMA clusteru v Azure](hpcpack-cluster-openfoam.md).
* Pokud vás zajímá při práci s Linuxovými uzly v clusteru HPC Pack v místním, najdete v článku [TechNet pokyny](https://technet.microsoft.com/library/mt595803.aspx).

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
