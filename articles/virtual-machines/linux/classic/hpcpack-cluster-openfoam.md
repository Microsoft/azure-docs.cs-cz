---
title: Spustit OpenFOAM pomocí sady HPC Pack na virtuální počítače s Linuxem | Microsoft Docs
description: Nasazení clusteru s podporou sady Microsoft HPC Pack v Azure a spusťte úlohu OpenFOAM v několika výpočetních uzlech Linux přes sítě RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: f43790d3495e1c09730e90b5077ec840731a7d83
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841907"
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Spuštění OpenFoam se sadou Microsoft HPC Pack v clusteru Linux RDMA v Azure
Tento článek ukazuje jeden ze způsobů spuštění OpenFoam ve virtuálních počítačích Azure. V tomto nasazení clusteru Microsoft HPC Pack s Linux výpočetní uzly na Azure a spusťte [OpenFoam](http://openfoam.com/) úlohy s Intel MPI. RDMA podporovat virtuálních počítačích Azure můžete použít pro výpočetní uzly, výpočetní uzly komunikovat přes síť Azure RDMA. Další možnosti spuštění OpenFoam v Azure zahrnují kompletně nakonfigurovaný komerční obrázky, které jsou k dispozici na webu Marketplace, jako je například na UberCloud [OpenFoam 2.3 na CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)a spuštěním na [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (pro operaci otevřete pole a manipulace s) je balíček softwaru open source výpočet dynamiky kapaliny (CFD), který se často používá v inženýrství a vědecké účely, v organizacích komerční i academic. Obsahuje nástroje pro meshing, zejména snappyHexMesh, parallelized mesher pro komplexní geometrie CAD a před a po zpracování. Téměř všechny procesy spustit souběžně, uživatelé budou moct využít všech výhod hardware počítače mají k dispozici.  

Microsoft HPC Pack poskytuje funkce, které chcete spouštět ve velkém měřítku HPC a paralelní aplikace, včetně aplikací MPI, v clusterech virtuální počítače Microsoft Azure. HPC Pack také podporuje spuštěné Linux HPC aplikace v systému Linux výpočetního uzlu, které virtuální počítače jsou nasazené v clusteru služby HPC Pack. V tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md) Úvod do používání Linux výpočetní uzly s HPC Pack.

> [!NOTE]
> Tento článek ukazuje, jak spouštět úlohy Linux MPI s HPC Pack. Předpokládá se, že máte některé znalosti s správu systému Linux a úlohy MPI systémem Linux clusterů. Pokud používáte verze MPI a OpenFOAM liší od hodnoty v tomto článku, budete pravděpodobně nutné změnit některé kroky instalace a konfigurace. 
> 
> 

## <a name="prerequisites"></a>Požadavky
* **HPC Pack clusteru s podporou RDMA Linux výpočetní uzly** – nasazení clusteru HPC Pack velikosti A8, A9, H16r, nebo H16rm Linuxových výpočetních uzlů, buď pomocí [šablony Azure Resource Manageru](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) nebo [Azure Skript prostředí PowerShell](hpcpack-cluster-powershell-script.md). V tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md) pro požadavky a kroky pro jednu z možností. Pokud zvolíte možnost nasazení skriptu prostředí PowerShell, naleznete v souboru konfigurace ukázka v ukázkové soubory na konci tohoto článku. Tuto konfiguraci můžete použijte k nasazení clusteru služby založené na Azure HPC Pack skládající se z hlavního uzlu velikosti A8 Windows Server 2012 R2 a 2 velikosti A8 SUSE Linux Enterprise Server 12 výpočetních uzlů. Nahraďte příslušnými hodnotami pro vaše předplatné a služby názvy. 
  
  **Další co potřebujete vědět**
  
  * Linux RDMA síťové požadavky v Azure, najdete v části [vysokovýkonné výpočetní velikosti virtuálních počítačů](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Pokud použijete možnost nasazení skriptu prostředí Powershell, nasaďte všechny Linux výpočetní uzly v rámci jednoho cloudové služby za účelem použití síťového připojení RDMA.
  * Po nasazení Linuxových uzlů, připojte pomocí SSH k provádění další úloh správy. Najít podrobnosti připojení SSH pro každý virtuální počítač s Linuxem na portálu Azure.  
* **Intel MPI** – Pokud chcete spustit OpenFOAM na SLES 12 HPC výpočetních uzlech v Azure, musíte nainstalovat modul runtime Intel MPI knihovny 5 z [Intel.com lokality](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 je předinstalován v bitové kopie založené na CentOS HPC).  V pozdější fázi v případě potřeby nainstalujte Intel MPI na výpočetní uzly Linux. Příprava pro tento krok po registraci pomocí Intel, použijte odkaz v e-mailu s potvrzením na související webové stránky. Zkopírujte soubor .tgz pro příslušnou verzi technologie Intel MPI odkaz ke stažení. Tento článek je založena na Intel MPI verze 5.0.3.048.
* **Zdroj Pack OpenFOAM** -stáhnout software OpenFOAM zdroj aktualizací Service Pack pro Linux z [OpenFOAM Foundation lokality](http://openfoam.org/download/2-3-1-source/). Tento článek je založen na verzi zdroje Pack 2.3.1, k dispozici ke stažení OpenFOAM 2.3.1.tgz. Postupujte podle pokynů dále v tomto článku rozbalte a kompilace OpenFOAM Linuxových výpočetních uzlů.
* **EnSight** (volitelné) – Pokud chcete zobrazit výsledky OpenFOAM simulace, stáhněte a nainstalujte [EnSight](https://www.ceisoftware.com/download/) program vizualizaci a analýzu. Informace o licencování a stažení najdete na webu EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Nastavení vzájemného vztahu důvěryhodnosti mezi výpočetní uzly
Spuštění úlohy mezi uzly ve více uzlech Linux vyžaduje uzly důvěřovat navzájem (podle **rsh** nebo **ssh**). Při vytváření clusteru HPC Pack pomocí skriptu pro nasazení Microsoft HPC Pack IaaS, skript automaticky nastaví trvalé vzájemné vztahu důvěryhodnosti pro účet správce, který zadáte. Pro uživatele bez oprávnění správce, které vytvoříte v doméně do clusteru budete muset nastavit dočasné vzájemné vztah důvěryhodnosti mezi uzly, když je přidělen úlohu a zrušení relace po dokončení úlohy. K navázání vztahu důvěryhodnosti pro každého uživatele, zadejte pár klíče RSA clusteru, který HPC Pack používá pro vztah důvěryhodnosti.

### <a name="generate-an-rsa-key-pair"></a>Vygenerovat pár klíče RSA
Snadno vygenerovat pár klíče RSA, který obsahuje veřejný klíč a soukromý klíč a spuštěním sady Linux **ssh-keygen** příkaz.

1. Přihlaste se na počítač se systémem Linux.
2. Spusťte následující příkaz:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Stiskněte klávesu **Enter** použít výchozí nastavení, dokud se nedokončí příkaz. Nezadávejte přístupové heslo zde; Po zobrazení výzvy k zadání hesla, stačí stisknout klávesu **Enter**.
   > 
   > 
   
   ![Vygenerovat pár klíče RSA][keygen]
3. Změňte adresář na adresář ~/.ssh. Privátní klíč uložen v id_rsa a veřejný klíč v id_rsa.pub.
   
   ![Privátní a veřejné klíče][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Přidat dvojici klíčů do clusteru HPC Pack
1. Vytvoření připojení vzdálené plochy k vaší hlavního uzlu s vaším účtem správce HPC Pack (účet správce, který můžete nastavit při spuštění skriptu nasazení).
2. Použijte standardní postupy systému Windows Server k vytvoření uživatelského účtu domény v doméně služby Active Directory do clusteru. Například pomocí nástroje Active Directory uživatele a počítače z hlavního uzlu. V příkladech v tomto článku předpokládá, že vytvoříte uživatele domény s názvem hpclab\hpcuser.
3. Vytvořte soubor s názvem C:\cred.xml a zkopírujte do ní data klíče RSA. Ukázkový soubor cred.xml je na konci tohoto článku.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Otevřete příkazový řádek a zadejte následující příkaz pro nastavení data přihlašovací údaje pro účet hpclab\hpcuser. Můžete použít **extendeddata** parametr předat název souboru C:\cred.xml jste vytvořili pro klíčová data.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Tento příkaz se dokončí úspěšně bez výstupu. Po nastavení přihlašovacích údajů pro uživatelské účty, které potřebujete ke spuštění úloh, cred.xml soubor uložte na bezpečném místě, nebo ho odstranit.
5. Pokud jste vygenerovali páru klíčů RSA na jednom z uzlů Linux, nezapomeňte odstranit klíče po dokončení jejich používání. Pokud HPC Pack najde existující id_rsa soubor nebo soubor id_rsa.pub, nenastaví až vzájemné vztah důvěryhodnosti.

> [!IMPORTANT]
> Nedoporučujeme spuštěna úlohu Linux jako správce clusteru na sdílený clusteru, protože úloha odeslána správce spustí pomocí kořenového účtu v uzlech systému Linux. Však úloha odeslána uživatel není správcem spouští pod místním uživatelským účtem Linux se stejným názvem jako uživatel úlohu. V takovém případě HPC Pack nastaví vzájemné vztahu důvěryhodnosti pro tohoto uživatele Linux mezi uzly přiděleny do úlohy. Můžete nastavit uživatele Linux na Linuxových uzlů ručně před spuštěním úlohy, nebo HPC Pack vytváří uživatele automaticky, když je úloha odeslána. Pokud uživatel vytvoří HPC Pack HPC Pack neodstraní po dokončení úlohy. Pokud chcete zkrátit bezpečnostních hrozeb, odebere HPC Pack klíče po dokončení úlohy.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Nastavení sdílené složky pro Linuxové uzly
Nyní nastavte standardní sdílené složky protokolu SMB ve složce z hlavního uzlu. Povolit Linuxové uzly pro přístup k souborům aplikace pomocí běžných cestu, připojte sdílenou složku na Linuxových uzlů. Pokud chcete, můžete použít jinou možnost, jako je například Azure Files sdílená složka – doporučené pro mnoho scénářů - nebo sdílené složky NFS pro sdílení souborů. Naleznete v souboru sdílení informací a podrobné pokyny v [začít pracovat s Linux výpočetní uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md).

1. Vytvořte složku na hlavního uzlu a sdílejte ji pro všechny uživatele, a to nastavením oprávnění pro čtení a zápis. Například sdílet C:\OpenFOAM z hlavního uzlu jako \\ \\SUSE12RDMA HN\OpenFOAM. Zde *SUSE12RDMA HN* je název hostitele hlavního uzlu.
2. Otevřete okno prostředí Windows PowerShell a spusťte následující příkazy:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

První příkaz vytvoří složku s názvem /openfoam ve všech uzlech v LinuxNodes skupiny. V druhém příkazu připojí //SUSE12RDMA-HN/OpenFOAM sdílenou složku na uzlech Linux s dir_mode a file_mode bits nastavený na 777. *Uživatelské jméno* a *heslo* v příkazu by měl být přihlašovací údaje uživatele z hlavního uzlu.

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená "," (čárku) je součástí příkazu.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Instalace MPI a OpenFOAM
Chcete-li spustit OpenFOAM jako úlohu MPI v síti RDMA, kompilace OpenFOAM s knihovnami Intel MPI. 

Nejprve spustit několik **clusrun** příkazy pro instalaci Intel MPI knihovny (pokud ještě není nainstalovaná) a OpenFOAM na Linuxových uzlů. Použijte sdílenou složku hlavního uzlu dříve nakonfigurován pro sdílení souborů instalace mezi uzly Linux.

> [!IMPORTANT]
> Tato instalace a kompilace kroky jsou příklady. Budete potřebovat některé znalosti Linux systému správy zkontrolujte, zda je správně nainstalován závislý kompilátory a knihovny. Možná budete muset změnit určité proměnné prostředí nebo jiná nastavení pro vaše verze Intel MPI a OpenFOAM. Podrobnosti najdete v tématu [Intel MPI knihovny pro Průvodce instalací Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) a [instalace sady zdroj OpenFOAM](http://openfoam.org/download/2-3-1-source/) pro vaše prostředí.
> 
> 

### <a name="install-intel-mpi"></a>Nainstalujte Intel MPI
Uložte stažený instalační balíček pro Intel MPI (l_mpi_p_5.0.3.048.tgz v tomto příkladu) v C:\OpenFoam z hlavního uzlu tak, aby uzly Linux můžete dostat k tomuto souboru z /openfoam. Spusťte **clusrun** k instalaci knihovny Intel MPI pro všechny uzly v systému Linux.

1. Následující příkazy zkopírujte instalační balíček a rozbalte ho /opt/intel na každém uzlu.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Pro tichou instalaci Intel MPI knihovny, použijte soubor silent.cfg. Příklad najdete v ukázkových souborů na konci tohoto článku. Umístěte soubor /openfoam sdílené složky. Podrobnosti o souboru silent.cfg najdete v tématu [Intel MPI knihovny pro Průvodce instalací Linux - tichou instalaci](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Ujistěte se, že můžete uložit vaše silent.cfg soubor jako textový soubor s Linux konce řádků, (pouze LF, není CR LF). Tento krok zajistí, že správně běží na Linuxových uzlů.
   > 
   > 
3. Knihovna MPI Intel instalaci v bezobslužném režimu.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Konfigurace MPI
Pro testování, by měl přidejte následující řádky do /etc/security/limits.conf na každém uzlu Linux:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Po aktualizaci souboru limits.conf, restartujte Linuxových uzlů. Například použít následující **clusrun** příkaz:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Po restartování počítače, zajistěte, aby se jako /openfoam připojené sdílené složce.

### <a name="compile-and-install-openfoam"></a>Kompilace a nainstalujte OpenFOAM
Uložte stažený instalační balíček pro Pack zdroje OpenFOAM (OpenFOAM-2.3.1.tgz v tomto příkladu) do C:\OpenFoam z hlavního uzlu tak, aby uzly Linux můžete dostat k tomuto souboru z /openfoam. Spusťte **clusrun** příkazy ke kompilaci OpenFOAM pro všechny uzly v systému Linux.

1. Vytvoření složky /opt/OpenFOAM na každém uzlu Linux, zkopírujte zdrojový balíček do této složky a rozbalte ho.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Kompilace OpenFOAM s knihovnou MPI Intel, nejprve nastavte některé proměnné prostředí pro Intel MPI i OpenFOAM. Pomocí skriptu bash názvem settings.sh nastavit proměnné. Příklad najdete v ukázkových souborů na konci tohoto článku. Umístíte tohoto souboru (Uložit s konce řádků Linux) v /openfoam sdílené složky. Tento soubor zároveň obsahuje nastavení pro MPI a OpenFOAM moduly runtime, který můžete použít ke spuštění úlohu OpenFOAM později.
3. Nainstalujte závislé balíčky, které jsou potřebné ke kompilaci OpenFOAM. V závislosti na vaší distribuci systému Linux může být nejprve nutné přidat úložiště. Spustit **clusrun** podobná následující příkazy:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    V případě potřeby SSH do každého uzlu Linux ke spuštění příkazů potvrďte, že fungují správně.
4. Spusťte následující příkaz pro kompilaci OpenFOAM. Proces kompilace trvá delší dobu a generuje velké množství informací protokolu na standardní výstup, takže použití **/ prokládaný** možnosti se zobrazí výstup prokládaný.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > "\`" Symbol v příkazu je symbol řídicí pro prostředí PowerShell. "\`&" znamená "a" je část příkazu.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Příprava ke spuštění úlohu OpenFOAM
Teď se spustit úlohu MPI názvem sloshingTank3D, což je jedno z ukázky OpenFoam, na dva uzly Linux. 

### <a name="set-up-the-runtime-environment"></a>Nastavení prostředí runtime
Pro nastavení prostředí runtime pro MPI a OpenFOAM na uzlech Linux, spusťte následující příkaz v okně prostředí Windows PowerShell z hlavního uzlu. (Tento příkaz je platný pro SUSE Linux pouze.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Příprava ukázková data
Použijte sdílenou složku hlavního uzlu, které jste nakonfigurovali dříve ke sdílení souborů mezi uzly Linux (připojit jako /openfoam).

1. SSH na jednu z vaší Linux výpočetních uzlů.
2. Pokud jste to ještě neudělali, spusťte následující příkaz pro nastavení prostředí runtime OpenFOAM.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Zkopírujte ukázková sloshingTank3D do sdílené složky a přejděte k němu.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Pokud použijete výchozí parametry této ukázky, může trvat desítkami minut, proto můžete chtít změnit některé parametry, chcete-li pracovat rychleji. Jeden jednoduchý volba je změnit čas krok proměnné deltaT a writeInterval v systému nebo controlDict souboru. Tento soubor uchovává všechny vstupní data týkající se řízení čas a čtení a zápis dat řešení. Můžete například změnit hodnotu deltaT z hodnotu 0,05 0,5 a hodnotu writeInterval z hodnotu 0,05 0,5.
   
   ![Umožňuje změnit proměnné krok][step_variables]
5. Zadejte požadované hodnoty pro proměnné v systému nebo decomposeParDict souboru. Tento příklad používá dva Linux uzly každý s 8 jádry, takže numberOfSubdomains nastavena na 16 a n hierarchicalCoeffs k (1 1 16), což znamená spustit OpenFOAM souběžně s 16 procesy. Další informace najdete v tématu [OpenFOAM uživatelská příručka: 3,4 aplikace běží paralelně](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Rozložit procesy][decompose]
6. Spusťte následující příkazy z adresáře sloshingTank3D Příprava ukázková data.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. Z hlavního uzlu měli byste vidět, že se zkopírují ukázkových datových souborů do C:\OpenFoam\sloshingTank3D. (C:\OpenFoam je název sdílené složky z hlavního uzlu.)
   
   ![Datové soubory z hlavního uzlu][data_files]

### <a name="host-file-for-mpirun"></a>Soubor hostitele pro mpirun
V tomto kroku vytvoříte soubor hostitele (seznam výpočetní uzly) který **mpirun** příkaz používá.

1. Na jednom z uzlů Linux vytvořte soubor s názvem hostfile pod /openfoam, takže tento soubor lze dosáhnout za /openfoam/hostfile na všech uzlech Linux.
2. Názvy uzlu Linux zapisovat do tohoto souboru. V tomto příkladu obsahuje soubor tyto názvy:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Tento soubor můžete vytvořit také v C:\OpenFoam\hostfile z hlavního uzlu. Pokud zvolíte tuto možnost, uložte ho jako textový soubor s Linuxem konce řádků (pouze LF, není CR LF). To zajišťuje, že ho běží správně na Linuxových uzlů.
   > 
   > 
   
   **Obálka bash skriptu**
   
   Pokud máte mnoho Linuxových uzlů a chcete úlohu spustit pouze na některých z nich, není vhodné použít soubor pevné hostitele, protože nevíte uzlů, které se přidělí úlohu. V takovém případě zápisu Obálka skriptů bash pro **mpirun** automaticky vytvořit soubor hostitele. Můžete najít obálku skriptů bash příklad názvem hpcimpirun.sh na konci tohoto článku a uložte ho jako /openfoam/hpcimpirun.sh. Tento ukázkový skript provede následující akce:
   
   1. Nastaví proměnné prostředí pro **mpirun**a některé parametry přidání příkaz ke spuštění úlohy MPI přes síť RDMA. V takovém případě nastaví následující proměnné:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Vytvoří soubor hostitele podle prostředí proměnné $CCP_NODES_CORES, který je nastaven pomocí hlavního uzlu HPC, když se aktivuje úlohu.
      
      Formát $CCP_NODES_CORES zahrnuje tento vzor:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      kde
      
      * `<Number of nodes>` -počet uzlů přidělených pro tuto úlohu.  
      * `<Name of node_n_...>` -název každého uzlu přidělené této úlohy.
      * `<Cores of node_n_...>` -počet jader na uzel přidělené této úlohy.
      
      Například pokud úlohy dvou uzlech pro spuštění, $CCP_NODES_CORES je podobná
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Volání **mpirun** příkazů a připojí dva parametry na příkazovém řádku.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` -cestu k souboru hostitele vytvoří skript
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` – Proměnná prostředí, která nastavuje hlavního uzlu HPC Pack, která ukládá počet celkový počet jader přidělené této úlohy. V takovém případě určuje počet procesů pro **mpirun**.

## <a name="submit-an-openfoam-job"></a>Odeslat úlohu OpenFOAM
Teď můžete odeslat úlohu ve Správci clusteru HPC. Je třeba předat hpcimpirun.sh skriptu příkazových řádků pro některé úlohy, úlohy.

1. Připojení k vaší hlavního uzlu clusteru a spusťte Správce clusteru HPC.
2. **Ve správě prostředků**, zajistěte, aby výpočetní uzly Linux v **Online** stavu. Pokud tomu tak není, vyberte je a klikněte na tlačítko **přepnout do režimu Online**.
3. V **úlohy správy**, klikněte na tlačítko **nová úloha**.
4. Zadejte název úlohy, jako *sloshingTank3D*.
   
   ![Podrobnosti úlohy][job_details]
5. V **úlohy prostředky**, vyberte typ prostředku jako "Uzel" a nastavte minimálně na 2. Tato konfigurace se spouští úlohy na dva uzly Linux, z nichž každá má osm jader v tomto příkladu.
   
   ![Prostředky úlohy][job_resources]
6. Klikněte na tlačítko **upravit úlohy** v levém navigačním panelu a pak klikněte na tlačítko **přidat** k přidání úkolu do úlohy. Přidání čtyři úkolů do úlohy s následující nastavení a příkazové řádky.
   
   > [!NOTE]
   > Spuštění `source /openfoam/settings.sh` nastaví prostředí runtime OpenFOAM a MPI, takže každý z těchto úloh volá před příkaz OpenFOAM.
   > 
   > 
   
   * **Úloha 1**. Spustit **decomposePar** generovat datové soubory pro spuštění **interDyMFoam** paralelně.
     
     * Jeden uzel přiřadit úlohy
     * **Příkazový řádek** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Pracovní adresář** -/ openfoam/sloshingTank3D
     
     Podívejte se na následující obrázek. Nakonfigurujte ve zbývajících úkolech podobně.
     
     ![Podrobnosti úlohy 1][task_details1]
   * **Úloha 2**. Spustit **interDyMFoam** paralelně výpočetní vzorku.
     
     * Přiřadit úlohy dvou uzlů
     * **Příkazový řádek** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Pracovní adresář** -/ openfoam/sloshingTank3D
   * **Úloha 3**. Spustit **reconstructPar** sloučit sady času adresářů z každý adresář processor_N_ do jedné sady.
     
     * Jeden uzel přiřadit úlohy
     * **Příkazový řádek** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Pracovní adresář** -/ openfoam/sloshingTank3D
   * **Úloha 4**. Spustit **foamToEnsight** paralelně převést výsledek OpenFOAM soubory do EnSight formátu a EnSight soubory umístit do adresáře s názvem Ensight v adresáři případu.
     
     * Přiřadit úlohy dvou uzlů
     * **Příkazový řádek** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Pracovní adresář** -/ openfoam/sloshingTank3D
7. Přidejte závislosti do těchto úloh ve vzestupném pořadí úkolů.
   
   ![Závislosti úkolů][task_dependencies]
8. Klikněte na tlačítko **odeslání** ke spuštění této úlohy.
   
   Ve výchozím nastavení HPC Pack předá úlohu jako váš aktuální účet přihlášeného uživatele. Po kliknutí na tlačítko **odeslání**, může se zobrazit dialogové okno s výzvou k zadání uživatelského jména a hesla.
   
   ![Přihlašovací údaje k úloze][creds]
   
   Za určitých podmínek HPC Pack pamatuje informace o uživateli, zadejte před a nezobrazí tohoto dialogového okna. Chcete-li HPC Pack jej znovu zobrazit, zadejte následující příkaz na příkazovém řádku a potom odeslání úlohy.
   
   ```
   hpccred delcreds
   ```
9. Úloha trvá z desítek minut podle parametry, které jste nastavili pro vzorovou několik hodin. Heat mapa se zobrazuje úlohy spuštěné v uzlech systému Linux. 
   
   ![Heat mapa][heat_map]
   
   Na každém uzlu jsou osm procesy spuštěné.
   
   ![Procesy Linux][linux_processes]
10. Po dokončení úlohy v složek C:\OpenFoam\sloshingTank3D a soubory protokolu na C:\OpenFoam najdete výsledky úlohy.

## <a name="view-results-in-ensight"></a>Zobrazení výsledků v EnSight
Volitelně použijte [EnSight](https://www.ceisoftware.com/) k vizualizaci a analýzu výsledky OpenFOAM úlohy. Další informace o vizualizace a animace v EnSight najdete v tématu to [video průvodce](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Po instalaci EnSight z hlavního uzlu, spusťte ji.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Zobrazí se nádrží v prohlížeči.
   
   ![Nádrž v EnSight][tank]
3. Vytvoření **Isosurface** z **internalMesh**a potom vyberte proměnnou **alpha_water**.
   
   ![Vytvoření isosurface][isosurface]
4. Nastavení barvy pro **Isosurface_part** vytvořili v předchozím kroku. Například nastavte ji na horních blue.
   
   ![Upravit isosurface barev][isosurface_color]
5. Vytvoření **Iso svazku** z **bariéry** výběrem **bariéry** v **částí** panelu a klikněte na tlačítko **Isosurfaces** tlačítka na panelu nástrojů.
6. V dialogovém okně vyberte **typ** jako **Isovolume** a nastavte minimum z **Isovolume rozsah** 0,5. Chcete-li vytvořit isovolume, klikněte na tlačítko **vytvořit s vybraných částí**.
7. Nastavení barvy pro **Iso_volume_part** vytvořili v předchozím kroku. Například nastavte ji na hloubkové horních blue.
8. Nastavení barvy pro **bariéry**. Například nastavte ji na transparentní prázdné.
9. Klikněte na tlačítko **přehrání** a zobrazte si výsledky simulace.
   
    ![Výsledek nádrž][tank_result]

## <a name="sample-files"></a>Ukázkové soubory
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Ukázkový soubor XML konfigurace pro nasazení clusteru pomocí skriptu prostředí PowerShell
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Ukázkový soubor cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Ukázkový soubor silent.cfg k instalaci MPI
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Ukázkový skript settings.sh
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>Ukázkový skript hpcimpirun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
