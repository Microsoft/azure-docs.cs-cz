---
title: Doporučené testy testů výkonnosti – Azure NetApp Files
description: Přečtěte si o doporučeních testování srovnávacích testů pro výkon a metriky na svazcích pomocí Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593402"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Doporučení srovnávacích testů výkonnosti pro Azure NetApp Files

Tento článek poskytuje doporučení pro testování testů pro výkon a metriky pomocí Azure NetApp Files.

## <a name="overview"></a>Přehled

Abyste pochopili charakteristiky výkonu Azure NetApp Filesho svazku, můžete pomocí Open Source nástroje [FIO](https://github.com/axboe/fio) spustit sérii srovnávacích testů pro simulaci různých úloh. FIO se dá nainstalovat na operační systémy Linux i Windows.  Je to vynikající nástroj pro rychlé vytvoření snímku a propustnosti pro svazek.

### <a name="vm-instance-sizing"></a>Velikost instance virtuálního počítače

Pro dosažení nejlepších výsledků se ujistěte, že používáte instanci virtuálního počítače, která má patřičnou velikost pro provedení testů. V následujících příkladech se používá instance Standard_D32s_v3. Další informace o velikostech instancí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows v Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro virtuální počítače s Windows a [velikosti pro virtuální počítače Linux v Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pro virtuální počítače se systémem Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Velikost Azure NetApp Filesho svazku

Ujistěte se, že jste zvolili správnou úroveň služby a velikost kvóty svazku pro očekávanou úroveň výkonu. Další informace najdete v tématu [úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Doporučení k virtuální síti (VNet)

Testování srovnávacích testů byste měli provést ve stejné virtuální síti jako Azure NetApp Files. Následující příklad ukazuje doporučení:

![Doporučení virtuální sítě](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalace FIO

FIO je k dispozici v binárním formátu pro Linux i Windows. Postupujte podle oddílu binární balíčky v [FIO](https://github.com/axboe/fio) a nainstalujte aplikaci podle vaší volby.

## <a name="fio-examples-for-iops"></a>Příklady FIO pro IOPS 

Příklady FIO v této části používají následující nastavení:
* Velikost instance virtuálního počítače: D32s_v3
* Úroveň a velikost služby fondu kapacity: Premium/50 TiB
* Velikost kvóty svazku: 48 TiB

Následující příklady znázorňují náhodné čtení a zápisy FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: velikost bloku 8k 100% náhodného čtení

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Výstup: zobrazeno 68k čtení IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: velikost bloku 8k 100% náhodného zápisu

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Výstup: zobrazil se 73k zápis IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Příklady FIO pro šířku pásma

Příklady v této části ukazují sekvenční čtení a zápisy FIO.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 KB velikost bloku 100% sekvenčního čtení

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Výstup: zobrazení propustnosti 11,8 GB/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 KB velikost bloku 100% sekvenčních zápisů

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Výstup: zobrazení propustnosti 12,2 GB/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metriky svazků

Údaje o výkonu Azure NetApp Files jsou k dispozici prostřednictvím čítačů Azure Monitor. Čítače jsou k dispozici prostřednictvím Azure Portal a REST API požadavků GET. 

Můžete si prohlédnout historická data pro následující informace:
* Průměrná latence čtení 
* Průměrná latence zápisu 
* Čtení IOPS (průměr)
* Zápis IOPS (průměr)
* Logická velikost svazku (průměr)
* Velikost snímku svazku (průměr)

### <a name="using-azure-monitor"></a>Pomocí Azure Monitoru 

K Azure NetApp Files čítačů můžete přistupovat na základě jednotlivých svazků ze stránky metriky, jak je znázorněno níže:

![Azure Monitor metriky](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Můžete také vytvořit řídicí panel v Azure Monitor pro Azure NetApp Files tak, že na stránku metriky vyfiltrujete NetApp a zadáte čítače svazku, které vás zajímají: 

![Řídicí panel služby Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Přístup k rozhraní API Azure Monitor

K Azure NetApp Filesm čítačům můžete přistupovat pomocí volání REST API. Další informace najdete v tématu [podporované metriky ve službě Azure monitor: Microsoft. NetApp/netAppAccounts/capacityPools/](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) Volumes pro čítače pro fondy kapacity a svazky.

Následující příklad ukazuje adresu URL GET pro zobrazení velikosti logického svazku:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Další kroky

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro Linux](performance-benchmarks-linux.md)