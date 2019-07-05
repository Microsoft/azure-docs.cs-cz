---
title: Srovnávací test testování výkonu svazku a metriky pomocí souborů NetApp Azure | Dokumentace Microsoftu
description: Poskytuje zkušební doporučení k výkonu svazku a použití služby soubory Azure NetApp metriky úlohy.
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
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478807"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Srovnávací testování pro metriky a výkon svazků s využitím Azure NetApp Files

Tento článek obsahuje testování doporučení pro svazek výkonu a metriky pomocí služby soubory Azure NetApp srovnávacího testu.

## <a name="overview"></a>Přehled

Lépe pochopit vlastnosti výkonu svazku souborů NetApp Azure, můžete použít open source nástroj [FIO](https://github.com/axboe/fio) zahrnuje spuštění řady srovnávací testy pro simulace širokou škálu úloh. FIO se dá nainstalovat na obou Linux a Windows na základě operačních systémů.  Je skvělým nástrojem k získání rychlého snímku vstupně-výstupních operací a propustnosti pro svazek.

### <a name="vm-instance-sizing"></a>Velikost instance virtuálního počítače

Nejlepších výsledků dosáhnete Ujistěte se, že používáte instance virtuálního počítače (VM), který odpovídajícím způsobem přizpůsoben pro provádění testů. Následující příklady používají Standard_D32s_v3 instance. Další informace o velikostech instancí virtuálních počítačů najdete v tématu [velikosti pro Windows virtual machines v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) u virtuálních počítačů s Windows a [velikostí pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pro virtuální počítače s linuxem.

### <a name="azure-netapp-files-volume-sizing"></a>Nastavení velikosti svazků Azure souborů NetApp

Ujistěte se, abyste zvolili velikosti správné služby úrovně a svazek kvóty pro úroveň očekávaný výkon. Zobrazit [úrovně služby pro Azure NetApp Files](azure-netapp-files-service-levels.md) Další informace.

### <a name="virtual-network-vnet-recommendations"></a>Doporučení pro virtuální síť (VNet)

Měli byste provést srovnávací test testování ve stejné virtuální síti jako NetApp soubory Azure. Následující příklad ukazuje doporučení:

![Doporučení pro virtuální síť](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalace FIO

FIO je k dispozici v binárním formátu pro Linux a Windows. Pomocí postupu v části binární balíčky v [FIO](https://github.com/axboe/fio) instalace pro platformu podle vašeho výběru.

## <a name="fio-examples-for-iops"></a>Příklady FIO pro vstupně-výstupních operací 

FIO příklady v této části použijte následující nastavení:
* Velikost instance virtuálního počítače: D32s_v3
* Úroveň služby kapacity fondu a velikost: Premium / 50 TiB
* Kvóta velikosti svazku: 48 TiB

Následující příklady ukazují FIO náhodné operace čtení a zápisu.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: blokovat náhodné čtení 100 % velikosti 8 kb

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Výstup: 68k čtení zobrazí vstupně-výstupních operací

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: blokovat náhodné zápisy 100 % velikosti 8 kb

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Výstup: 73k zápisu zobrazí vstupně-výstupních operací

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Příklady FIO šířky pásma

Příklady v této části zobrazit sekvenční FIO čte a zapisuje.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: sekvenční čtení 100 % velikosti bloku 64 kB

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Výstup: 11.8 zobrazí propustnost GB/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: blokovat sekvenční zápisy 100 % velikosti 64 kB

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Výstup: 12.2 zobrazí propustnost GB/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Svazek metriky

Údaje o výkonu NetApp soubory Azure je dostupná prostřednictvím Azure Monitor čítače. Tyto čítače jsou k dispozici prostřednictvím webu Azure portal a požadavky metody REST API GET. 

Můžete zobrazit historická data pro následující informace:
* Průměrná latence čtení 
* Zápis Průměrná latence 
* Vstupně-výstupních operací čtení (průměr)
* Zápis IOPS (průměr)
* Logická velikost svazku (průměr)
* Velikost snímku svazku (průměr)

### <a name="using-azure-monitor"></a>Pomocí Azure Monitoru 

Soubory Azure NetApp čítače na základě jednotlivé svazky přístupné ze stránky metriky, jak je znázorněno níže:

![Metriky Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Řídicí panel můžete také vytvořit ve službě Azure Monitor pro soubory Azure NetApp přechod na stránku metriky, filtrování NetApp a určením čítače svazku které vás zajímají: 

![Řídicí panel služby Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure přístup k monitorování rozhraní API

Čítače NetApp soubory Azure přístupná pomocí volání rozhraní REST API. Zobrazit [podporované metriky ve službě Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) týkajících se čítačů pro fondy kapacity a svazky.

Následující příklad ukazuje získat adresu URL pro velikost svazku logické zobrazení:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Další postup

- [Úrovně služeb pro soubory Azure NetApp](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro soubory Azure NetApp](azure-netapp-files-performance-benchmarks.md)