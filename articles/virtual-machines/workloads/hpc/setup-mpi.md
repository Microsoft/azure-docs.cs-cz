---
title: Nastavení rozhraní pro předávání zpráv pro HPC – Azure Virtual Machines | Microsoft Docs
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023986"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Nastavení rozhraní pro předávání zpráv pro HPC

Úlohy rozhraní MPI (Message Passing Interface) jsou důležitou součástí tradičních úloh HPC. Velikosti virtuálních počítačů s podporou SR-IOV v Azure umožňují téměř jakýkoli charakter MPI. 

Spuštění úloh MPI na virtuálních počítačích vyžaduje nastavení klíčů oddílů (p-Keys) v rámci tenanta. Použijte postup v části [Vyhledat klíče oddílu](#discover-partition-keys) , kde najdete podrobnosti o určování hodnot p-Key.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) nabízí nejlepší výkon v IB a funguje s MPICH a OpenMP.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMP

Nainstalujte UCX, jak je popsáno výše.

```bash
sudo yum install –y openmpi
```

Sestavit OpenMP.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Spusťte OpenMP.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Podívejte se na klíč oddílu, jak je uvedeno výše.

## <a name="mpich"></a>MPICH

Nainstalujte UCX, jak je popsáno výše.

Sestavování MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Spouští se MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Podívejte se na klíč oddílu, jak je uvedeno výše.

## <a name="mvapich2"></a>MVAPICH2

Sestavování MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Spouští se MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Nainstalujte požadované balíčky pro MPI platformy.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postupujte podle instalačního procesu.

## <a name="intel-mpi"></a>Intel MPI

[Stáhněte si Intel MPI](https://software.intel.com/mpi-library/choose-download).

Změňte proměnnou prostředí I_MPI_FABRICS v závislosti na verzi. Pro Intel MPI 2018 použijte `I_MPI_FABRICS=shm:ofa` a pro 2019 použijte `I_MPI_FABRICS=shm:ofi`.

Ve výchozím nastavení funguje připnutí procesu pro 15, 30 a 60 PPN správně.

## <a name="osu-mpi-benchmarks"></a>OSU MPI – srovnávací testy

[Stáhněte si srovnávací testy osu MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) a untar.

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

Srovnávací testy MPI se nacházejí v `mpi/` složce.


## <a name="discover-partition-keys"></a>Zjistit klíče oddílu

Zjištění klíčů oddílů (p-Keys) pro komunikaci s ostatními virtuálními počítači v rámci stejného tenanta (Skupina dostupnosti nebo sada škálování virtuálního počítače).

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

Všimněte si také, že pokud existuje tenant (AVSet nebo VMSS), zůstane PKEYs stejné. To platí i při přidání nebo odstranění uzlů. Noví klienti získají různé PKEYs.


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

Přečtěte si další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
