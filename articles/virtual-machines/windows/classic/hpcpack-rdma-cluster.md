---
title: Nastavení clusteru Windows RDMA pro spouštění aplikací MPI | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Windows HPC Pack s velikostí H16r, H16mr, instance A8 nebo A9 virtuální počítače pomocí sítě Azure RDMA pro spouštění aplikací MPI.
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
ms.openlocfilehash: 52338cc21e46b544c2abb79cd7094615c837a2e8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233775"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Nastavení clusteru Windows RDMA pomocí sady HPC Pack pro spouštění aplikací MPI
Nastavení clusteru Windows RDMA v Azure pomocí [sady Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) a [velikosti virtuálních počítačů s podporou RDMA HPC](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) ke spouštění paralelních aplikací rozhraní MPI (Message Passing Interface). Při nastavování podporující RDMA, založené na Windows Server uzly v clusteru HPC Pack aplikací MPI efektivně komunikovat prostřednictvím s nízkou latencí a vysokou propustnost sítě v Azure, která je založena na technologii vzdáleného přímého paměti přístup (počítače RDMA).

## <a name="hpc-pack-cluster-deployment-options"></a>Možnosti nasazení clusteru HPC Pack
Sady Microsoft HPC Pack je nástroj pro zadaný bez dalších poplatků k vytvoření clusterů HPC v místním prostředí nebo v Azure ke spouštění aplikací Windows nebo Linuxem HPC. HPC Pack obsahuje prostředí modulu runtime pro implementaci společnosti Microsoft zprávu předá rozhraní pro Windows (MS-MPI). Při použití s podporou RDMA instance s ovladači podporovaný operační systém Windows Server, HPC Pack obsahuje efektivní možnosti pro spuštění aplikací Windows MPI, které přístup k síti Azure RDMA. 

Tento článek představuje dva scénáře a odkazy na podrobné pokyny k nastavení clusteru Windows RDMA pomocí Microsoft HPC Pack 2012 R2. 

* Scénář 1. Nasazení instancí rolí pracovního procesu náročné na výpočetní prostředky (PaaS)
* Scénář 2. Nasaďte výpočetní uzly ve virtuálních počítačů náročných na výpočetní prostředky (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scénář 1: Nasazení instancí rolí pracovního procesu náročné na výpočetní prostředky (PaaS)
Z existujícího clusteru HPC Pack přidáte další výpočetní prostředky v instancích role pracovního procesu systému Azure (uzly Azure) spuštěná v cloudové službě (PaaS). Tato funkce také nazývané "Přejít na Azure" ze sady HPC Pack podporuje širokou škálu velikostí pro instance rolí pracovního procesu. Při přidávání uzlů Azure, zadejte jednu velikostí podporující RDMA.

Toto jsou požadavky a kroky k přejít na RDMA podporovat Azure instancí z existující (obvykle v místním prostředí) clusteru. Podobné postupy můžete použijte k přidání instancí rolí pracovního procesu k hlavnímu uzlu HPC Pack, který je nasazen ve Virtuálním počítači Azure.

> [!NOTE]
> Kurz k přejít na Azure pomocí sady HPC Pack, najdete v tématu [nastavení hybridního clusteru pomocí sady HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Všimněte si důležité informace v následujících krocích, které platí konkrétně pro RDMA podporovat Azure uzly.
> 
> 

![Přejít na Azure][burst]

### <a name="steps"></a>Kroky
1. **Nasaďte a nakonfigurujte hlavní uzel HPC Pack 2012 R2**
   
    Stažení instalačního balíčku ze sady HPC Pack [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Požadavky a pokyny k přípravě nasazení služby Azure burst najdete v tématu [Burst to Azure instancí pracovních procesů pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurace certifikátu pro správu v rámci předplatného Azure**
   
    Konfigurace certifikátu pro zabezpečené připojení mezi hlavní uzel a Azure. Možnosti a postupy najdete v tématu [scénáře ke konfiguraci certifikátu pro správu Azure pro prostředí HPC Pack](https://technet.microsoft.com/library/gg481759.aspx). Pro testovací nasazení nainstaluje sady HPC Pack výchozí Microsoft HPC certifikát správy platformy Azure můžete rychle nahrát do vašeho předplatného Azure.
3. **Vytvořit novou cloudovou službu a účet úložiště**
   
    Vytvoření cloudové služby (klasické) a účet úložiště (classic) pro nasazení pomocí webu Azure portal. Vytvoření těchto prostředků v oblasti, kde je k dispozici řada H-series, instance A8 nebo A9 velikost, kterou chcete použít. Zobrazit [produkty Azure podle oblastí](https://azure.microsoft.com/regions/services/).

4. **Vytvoření šablony uzlu Azure**
   
    Použití uzlu Průvodce vytvořením šablony ve Správci clusteru HPC. Pokyny najdete v tématu [vytvořit šablonu Azure uzel](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) v "Kroky k nasazení Azure uzly pomocí sady Microsoft HPC Pack".
   
    Pro počáteční testování doporučujeme konfigurace zásad ruční dostupnosti v šabloně.
5. **Přidání uzlů do clusteru**
   
    Použití Průvodce přidáním uzlu ve Správci clusteru HPC. Další informace najdete v tématu [Azure přidat uzly do clusteru Windows HPC](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Při zadávání velikost uzlů, vyberte jednu z podporující RDMA velikostí instancí.
   
   > [!NOTE]
   > HPC Pack v každé rozšíření do nasazení s výpočetně náročných instancí Azure automaticky nasadí minimálně dvě instance podporující RDMA (například A8) jako proxy uzly kromě instancí rolí pracovního procesu systému Azure, kterou zadáte. Uzly proxy server používat jader, které jsou přiděleny k předplatnému a budou účtovat poplatky za spolu s instancí rolí pracovního procesu systému Azure.
   > 
   > 
6. **Spustit (zřizování) uzlů a přiřaďte je online ke spouštění úloh**
   
    Vyberte uzly a použít **Start** akcí ve Správci clusteru HPC. Jakmile je zřizování dokončeno, vyberte uzly a použít **přepnout do režimu Online** akcí ve Správci clusteru HPC. Uzly jsou připravené ke spuštění úlohy.
7. **Odesílání úloh do clusteru**
   
   Pomocí nástroje pro odeslání úlohy HPC Pack můžete spouštět úlohy clusteru. Zobrazit [sady Microsoft HPC Pack: Správa úloh](https://technet.microsoft.com/library/jj899585.aspx).
8. **Zastavení (zrušení zřízení) uzly**
   
   Po dokončení spuštěné úlohy, odpojit, uzly a použít **Zastavit** akcí ve Správci clusteru HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scénář 2: Nasazení výpočetních uzlů ve virtuálních počítačů náročných na výpočetní prostředky (IaaS)
V tomto scénáři nasazení hlavní uzel HPC Pack a výpočetní uzly clusteru na virtuální počítače ve virtuální síti Azure. HPC Pack obsahuje několik [možnosti nasazení ve virtuálních počítačích Azure](../../windows/hpcpack-cluster-options.md), včetně nasazení automatizovaných skriptů a šablon rychlý start Azure. Například následující požadavky a kroky vás provedou používat [skriptem nasazení IaaS sady HPC Pack](hpcpack-cluster-powershell-script.md) k automatizaci nasazení clusteru HPC Pack 2012 R2 v Azure.

![Cluster ve virtuálních počítačích Azure][iaas]

### <a name="steps"></a>Kroky
1. **Vytvoření hlavního uzlu clusteru a výpočetní uzel virtuální počítače spuštěním skriptu nasazení IaaS sady HPC Pack na klientském počítači**
   
    Stáhněte si balíček skriptem nasazení IaaS sady HPC Pack z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Příprava klientského počítače, vytvořte konfigurační soubor skriptu a spusťte skript, viz [vytvoření clusteru HPC se skriptem nasazení IaaS sady HPC Pack](hpcpack-cluster-powershell-script.md). 
   
    Důležité informace o nasazení podporující RDMA výpočetních uzlů najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) a mějte na paměti následující:
   
   * **Virtuální síť**: Zadejte novou virtuální síť v oblasti, ve kterém je k dispozici řady H-series, instance A8 nebo A9 velikost, kterou chcete použít. Zobrazit [produkty Azure podle oblastí](https://azure.microsoft.com/regions/services/).

   * **Operační systém Windows Server**: pro podporu připojení RDMA, zadejte kompatibilní operační systém Windows Server jako je například Windows Server 2012 R2 pro virtuální počítače výpočetních uzlů.
   * **Cloudové služby**: vzhledem k tomu tento skript používá model nasazení classic, jsou nasazené virtuální počítače clusteru pomocí cloudových služeb Azure (`ServiceName` nastavení v konfiguračním souboru). Doporučujeme nasadit váš hlavní uzel v jedné cloudové službě a vaše výpočetní uzly v jinou cloudovou službu. 
   * **Velikost uzlu HEAD**: v tomto scénáři zvažte o velikosti alespoň A4 (velký) pro hlavní uzel.
   * **Rozšíření HpcVmDrivers**: skript nasazení nainstaluje agenta virtuálního počítače Azure a rozšíření HpcVmDrivers automaticky při nasazení velikosti A8 nebo A9 výpočetních uzlů s operačním systémem Windows Server. HpcVmDrivers instaluje ovladače na výpočetních uzlech virtuálních počítačů, aby se mohli připojit k síti přístup RDMA. Na virtuálních počítačích s podporou RDMA H-series musíte ručně nainstalovat rozšíření HpcVmDrivers. Zobrazit [vysoce výkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Konfiguraci sítě s clustery**: skript nasazení automaticky nastaví clusteru HPC Pack v topologii 5 (všechny uzly v podnikové síti). Tato topologie je vyžadován pro všechna nasazení clusteru HPC Pack ve virtuálních počítačích. Později neměňte topologie sítě clusteru.
1. **Používání online výpočetních uzlů pro spouštění úloh**
   
    Vyberte uzly a použít **přepnout do režimu Online** akcí ve Správci clusteru HPC. Uzly jsou připravené ke spuštění úlohy.
3. **Odesílání úloh do clusteru**
   
    Připojení k hlavnímu uzlu odesílat úlohy, nebo nastavit v místním počítači k tomu. Informace najdete v tématu [odeslání úloh HPC clusteru v Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Uzly převést do režimu offline a zastavení (uvolnění) je**
   
    Po dokončení spuštěné úlohy, uzly v režimu offline provést ve Správci clusteru HPC. Pak pomocí nástroje pro správu Azure tak je vypnete.

## <a name="run-mpi-applications-on-the-cluster"></a>Spouštění aplikací MPI v clusteru
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Příklad: Spusťte mpipingpong v clusteru HPC Pack
Pokud chcete ověřit nasazení služby HPC Pack podporující RDMA instancí, spusťte sady HPC Pack **mpipingpong** příkaz v clusteru. **mpipingpong** odesílá pakety dat mezi spárovaných uzlů opakovaně k výpočtu měření latence a propustnosti a statistiky pro síťové aplikace s podporou RDMA. Tento příklad ukazuje typický vzor pro spuštění úlohy MPI (v tomto případě **mpipingpong**) s použitím clusteru **mpiexec** příkazu.

Tento příklad předpokládá, že jste přidali uzly Azure v konfiguraci "rozšíření do Azure" ([scénář 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Pokud jste nasadili sady HPC Pack v clusteru virtuálních počítačů Azure, budete potřebovat ke změně syntaxe příkazu k zadání skupiny jiného uzlu a nastavte další proměnné prostředí pro směrování provozu sítě do sítě RDMA.

Pokud chcete spustit mpipingpong v clusteru:

1. Hlavního uzlu nebo správně nakonfigurovanými klientském počítači otevřete příkazový řádek.
2. Pokud chcete odhadnout latence mezi dvojice uzlů v nasazení Azure burst čtyři uzly, zadejte následující příkaz se odeslat úlohu pro spuštění mpipingpong s velikostí malý paket a více iterací:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Příkaz vrátí ID úlohy, které je odeslána.
   
    Pokud jste nasadili clusteru HPC Pack nasazeného na virtuálních počítačích Azure, určete skupinu uzel, který obsahuje výpočetní uzel virtuální počítače nasazené v jedné cloudové službě a upravit **mpiexec** takto:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Po dokončení úlohy, chcete-li zobrazit výstup (v tomto případě výstup úkolu 1 úloha), zadejte následující příkaz
   
    ```Command
    task view <JobID>.1
    ```
   
    kde &lt; *JobID* &gt; je ID úlohy, která byla odeslána.
   
    Výstup obsahuje latence výsledky podobné následujícím.
   
    ![Příkaz ping pong latence][pingpong1]
4. Pokud chcete odhadnout propustnost mezi dvojice Azure shlukových uzlů, zadejte následující příkaz k odeslání úlohy pro spuštění **mpipingpong** s velikostí velkých paketů a několik iterace:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Příkaz vrátí ID úlohy, které je odeslána.
   
    V clusteru HPC Pack nasazený na virtuálních počítačích Azure upravte příkaz, jak je uvedeno v kroku 2.
5. Po dokončení úlohy, chcete-li zobrazit výstup (v tomto případě výstup úkolu 1 úloha), zadejte následující příkaz:
   
    ```Command
    task view <JobID>.1
    ```
   
   Výstup obsahuje propustnost výsledky podobné následujícím.
   
   ![Příkaz ping pong propustnost][pingpong2]

### <a name="mpi-application-considerations"></a>Aspekty aplikací MPI
Tady jsou důležité informace týkající se spouštění aplikací MPI pomocí sady HPC Pack v Azure. Některé jsou aplikovatelné pouze na nasazení Azure uzlů (přidá "rozšíření do Azure" Konfigurace instancí rolí pracovního procesu.).

* Instance role pracovního procesu v cloudové službě se pravidelně znovu zajištěny bez předchozího upozornění v Azure (například pro údržbu systému, nebo v případě selhání instance). Pokud instance je znovu zajištěny během jejího běhu úlohu MPI, instance ztratí svoje data a vrátí do stavu, pokud bylo poprvé nasazeno, což může způsobit selhání úlohy MPI. Další uzly, které používáte pro jednu úlohu MPI a tím déle je úloha spuštěná, tím spíše, která se jedna z instancí je znovu zajištěny, zatímco úloha běží. Také Zvažte proto pokud určíte jako souborový server v nasazení jeden uzel.
* Ke spouštění úloh MPI v Azure, nemusíte používat podporující RDMA instance. Můžete použít libovolné velikosti instance, která je podporována sadou HPC Pack. Ale RDMA podporovat instancí se doporučují pro spouštění relativně rozsáhlých úloh MPI, které jsou citlivé na latenci a šířky pásma sítě, která se připojuje uzly. Pokud používáte ke spouštění úloh MPI citlivý na latenci a šířky pásma Další velikosti, doporučujeme spustit malé úlohy, ve kterých běží jeden úkol v pouze několika uzlů.
* Aplikace nasazené pro instance Azure jsou v souladu s podmínkami této licenční smlouvy přidružené k aplikaci. Obraťte se na dodavatele komerčních aplikací pro licencování a dalších omezení při spouštění v cloudu. Ne všichni dodavatelé nabízejí licencování formou průběžných plateb.
* Instance Azure potřebovat další nastavení přístupu místních uzlech, sdílených složek a licenční servery. Například pokud chcete povolit Azure uzly pro přístup licenčnímu serveru místní, můžete nakonfigurovat site-to-site virtuální síť Azure.
* Pro spouštění aplikací MPI s instancemi Azure, zaregistrujte jednotlivých aplikací MPI pomocí brány Windows Firewall na instancích spuštěním **hpcfwutil** příkazu. To umožňuje komunikaci MPI uskutečnit na portu, který je přiřazen dynamicky bránou firewall.
  
  > [!NOTE]
  > Pro rozšíření pro nasazení Azure můžete taky nakonfigurovat příkaz výjimky brány firewall na automatické spuštění na všechny nové uzly pro Azure, které se přidají do vašeho clusteru. Po spuštění **hpcfwutil** příkazu a ověřte, že vaše aplikace funguje, přidejte příkaz ke spuštění skriptu pro svých uzlech Azure. Další informace najdete v tématu [použijte spouštěcí skript pro uzlů Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack používá proměnnou prostředí CCP_MPI_NETMASK clusteru můžete určit rozsah přípustných adres pro komunikaci MPI. Spuštění v prostředí HPC Pack 2012 R2, je proměnná prostředí clusteru CCP_MPI_NETMASK ovlivní pouze MPI komunikace mezi jednotlivými výpočetními uzly clusteru připojené k doméně (ať už místní nebo ve virtuálních počítačích Azure). Proměnná je ignorován v uzlů do konfigurace Azure zřízeno.
* Úlohy MPI nelze spouštět napříč instancemi Azure, které jsou nasazené v různých cloudových služeb (třeba v burst k nasazení pomocí šablony, jiný uzel, nebo výpočetní uzly virtuálního počítače Azure nasazené v několika cloudových služeb Azure). Pokud máte více nasazení uzlu Azure, které jsou spuštěny se šablonami jiný uzel, úlohy MPI spouštět pouze jednu sadu uzlů Azure.
* Při přidání uzlů Azure do vašeho clusteru a přiřaďte je online, okamžitě pokusí spustit úlohy v uzlech služby Scheduleru úloh HPC. Pokud pouze část vašich úloh můžete spustit v Azure, zajistěte, aby aktualizovat nebo vytvořit šablony projektu definovat, jaké typy úloh můžete spouštět v Azure. Například pokud chcete mít jistotu, že úlohy odeslané k šabloně úlohy spustit jenom na uzlech Azure, přidejte vlastnost uzlu skupiny do šablony úlohy a vyberte AzureNodes jako povinná hodnota. Vytvořit vlastní skupiny pro svých uzlech Azure, použijte rutinu prostředí PowerShell přidat HpcGroup HPC.

## <a name="next-steps"></a>Další postup
* Jako alternativu k použití sady HPC Pack vývoj pomocí služby Azure Batch ke spouštění aplikací MPI na spravovaný fond výpočetních uzlů v Azure. Zobrazit [použití úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing Interface) ve službě Azure Batch](../../../batch/batch-mpi.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
