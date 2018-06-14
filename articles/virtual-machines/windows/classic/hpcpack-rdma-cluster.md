---
title: Nastavení clusteru s podporou Windows RDMA ke spouštění aplikací MPI | Microsoft Docs
description: Zjistěte, jak vytvořit cluster Windows HPC Pack s velikostí H16r, H16mr, A8 nebo A9 virtuálních počítačů, které se použije ke spuštění aplikací MPI sítě Azure RDMA.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 437c475735ec3823de51c5f9e996a5303fe9cfa7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852536"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Nastavení clusteru s podporou Windows RDMA pomocí sady HPC Pack ke spouštění aplikací MPI
Nastavení clusteru s podporou Windows RDMA v Azure pomocí [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) a [velikosti virtuálních počítačů podporující RDMA HPC](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) ke spouštění paralelních aplikací Message Passing Interface (MPI). Při nastavování podporu rdma, systémem Windows Server uzly v clusteru služby HPC Pack aplikací MPI efektivně komunikují přes nízkou latencí a vysokou propustnost sítě v Azure, která je založena na technologii do paměti vzdáleného přímý přístup do (počítače RDMA).

Pokud chcete spustit úlohy MPI na virtuální počítače s Linuxem, přístup k síti Azure RDMA najdete v tématu [nastavení clusteru s podporou Linux RDMA ke spuštění aplikací MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Možnosti nasazení clusteru HPC Pack
Microsoft HPC Pack je nástroj zadaný bez dalších poplatků k vytvoření clusterů HPC místně nebo v Azure ke spouštění aplikací systému Windows nebo Linux HPC. HPC Pack zahrnuje běhového prostředí pro implementaci společnosti Microsoft zprávu předávání rozhraní pro Windows (MS-MPI). Při použití s podporou RDMA instancí podporovaným operačním systémem Windows Server, HPC Pack nabízí efektivní možnost ke spouštění aplikací Windows MPI, které přístup k síti Azure RDMA. 

Tento článek představuje dva scénáře a odkazy na podrobné pokyny k nastavení clusteru s podporou Windows RDMA s Microsoft HPC Pack 2012 R2. 

* Scénář 1. Nasazení instance rolí pracovního procesu náročné (PaaS)
* Scénář 2. Nasaďte výpočetní uzly ve virtuálních počítačích náročné (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scénář 1: Nasazení instance rolí pracovního procesu náročné (PaaS)
Z existujícího clusteru HPC Pack přidejte další výpočetní prostředky v instance rolí pracovního procesu systému Azure (Azure uzlů) spuštěna v rámci cloudové služby (PaaS). Tato funkce, také nazývaná "rozšíření do služby Azure" ze sady HPC Pack podporuje celou řadu velikosti pro instance rolí pracovního procesu. Při přidávání uzlů Azure, zadejte jednu velikostí RDMA podporovat.

Následují požadavky a kroky k rozšíření do RDMA podporovat instancemi Azure z existující (obvykle místní) clusteru. Podobně jako postupy můžete použijte k přidání instance rolí pracovního procesu k hlavnímu uzlu HPC Pack, který je nasazen virtuální počítač Azure.

> [!NOTE]
> Kurz k rozšíření do služby Azure pomocí sady HPC Pack, naleznete v části [nastavení hybridního clusteru pomocí sady HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pozorně si projděte informace v následujících krocích, které platí konkrétně pro podporu rdma uzlů Azure.
> 
> 

![Rozšíření do služby Azure][burst]

### <a name="steps"></a>Kroky
1. **Nasaďte a nakonfigurujte hlavnímu uzlu HPC Pack 2012 R2**
   
    Stáhněte balíček instalace sady HPC Pack [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Požadavky a pokyny k přípravě nasazení Azure shluků najdete v tématu [rozšíření do instancí pracovního procesu Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurovat certifikát pro správu v rámci předplatného Azure**
   
    Konfigurujte certifikát k zabezpečení připojení mezi Azure a hlavního uzlu. Možnosti a postupy najdete v tématu [scénáře konfigurace certifikátu pro správu Azure pro HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Pro testovací nasazení nainstaluje HPC Pack výchozí Microsoft HPC Azure certifikát pro správu můžete rychle nahrát do vašeho předplatného Azure.
3. **Vytvořte novou cloudovou službu a účet úložiště**
   
    Vytvoření cloudové služby (klasické) a účet úložiště (klasické) pro nasazení pomocí portálu Azure. Vytvořte tyto prostředky v oblasti, kde H-series, A8 a A9 velikost, kterou chcete použít je k dispozici. V tématu [produkty Azure podle oblasti](https://azure.microsoft.com/regions/services/).

4. **Vytvořit šablonu Azure uzlu**
   
    Použití v uzlu Průvodce vytvořením šablony ve Správci clusteru HPC. Pokyny najdete v tématu [vytvořit šablonu Azure uzlu](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) v "Kroky k nasazení Azure uzly s Microsoft HPC Pack".
   
    Pro počáteční testy doporučujeme nakonfigurujete zásady ruční dostupnosti v šabloně.
5. **Přidat uzly do clusteru**
   
    Pomocí Průvodce přidáním uzlu ve Správci clusteru HPC. Další informace najdete v tématu [Azure přidat uzly do clusteru Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Při zadávání velikost uzlů, vyberte jednu z podporující RDMA velikosti instance.
   
   > [!NOTE]
   > V každé shluků Azure nasazení s instancí náročné HPC Pack automaticky nasadí minimálně dvě instance RDMA podporovat (například A8) jako proxy uzly, kromě instance rolí pracovního procesu systému Azure, které zadáte. Uzly proxy serveru používat jádra, které jsou přiděleny předplatné a platit poplatky společně s instancí role pracovního procesu systému Azure.
   > 
   > 
6. **Spuštění (zřídit) uzly a přiřaďte je online ke spuštění úloh**
   
    Vyberte je a používat **spustit** akce ve Správci clusteru HPC. Jakmile je zřizování dokončeno, vyberte je a používat **přepnout do režimu Online** akce ve Správci clusteru HPC. Uzly jsou připravené ke spuštění úloh.
7. **Odesílání úloh do clusteru**
   
   Pomocí nástrojů odeslání úlohy HPC Pack ke spuštění úloh clusteru. V tématu [sady Microsoft HPC Pack: Správa úloh](http://technet.microsoft.com/library/jj899585.aspx).
8. **Zastavit (deprovision) uzly**
   
   Po dokončení probíhající úlohy, odpojit, uzly a použít **Zastavit** akce ve Správci clusteru HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scénář 2: Nasaďte výpočetní uzly ve virtuálních počítačích náročné (IaaS)
V tomto scénáři nasazení hlavního uzlu HPC Pack a výpočetní uzly clusteru na virtuálních počítačích v virtuální sítě Azure. HPC Pack nabízí několik [možnosti nasazení ve virtuálních počítačích Azure](../../linux/hpcpack-cluster-options.md), včetně skriptů automatizované nasazení a šablony Azure rychlý start. Jako příklad následující požadavky a kroky průvodce vám umožní používat [skript nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md) k automatizaci nasazení clusteru HPC Pack 2012 R2 v Azure.

![Cluster ve virtuálních počítačích Azure][iaas]

### <a name="steps"></a>Kroky
1. **Vytvoření hlavního uzlu clusteru a výpočetní uzel virtuální počítače tak, že spustíte skript nasazení HPC Pack IaaS na klientském počítači**
   
    Stáhněte balíček HPC Pack IaaS nasazení skriptu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Příprava klientského počítače, vytvořte konfigurační soubor skriptu a spusťte skript, viz [vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md). 
   
    Důležité informace k nasazení podporující RDMA výpočetních uzlů najdete v tématu [vysokovýkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) a Všimněte si následujícího:
   
   * **Virtuální síť**: Zadejte novou virtuální síť v oblasti, ve kterém je k dispozici velikost H-series, A8 a A9, kterou chcete použít. V tématu [produkty Azure podle oblasti](https://azure.microsoft.com/regions/services/).

   * **Operační systém Windows Server**: pro podporu připojení RDMA, zadejte kompatibilní operační systém Windows Server například Windows Server 2012 R2 pro výpočetní uzel virtuálních počítačů.
   * **Cloudové služby**: protože skript používá model nasazení classic, virtuální počítače clusteru jsou nasazeny pomocí cloudových služeb Azure (`ServiceName` nastavení v konfiguračním souboru). Doporučujeme, abyste nasazení vaší hlavního uzlu v rámci jednoho cloudové služby a výpočetní uzly v rámci různých cloudové služby. 
   * **HEAD velikost uzlu**: pro tento scénář, zvažte velikost alespoň A4 (Extra velký) pro hlavní uzel.
   * **Rozšíření HpcVmDrivers**: skript nasazení nainstaluje agenta virtuálního počítače Azure a rozšíření HpcVmDrivers automaticky při nasazení velikosti A8 a A9 výpočetní uzly s operačním systémem Windows Server. HpcVmDrivers nainstaluje ovladače na výpočetním uzlu virtuální počítače, aby se mohli připojit k síti RDMA. Na virtuálních počítačích podporující RDMA H-series musíte ručně nainstalovat rozšíření HpcVmDrivers. V tématu [vysokovýkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Konfiguraci sítě s clustery**: skript nasazení automaticky nastaví clusteru HPC Pack topologie 5 (do všech uzlů v podnikové síti). Tato topologie je vyžadována pro všechna nasazení clusteru HPC Pack ve virtuálních počítačích. Později neměňte topologie sítě clusteru.
1. **Aby výpočetní uzly online ke spuštění úloh**
   
    Vyberte je a používat **přepnout do režimu Online** akce ve Správci clusteru HPC. Uzly jsou připravené ke spuštění úloh.
3. **Odesílání úloh do clusteru**
   
    Připojení k hlavnímu uzlu k odesílání úloh nebo nastavení místním počítači k tomu. Informace najdete v tématu [odeslání úlohy HPC cluster v Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Uzly převést do režimu offline a zastavení (zrušit přidělení) je**
   
    Po dokončení probíhající úlohy, ve Správci clusteru HPC trvat uzlu do režimu offline. Poté použijte nástroje pro správu Azure, abyste je vypnout.

## <a name="run-mpi-applications-on-the-cluster"></a>Spouštění aplikací MPI v clusteru
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Příklad: Spusťte mpipingpong v clusteru HPC Pack
Pokud chcete ověřit nasazení služby HPC Pack instancí podporu rdma, spusťte HPC Pack **mpipingpong** příkazu v clusteru. **mpipingpong** odesílá pakety dat mezi uzly spárované opakovaně k výpočtu latence a propustnosti měření a statistiku sítě aplikací, které podporují RDMA. Tento příklad ukazuje typický vzor pro spuštění úlohu MPI (v tomto případě **mpipingpong**) pomocí clusteru **mpiexec** příkaz.

Tento příklad předpokládá, že jste přidali uzlů Azure v konfiguraci "shluků do Azure" ([scénáři 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Pokud jste nasadili HPC Pack v clusteru virtuálních počítačů Azure, budete muset upravit syntaxe příkazu k zadejte skupinu jiný uzel a nastavení proměnných prostředí další směrovat síťový provoz v síti RDMA.

Spuštění mpipingpong v clusteru:

1. Z hlavního uzlu nebo správnou konfiguraci klientských počítačích otevřete příkazový řádek.
2. K zjištění přibližné hodnoty latence mezi páry uzlů v nasazení Azure shluků ze čtyř uzlů, zadejte následující příkaz se odeslat úlohu spustit mpipingpong s velikost malých paketu a velký počet iterací:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Příkaz vrátí ID úlohy, které je odeslána.
   
    Pokud jste nasadili cluster HPC Pack nasazena na virtuálních počítačích Azure, zadejte skupinu uzlu, který obsahuje výpočetní uzel virtuálních počítačích nasazených v jednom cloudové služby a upravit **mpiexec** příkaz takto:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Po dokončení úlohy k zobrazení výstupu (v tomto případě se výstup úlohy 1 úlohy), zadejte následující příkaz
   
    ```Command
    task view <JobID>.1
    ```
   
    kde &lt; *JobID* &gt; je ID úlohu, která byla odeslána.
   
    Výstup obsahuje latence výsledky podobné následujícím.
   
    ![Latence pong příkazu ping][pingpong1]
4. K zjištění přibližné hodnoty propustnost mezi páry uzlů Azure shluků, zadejte následující příkaz k odeslání spuštění úlohy **mpipingpong** s velikostí velkých paketů a několik iterací:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Příkaz vrátí ID úlohy, které je odeslána.
   
    V clusteru HPC Pack nasazena na virtuálních počítačích Azure upravte příkaz jak jsme uvedli v kroku 2.
5. Po dokončení úlohy k zobrazení výstupu (v tomto případě se výstup úlohy 1 úlohy), zadejte následující příkaz:
   
    ```Command
    task view <JobID>.1
    ```
   
   Výstup obsahuje propustnost výsledky podobné následujícím.
   
   ![Propustnost pong příkazu ping][pingpong2]

### <a name="mpi-application-considerations"></a>Aspekty aplikací MPI
Tady jsou důležité informace týkající se s aplikací MPI HPC Pack v Azure. Některé platí pouze pro nasazení Azure uzlů (Přidat v konfiguraci "shluků do Azure" instance role pracovního procesu.).

* Instance role pracovního procesu v rámci cloudové služby jsou pravidelně znovu poskytnuto bez upozornění v Azure (například za účelem údržby systému nebo v případě, kdy selže instance). Pokud instance je znovu poskytnuto, když je spuštěná úloha MPI, instance ztratí svoje data a vrátí do stavu, pokud bylo poprvé nasazeno, což může způsobit selhání úlohy MPI. Další uzly, které používáte pro jednu úlohu MPI a tím déle má být úloha spuštěna, tím spíš, jedna z instancí je znovu poskytnuto když úloha běží. To taky zvážit, když určíte jako souborový server do jednoho uzlu v nasazení.
* Ke spouštění úloh MPI v Azure, nemusíte používat podporu rdma instance. Můžete použít libovolnou velikost instance, která je podporována sadou HPC Pack. Podporuje RDMA instance jsou však doporučováno pro spouštění poměrně rozsáhlé úlohy MPI, které jsou citlivá na latenci a šířku pásma sítě, která se připojuje uzly. Pokud používáte jiné velikosti pro spouštění úloh MPI citlivý na latenci a šířky pásma, doporučujeme spustit malé přenosy, ve kterých běží jeden úkol na pouze několik uzlů.
* Aplikace nasazené do instancemi Azure se vztahují licenční podmínky, které jsou přidružené k aplikaci. Zkontrolujte s dodavateli komerčních aplikací se vždy pro licencování a dalších omezení při spouštění v cloudu. Ne všichni dodavatelé nabízejí licencování formou průběžných plateb.
* Azure instancí potřebovat další instalační program přístup k místní uzlů, sdílených složek a licenčních serverů. Například pokud chcete povolit uzlů Azure pro přístup licenčnímu serveru místní, můžete nakonfigurovat virtuální síť Azure site-to-site.
* Ke spouštění aplikací MPI s instancemi Azure, zaregistrovat každou aplikaci MPI s bránou Windows Firewall v instancích spuštěním **hpcfwutil** příkaz. To umožňuje komunikaci MPI proběhla na portu, který se přiřadí dynamicky bránou firewall.
  
  > [!NOTE]
  > Pro shluků Azure nasazení můžete také nakonfigurovat příkaz výjimky brány firewall na automatické spuštění na všechny nové uzly Azure, které jsou přidány do clusteru. Po spuštění **hpcfwutil** příkazů a ověřte, že vaše aplikace funguje, přidejte příkaz do spouštěcí skript pro Azure uzly. Další informace najdete v tématu [pomocí spouštěcího skriptu pro Azure uzly](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack používá proměnnou prostředí clusteru CCP_MPI_NETMASK zadat rozsah adres přijatelné pro MPI komunikaci. Spouštění v prostředí HPC Pack 2012 R2, proměnné prostředí clusteru CCP_MPI_NETMASK ovlivňuje pouze MPI komunikace mezi výpočetní uzly clusteru připojené k doméně (místně nebo ve virtuálních počítačích Azure). Proměnná je ignorován v uzlech přidat v shluku do konfigurace služby Azure.
* Úloh MPI nelze spustit napříč instancemi Azure, které jsou nasazeny v různých cloudové služby (například v shluků na jiný uzel šablony nebo virtuálního počítače Azure výpočetní uzly nasazené v několika cloudových služeb Azure nasazení). Pokud máte více nasazení Azure uzlu zahájených šablonami jiného uzlu, úlohy MPI musíte spustit na jen jednu sadu uzlů Azure.
* Při přidání uzlů Azure do clusteru a jejich převedení do online režimu, služba Plánovač úloh HPC okamžitě se pokusí spustit úlohy na uzlech. Pokud jenom část úlohu můžete spustit v Azure, ujistěte se, aktualizovat nebo vytvoření šablony úloh můžete definovat, jaké typy úloh můžete spustit v Azure. Aby se zajistilo, že úlohy, odeslané s šablonu úlohy spustit jen u uzlů Azure, například přidání vlastnost uzlu skupiny do šablony úlohy a vyberte AzureNodes jako požadovaná hodnota. Pokud chcete vytvořit vlastní skupiny pro Azure uzly, použijte rutinu Add-HpcGroup prostředí HPC PowerShell.

## <a name="next-steps"></a>Další postup
* Jako alternativu k použití HPC Pack vývoj pomocí služby Azure Batch ke spouštění aplikací MPI na spravovaný fond výpočetních uzlů v Azure. V tématu [pomocí úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing) ve službě Azure Batch](../../../batch/batch-mpi.md).
* Pokud chcete spouštět aplikace, které přístup k síti Azure RDMA najdete v tématu Linux MPI [nastavení clusteru s podporou Linux RDMA ke spuštění aplikací MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
