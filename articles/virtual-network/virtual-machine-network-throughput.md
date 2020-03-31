---
title: Propustnost sítě virtuálních strojů Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o propustnostsítě virtuálních strojů Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245431"
---
# <a name="virtual-machine-network-bandwidth"></a>Šířka pásma sítě virtuálních strojů

Azure nabízí různé velikosti a typy virtuálních počítačů, každý s jinou kombinací možností výkonu. Jednou z možností je propustnost sítě (nebo šířka pásma), měřeno v megabitech za sekundu (Mb/s). Vzhledem k tomu, že virtuální počítače jsou hostované na sdíleném hardwaru, musí být kapacita sítě spravedlivě sdílena mezi virtuálními počítači, které sdílejí stejný hardware. Větší virtuální počítače jsou přiděleny relativně větší šířku pásma než menší virtuální počítače.
 
Šířka pásma sítě přidělená každému virtuálnímu počítači se měří při odchozím (odchozím) provozu z virtuálního počítače. Veškerý síťový provoz opouštějící virtuální počítač se započítává do přiděleného limitu bez ohledu na cíl. Například pokud virtuální počítač má limit 1 000 Mb/s, toto omezení platí, zda odchozí provoz je určen pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
 
Příchozí přenos není měřennebo omezen přímo. Existují však další faktory, jako jsou limity procesoru a úložiště, které mohou ovlivnit schopnost virtuálního počítače zpracovávat příchozí data.

Akcelerovaná síť je funkce navržená ke zlepšení výkonu sítě, včetně latence, propustnosti a využití procesoru. Zatímco zrychlené sítě mohou zlepšit propustnost virtuálního počítače, může tak učinit pouze do přidělené šířky pásma virtuálního počítače. Další informace o zrychlených sítích najdete v tématu Accelerated networking for [Windows](create-vm-accelerated-networking-powershell.md) or [Linux](create-vm-accelerated-networking-cli.md) virtual machines.
 
Virtuální počítače Azure musí mít jeden, ale může mít několik, síťová rozhraní k nim připojené. Šířka pásma přidělená virtuálnímu počítači je součtem všech odchozích přenosů ve všech síťových rozhraních připojených k virtuálnímu počítači. Jinými slovy přidělená šířka pásma je na virtuální počítač, bez ohledu na to, kolik síťových rozhraní jsou připojeny k virtuálnímu počítači. Informace o tom, kolik síťových rozhraní různé velikosti virtuálních zařízení Azure podporují, najdete v tématu Velikosti virtuálních zařízení Azure [windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [linuxových](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních zařízení. 

## <a name="expected-network-throughput"></a>Očekávaná propustnost sítě

Očekávaná odchozí propustnost a počet síťových rozhraní podporovaných jednotlivými velikostmi virtuálních zařízení je podrobný ve velikostech virtuálních zařízení Azure [pro Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Vyberte typ, například Obecné účely, a pak vyberte velikostní řadu na výsledné stránce, například řadu Dv2. Každá řada má tabulku se specifikacemi sítě v posledním sloupci s názvem **Maximální síťových připojení / Očekávaný výkon sítě (Mb/s).** 

Limit propustnost platí pro virtuální počítač. Propustnost není ovlivněna následujícími faktory:
- **Počet síťových rozhraní**: Limit šířky pásma se sčítá jako kumulativní veškerý odchozí provoz z virtuálního počítače.
- **Zrychlené vytváření sítí**: Ačkoli tato funkce může být užitečná při dosahování publikovaného limitu, nemění limit.
- **Cíl provozu**: Všechny cíle se počítají do odchozího limitu.
- **Protokol**: Veškerý odchozí provoz ve všech protokolech se započítává do limitu.

## <a name="network-flow-limits"></a>Limity síťového toku

Kromě šířky pásma může počet síťových připojení, která jsou v daném okamžiku k dispozici na virtuálním počítači, ovlivnit jeho výkon v síti. Síťový zásobník Azure udržuje stav pro každý směr připojení TCP/UDP v datových strukturách nazývaných toky. Typické připojení TCP/UDP bude mít vytvořené 2 toky, jeden pro příchozí a druhý pro odchozí směr. 

Přenos dat mezi koncovými body vyžaduje vytvoření několika toků kromě těch, které provádějí přenos dat. Některé příklady jsou toky vytvořené pro rozlišení DNS a toky vytvořené pro sondy stavu vykladače zatížení. Všimněte si také, že síťová virtuální zařízení (NVA), jako jsou brány, proxy servery, brány firewall, uvidí toky vytvářené pro připojení ukončená v zařízení a pocházející ze zařízení. 

![Počet toků pro konverzaci TCP prostřednictvím zařízení pro předávání](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limity toku a doporučení

Síťový zásobník Azure dnes podporuje celkové síťové toky 250 tisíc s dobrým výkonem pro virtuální počítače s více než 8 procesorovými jádry a celkovým tokem 100 tisíc s dobrým výkonem pro virtuální počítače s méně než 8 procesorovými jádry. Za tento limit výkon sítě snižuje řádně pro další toky až do pevného limitu 500 kS celkových toků, 250 kS příchozí a 250 kM odchozí, po kterém jsou vynechány další toky.

||Virtuální počítače s <8 procesorových jader|Virtuální počítače s více než 8 procesorovými jádry|
|---|---|---|
|<b>Dobrý výkon</b>|Toky 100 k |250k toků|
|<b>Snížený výkon</b>|Nad 100k toků|Nad 250k toků|
|<b>Limit průtoku</b>|500k toky|500k toky|

Metriky jsou k dispozici ve [službě Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) ke sledování počtu síťových toků a rychlosti vytváření toku na vašich instancích virtuálních počítačích nebo vmss.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Sazby za navázání připojení a ukončení mohou také ovlivnit výkon sítě, protože navázání připojení a ukončení sdílejí procesor s rutinami zpracování paketů. Doporučujeme porovnat úlohy s očekávanými vzory provozu a odpovídajícím způsobem škálovat úlohy tak, aby odpovídaly vašim potřebám v oblasti výkonu. 

## <a name="next-steps"></a>Další kroky

- [Optimalizace propustnosti sítě pro operační systém virtuálního počítače](virtual-network-optimize-network-bandwidth.md)
- [Testování propustnost sítě](virtual-network-bandwidth-testing.md) pro virtuální počítač.
