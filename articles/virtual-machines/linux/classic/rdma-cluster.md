---
title: Nastavení clusteru s podporou Linux RDMA ke spuštění aplikací MPI | Microsoft Docs
description: Vytvoření clusteru s podporou Linux velikosti H16r, H16mr, A8 nebo A9 virtuálních počítačů, které se použije ke spuštění aplikací MPI sítě Azure RDMA
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Nastavení clusteru Linux RDMA pro spouštění aplikací MPI
Zjistěte, jak nastavit clusteru s podporou Linux RDMA v Azure pomocí [vysokovýkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ke spouštění paralelních aplikací Message Passing Interface (MPI). Tento článek obsahuje kroky k přípravě image Linux HPC ke spuštění v clusteru s podporou Intel MPI. Po přípravě nasazení clusteru virtuálních počítačů pomocí tuto bitovou kopii a jeden velikostí podporující RDMA virtuální počítač Azure (aktuálně H16r, H16mr, A8 a A9). Použijte cluster ke spouštění aplikací MPI, které efektivně komunikují přes síť s nízkou latencí, vysokou propustností založené na technologii do paměti vzdáleného přímý přístup do (počítače RDMA).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager](../../../resource-manager-deployment-model.md) a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="cluster-deployment-options"></a>Možnosti nasazení clusteru
Toto jsou metody, které můžete použít k vytvoření clusteru s podporou Linux RDMA s nebo bez Plánovač úloh.

* **Azure CLI skripty**: Jak uvidíte později v tomto článku, použijte [rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md) (CLI) pro skript nasazení clusteru s podporou RDMA podporovat virtuálních počítačů. Uzly clusteru rozhraní příkazového řádku v režimu správy služby sériově vytvoří v modelu nasazení classic, takže nasazení mnoho výpočetních uzlů může trvat několik minut. Chcete-li povolit síťového připojení RDMA při použití modelu nasazení classic, nasaďte virtuální počítače ve stejné cloudové služby.
* **Šablony Azure Resource Manageru**: modelu nasazení Resource Manager můžete také použít k nasazení clusteru s podporou RDMA podporovat virtuální počítače připojené k síti RDMA. Můžete [vytvořit vlastní šablonu](../../../resource-group-authoring-templates.md), nebo zkontrolujte [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/) pro šablony přispěli k nasazení řešení. Chcete společnosti Microsoft nebo komunitou. Šablony Resource Manageru nabízejí rychlý a spolehlivý způsob, jak nasadit Linux cluster. Pokud chcete povolit síťového připojení RDMA při použití modelu nasazení Resource Manager, nasaďte virtuální počítače ve stejné sadě dostupnosti.
* **HPC Pack**: vytvoření clusteru s podporou sady Microsoft HPC Pack v Azure a přidat podporu rdma výpočetní uzly, které využívají podporované distribuce systému Linux pro přístup k síti RDMA. Další informace najdete v tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Kroky nasazení ukázkové v klasickém modelu
Následující kroky ukazují, jak používat rozhraní příkazového řádku Azure k nasazení virtuálního počítače SUSE Linux Enterprise Server (SLES) 12 SP1 HPC z Azure Marketplace, přizpůsobit a vytvořit vlastní image virtuálního počítače. Pak můžete použít bitovou kopii pro skript nasazení clusteru s podporou RDMA podporovat virtuálních počítačů.

> [!TIP]
> Podobným způsobem použijte k nasazení clusteru s podporou RDMA podporovat virtuálních počítačů založené na imagích na základě CentOS HPC v Azure Marketplace. Některé kroky poněkud, jak jsme uvedli lišit. 
>
>

### <a name="prerequisites"></a>Požadavky
* **Klientský počítač**: je třeba klientský počítač Mac, Linux nebo Windows ke komunikaci s Azure. Tento postup předpokládá, že používáte klienta Linux.
* **Předplatné Azure**: Pokud nemáte předplatné, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) si během několika minut. Pro větší clustery zvažte průběžnými platbami předplatné nebo jiné možnosti nákupu.
* **Virtuální počítač velikost dostupnosti**: následující instance velikosti jsou podporující RDMA: H16r, H16mr, A8 a A9. Zkontrolujte [produkty podle oblasti](https://azure.microsoft.com/regions/services/) pro dostupnost v oblastech Azure.
* **Kvóta jader**: možná budete muset zvýšit kvótu jader, který má nasadit cluster virtuálních počítačů náročné. Pokud chcete nasadit virtuální počítače 8 A9, jak je znázorněno v tomto článku se například potřebovat nejméně 128 jader. Vaše předplatné může také omezit počet jader, který můžete nasadit v určité rodiny velikost virtuálního počítače, včetně H-series. Požádat o zvýšení kvóty, [otevřete žádosti o podporu online zákazníka](../../../azure-supportability/how-to-create-azure-support-request.md) zdarma.
* **Rozhraní příkazového řádku Azure**: [nainstalovat](../../../cli-install-nodejs.md) rozhraní příkazového řádku Azure a [připojení k předplatnému Azure](/cli/azure/authenticate-azure-cli) z klientského počítače.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Zřizování virtuálních počítačů SLES 12 SP1 HPC
Po přihlášení do Azure pomocí Azure CLI, spusťte `azure config list` potvrďte, že výstup zobrazuje režimu správy služby. Pokud ne, nastavte režim spuštěním tohoto příkazu:

    azure config mode asm


Zadejte následující příkaz k zobrazení seznamu všechny odběry, které mají oprávnění využívat:

    azure account list

Aktuální aktivní předplatné, je označen `Current` nastavena na `true`. Pokud toto předplatné není ten, který chcete použít k vytvoření clusteru, nastavte ID příslušné předplatné jako aktivní předplatné:

    azure account set <subscription-Id>

Pokud chcete zobrazit veřejně dostupné Image SLES 12 SP1 HPC v Azure, spusťte příkaz podobně jako tento, za předpokladu, že vaše prostředí podporuje prostředí **grep**:

    azure vm image list | grep "suse.*hpc"

Zřídit podporou RDMA virtuální počítač s bitovou kopii SLES 12 SP1 HPC spuštěním příkazu takto:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Kde:

* Velikost (v tomto příkladu A9) je jedním z velikosti virtuálních počítačů RDMA podporovat.
* Externí číslo portu SSH (22 v tomto příkladu, což je výchozí SSH) je platné číslo portu. Interní číslo portu SSH je nastavena na 22.
* Vytvoří novou cloudovou službu se v oblasti Azure určeného umístění. Zadejte umístění, ve kterém je k dispozici velikost virtuálního počítače, které zvolíte.
* Pro podporu s prioritou SUSE (který budou vám účtovány dodatečné poplatky) název bitové kopie SLES 12 SP1 aktuálně může být jedna z těchto dvou možností: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Přizpůsobení virtuálního počítače
Až virtuální počítač dokončí zřizování, SSH k virtuálnímu počítači pomocí externí IP adresu Virtuálního počítače (nebo název DNS) a externí port číslo jste nakonfigurovali a pak jej přizpůsobit. Podrobnosti připojení najdete v tématu [přihlášení do virtuálního počítače se systémem Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Proveďte příkazy jako uživatel, který jste nakonfigurovali ve virtuálním počítači, pokud kořenový přístup je nutný k dokončení kroku.

> [!IMPORTANT]
> Microsoft Azure neposkytuje kořenový přístup k virtuálním počítačům systému Linux. Chcete-li získat přístup pro správu při připojení jako uživatel k virtuálnímu počítači, spusťte příkazy pomocí `sudo`.
>
>

* **Aktualizace**: nainstalujte aktualizace pomocí zypper. Také můžete chtít nainstalovat nástroje pro systém souborů NFS.

  > [!IMPORTANT]
  > V SLES 12 SP1 HPC virtuální počítač doporučujeme nepoužijete jádra aktualizace, které může způsobit problémy s Linux RDMA ovladače.
  >
  >
* **Intel MPI**: dokončení instalace Intel MPI ve virtuálním počítači se systémem SLES 12 SP1 HPC spuštěním následujícího příkazu:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Uzamknout paměť**: přidat kódy pro MPI k uzamčení paměti k dispozici pro RDMA, nebo změnit následující nastavení v souboru /etc/security/limits.conf. Musíte kořenový přístup k úpravě tohoto souboru.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Pro účely testování můžete také nastavit memlock na neomezený. Například: `<User or group name>    hard    memlock unlimited`. Další informace najdete v tématu [známé nejlepší metody pro nastavení uzamčení velikost paměti](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Klíče SSH pro virtuální počítače SLES**: generování SSH klíče k navázání vztahu důvěryhodnosti pro váš uživatelský účet mezi výpočetní uzly v SLES clusteru při spouštění úloh MPI. Pokud nasadíte virtuální počítač na základě CentOS HPC, nemusíte postupujte podle tohoto kroku. Zobrazí pokyny později v tomto článku nastavit passwordless SSH vztah důvěryhodnosti mezi uzly clusteru po zachycení bitové kopie a nasazení clusteru.

    K vytvoření klíčů SSH, spusťte následující příkaz. Po zobrazení výzvy pro vstup, vyberte **Enter** ke generování klíče ve výchozím umístění bez nastavení hesla.

        ssh-keygen

    Připojí veřejný klíč v souboru authorized_keys známé veřejných klíčů.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    V adresáři ~/.ssh upravovat nebo vytvářet do konfiguračního souboru. Zadejte rozsah IP adres privátní sítě, kterou chcete použít v Azure (v tomto příkladu 10.32.0.0/16):

        host 10.32.0.*
        StrictHostKeyChecking no

    Případně seznam IP adresu privátní sítě jednotlivé virtuální počítače v clusteru následujícím způsobem:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Konfigurace `StrictHostKeyChecking no` můžete vytvořit představuje potenciální bezpečnostní riziko, pokud není zadán konkrétní IP adresu nebo rozsah.
  >
  >
* **Aplikace**: Nainstalujte aplikace potřebují nebo provedení jiných úprav, než zachytit bitovou kopii.

### <a name="capture-the-image"></a>Zachycení bitové kopie
K zachycení bitové kopie, nejprve spusťte následující příkaz na virtuální počítač s Linuxem. Tento příkaz deprovisions virtuální počítač ale uchovává uživatelské účty a klíče SSH, které nastavíte.

```
sudo waagent -deprovision
```

Na klientském počítači spusťte následující příkazy rozhraní příkazového řádku Azure k zachycení bitové kopie. Další informace najdete v tématu [jak zachytit klasický Linuxový virtuální počítač jako image](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Po spuštění těchto příkazů zachycenou image virtuálního počítače pro vaše použití a odstranění virtuálního počítače. Nyní máte vlastní bitovou kopii připraveny k nasazení clusteru.

### <a name="deploy-a-cluster-with-the-image"></a>Nasazení clusteru s obrázkem
Upravte následující skript Bash s příslušnými hodnotami pro vaše prostředí a spusťte jej z klientského počítače. Protože Azure nasadí virtuálních počítačů sériově v modelu nasazení classic, trvá několik minut nasazení osm virtuálních počítačů A9 navržený v tento skript.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Důležité informace pro cluster prostředí HPC CentOS
Pokud chcete nastavení clusteru na základě jedné z bitové kopie založené na CentOS HPC v Azure Marketplace místo SLES 12 pro prostředí HPC, postupujte podle obecné kroky v předchozí části. Při zřizování a konfiguraci virtuálního počítače, Všimněte si následujících rozdílů:

- Intel MPI je již nainstalován na virtuální počítač z bitové kopie založené na CentOS HPC zřízený.
- Nastavení uzamčení paměti jsou již přidán do souboru /etc/security/limits.conf Virtuálního počítače.
- Nedojde k vytvoření klíčů SSH na virtuálním počítači, zřídíte pro zachycení. Místo toho doporučujeme nastavení ověřování na základě uživatele po nasazení clusteru. Další informace najdete v následující části.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Nastavení passwordless SSH důvěryhodnosti v clusteru
V clusteru HPC na základě CentOS, existují dvě metody pro vytvoření vztahu důvěryhodnosti mezi výpočetní uzly: ověřování na základě hostitele a ověřování na základě uživatele. Ověřování na základě hostitele je mimo rámec tohoto článku a obecně je třeba provést pomocí skriptu rozšíření během nasazení. Ověřování na základě uživatele je vhodné pro navázání vztahu důvěryhodnosti po nasazení a vyžaduje generování a sdílení klíčů SSH mezi výpočetní uzly v clusteru. Tato metoda se běžně označuje jako passwordless přihlašování přes SSH a je požadován při spouštění úloh MPI.

Ukázkový skript podílí od komunity je k dispozici na [Githubu](https://github.com/tanewill/utils/blob/master/user_authentication.sh) k povolení ověřování snadno uživatele na základě CentOS HPC clusteru. Stáhnout a použít tento skript pomocí následujících kroků. Můžete také upravit tento skript nebo použít jinou metodu pro vytvoření passwordless ověřování SSH mezi výpočetní uzly clusteru.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

Pokud chcete spustit skript, musíte znát Předpona podsítě IP adres. Předpona získáte spuštěním následujícího příkazu na jednom z uzlů clusteru. Výstup by měl vypadat podobně jako 10.1.3.5 a předpona je 10.1.3 část.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Nyní spusťte skript pomocí tři parametry: běžné uživatelské jméno na výpočetních uzlech, společné heslo pro daného uživatele na výpočetní uzly a předponu podsítě, který byl vrácen z předchozí příkaz.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Skript provede následující akce:

* Vytvoří adresář na uzlu hostitele s názvem .ssh, což je vyžadováno pro passwordless přihlášení.
* Vytvoří konfigurační soubor v adresáři .ssh obsahující pokyn passwordless přihlášení k povolení přihlášení z libovolného uzlu v clusteru.
* Vytvoří soubory obsahující názvy a uzel IP adresy pro všechny uzly v clusteru. Tyto soubory jsou ponechány po spuštění skriptu pro pozdější použití.
* Vytvoří pár klíčů privátní a veřejné pro každý uzel clusteru (včetně uzlu hostitele) a vytvoří záznamy v souboru authorized_keys.

> [!WARNING]
> Spuštěním tohoto skriptu můžete vytvořit představuje potenciální bezpečnostní riziko. Ujistěte se, že není informací veřejného klíče v ~/.ssh distribuován.
>
>

## <a name="configure-intel-mpi"></a>Konfigurace Intel MPI
Pro spouštění aplikací MPI v Azure Linux RDMA, musíte nakonfigurovat některé specifické pro Intel MPI proměnné prostředí. Tady je ukázkový skript Bash nakonfigurovat proměnné, které jsou potřebné ke spuštění aplikace. Změňte cestu k mpivars.sh podle potřeby pro instalaci nástroje Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Formát souboru hostitele je následující. Přidejte jeden řádek pro každý uzel v clusteru. Zadejte, že privátní IP adresy z virtuální sítě definovaného dříve, není názvy DNS. Například na dva hostitele s IP adresami 10.32.0.1 a 10.32.0.2, soubor obsahuje následující:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Spustit MPI na Základní dvojuzlový cluster
Pokud jste tak již neučinili, nejprve nastavení prostředí pro Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Spusťte příkaz MPI
Spusťte příkaz MPI na jednom z výpočetních uzlů k MPI, je správně nainstalována a může komunikovat mezi alespoň že dva výpočetních uzlů. Následující **mpirun** příkaz spustí **hostname** na dvou uzlech.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Vaše výstup by měl obsahovat názvy všech uzlů, které předávají jako vstup pro `-hosts`. Například **mpirun** příkaz s dvěma uzly vrátí výstup takto:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Spuštění MPI srovnávacího testu
Následující příkaz Intel MPI se spustí pingpong srovnávací test pro ověření konfigurace clusteru a připojení k síti RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Na pracovní clusteru se dvěma uzly měli byste vidět výstup jako následující. V síti Azure RDMA očekávejte, že latenci nebo pod 3 mikrosekundách zprávy velikosti až 512 bajtů.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Další postup
* Nasazení a spuštění vaší Linux MPI aplikace na cluster systému Linux.
* Najdete v článku [dokumentaci ke knihovně MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) pokyny k Intel MPI.
* Zkuste [šablony rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) k vytvoření clusteru Intel Lustre pomocí bitové kopie založené na CentOS HPC. Podrobnosti najdete v tématu [nasazení Intel cloudu Edition pro počítače s Lustre v Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
