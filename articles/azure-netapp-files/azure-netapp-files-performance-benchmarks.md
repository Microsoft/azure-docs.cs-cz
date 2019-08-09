---
title: Výsledky testu výkonnosti pro Azure NetApp Files | Microsoft Docs
description: Popisuje výsledky testů výkonnosti výkonu pro Azure NetApp Files na úrovni svazku.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881752"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Výsledky testu výkonnosti pro Azure NetApp Files

Tento článek popisuje výsledky testů výkonnosti výkonu pro Azure NetApp Files na úrovni svazku. 

## <a name="sample-application-used-for-the-tests"></a>Ukázková aplikace použitá pro testy

Testy výkonu byly spuštěny s ukázkovou aplikací pomocí Azure NetApp Files. Aplikace má následující vlastnosti: 

* Aplikace založená na systému Linux vytvořená pro Cloud
* Dá se lineárně škálovat s přidanými virtuálními počítači, aby se zvýšil výkon, jak je potřeba.
* Vyžaduje rychlé usnadnění pro data Lake.
* Má v/v vzory, které jsou někdy náhodné a někdy sekvenční 
    * Náhodný vzor vyžaduje nízkou latenci pro velké objemy vstupně-výstupních operací. 
    * Sekvenční vzor vyžaduje velké množství šířky pásma. 

## <a name="about-the-workload-generator"></a>O generátoru úloh

Výsledky pocházejí ze souhrnných souborů Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) je nástroj příkazového řádku, který generuje vstupně-výstupní úlohy disku pro ověřování výkonu úložiště. Použitá konfigurace klienta a serveru je škálovatelná.  Zahrnuje jeden smíšený hlavní počítač/klienta a 14 vyhrazených virtuálních počítačů klienta.

## <a name="about-the-tests"></a>O testech

Testy byly navržené tak, aby identifikovaly omezení, která může mít ukázková aplikace, a dobu odezvy, která se zakřiví do limitů.  

Následující testy byly spuštěny: 

* 100% 8 – KiB náhodné čtení
* 100% 8 – KiB náhodný zápis
* 100% 64-KiB sekvenční čtení
* 100% 64-KiB sekvenční zápis
* 50% 64-KiB sekvenční čtení, 50% 64-KiB sekvenční zápis
* 50% 8 – KiB náhodné čtení, 50% 8 – KiB náhodný zápis

## <a name="bandwidth"></a>Šířka pásma

Azure NetApp Files nabízí více [úrovní služeb](azure-netapp-files-service-levels.md). Každá úroveň služby nabízí různé množství šířky pásma na TiB zřízené kapacity (kvóta svazku). Limit šířky pásma pro svazek se zřizuje na základě kombinace úrovně služby a kvóty svazku. Limit šířky pásma je pouze jedním faktorem při určování skutečné propustnosti, která bude realizována.  

V současné době je 4 500 MiB nejvyšší propustnost, kterou dosáhla zatížení na jednom svazku v rámci testování.  U úrovně Premium Service by kvóta 70,31 TiB měla zajistit dostatečnou šířku pásma pro dosažení této propustnosti na základě následujícího výpočtu: 

![Vzorec šířky pásma](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvóta a úroveň služeb](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Úlohy náročné na propustnost

Test propustnosti využíval Vdbench a kombinaci virtuálních počítačů úložiště 12xD32s v3. Vzorový svazek v testu dosáhl následujících čísel propustnosti:

![Test propustnosti](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Úlohy náročné na vstupně-výstupní operace

Test I/O používá Vdbench a kombinaci virtuálních počítačů úložiště 12xD32s v3. Vzorový svazek v testu dosáhl následujících vstupně-výstupních čísel:

![Vstupně-výstupní testování](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

Vzdálenost mezi testovacími virtuálními počítači a svazkem Azure NetApp Files má dopad na výkon vstupně-výstupních operací.  Následující graf porovnává počet vstupně-výstupních operací s odezvou latence pro dvě různé sady virtuálních počítačů.  Jedna sada virtuálních počítačů je blízko Azure NetApp Files a druhá sada je dále volná.  Vyšší latence pro další sadu virtuálních počítačů má vliv na množství IOPS dosažené na dané úrovni paralelismu.  Bez ohledu na to, že čtení na svazku může překročit 300 000 IOPS, jak je znázorněno níže: 

![Studie latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Souhrn

Úlohy citlivé na latenci (databáze) můžou mít dobu odezvy s jednou milisekundou. Transakční výkon může být nad 300k IOPS pro jeden svazek.

Aplikace citlivé na propustnost (pro streamování a vytváření imagí) můžou mít GiB propustnost 4,5/s.

## <a name="example-scripts"></a>Ukázkové skripty

Následující příklady skriptů slouží pouze k demonstračnímu účelu.  Nepoužívají se k produkčním účelům.  

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
