---
title: Nastavení rozhraní MPI (Message Passing Interface) pro HPC-Azure Virtual Machines | Microsoft Docs
description: Přečtěte si, jak nastavit MPI pro HPC v Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606636"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Nastavení rozhraní pro předávání zpráv pro HPC

[Rozhraní MPI (Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) je otevřená knihovna a ve skutečnosti standardní pro paralelní distribuci distribuované paměti. Obvykle se používá napříč mnoha úlohami HPC. Úlohy prostředí HPC na virtuálních počítačích s [podporou RDMA](../../sizes-hpc.md#rdma-capable-instances) [a](../../sizes-hpc.md) [N-Series](../../sizes-gpu.md) můžou používat MPI ke komunikaci přes síť s nízkou latencí a velkou šířkou pásma InfiniBand.
- Velikosti virtuálních počítačů s povoleným rozhraním SR-IOV v Azure umožňují téměř libovolné charaktery MPI pro použití s Mellanox OFED.
- V případě virtuálních počítačů s podporou SR-IOV podporované implementace MPI používají ke komunikaci mezi virtuálními počítači rozhraní Microsoft Network Direct (ND). Proto jsou podporovány pouze verze Microsoft MPI (MS-MPI) 2012 R2 nebo novější a Intel MPI 5. x. Novější verze (2017, 2018) běhové knihovny Intel MPI mohou nebo nemusí být kompatibilní s ovladači Azure RDMA.

Pro [virtuální počítače podporující](../../sizes-hpc.md#rdma-capable-instances)rozhraní SR-IOV s podporou RDMA jsou vhodné [image virtuálních počítačů CentOS-HPC](configure.md#centos-hpc-vm-images) verze 7,6 a novější. Tyto image virtuálních počítačů přináší optimalizované a předem načtené ovladače OFED pro RDMA a různé běžně používané knihovny MPI a vědecké výpočetní balíčky a představují nejjednodušší způsob, jak začít.

I když tady jsou příklady pro RHEL/CentOS, ale kroky jsou obecné a dají se použít pro libovolný kompatibilní operační systém Linux, jako je například Ubuntu (16,04, 18,04 19,04, 20,04) a SLES (12 SP4 a 15). Další příklady pro nastavení dalších MPIch implementací na jiné distribuce jsou v [úložišti azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Spouštění úloh MPI na virtuálních počítačích s povoleným rozhraním SR-IOV s některými MPI knihovnami (například platformou MPI) může vyžadovat nastavení klíčů oddílů (p-Keys) v rámci tenanta na izolaci a zabezpečení. Postupujte podle kroků v části [Vyhledat klíče oddílu](#discover-partition-keys) , kde najdete podrobnosti o určení hodnot p-Key a jejich správné nastavení pro úlohu MPI pomocí této knihovny MPI.

> [!NOTE]
> Níže uvedené fragmenty kódu jsou příklady. Doporučujeme používat nejnovější stabilní verze balíčků nebo odkazy na [úložiště azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

[Unified Communications X (UCX)](https://github.com/openucx/ucx) je rozhraní komunikačních rozhraní API pro HPC. Je optimalizovaný pro MPI komunikaci přes InfiniBand a funguje s mnoha implementacemi MPI, jako je OpenMP a MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Poslední buildy UCX vyřešily [problém](https://github.com/openucx/ucx/pull/5965) , při kterém se v přítomnosti více rozhraní síťových adaptérů volí správné rozhraní InfiniBand. Další podrobnosti [najdete](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) na stránce MPI over InfiniBand, když je ve virtuálním počítači povolené urychlení sítě.

## <a name="hpc-x"></a>HPC-X

[Sada nástrojů HPC-X software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) obsahuje UCX a HCOLL a je možné ji sestavit proti UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Spuštění HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optimalizace kolektivních MPI

MPI kolektivní komunikace nabízí flexibilní a přenosný způsob implementace skupinových operací komunikace. Jsou široce používány v různých vědeckých paralelních aplikacích a mají výrazný dopad na celkový výkon aplikace. Podrobnosti o parametrech konfigurace pro optimalizaci výkonu kolektivní komunikace pomocí knihovny HPC-X a HCOLL pro kolektivní komunikaci najdete v [článku TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) .

> [!NOTE] 
> Pomocí HPC-X 2.7.4 + může být nutné explicitně předat LD_LIBRARY_PATH, pokud UCX verze v MOFED vs. v HPC-X se liší.

## <a name="openmpi"></a>OpenMP

Nainstalujte UCX, jak je popsáno výše. HCOLL je součástí sady [nástrojů HPC-X software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) a nevyžaduje speciální instalaci.

OpenMP lze instalovat z balíčků, které jsou k dispozici v úložišti.

```bash
sudo yum install –y openmpi
```

Doporučujeme sestavit nejnovější a stabilní vydání OpenMP s pomocí UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Pro zajištění optimálního výkonu spusťte OpenMP s `ucx` a `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Podívejte se na klíč oddílu, jak je uvedeno výše.

## <a name="intel-mpi"></a>Intel MPI

Stáhněte si svoji volbu verze [Intel MPI](https://software.intel.com/mpi-library/choose-download). Změňte proměnnou prostředí I_MPI_FABRICS v závislosti na verzi.
- Intel MPI 2019 a 2021: použijte `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . `mlx`Zprostředkovatel používá UCX. Bylo zjištěno, že použití operací bylo nestabilní a méně výkonné. Další podrobnosti najdete v [článku TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) .
- Intel MPI 2018: použití `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: použití `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>Virtuální počítače, které nejsou SR-IOV
V případě virtuálních počítačů, které nejsou SR-IOV, je příklad stažení [verze zkušební verze](https://registrationcenter.intel.com/en/forms/?productid=1740) 5. x modulu runtime následující:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Pokyny k instalaci najdete v [příručce k instalaci knihovny Intel MPI Library](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Volitelně můžete chtít povolit ptrace pro nekořenové procesy bez ladicího programu (nutné pro nejnovější verze Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Verze imagí SUSE Linux Enterprise Server VM – SLES 12 SP3 pro HPC, SLES 12 SP3 pro HPC (Premium), SLES 12 SP1 pro HPC, SLES 12 SP1 pro HPC (Premium), SLES 12 SP4 a SLES 15, jsou nainstalované ovladače RDMA a na virtuálním počítači jsou distribuované balíčky Intel MPI. Nainstalujte Intel MPI spuštěním následujícího příkazu:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

Sestavování MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Spouští se MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>MPI platformy

Nainstalujte požadované balíčky pro Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postupujte podle instalačního procesu.

Následující příkazy jsou příklady spuštění MPI pingpong a allreduce s použitím MPI platformy na HBv3 virtuálních počítačů pomocí CentOS-HPC 7,6, 7,8 a 8,1 image virtuálních počítačů.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Nainstalujte UCX, jak je popsáno výše. Sestavování MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Spouští se MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Podívejte se na klíč oddílu, jak je uvedeno výše.

## <a name="osu-mpi-benchmarks"></a>OSU MPI – srovnávací testy

Stáhněte si [srovnávací testy osu MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) a untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Vytváření srovnávacích testů pomocí konkrétní knihovny MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Srovnávací testy MPI jsou ve `mpi/` složce.


## <a name="discover-partition-keys"></a>Zjistit klíče oddílu

Objevte klíče oddílů (p-Keys) pro komunikaci s ostatními virtuálními počítači v rámci stejného tenanta (skupiny dostupnosti nebo sady škálování virtuálních počítačů).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Čím větší z těchto dvou je klíč tenanta, který se má použít s MPI. Příklad: Pokud jsou následující klíče p-, 0x800b by se měly použít s MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Použijte jiný oddíl než výchozí (0x7FFF) klíč oddílu. UCX vyžaduje, aby se vymazala MSBa p-Key. Nastavte například UCX_IB_PKEY jako 0x000b pro 0x800b.

Všimněte si také, že pokud existuje tenant (Skupina dostupnosti nebo sada škálování virtuálního počítače), zůstane PKEYs stejné. To platí i při přidání nebo odstranění uzlů. Noví klienti získají různé PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Nastavení uživatelských omezení pro MPI

Nastavte omezení uživatelů pro MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Nastavení klíčů SSH pro MPI

Nastavte klíče SSH pro MPI typy, které to vyžadují.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Výše uvedená syntaxe předpokládá sdílený domovský adresář, jinak musí být adresář. ssh zkopírován do každého uzlu.

## <a name="next-steps"></a>Další kroky

- Seznamte se s [povolenými](../../sizes-hpc.md#rdma-capable-instances) virtuálními počítači řady [H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) InfiniBand
- Přečtěte si přehled [HBv3-Series](hbv3-series-overview.md) Overview a [HC-Series](hc-series-overview.md).
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
