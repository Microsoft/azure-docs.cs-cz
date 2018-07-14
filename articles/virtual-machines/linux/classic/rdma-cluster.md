---
title: Nastavení clusteru Linux RDMA pro spouštění aplikací MPI | Dokumentace Microsoftu
description: Vytvoření clusteru s Linuxem velikosti H16r, H16mr, instance A8 nebo A9 virtuální počítače pomocí sítě Azure RDMA pro spouštění aplikací MPI
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
ms.date: 07/11/2018
ms.author: danlep
ms.openlocfilehash: 471fd4095fe45e76f94df8c61a07eeb9bbc1c120
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990723"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Nastavení clusteru Linux RDMA pro spouštění aplikací MPI

Další informace o nastavení clusteru Linux RDMA v Azure pomocí [vysoce výkonné výpočetní velikosti virtuálních počítačů](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ke spouštění paralelních aplikací rozhraní MPI (Message Passing Interface). Tento článek vysvětluje, jak připravit bitovou kopii HPC pro Linux ke spuštění v clusteru Intel MPI. Po přípravě, nasaďte cluster pomocí této bitové kopie a jeden z virtuálních počítačů [velikosti virtuálních počítačů Azure s podporou RDMA](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances). Ke spuštění aplikací MPI, které se efektivně komunikovat s nízkou latencí a vysokou propustnost sítě založené na technologii vzdáleného přímého paměti přístup (počítače RDMA) se používá cluster.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru](../../../azure-resource-manager/resource-manager-deployment-model.md) a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="cluster-deployment-options"></a>Možnosti nasazení clusteru
Toto jsou různé metody, které můžete použít k vytvoření clusteru Linux RDMA s nebo bez něj Plánovač úloh.

* **Skripty Azure CLI**: Jak uvidíte později v tomto článku, použijte [rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md) (rozhraní příkazového řádku) se skriptově nasazení clusteru virtuální počítače s podporou RDMA. Rozhraní příkazového řádku v režimu správy služby vytvoří uzly clusteru sériově v modelu nasazení classic, takže nasazení mnoho výpočetních uzlů může trvat několik minut. K povolení síťového připojení RDMA, při použití modelu nasazení classic, nasaďte virtuální počítače ve stejné cloudové službě.
* **Šablony Azure Resource Manageru**: modelu nasazení Resource Manager můžete také použít k nasazení clusteru virtuální počítače s podporou RDMA, která se připojuje k síti přístup RDMA. Můžete [vytvořit vlastní šablonu](../../../resource-group-authoring-templates.md), nebo zkontrolujte [šablony rychlý start Azure](https://azure.microsoft.com/documentation/templates/) pro šablony přispívají Microsoftem nebo komunitou řešení, které chcete nasadit. Šablony Resource Manageru můžete poskytují rychlý a spolehlivý způsob, jak nasadit cluster s Linuxem. Při použití modelu nasazení Resource Manager, povolit síťového připojení RDMA, nasaďte virtuální počítače ve stejné skupině dostupnosti nebo škálovací sady virtuálních počítačů.
* **HPC Pack**: vytvoření clusteru sady Microsoft HPC Pack v Azure a přidejte podporující RDMA výpočetní uzly, na kterých běží podporované Linuxová distribuce vytvořená pro přístup k síti přístup RDMA. Další informace najdete v tématu [začít pracovat s Linuxovými výpočetními uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Kroky v klasickém modelu nasazení ukázky
Následující kroky ukazují, jak používat rozhraní příkazového řádku Azure k nasazení virtuálního počítače s operačním systémem SUSE Linux Enterprise Server (SLES) 12 prostředí HPC v Azure Marketplace, přizpůsobit a vytvořit vlastní image virtuálního počítače. Pak můžete použít bitovou kopii do skriptu nasazení clusteru virtuální počítače s podporou RDMA.

> [!TIP]
> Podobným způsobem použijte nasazení clusteru s podporou RDMA virtuálních počítačů založených na imagích založené na CentOS HPC v Azure Marketplace. Některé kroky mírně, jak je uvedeno.
>

### <a name="prerequisites"></a>Požadavky
* **Klientský počítač**: budete potřebovat počítač Mac, Linux nebo Windows klienta ke komunikaci s Azure. Tento postup předpokládá, že používáte klienta systému Linux.
* **Předplatné Azure**: Pokud předplatné nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut. Pro větší clustery zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu.
* **Dostupnost velikostí virtuálních počítačů**: Zkontrolujte [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/) dostupnost řady H-series nebo dalších velikostí virtuálního počítače podporující RDMA v oblastech Azure.
* **Kvóta pro jádra**: možná budete muset zvýšit kvótu jader, která se nasadit cluster virtuálních počítačů náročných na výpočetní prostředky. Například budete potřebovat nejméně 128 jader, pokud chcete nasadit virtuální počítače s 8 A9, jak je znázorněno v tomto článku. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-series. Požádat o zvýšení kvóty, [otevřete žádost o online zákaznickou podporu](../../../azure-supportability/how-to-create-azure-support-request.md) bez poplatků.
* **Azure CLI**: [nainstalovat](../../../cli-install-nodejs.md) rozhraní příkazového řádku Azure a [připojit ke svému předplatnému Azure](/cli/azure/authenticate-azure-cli) z klientského počítače.

Projděte si také informace o nasazení pro [velikosti virtuálních počítačů Azure s podporou RDMA](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances).

### <a name="provision-an-sles-12-hpc-vm"></a>Zřízení virtuálního počítače s SLES 12 HPC
Po přihlášení k Azure pomocí Azure CLI, spusťte `azure config list` potvrďte, že výstup zobrazuje režimu správy služeb. Pokud tomu tak není, nastavení režimu spuštěním tohoto příkazu:

    azure config mode asm


Zadejte následující příkaz pro výpis všech předplatných, které obsahují oprávnění k používání:

    azure account list

Aktuální aktivní předplatné se určuje podle `Current` nastavena na `true`. Pokud toto předplatné není ten, který chcete použít k vytvoření clusteru, nastavte ID odpovídající předplatné jako aktivní předplatné:

    azure account set <subscription-Id>

Pokud chcete zobrazit veřejně dostupných imagí SLES 12 HPC v Azure, spusťte příkaz podobný tomuto, za předpokladu, že vaše prostředí podporuje prostředí **grep**:

    azure vm image list | grep "suse.*hpc"

Zřízení virtuálního počítače s podporou RDMA s imagí SLES 12 SP3 HPC příkaz podobný tomuto:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-v20170913

Kde:

* Velikost (v tomto příkladu A9) je jedním z velikosti virtuálních počítačů s podporou RDMA.
* Externí číslo portu SSH (22 v tomto příkladu, což je výchozí pro SSH) je platné číslo portu. Interní číslo portu SSH je nastavena na 22.
* V oblasti Azure, které jsou určené umístění se vytvoří novou cloudovou službu. Zadejte umístění, ve kterém je k dispozici, jako je například "Západní USA" velikost virtuálního počítače, které zvolíte.
* Podpora SUSE priority (který se účtuje další poplatky) název image SLES 12 aktuálně může být jedna z možností, které má `priority` v názvu, jako například: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-priority-v20170913`

### <a name="customize-the-vm"></a>Přizpůsobení virtuálního počítače
Po dokončení virtuálního počítače pro zřizování a SSH k virtuálnímu počítači s použitím externí IP adresu Virtuálního počítače (nebo název DNS) a externí port číslo jste nakonfigurovali a pak ji přizpůsobit. Podrobnosti připojení najdete v tématu [jak se přihlásit k virtuálnímu počítači s Linuxem](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Provádění příkazů jako uživatel, který jste nakonfigurovali na virtuálním počítači, pokud k dokončení kroku se vyžaduje kořenový přístup.

> [!IMPORTANT]
> Microsoft Azure neposkytuje přístup uživatele root pro virtuální počítače s Linuxem. Pokud chcete získat přístup pro správu připojeni jako uživatel k virtuálnímu počítači, spusťte příkazy pomocí `sudo`.
>
>

* **Aktualizace**: nainstalujte aktualizace pomocí zypperu. Můžete také chtít nainstalovat nástroje pro systém souborů NFS.

  > [!IMPORTANT]
  > Ve virtuálním počítači SLES 12 prostředí HPC doporučujeme vám, že nemůžete použít aktualizace jádra, které může způsobit problémy s podporou RDMA Linuxu ovladače.
  >
  >
* **Intel MPI**: dokončení instalace Intel MPI na virtuálním počítači spuštěním následujícího příkazu:

    ```bash
    sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
    ```

* **Uzamčení paměti**: přidat kódy pro MPI k uzamčení paměti k dispozici pro přístup RDMA, nebo změnit následující nastavení v souboru /etc/security/limits.conf. Potřebujete kořenový přístup k úpravě tohoto souboru.

    ```bash
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Pro účely testování můžete také nastavit memlock na neomezený. Například: `* hard memlock unlimited`. Další informace najdete v tématu [Lego metody pro nastavení uzamčení velikost paměti](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Klíče SSH pro virtuální počítače SLES**: generování klíčů SSH k navázání vztahu důvěryhodnosti pro váš uživatelský účet mezi výpočetní uzly ve SLES clusteru při spouštění úloh MPI. Pokud jste nasadili virtuálního počítače založené na CentOS HPC, není postupujte podle tohoto kroku. Zobrazit pokyny k nastavení passwordless SSH důvěry mezi uzly clusteru po zachycení bitové kopie a nasaďte cluster dále v tomto článku.

  Pokud chcete vytvořit klíče SSH, spusťte `ssh-keygen` příkazu. Po zobrazení výzvy pro zadání, vyberte **Enter** ke generování klíče ve výchozím umístění bez nastavení hesla.

  Veřejný klíč se připojte k souboru authorized_keys pro známé veřejných klíčů.

  ```bash
  cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

  V adresáři ~/.ssh upravit nebo vytvořit `config` souboru. Zadejte rozsah IP adres privátní sítě, ke které máte v plánu používat v systému Azure (v tomto příkladu 10.32.0.0/16):

  ```bash
  host 10.32.0.*
  StrictHostKeyChecking no
  ```

  Seznam můžete také IP adresu privátní síť každého virtuálního počítače ve vašem clusteru takto:

  ```bash
  host 10.32.0.1
  StrictHostKeyChecking no
  host 10.32.0.2
  StrictHostKeyChecking no
  host 10.32.0.3
  StrictHostKeyChecking no
  ...
  ```

  > [!NOTE]
  > Konfigurace `StrictHostKeyChecking no` můžete vytvořit potenciální bezpečnostní riziko, pokud není zadána specifickou IP adresu nebo rozsah.
  >
  >
* **Aplikace**: Nainstalujte aplikace potřebujete nebo provést další úpravy, než zachytíte image.

### <a name="capture-the-image"></a>Zachycení bitové kopie
K zachycení bitové kopie, nejdřív spusťte následující příkaz na Linuxovém virtuálním počítači. Tento příkaz deprovisions virtuálního počítače, ale zachová uživatelských účtů a klíčů SSH, které jste nastavili.

```bash
sudo waagent -deprovision
```

Z klientského počítače spusťte následující příkazy rozhraní příkazového řádku Azure k zachycení bitové kopie. Další informace najdete v tématu [jak zachytit klasický virtuální počítač s Linuxem jako image](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Po spuštění těchto příkazů pro používání zachytí image virtuálního počítače a virtuální počítač se odstraní. Teď máte vlastní image připravená k nasazení clusteru.

### <a name="deploy-a-cluster-with-the-image"></a>Nasazení clusteru s imagí
Upravte následující skript Bash s příslušnými hodnotami pro vaše prostředí a spusťte jej z klientského počítače. Protože Azure nasadí virtuální počítače sériově v modelu nasazení classic, trvá několik minut, než osm virtuálních počítačů A9 navržené v tomto skriptu nasazení.

```bash
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a classic VNet in Azure
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
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>;
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Důležité informace pro cluster prostředí HPC CentOS
Pokud chcete vytvořit cluster na základě jedné z bitové kopie založené na CentOS HPC v Azure Marketplace místo SLES 12 pro prostředí HPC, postupujte podle obecných kroků v předchozí části. Pokud zřídíte a nakonfigurujete virtuální počítač, mějte na paměti následující rozdíly:

- Intel MPI už je nainstalovaná na virtuálním počítači zřízeném z bitové kopie založené na CentOS HPC.
- Nastavení uzamčení paměti jsou již přidán do souboru /etc/security/limits.conf Virtuálního počítače.
- Nelze vytvořit klíče SSH na virtuálním počítači je zřídit pro zachycení. Místo toho doporučujeme nastavit ověřování podle uživatelů po nasazení clusteru. Další informace najdete v následující části.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Nastavení důvěryhodnosti passwordless SSH v clusteru
V clusteru HPC založené na CentOS, existují dvě metody pro vytvoření vztahu důvěryhodnosti mezi jednotlivými výpočetními uzly: ověřování na základě hostitele a ověřování založené na uživatelích. Ověřování na základě hostitele je mimo rámec tohoto článku a obecně se musí provést prostřednictvím skriptu rozšíření během nasazení. Ověřování založené na uživatelích je vhodné pro vytvoření vztahu důvěryhodnosti po nasazení a vyžaduje generování a sdílení klíčů SSH mezi výpočetní uzly v clusteru. Tato metoda se běžně označuje jako passwordless přihlašování přes SSH a je povinný při spouštění úloh MPI.

Ukázkový skript `user_authentication.sh` povolit ověřování založené na uživatelích na HPC se systémem CentOS clusteru následující:

```bash
#!/bin/bash
# For CentOS user must first install epel-release, sshpass, and nmap (sshpass and nmap are available from epel-release for CentOS)

# usage ./user_authentication.sh [username] [password] [internalIP prefix]
# ./user_authentication.sh azureuser Azure@123 10.32.0
USER=$1
PASS=$2
IPPRE=$3
HEADNODE=`hostname`

mkdir -p .ssh
echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''

echo 'Host *' >> .ssh/config
echo 'StrictHostKeyChecking no' >> .ssh/config
chmod 400 .ssh/config
chown azureuser:azureuser /home/azureuser/.ssh/config

nmap -sn $IPPRE.* | grep $IPPRE. | awk '{print $5}' > nodeips.txt
for NAME in `cat nodeips.txt`; do sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'hostname' >> nodenames.txt;done

NAMES=`cat nodenames.txt` #names from names.txt file
for NAME in $NAMES; do
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 /home/$USER/nodenames.txt $USER@$NAME:/home/$USER/
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "mkdir .ssh && chmod 700 .ssh"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'touch /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo "Host *" >  /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo StrictHostKeyChecking no >> /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 400 /home/'$USER'/.ssh/config'
    cat .ssh/id_rsa.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'cat >> .ssh/authorized_keys'
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 $USER@$NAME:/home/$USER/.ssh/id_rsa.pub .ssh/sub_node.pub

    for SUBNODE in `cat nodeips.txt`; do
         sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'mkdir -p .ssh'
         cat .ssh/sub_node.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'cat >> .ssh/authorized_keys'
    done
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 700 .ssh/'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 640 .ssh/authorized_keys'
done
```

Spusťte tento skript pomocí následujících kroků. Můžete také upravit tento skript nebo použít jinou metodu k navázání passwordless ověřování SSH mezi jednotlivými výpočetními uzly clusteru.

Pro spuštění skriptu, je potřeba vědět předpona IP adresy vaší podsítě. Předpona, která získáte spuštěním následujícího příkazu na jednom z uzlů clusteru. Výstup by měl vypadat podobně jako 10.1.3.5 a předpona, která je 10.1.3 část.

```bash
ifconfig eth0 | grep -w inet | awk '{print $2}'
```

Nyní spusťte skript pomocí tři parametry: běžné uživatelské jméno na výpočetních uzlech, společné heslo pro tohoto uživatele na výpočetních uzlech a předponu podsítě, která byla vrácena z předchozího příkazu.

```bash
./user_authentication.sh <myusername> <mypassword> 10.1.3
```

Skript provede následující akce:

* Vytvoří adresář v uzlu hostitele s názvem .ssh, které jsou požadovány pro passwordless přihlášení.
* Vytvoří konfigurační soubor v adresáři .ssh, který dává pokyn passwordless přihlášení umožňující přihlášení z libovolného uzlu v clusteru.
* Vytvoří soubory obsahující názvy wwnn a uzel IP adres pro všechny uzly v clusteru. Tyto soubory zůstávají po spuštění skriptu pro pozdější použití.
* Vytvoří dvojici klíčů privátních a veřejných pro každý uzel clusteru (včetně adresa uzlu hostitele) a vytvoří v souboru authorized_keys položky.

> [!WARNING]
> Spuštěním tohoto skriptu můžete vytvořit potenciální bezpečnostní riziko. Ujistěte se, že není distribuován informace o veřejném klíči v ~/.ssh.
>

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Základní dvojuzlový cluster spouštět MPI
Pokud jste tak již neučinili, nejprve nastavte prostředí pro Intel MPI.

```bash
# For a SLES 12 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Spustit příkaz MPI
Spusťte příkaz MPI na jednotlivé výpočetní uzly s vysvětlením, že MPI je správně nainstalována a může komunikovat mezi alespoň dvou výpočetních uzlů. Následující **mpirun** příkaz spustí **hostname** příkaz na dva uzly. Pro `-hosts` parametr, předejte adresy dvou uzlů ve virtuální síti Azure (třeba 10.32.0.4,10.32.0.5).

```bash
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Výstup by měl vypsat názvy všech uzlů, které jste předali jako vstup pro `-hosts`. Například **mpirun** příkaz se dvěma uzly vrátí výstup podobný tomuto:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Spuštění MPI srovnávacích testů
V tomto příkazu Intel MPI spouští pingpong testu výkonnosti a ověřte konfiguraci clusteru a připojení k síti přístup RDMA.

```bash
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Na pracovní clusteru se dvěma uzly byste měli vidět výstup podobný tomuto. V síti Azure RDMA očekávat, že latence v nebo pod 3 mikrosekundy pro zprávu o velikosti 512 bajtů.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 6 17:16:46 2018
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
### <a name="sample-mpi-run-script"></a>Ukázka MPI spustit skript
Tady je ukázkový skript Bash konfigurace proměnných potřebných ke spuštění aplikace MPI. Změňte cestu k `mpivars.sh` podle potřeb vaší instalace Intel MPI.

```bash
#!/bin/bash -x

# For a SLES 12 HPC cluster

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

# Command line to run the MPI job. Substitute with values appropriate for your application.

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Formát souboru hostitele je následujícím způsobem. Přidáte jeden řádek pro každý uzel v clusteru. Zadejte privátní IP adresy virtuální sítě z ní definovali dříve, ne názvy DNS. Pro dva hostitele s IP adresami 10.32.0.4 a 10.32.0.4 obsahuje například následující soubor:

```bash
10.32.0.4:16
10.32.0.5:16
```


## <a name="next-steps"></a>Další postup
* Nasazování a spouštění aplikací MPI vašeho systému Linux v clusteru s Linuxem.
* Zobrazit [dokumentaci ke knihovně Intel MPI](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) pro doprovodné materiály k Intel MPI.
* Vyzkoušejte [šablonu pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) k vytvoření clusteru Intel Lustre pomocí bitové kopie založené na CentOS HPC. 
