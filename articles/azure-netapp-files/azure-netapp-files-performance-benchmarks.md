---
title: Srovnávací testy výkonu u souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje výsledky srovnávacích testů výkonu pro Azure Files NetApp na úrovni svazku.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449509"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Srovnávací testy výkonu pro Azure NetApp Files

Tento článek popisuje výsledky srovnávacích testů výkonu pro soubory Azure NetApp na úrovni svazku. 

## <a name="sample-application-used-for-the-tests"></a>Ukázkovou aplikací používanou pro testy

Testy výkonu nebyly spuštěny s ukázkovou aplikací pomocí souborů NetApp Azure. Aplikace má následující vlastnosti: 

* Aplikace založené na Linuxu vytvořená pro cloud
* Můžete se škálují lineárně s přidání virtuálních počítačů (VM) Chcete-li zvýšit výkon podle potřeby
* Vyžaduje rychlé usnadnění data lake
* Má vzory vstupů/výstupů, které jsou někdy náhodné a někdy sekvenční 
    * Náhodné vzor vyžaduje s nízkou latencí pro velké objemy vstupně-výstupních operací. 
    * Sekvenční vzor vyžaduje velkou šířku pásma. 

## <a name="about-the-workload-generator"></a>O generátor zatížení

Výsledky pocházet ze souborů souhrnů Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) je nástroj příkazového řádku, který generuje diskové vstupně-výstupních operací úloh k ověření výkonu úložiště. Konfigurace klienta a serveru použitá je škálovatelný.  Obsahuje smíšený jeden hlavní server/klient a vyhrazený klientský 14 virtuálních počítačů.

## <a name="about-the-tests"></a>O testech

Testy byly navrženy k identifikaci omezení, která může mít ukázkovou aplikaci a doby odezvy této křivky až do omezení.  

Následující testy nebyly spuštěny: 

* 100 % 8-KiB náhodné čtení
* 100 % 8 KiB náhodného zápisu
* 100 % 64-KiB sekvenční čtení
* 100 % 64 KiB sekvenční zápisu
* 50 % 64-KiB sekvenční čtení, zápis sekvenčních 64 KiB 50 %
* 50 % 8-KiB náhodné čtení, zápis náhodné 8 KiB 50 %

## <a name="bandwidth"></a>Šířka pásma

Služba soubory Azure NetApp nabízí několik [úrovně služby](azure-netapp-files-service-levels.md). Každá úroveň služeb nabízí různé šířku pásma za TiB zřízená kapacita (kvóty). Limit šířky pásma pro svazek se zřizuje, založené na kombinaci úrovně služby a kvóty svazku. Limit šířky pásma je pouze jediný faktor při určování skutečného propustnost, které se dají realizovat.  

V současné době je 4 500 MiB nejvyšší propustnost, které bylo dosaženo úlohou na jednom svazku při testování.  S požadovanou úrovní aktualizace service Premium zřídí se svazek kvóta 70.31 TiB dostatečnou šířku pásma pro realizaci propustnost za výpočtu níže: 

![Vzorec šířky pásma](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvóty a služba úrovně](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Úlohy náročné na propustnost

Propustnost test použil Vdbench a kombinaci 12xD32s V3 úložiště virtuálních počítačů. Ukázka svazku v testu dosáhnout následující čísla propustnost:

![Propustnost testu](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Můžu intenzivních vstupně-výstupních operací

Vstupně-výstupní operace test použil Vdbench a kombinaci 12xD32s V3 úložiště virtuálních počítačů. Ukázka svazku v testu dosáhnout následující čísla vstupně-výstupních operací:

![Test vstupně-výstupních operací](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

Vzdálenost mezi testovací virtuální počítače a svazek souborů NetApp Azure má vliv na výkon vstupně-výstupních operací.  Graf níže porovnává vstupně-výstupních operací a čekací doba odezvy křivky pro dvě různé sady virtuálních počítačů.  Jedna sada virtuálních počítačů je téměř NetApp soubory Azure a druhá sada je další okamžitě.  Zvýší latence pro další sadu virtuálních počítačů má dopad na množství vstupně-výstupních operací dosáhnout na dané úrovni paralelismu.  Čtení na svazku bez ohledu na to, můžete překročit 300 000 vstupně-výstupních operací, jak je znázorněno níže: 

![Studie latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Souhrn

Úlohy nízkou latenci (databáze) může mít jeden milisekund odezvu. Transakční výkon může být větší než 300 kB vstupně-výstupních operací pro samostatný svazek.

Citlivé na propustnost aplikace (pro streamování a vytvoření bitové kopie) může mít 4.5GiB / s propustností.

## <a name="example-scripts"></a>Příklady skriptů

Následují příklady skriptů jsou pro demonstrační účely pouze.  Jsou použity pro produkční účely.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
