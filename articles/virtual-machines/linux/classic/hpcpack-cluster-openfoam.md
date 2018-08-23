---
title: Spuštění OpenFOAM se sadou HPC Pack na virtuální počítače s Linuxem | Dokumentace Microsoftu
description: Nasazení clusteru sady Microsoft HPC Pack v Azure a spuštění OpenFOAM úlohy na několika výpočetních uzlech Linuxu přes síť RDMA.
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
ms.openlocfilehash: 9032a0b68c4c8789010b0304b64a63d4924521fb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42057484"
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Spuštění OpenFoam se sadou Microsoft HPC Pack v clusteru Linux RDMA v Azure
Tento článek ukazuje jeden ze způsobů spuštění OpenFoam ve službě Azure virtual machines. Tady, nasazení clusteru sady Microsoft HPC Pack s výpočetními uzly Linuxu v Azure a spustit [OpenFoam](http://openfoam.com/) úlohu s technologií Intel MPI. Pro výpočetní uzly, můžete použít virtuální počítače Azure s podporou RDMA, tak, aby výpočetní uzly komunikovat přes síť Azure RDMA. Další možnosti pro spuštění OpenFoam v Azure zahrnují plně nakonfigurované komerční Image, které jsou k dispozici na webu Marketplace, jako je například společnosti UberCloud [OpenFoam 2.3 na CentOS 6](https://azuremarketplace.microsoft.com/marketplace/apps/cfd-direct.cfd-direct-from-the-cloud)a to spuštěním na [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (pro operaci otevřít pole a manipulaci s) je balíček softwaru open source výpočty dynamiky tekutin (CFD), která se běžně používá v inženýrství a vědecké účely, v organizacích komerční i akademického. Obsahuje nástroje pro meshing, zejména snappyHexMesh, paralelizované mesher pro komplexní CAD geometrie a pro provedení před instrumentací a následného zpracování. Téměř všechny procesy jsou spuštěny paralelně, umožňuje uživatelům využívat všech výhod hardwaru počítače mají k dispozici.  

Microsoft HPC Pack obsahuje funkce, které chcete spouštět ve velkém měřítku HPC a paralelních aplikací, jako jsou třeba aplikace MPI na clusterech virtuálních počítačích Microsoft Azure. HPC Pack podporuje také aplikace v Linuxu výpočetního uzlu, které virtuální počítače nasazené v clusteru HPC Pack spuštěné HPC pro Linux. Zobrazit [začít pracovat s Linuxovými výpočetními uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md) s úvodem do systému Linux výpočetních uzlů pomocí sady HPC Pack.

> [!NOTE]
> Tento článek ukazuje, jak pomocí sady HPC Pack spuštění úlohy Linux MPI. Předpokládá se, že máte některé znalosti s Správa systému Linux a spouštění úloh MPI na clusterech s Linuxem. Pokud používáte verzi MPI a OpenFOAM lišit od těch uvedených v tomto článku, budete pravděpodobně nutné upravit některé kroky instalace a konfigurace. 
> 
> 

## <a name="prerequisites"></a>Požadavky
* **Výpočetní uzly HPC Pack clusteru s podporou RDMA Linuxu** – nasazení clusteru HPC Pack s velikosti A8, A9, H16r, nebo H16rm Linuxových výpočetních uzlů pomocí [šablony Azure Resource Manageru](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) nebo [Azure Skript prostředí PowerShell](hpcpack-cluster-powershell-script.md). Zobrazit [začít pracovat s Linuxovými výpočetními uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md) požadavky a kroky pro jednu z možností. Pokud zvolíte možnost nasazení skriptu prostředí PowerShell, najdete v článku Ukázkový konfigurační soubor v ukázkové soubory na konci tohoto článku. Pomocí této konfigurace k nasazení clusteru HPC Pack založené na Azure skládající se z hlavního uzlu velikosti A8 systému Windows Server 2012 R2 a 2 velikosti A8 operačním systémem SUSE Linux Enterprise Server 12 výpočetních uzlů. Nahraďte příslušnými hodnotami pro vaše předplatné a služby názvy. 
  
  **Další věcí, které znáte**
  
  * Síťové požadavky Linux RDMA v Azure, najdete v části [vysoce výkonné výpočetní velikosti virtuálních počítačů](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Pokud použijete možnost nasazení skriptu prostředí Powershell, nasaďte všechny Linuxu výpočetních uzlů v rámci jedné cloudové služby, použití síťového připojení RDMA.
  * Po nasazení uzly s Linuxem, připojte pomocí SSH k provádět žádné další úlohy správy. Podrobnosti připojení SSH pro každý virtuální počítač s Linuxem najdete na webu Azure Portal.  
* **Intel MPI** – Pokud chcete spuštění OpenFOAM na výpočetních uzlech SLES 12 HPC v Azure, musíte nainstalovat modul runtime Intel MPI knihovny 5 z [Intel.com lokality](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 je předinstalován v bitové kopie založené na CentOS HPC).  V pozdějším kroku v případě potřeby nainstalujte Intel MPI ve výpočetních uzlech Linuxu. Příprava pro tento krok po registraci pomocí Intel, použijte odkaz v e-mailové potvrzení na související webové stránky. Zkopírujte odkaz ke stažení pro soubor .tgz pro příslušnou verzi Intel MPI. Tento článek je založen na verzi 5.0.3.048 Intel MPI.
* **OpenFOAM zdroje balíčku** -OpenFOAM zdroje balíčku softwaru stáhnout pro Linux z [webu OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Tento článek je založen na verzi zdroje balíčku 2.3.1, k dispozici ke stažení OpenFOAM 2.3.1.tgz. Postupujte podle pokynů dále v tomto článku můžete rozbalit a kompilovat OpenFOAM na Linuxových výpočetních uzlech.
* **EnSight** (volitelné) – Pokud chcete zobrazit výsledky simulace OpenFOAM, stáhněte a nainstalujte [EnSight](https://ensighttransfe.wpengine.com/direct-access-downloads/) vizualizace a analýzy programu. Informace o licencování a stažení se v lokalitě EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Nastavte si vzájemné důvěry mezi jednotlivými výpočetními uzly
Spuštění úlohy mezi uzly na několika uzlech Linuxu vyžaduje, aby uzly důvěřovat navzájem (podle **rsh** nebo **ssh**). Při vytváření clusteru HPC Pack se skriptem nasazení IaaS sady Microsoft HPC Pack skriptu automaticky nastaví trvalé vzájemné důvěry pro účet správce, který zadáte. Pro uživatele bez oprávnění správce, který vytvoříte v doméně clusteru máte k nastavení dočasného vzájemné důvěry mezi uzly při přidělení úlohy k nim a zničit relace po dokončení úlohy. K navázání vztahu důvěryhodnosti pro jednotlivé uživatele, poskytnout dvojici klíčů RSA do clusteru HPC Pack se používá pro vztah důvěryhodnosti.

### <a name="generate-an-rsa-key-pair"></a>Generování páru klíčů RSA
Je snadné vygenerovat pár klíče RSA, který obsahuje veřejný klíč a privátní klíč, spuštěním Linuxový **ssh-keygen** příkazu.

1. Přihlaste se k počítači s Linuxem.
2. Spusťte následující příkaz:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Stisknutím klávesy **Enter** používat výchozí nastavení, dokud se příkaz dokončí. Nezadávejte přístupové heslo. Po zobrazení výzvy k zadání hesla, stačí stisknout kombinaci kláves **Enter**.
   > 
   > 
   
   ![Generování páru klíčů RSA][keygen]
3. Změňte adresář na adresář ~/.ssh. Privátní klíč je uložený ve id_rsa a veřejný klíč v id_rsa.pub.
   
   ![Privátní a veřejné klíče][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Přidat dvojici klíčů do clusteru HPC Pack
1. Vytvoření připojení vzdálené plochy k hlavnímu uzlu s účtem správce sady HPC Pack (účet správce, které jste nastavili při spuštění skriptu nasazení).
2. Vytvořte účet uživatele domény v doméně služby Active Directory clusteru pomocí standardní postupy systému Windows Server. Například můžete použijte nástroj uživatel služby Active Directory a počítače hlavního uzlu. V příkladech v tomto článku se předpokládá, že vytvoříte uživatele domény s názvem hpclab\hpcuser.
3. Vytvořte soubor s názvem C:\cred.xml a zkopírujte do něj data klíče RSA. Ukázkový soubor cred.xml je na konci tohoto článku.
   
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
   
   Tento příkaz se dokončí úspěšně žádný výstup. Po nastavení přihlašovacích údajů pro uživatelské účty, které je potřeba spouštět úlohy, cred.xml soubor uložte na bezpečné místo, nebo ho odstranit.
5. Pokud jste vygenerovali dvojici klíčů RSA v jednom z uzlů s Linuxem, nezapomeňte odstranit klíče po dokončení jejich používání. HPC Pack najde existující id_rsa soubor nebo soubor id_rsa.pub, je nastavit není míra vzájemné důvěry.

> [!IMPORTANT]
> Nedoporučujeme ale spustili v režimu Správce clusteru Linux úlohy ve sdíleném clusteru, protože spouští úlohu odeslal správce pomocí kořenového účtu v uzlech systému Linux. Však úlohu odeslal uživatele bez oprávnění správce spouští pod místním uživatelským účtem Linux se stejným názvem jako uživatel úlohu. V takovém případě sady HPC Pack nastaví vzájemné důvěry pro tohoto uživatele Linux napříč uzly přiděleny do úlohy. Můžete nastavit uživatele Linuxu ručně na Linuxové uzly před spuštěním úlohy, nebo sady HPC Pack vytváří uživatele automaticky, když je úloha odeslána. Pokud se sadou HPC Pack vytvoří uživatele, sady HPC Pack neodstraní po dokončení úlohy. Ke snížení rizika zabezpečení, odebere sady HPC Pack klíčů po dokončení úlohy.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Nastavení sdílené složky pro uzly s Linuxem
Nyní nastavte standardní sdílenou složku protokolu SMB ve složce hlavního uzlu. Povolit Linuxové uzly pro přístup k souborům aplikace se společná cesta, připojte sdílenou složku v uzlech systému Linux. Pokud chcete, můžete použít jiný soubor možností, jako je například do sdílené složky Azure soubory – vhodné pro mnoho scénářů - nebo sdílené složky NFS pro sdílení obsahu. Zobrazit soubor sdílet informace a podrobné kroky [začít pracovat s Linuxovými výpočetními uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md).

1. Vytvoření složky na k hlavnímu uzlu a sdílet pro všechny uživatele tak, že nastavíte oprávnění ke čtení a zápisu. Například sdílet C:\OpenFOAM hlavního uzlu jako \\ \\SUSE12RDMA HN\OpenFOAM. Tady *SUSE12RDMA HN* je název hostitele k hlavnímu uzlu.
2. Otevřete okno Windows Powershellu a spusťte následující příkazy:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

První příkaz vytvoří složku s názvem /openfoam na všechny uzly ve skupině LinuxNodes. Druhý příkaz slouží pro připojení //SUSE12RDMA-HN/OpenFOAM sdílenou složku na uzly s Linuxem pomocí dir_mode a file_mode bity nastaveny na 777. *Uživatelské jméno* a *heslo* v příkazu by měl být přihlašovací údaje uživatele hlavního uzlu.

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená "," (znak čárky) je součástí příkazu.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Instalace MPI a OpenFOAM
Spuštění OpenFOAM jako úlohu služby MPI sítě RDMA, budete muset kompilovat OpenFOAM s knihovnami Intel MPI. 

Nejprve spusťte několik **clusrun** příkazy instalace Intel MPI knihovny (pokud ještě není nainstalován) a OpenFOAM na uzly s Linuxem. Použijte sdílenou složku hlavního uzlu, dříve nakonfigurovaný tak, aby sdílet instalačních souborů mezi uzly s Linuxem.

> [!IMPORTANT]
> Tato instalace a kompilaci kroky jsou příklady. Potřebujete základní znalost správy systému Linux k zajištění, že jsou správně nainstalovány závislé kompilátory a knihovny. Můžete třeba upravit určité proměnné prostředí nebo jiné nastavení pro vaši verzi Intel MPI a OpenFOAM. Podrobnosti najdete v tématu [Intel MPI knihovny pro Linux instalační příručce](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) a [instalace sady zdroje OpenFOAM](http://openfoam.org/download/2-3-1-source/) pro vaše prostředí.
> 
> 

### <a name="install-intel-mpi"></a>Instalace Intel MPI
Uložení staženého instalačního balíčku pro Intel MPI (l_mpi_p_5.0.3.048.tgz v tomto příkladu) do C:\OpenFoam hlavního uzlu tak, aby uzly s Linuxem můžete přistupovat k tomuto souboru z /openfoam. Potom spusťte **clusrun** instalace Intel MPI knihovny pro všechny uzly v Linuxu.

1. Následující příkazy zkopírujte instalační balíček a rozbalte ho /opt/intel na každém uzlu.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Tiché instalace Intel MPI knihovny, použijte soubor silent.cfg. Příklad najdete v ukázkové soubory na konci tohoto článku. Umístěte soubor /openfoam sdílenou složku. Podrobnosti o souboru silent.cfg najdete v tématu [Intel MPI knihovny pro Průvodce instalací Linux - tichá instalace](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Ujistěte se, že můžete uložit soubor jako textový soubor s Linuxem silent.cfg once (pouze znaky LF, nelze CR LF). Tento krok zajistí, že správně běží na uzly s Linuxem.
   > 
   > 
3. Instalace Intel MPI knihovny v bezobslužném režimu.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Konfigurace MPI
Pro testování, měli byste k /etc/security/limits.conf na všech uzlech Linuxu přidat následující řádky:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Po aktualizaci souboru limits.conf, restartujte uzly s Linuxem. Použijte například **clusrun** příkaz:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Po restartování počítače se ujistěte, že je jako /openfoam připojené sdílené složky.

### <a name="compile-and-install-openfoam"></a>Kompilace a nainstalovat OpenFOAM
Uložte staženého instalačního balíčku pro balíček OpenFOAM zdroje (OpenFOAM-2.3.1.tgz v tomto příkladu) do C:\OpenFoam hlavního uzlu tak, aby uzly s Linuxem můžete přistupovat k tomuto souboru z /openfoam. Potom spusťte **clusrun** příkazy ke kompilaci OpenFOAM na všech uzlech systému Linux.

1. Vytvořte /opt/OpenFOAM složky na všech uzlech Linuxu, zkopírujte zdrojový balíček do této složky a rozbalte ho.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Chcete-li zkompilovat OpenFOAM s knihovnou Intel MPI, nejprve nastavte některé proměnné prostředí pro Intel MPI a OpenFOAM. Pomocí skriptu bash volá settings.sh můžete nastavit proměnné. Příklad najdete v ukázkové soubory na konci tohoto článku. Ve sdílené složce /openfoam umístíte tento soubor (uložen s Linuxem konce řádků). Tento soubor obsahuje také nastavení pro MPI a OpenFOAM moduly runtime, který se spouští úloha OpenFOAM použijete později.
3. Nainstalujte potřebné ke kompilaci OpenFOAM závislé balíčky. V závislosti na vaší distribuci Linuxu možná musíte nejprve přidat do úložiště. Spustit **clusrun** příkazy podobný následujícímu:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    V případě potřeby SSH do každého uzlu Linux ke spuštění příkazů pro potvrzení, že jsou správně fungovat.
4. Spusťte následující příkaz pro kompilaci OpenFOAM. Proces kompilace nějakou dobu trvá a generuje velké množství informace protokolu do standardního výstupu, proto **/ prokládané** možnosti se zobrazí výstup prokládané.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > "\`" Symbol v příkazu je symbol řídicí pro prostředí PowerShell. "\`&" znamená, "&" je součástí příkazu.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Příprava na spuštění OpenFOAM úlohy
Teď připravte se na spuštění úlohy MPI sloshingTank3D, který je jednou z ukázek OpenFoam, volá se na dva uzly s Linuxem. 

### <a name="set-up-the-runtime-environment"></a>Nastavení prostředí runtime
Nastavení prostředí modulu runtime MPI a OpenFOAM v uzlech systému Linux, spusťte následující příkaz v okně Windows Powershellu na hlavní uzel. (Tento příkaz je platný pro SUSE Linux pouze.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Příprava ukázkových dat
Použijte sdílenou složku hlavního uzlu, který jste nakonfigurovali dříve ke sdílení souborů uzly s Linuxem (připojit jako /openfoam).

1. SSH do jednoho z vašich Linux výpočetních uzlů.
2. Spusťte následující příkaz pro nastavení prostředí runtime OpenFOAM, pokud jste to ještě neudělali.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Zkopírujte ukázkový sloshingTank3D do sdílené složky a přejít k němu.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Při použití výchozích parametrů této ukázky, může trvat desítky minut, takže můžete chtít změnit některé parametry, aby běžel rychleji. Jeden jednoduchý je chcete upravit čas krok proměnné deltaT a writeInterval v systému/controlDict souboru. Tento soubor uchovává všechny vstupní data týkající se řízení času a čtení a zápis dat z řešení. Můžete například změnit hodnotu deltaT z 0,05 0,5, znamená a hodnota writeInterval z 0,05 0,5, znamená.
   
   ![Upravit krok proměnné][step_variables]
5. Zadejte požadované hodnoty pro proměnné v systému/decomposeParDict souboru. Tento příklad používá dva uzly s Linuxem každý s 8 jádry, takže nastavte numberOfSubdomains 16 a n hierarchicalCoeffs k (1 1 16), což znamená, že spuštění OpenFOAM paralelně s 16 procesy. Další informace najdete v tématu [OpenFOAM uživatelskou příručku: 3.4 běžící aplikace paralelně](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Rozložit procesy][decompose]
6. Spusťte následující příkazy z adresáře sloshingTank3D Příprava ukázková data.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. Hlavního uzlu měli byste vidět, že se zkopírují ukázkových datových souborů do C:\OpenFoam\sloshingTank3D. (C:\OpenFoam je název sdílené složky na k hlavnímu uzlu.)
   
   ![Datové soubory na hlavní uzel][data_files]

### <a name="host-file-for-mpirun"></a>Soubor hostitele pro mpirun
V tomto kroku vytvoříte soubor hostitelů (seznam výpočetních uzlů) která **mpirun** příkaz používá.

1. Na jednom z uzlů s Linuxem vytvořte soubor s názvem hostfile pod /openfoam, takže tento soubor se dá kontaktovat na /openfoam/hostfile na všechny uzly s Linuxem.
2. Názvy uzlů Linux zapisovat do tohoto souboru. V tomto příkladu soubor obsahuje následující názvy:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Tento soubor můžete taky vytvořit v C:\OpenFoam\hostfile hlavního uzlu. Pokud zvolíte tuto možnost, uložte jej jako textový soubor s Linuxem once (pouze znaky LF, nelze CR LF). Tím se zajistí, že správně běží na uzly s Linuxem.
   > 
   > 
   
   **Obálka skriptu bash**
   
   Pokud máte mnoho uzlů s Linuxem a má vaše úloha spouštět jenom některé z nich, není vhodné použít soubor pevné hostitele, protože zatím nevíte, uzly, na kterých bude přidělena pro vaši úlohu. V takovém případě zápisu obálku skriptu bash pro **mpirun** automaticky vytvořit soubor hostitele. Můžete najít obálky skriptu bash příklad volá hpcimpirun.sh na konci tohoto článku a uložte ho jako /openfoam/hpcimpirun.sh. Tento ukázkový skript provede následující akce:
   
   1. Nastaví proměnné prostředí pro **mpirun**a některé parametry přidání příkazu ke spuštění úlohy MPI přes síť RDMA. V takovém případě nastaví následující proměnné:
      
      * I_MPI_FABRICS = shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION = 0
   2. Vytvoří soubor hostitelů podle prostředí proměnné $CCP_NODES_CORES, která se nastavuje přes hlavní uzel HPC, když se aktivuje úloha.
      
      Formát $CCP_NODES_CORES používá tento vzor:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      kde
      
      * `<Number of nodes>` -počet uzlů přidělených pro tuto úlohu.  
      * `<Name of node_n_...>` -název každého uzlu přidělené pro tuto úlohu.
      * `<Cores of node_n_...>` -počet jader na uzel přidělené pro tuto úlohu.
      
      Například pokud úloha potřebuje dvou uzlech pro spuštění, $CCP_NODES_CORES je podobný
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Volání **mpirun** příkaz a připojí dva parametry příkazového řádku.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` -cestu k souboru hostitele, skript vytvoří
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` -Nastavit hlavní uzel HPC Pack, která ukládá počet celkový počet jader, které jsou přiděleny tuto úlohu proměnné prostředí. V tomto případě určuje počet procesů pro **mpirun**.

## <a name="submit-an-openfoam-job"></a>Odeslat úlohu OpenFOAM
Nyní můžete odeslat úlohu v modulu Správce clusteru HPC. Je potřeba předat hpcimpirun.sh skript v příkazových řádků pro některé úkoly úlohy.

1. Připojení k hlavnímu uzlu vašeho clusteru a spustit Správce clusterů HPC.
2. **Ve správě zdrojů**, se ujistit, že výpočetní uzly s Linuxem **Online** stavu. Pokud není, vyberte je a klikněte na tlačítko **přepnout do režimu Online**.
3. V **Správa úloh**, klikněte na tlačítko **nová úloha**.
4. Zadejte název úlohy, jako například *sloshingTank3D*.
   
   ![Podrobnosti úlohy][job_details]
5. V **zdroje projektu**, vyberte typ prostředku, jako "Uzel" a nastavte minimálně na 2. Tato konfigurace spustí úlohu na dva uzly s Linuxem, z nichž každá má osmi jádry v tomto příkladu.
   
   ![Prostředky úlohy][job_resources]
6. Klikněte na tlačítko **upravit úlohy** v levém navigačním panelu a pak klikněte na tlačítko **přidat** přidání úkolu do úlohy. Přidejte čtyři úkoly do úlohy pomocí následujících příkazových řádků a nastavení.
   
   > [!NOTE]
   > Spuštění `source /openfoam/settings.sh` nastaví OpenFOAM a MPI prostředí modulu runtime, aby každá z těchto úloh volá před příkazem OpenFOAM.
   > 
   > 
   
   * **Úloha 1**. Spustit **decomposePar** generovat datové soubory ke spuštění **interDyMFoam** paralelně.
     
     * Přiřaďte jeden uzel k úkolu
     * **Příkazový řádek** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Pracovní adresář** – / openfoam/sloshingTank3D
     
     Podívejte se na následující obrázek. Podobně můžete nakonfigurovat ve zbývajících úkolech.
     
     ![Podrobnosti úlohy 1][task_details1]
   * **Úloha 2**. Spustit **interDyMFoam** paralelní výpočetní vzorku.
     
     * Dva uzly přiřazení k úkolu
     * **Příkazový řádek** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Pracovní adresář** – / openfoam/sloshingTank3D
   * **Úloha 3**. Spustit **reconstructPar** sloučit nastaví čas adresáře v každém adresáři processor_N_ do jediné sady.
     
     * Přiřaďte jeden uzel k úkolu
     * **Příkazový řádek** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Pracovní adresář** – / openfoam/sloshingTank3D
   * **Úloha 4**. Spustit **foamToEnsight** paralelní k převedení výsledku OpenFOAM soubory do EnSight formátování a umístěte EnSight soubory v adresáři s názvem Ensight v adresáři případu.
     
     * Dva uzly přiřazení k úkolu
     * **Příkazový řádek** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Pracovní adresář** – / openfoam/sloshingTank3D
7. Přidáte závislosti s těmito úkoly ve vzestupném pořadí úkolů.
   
   ![Závislosti úkolů][task_dependencies]
8. Klikněte na tlačítko **odeslat** ke spuštění této úlohy.
   
   HPC Pack ve výchozím nastavení, odešle úlohu jako váš aktuální účet přihlášeného uživatele. Po kliknutí na **odeslat**, může se zobrazit dialogové okno s výzvou k zadání uživatelského jména a hesla.
   
   ![Přihlašovací údaje k úloze][creds]
   
   Za určitých podmínek sady HPC Pack si pamatuje informace o uživateli vstupní před a toto dialogové okno nezobrazí. Chcete-li sady HPC Pack ji znovu zobrazit, zadáním následujícího příkazu na příkazovém řádku a potom odešlete úlohu.
   
   ```
   hpccred delcreds
   ```
9. Úloha trvá z desítek minut i několik hodin podle parametrů, které jste nastavili pro ukázku. V heat mapě zobrazit úlohy spuštěné na uzlech systému Linux. 
   
   ![Heat mapa][heat_map]
   
   Na každém uzlu osm procesy jsou spuštěny.
   
   ![Procesy Linux][linux_processes]
10. Až se úloha dokončí, najdete ve složkách v rámci C:\OpenFoam\sloshingTank3D a soubory protokolu na C:\OpenFoam výsledky úlohy.

## <a name="view-results-in-ensight"></a>Zobrazení výsledků v EnSight
Volitelně použít [EnSight](http://www.ensight.com/) umožňuje vizualizovat a analyzovat výsledky OpenFOAM úlohy. Další informace o vizualizaci a animace v EnSight, najdete v tomto [videa průvodce](http://www.ensight.com/ensight.com/envideo/).

1. Po instalaci EnSight hlavního uzlu, spusťte ji.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Najdete v článku hladině v nádrži v prohlížeči.
   
   ![Hladině v nádrži v EnSight][tank]
3. Vytvoření **Isosurface** z **internalMesh**a klikněte na tlačítko proměnnou **alpha_water**.
   
   ![Vytvoření isosurface][isosurface]
4. Nastavit barvu **Isosurface_part** vytvořili v předchozím kroku. Například nastavte ji na vody modrá.
   
   ![Upravit barvu isosurface][isosurface_color]
5. Vytvoření **Iso-volume** z **stěn** tak, že vyberete **stěn** v **částí** panelu a klikněte na tlačítko **Isosurfaces** tlačítko na panelu nástrojů.
6. V dialogovém okně vyberte **typ** jako **Isovolume** a nastavte minimum pro **Isovolume rozsah** 0,5, znamená. Vytvořte isovolume, klikněte na tlačítko **vytvořit pomocí vybrané části**.
7. Nastavit barvu **Iso_volume_part** vytvořili v předchozím kroku. Například nastavte ji na hloubkové vody modrá.
8. Nastavit barvu **stěn**. Například nastavte na transparentní bílou.
9. Nyní klikejte na příkaz **Přehrát** zobrazíte výsledky simulace.
   
    ![Výsledek hladiny v nádržích][tank_result]

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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Ukázkový soubor silent.cfg instalace MPI
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
