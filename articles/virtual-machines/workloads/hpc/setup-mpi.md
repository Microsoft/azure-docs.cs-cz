---
title: Nastavení rozhraní pro předávání zpráv pro HPC – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit MPI pro HPC v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023986"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Nastavit rozhraní pro předávání zpráv pro HPC

Úlohy rozhraní MPI (Message Passing Interface) jsou významnou součástí tradičních úloh HPC. Velikosti virtuálních zařízení v Azure s povolenou technologií SR-IOV umožňují použití téměř libovolné varianty MPI. 

Spuštění úloh MPI na virtuálních počítačích vyžaduje nastavení klíčů oddílů (p-klíče) přes tenanta. Podle pokynů v části [Zjistit klíče oddílu](#discover-partition-keys) podrobnosti o určení hodnot p-key.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) nabízí nejlepší výkon na IB a pracuje s MPICH a OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Nainstalujte UCX, jak bylo popsáno výše.

```bash
sudo yum install –y openmpi
```

Sestavení OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Spusťte OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Zkontrolujte klíč oddílu, jak je uvedeno výše.

## <a name="mpich"></a>MPICH

Nainstalujte UCX, jak bylo popsáno výše.

Sestavení MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Spuštění MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Zkontrolujte klíč oddílu, jak je uvedeno výše.

## <a name="mvapich2"></a>MVAPICH2

Sestavení MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Spuštění MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platforma MPI Community Edition

Nainstalujte požadované balíčky pro platformu MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postupujte podle procesu instalace.

## <a name="intel-mpi"></a>Intel MPI

[Stáhnout Intel MPI](https://software.intel.com/mpi-library/choose-download).

Změňte proměnnou prostředí I_MPI_FABRICS v závislosti na verzi. Pro Intel MPI 2018, použití `I_MPI_FABRICS=shm:ofa` a pro `I_MPI_FABRICS=shm:ofi`2019, použijte .

Připnutí procesu funguje ve výchozím nastavení správně pro 15, 30 a 60 PPN.

## <a name="osu-mpi-benchmarks"></a>Benchmarky OSU MPI

[Stáhněte si OSU MPI benchmarky](http://mvapich.cse.ohio-state.edu/benchmarks/) a untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Vytvářejte benchmarky pomocí určité knihovny MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Mpi benchmarky `mpi/` jsou ve složce.


## <a name="discover-partition-keys"></a>Zjišťování klíčů oddílů

Objevte klíče oddílů (p-klíče) pro komunikaci s ostatními virtuálními zařízeními v rámci stejného klienta (dostupnost set nebo škálovací sada virtuálních zařízení).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Větší z těchto dvou je klíč klienta, který by měl být použit s MPI. Příklad: Pokud jsou následující p-klíče, 0x800b by měl být použit s MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Použijte jiný než výchozí klíč oddílu (0x7fff). UCX vyžaduje, aby msb p-key vymazány. Například nastavte UCX_IB_PKEY jako 0x000b pro 0x800b.

Všimněte si také, že tak dlouho, dokud tenant (AVSet nebo VMSS) existuje, PKEYs zůstávají stejné. To platí i v případě, že jsou přidány nebo odstraněny uzly. Noví nájemníci dostanou různé PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Nastavení uživatelských limitů pro MPI

Nastavte uživatelské limity pro MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Nastavení kláves SSH pro MPI

Nastavte klávesy SSH pro typy MPI, které to vyžadují.

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

Výše uvedená syntaxe předpokládá sdílený domovský adresář, jinak musí být adresář .ssh zkopírován do každého uzlu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC v](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure.
