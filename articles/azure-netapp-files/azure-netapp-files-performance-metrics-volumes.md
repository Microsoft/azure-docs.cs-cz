---
title: Doporučené testy benchmarku výkonu – soubory Azure NetApp
description: Přečtěte si o doporučeních pro testování srovnávacích testů pro výkon svazku a metriky pomocí souborů Azure NetApp.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551518"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Doporučení srovnávacích testů výkonnosti pro Azure NetApp Files

Tento článek obsahuje doporučení pro testování srovnávacích testů pro výkon svazku a metriky pomocí souborů Azure NetApp.

## <a name="overview"></a>Přehled

Chcete-li porozumět charakteristikám výkonu svazku Souborů Azure NetApp, můžete pomocí nástroje s otevřeným zdrojovým kódem [FIO](https://github.com/axboe/fio) spustit řadu srovnávacích kritérií k simulaci různých úloh. FIO lze nainstalovat jak na Linux, tak na operační systémy windows.  Je to vynikající nástroj pro získání rychlého snímku iops a propustnosti pro svazek.

### <a name="vm-instance-sizing"></a>Změna velikosti instance virtuálního virtuálního soudu

Chcete-li dosáhnout nejlepších výsledků, ujistěte se, že používáte instanci virtuálního počítače , která je vhodně dimenzována k provedení testů. Následující příklady používají instanci Standard_D32s_v3. Další informace o velikostech instancí virtuálních počítačů najdete [v tématu Velikosti pro virtuální počítače s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) pro virtuální počítače založené na Windows a [Velikosti pro linuxové virtuální počítače ve](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuálních počítačích založených na Linuxu.

### <a name="azure-netapp-files-volume-sizing"></a>Změna velikosti svazku souborů Azure NetApp

Ujistěte se, že jste zvolili správnou úroveň služby a velikost kvóty svazku pro očekávanou úroveň výkonu. Další informace najdete [v tématu Úrovně služeb pro soubory Azure NetApp.](azure-netapp-files-service-levels.md)

### <a name="virtual-network-vnet-recommendations"></a>Doporučení pro virtuální síť (VNet)

Testování benchmarku byste měli provést ve stejné virtuální síti jako soubory Azure NetApp. Následující příklad ukazuje doporučení:

![Doporučení virtuální sítě](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalace FIO

FIO je k dispozici v binárním formátu pro Linux i Windows. Postupujte podle binárních balíčků části [fio](https://github.com/axboe/fio) nainstalovat pro platformu dle vašeho výběru.

## <a name="fio-examples-for-iops"></a>Příklady FIO pro IOPS 

Příklady FIO v této části používají následující nastavení:
* Velikost instance virtuálního počítače: D32s_v3
* Úroveň a velikost kapacitního fondu: Premium / 50 TiB
* Objemová kvóta: 48 TiB

Následující příklady ukazují fio náhodné čtení a zápisy.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k blok velikost 100% náhodné čtení

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Výstup: 68k číst IOPS zobrazeno

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blok velikost 100% náhodné zápisy

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Výstup: 73k zápis IOPS zobrazen

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Příklady FIO pro šířku pásma

Příklady v této části ukazují FIO sekvenční čtení a zápisy.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: Velikost bloku 64k 100% sekvenční čtení

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Výkon: Zobrazena propustnost 11,8 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: Velikost bloku 64k 100% sekvenční zápisy

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Výkon: Zobrazena propustnost 12,2 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metriky svazku

Data o výkonu souborů Azure NetApp jsou dostupná prostřednictvím čítačů Azure Monitoru. Čítače jsou k dispozici prostřednictvím portálu Azure a požadavky REST API GET. 

Historická data můžete zobrazit pro následující informace:
* Průměrná latence čtení 
* Průměrná latence zápisu 
* Čtení VOPS (průměr)
* Zapsat VOPS (průměr)
* Logická velikost svazku (průměr)
* Velikost snímku svazku (průměr)

### <a name="using-azure-monitor"></a>Pomocí Azure Monitoru 

K čítačům souborů Azure NetApp můžete přistupovat na základě počtu svazků na stránce Metriky, jak je znázorněno níže:

![Metriky Azure Monitoru](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Řídicí panel můžete vytvořit také v Azure Monitoru pro soubory Azure NetApp tak, že přejdete na stránku Metriky, vyfiltrujete NetApp a určíte čítače svazků, které vás zajímají: 

![Řídicí panel služby Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Přístup k rozhraní API azure monitoru

K čítačům souborů Azure NetApp můžete přistupovat pomocí volání rozhraní REST API. Viz [Podporované metriky s Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) pro čítače pro fondy kapacity a svazky.

Následující příklad ukazuje adresu URL GET pro zobrazení velikosti logického svazku:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Další kroky

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)