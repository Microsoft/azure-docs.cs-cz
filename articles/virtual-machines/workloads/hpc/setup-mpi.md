---
title: Nastavení rozhraní předávání zpráv pro prostředí HPC - Azure Virtual Machines | Dokumentace Microsoftu
description: Zjistěte, jak nastavit MPI pro prostředí HPC v Azure.
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
ms.openlocfilehash: 541e42a72ea604c4d71dc546b14dea2f0857bcc1
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797513"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Nastavení rozhraní předávání zpráv pro prostředí HPC

Message Passing Interface (MPI) úlohy jsou významnou součástí tradiční úlohy HPC. SR-IOV povoleno velikosti virtuálních počítačů v Azure umožňují téměř jakékoli flavor MPI, který se má použít. 

Spouštění úloh MPI ve virtuálních počítačích vyžaduje nastavení klíče oddílu (p-keys) na tenanta. Postupujte podle kroků v [zjistit klíče oddílů](#discover-partition-keys) podrobné informace o určení hodnoty p-key.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) nabízí výkon na IB a spolupracuje s MPICH a OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Nainstalujte UCX, jak je uvedeno výše.

```bash
sudo yum install –y openmpi
```

Vytvářejte OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Run OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Zkontrolujte svůj klíč oddílu, jak je uvedeno výše.

## <a name="mpich"></a>MPICH

Nainstalujte UCX, jak je uvedeno výše.

Build MPICH.

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

Zkontrolujte svůj klíč oddílu, jak je uvedeno výše.

## <a name="mvapich2"></a>MVAPICH2

Vytvářejte MVAPICH2.

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

Pokračujte v procesu instalace.

## <a name="intel-mpi"></a>Intel MPI

[Stáhněte si Intel MPI](https://software.intel.com/mpi-library/choose-download).

Změňte proměnné prostředí I_MPI_FABRICS v závislosti na verzi. Pro Intel MPI 2018, použijte `I_MPI_FABRICS=shm:ofa` a 2019, použijte `I_MPI_FABRICS=shm:ofi`.

Připnutí procesu funguje správně pro 15, 30 a 60 PPN ve výchozím nastavení.

## <a name="osu-mpi-benchmarks"></a>Srovnávací testy OSU MPI

[Stáhněte si srovnávací testy MPI OSU](http://mvapich.cse.ohio-state.edu/benchmarks/) a untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Vytvořit srovnávací testy pomocí konkrétní knihovny MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI srovnávací testy jsou v rámci `mpi/` složky.


## <a name="discover-partition-keys"></a>Zjištění klíče oddílu

Objevte klíče oddílů (p-keys) pro komunikaci s jiným virtuálním počítačům ve stejném tenantovi (skupiny dostupnosti nebo Škálovací sady virtuálních počítačů).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Větší z nich je klíč tenanta, který se má použít s MPI. Příklad: Pokud následují p-keys, 0x800b by měla sloužit s MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Použijte oddíl jiné než výchozí (0x7fff) klíč oddílu. UCX vyžaduje MSB p-key vymazat. Například nastavte UCX_IB_PKEY jako 0x000b pro 0x800b.

Všimněte si také, že za předpokladu, že tenant (AVSet nebo VMSS) existuje, PKEYs zůstávají stejné. To platí i v případě, že uzly jsou přidány nebo odstraněny. Nové tenanty získat různé PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Nastavení omezení uživatele pro MPI

Nastavení omezení uživatele pro MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Nastavení klíče SSH pro MPI

Nastavení klíče SSH pro typy MPI, které to vyžadují.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Výše uvedené syntaxi předpokládá sdílené domovský adresář, else adresáři .ssh musí zkopírovat do každého uzlu.

## <a name="next-steps"></a>Další postup

Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
