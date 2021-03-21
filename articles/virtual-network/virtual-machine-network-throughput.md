---
title: Propustnost sítě virtuálních počítačů Azure | Microsoft Docs
description: Přečtěte si o propustnosti sítě virtuálních počítačů Azure, včetně způsobu přidělování šířky pásma virtuálnímu počítači.
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
ms.openlocfilehash: cb128f9269895f04d1e0dad8e0c8d06c481e86c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576168"
---
# <a name="virtual-machine-network-bandwidth"></a>Šířka pásma sítě virtuálních počítačů

Azure nabízí nejrůznější velikosti a typy virtuálních počítačů, z nichž každá má různou kombinaci možností výkonu. Jedna z možností je propustnost sítě (neboli šířka pásma) měřená v megabajtech za sekundu (MB/s). Vzhledem k tomu, že virtuální počítače jsou hostované na sdíleném hardwaru, musí být síťová kapacita sdílená mezi virtuálními počítači, které sdílejí stejný hardware. Větší virtuální počítače jsou přidělovány relativně větší šířce pásma než menší virtuální počítače.
 
Šířka pásma sítě přidělená každému virtuálnímu počítači se měří na odchozím (odchozím) provozu z virtuálního počítače. Veškerý síťový provoz opouštějící virtuální počítač se započítává k přidělenému limitu bez ohledu na cíl. Pokud má třeba virtuální počítač omezení 1 000 MB/s, platí toto omezení, jestli je odchozí provoz určený pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
 
Příchozí přenos dat není měřený ani omezený přímo. Existují však i jiné faktory, například omezení pro procesor a úložiště, což může mít vliv na schopnost virtuálního počítače zpracovávat příchozí data.

Akcelerovaná síť je funkce navržená tak, aby vylepšila výkon sítě, včetně latence, propustnosti a využití procesoru. I když akcelerované síťové služby můžou zvýšit propustnost virtuálního počítače, můžete tak učinit jenom s přidělenou šířkou pásma virtuálního počítače. Další informace o akcelerovaných sítích najdete v tématu urychlení sítě pro virtuální počítače se [systémem Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) .
 
Virtuální počítače Azure musí mít jednu, ale může mít několik síťových rozhraní, která jsou k nim připojená. Šířka pásma přidělená virtuálnímu počítači je součet všech odchozích přenosů napříč všemi síťovými rozhraními připojenými k virtuálnímu počítači. Jinými slovy, přidělená šířka pásma je vázaná na virtuální počítač bez ohledu na to, kolik síťových rozhraní je připojeno k virtuálnímu počítači. Další informace o tom, kolik síťových rozhraní různé velikosti virtuálních počítačů Azure podporuje, najdete v tématu velikosti virtuálních počítačů Azure s [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 

## <a name="expected-network-throughput"></a>Očekávaná propustnost sítě

Očekávaná odchozí propustnost a počet síťových rozhraní podporovaných jednotlivými velikostmi virtuálních počítačů je podrobně popsán v části velikosti virtuálních počítačů Azure s [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Vyberte typ, například obecné účely, a pak na výsledné stránce vyberte řadu velikostí, například Dv2-Series. Každá řada obsahuje tabulku se specifikacemi sítě v posledním sloupci s názvem, **maximální počet síťových adaptérů/očekávaný výkon sítě (MB/s)**. 

Limit propustnosti se vztahuje na virtuální počítač. Propustnost není ovlivněná následujícími faktory:
- **Počet síťových rozhraní**: limit šířky pásma je kumulativní ze všech odchozích přenosů z virtuálního počítače.
- **Akcelerované síťové služby**: i když může být tato funkce užitečná při dosahování publikovaného limitu, tento limit se nemění.
- **Cíl provozu**: celkový počet cílů směrem k odchozímu limitu.
- **Protokol**: veškerý odchozí provoz ve všech protokolech počítá směrem k limitu.

## <a name="network-flow-limits"></a>Omezení toku sítě

Kromě šířky pásma může mít počet síťových připojení na virtuálním počítači v určitou dobu vliv na výkon sítě. Azure Networking Stack udržuje stav pro každý směr připojení TCP/UDP v datových strukturách nazývaných toky. Typické připojení TCP/UDP bude mít vytvořené 2 toky, jednu pro příchozí a další pro odchozí směr. 

Přenos dat mezi koncovými body vyžaduje vytvoření několika toků kromě těch, které provádějí přenos dat. Některé příklady jsou toky vytvořené pro překlad DNS a toky vytvořené pro sondy stavu nástroje pro vyrovnávání zatížení. Všimněte si také, že síťová virtuální zařízení (síťová virtuální zařízení), jako jsou brány, proxy servery a brány firewall, uvidí toky vytvořené pro připojení ukončená na zařízení a vzniklá zařízením. 

![Počet toků pro konverzaci TCP prostřednictvím zařízení pro předávání](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>Omezení toků a doporučení aktivních připojení

V současné době podporuje zásobník sítě Azure pro virtuální počítač 1 milion celkových toků (příchozí a 500 000 odchozí přenosy 500 000). K disdílnému počtu aktivních připojení, která může být zpracována virtuálním počítačem v různých scénářích:
- Virtuální počítače, které patří do virtuální sítě, můžou zpracovávat 500 000 ***aktivní připojení** _ pro všechny velikosti virtuálních počítačů pomocí 500 000 _ *_aktivních toků v každém směru_* *.  
- Virtuální počítače se síťovými virtuálními zařízeními (síťová virtuální zařízení), jako je brána, proxy server nebo brána firewall, můžou zpracovávat 250 tisíc ***aktivní připojení** _ pomocí 500 000 _ *_aktivních toků v každém směru_** kvůli předávání a dodatečnému novému vytváření toků při novém nastavení připojení k dalšímu segmentu směrování, jak je znázorněno na výše uvedeném diagramu. 

Po dosažení tohoto limitu jsou další připojení vyřazená. Zatížení sítě a sazby za ukončení můžou mít vliv na výkon sítě, protože vytváření připojení a ukončení sdílí procesor s rutinami zpracování paketů. Doporučujeme, abyste provedli úlohy srovnávacích testů oproti očekávaným tokům provozu a mohli škálovat úlohy správně, aby odpovídaly vašim požadavkům na výkon.

Metriky jsou k dispozici v [Azure monitor](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) ke sledování počtu toků sítě a rychlosti vytváření toku na INSTANCÍCH virtuálních počítačů nebo VMSS.

![Snímek obrazovky se stránkou metrik Azure Monitor se spojnicovým grafem a součty pro příchozí a výstupní toky.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Zatížení sítě a sazby za ukončení můžou mít vliv na výkon sítě, protože vytváření připojení a ukončení sdílí procesor s rutinami zpracování paketů. Doporučujeme, abyste provedli úlohy srovnávacích testů oproti očekávaným tokům provozu a mohli škálovat úlohy správně, aby odpovídaly vašim požadavkům na výkon. 

## <a name="next-steps"></a>Další kroky

- [Optimalizace propustnosti sítě pro operační systém virtuálního počítače](virtual-network-optimize-network-bandwidth.md)
- [Test propustnosti sítě](virtual-network-bandwidth-testing.md) pro virtuální počítač.