---
title: Propustnost sítě virtuálních počítačů Azure | Dokumentace Microsoftu
description: Další informace o propustnosti sítě virtuálních počítačů Azure.
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
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153744"
---
# <a name="virtual-machine-network-bandwidth"></a>Šířka pásma sítě virtuálních počítačů

Azure nabízí širokou škálu velikostí virtuálních počítačů a typy, každý s různou kombinaci možností výkonu. Jedna funkce je sítě propustnost (nebo šířky pásma) měří v MB za sekundu (MB/s). Vzhledem k tomu virtuální počítače hostují na sdíleném hardwaru, musí být kapacita šířky sdílené poměrně mezi virtuálními počítači, které sdílení stejného hardwaru. Větší virtuální počítače mají při přidělování relativně větší šířku pásma než menších virtuálních počítačích.
 
Šířka pásma sítě, které jsou přidělené na každý virtuální počítač se měří na výchozí přenos (odchozí) z virtuálního počítače. Veškerý přenos v síti byste museli opustit virtuální počítač se počítá směrem k přidělené limit, bez ohledu na cílový. Například pokud virtuální počítač má limit 1 000 MB/s, toto omezení platí, zda je odchozí provoz určený pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
 
Příchozí přenos dat se měří nebo jsou omezena přímo. Existují však dalších faktorů, jako jsou omezení úložiště a procesoru, které můžou ovlivnit virtuálního počítače není schopen zpracovávat příchozí data.

Akcelerované síťové služby se funkci navrženou pro zvýšení výkonu sítě, včetně latence, propustnosti a využití procesoru. Akcelerované síťové služby může zlepšit propustnost virtuálních počítačů, je to tak, jen až k virtuálnímu počítači přidělená šířky pásma. Další informace o akcelerovaných síťových službách najdete v tématu Accelerated networking pro [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) virtuálních počítačů.
 
Virtuální počítače Azure musí mít jeden, ale může mít několik, síťová rozhraní připojená k nim. Šířka pásma přidělená k virtuálnímu počítači je součet veškerého odchozího provozu ve všech síťových rozhraní připojených k virtuálnímu počítači. Jinými slovy přidělené šířky pásma je na virtuální počítač, bez ohledu na to, kolik síťových rozhraní, které jsou připojené k virtuálnímu počítači. Informace o tom, kolik síťová rozhraní různé podporu velikosti virtuálního počítače Azure, podívejte se na Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. 

## <a name="expected-network-throughput"></a>Propustnost sítě očekávané

Očekávaná výstupní propustnost a počet síťových rozhraní, které podporuje všechny velikosti virtuálních počítačů je podrobně popsán v Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. Vyberte typ, například pro obecné účely, pak vyberte velikost series na výsledný stránky, například řady Dv2-series. Každé datové řady obsahuje tabulku s sítě specifikace v poslední sloupec s názvem, **maximální počet síťových karet / očekávaný výkon sítě (MB/s)** . 

Omezení propustnosti platí pro virtuální počítač. Propustnost je tato akce vliv následující faktory:
- **Počet síťových rozhraní**: Limit šířky pásma je kumulativní z veškerého odchozího provozu z virtuálního počítače.
- **Akcelerované síťové služby**: I když tato funkce může být užitečné při dosažení limitu publikované, nezmění limit.
- **Určení provozu**: Všechny cíle počítají odchozí limit.
- **Protokol**: Veškerý odchozí provoz přes všechny protokoly započítává limitu.

## <a name="network-flow-limits"></a>Síťové omezení toku

Kromě šířky pásma počet síťových připojení, které jsou k dispozici na virtuálním počítači v daném okamžiku může ovlivnit výkon sítě. Azure stack sítě udržuje svůj stav pro každý směr připojení TCP/UDP v datových struktur nazývá "toky". Typické připojení TCP nebo UDP bude mít 2 toky vytvořené, jeden pro příchozí a druhý pro odchozí směr. 

Přenos dat mezi koncovými body vyžaduje vytvoření několika toků kromě těch, které provádějí přenos dat. Mezi příklady patří toky vytvořené pro překlad názvů DNS a toků vytvořených pro sondy stavu nástroje pro vyrovnávání zatížení. Všimněte si, že síťová virtuální zařízení (Nva), jako jsou brány, proxy servery, brány firewall, se také zobrazí toky vytváří pro připojení k ukončuje na zařízení a vytvoří se zařízení. 

![Počet toku pro konverzace TCP přes zařízení předávání](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Tok omezení a doporučení

V současné době podporuje Azure síťového zásobníku celé sítě 250 tis. toků s dobrého výkonu pro virtuální počítače s větší než 8 jader procesoru a 100 tisíc celkový počet toků s dobrého výkonu pro virtuální počítače s méně než 8 jader procesoru. Za toto omezení sítě výkon sníží, přetrénujte řádně pro další toky až vynucené omezení milionu celkový počet toků, 500 tisíc příchozí a 500 tisíc odchozí, po které další toky se zahodí.

||Virtuální počítače s < procesor s 8 jádry|Virtuální počítače s 8 + jader procesoru|
|---|---|---|
|<b>Dobrý výkon</b>|100 tis. toků |250 tis. toků|
|<b>Důvodem sníženého výkonu</b>|Nad 100 tisíc toky|Nad 250 tis. toků|
|<b>Limit toku</b>|1 milion toků|1 milion toků|

Metriky jsou k dispozici v [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) můžete sledovat počet síťovými toky a rychlost vytváření toku u virtuálního počítače nebo VMSS instancí.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Připojení zařízení a ukončení míra může také ovlivnit výkon sítě jako připojení zařízení a ukončení složek procesoru pomocí rutiny zpracování paketů. Doporučujeme vám, že jste srovnávací testy úlohy s využitím očekávaného provozu a horizontální navýšení kapacity úloh odpovídajícím způsobem tak, aby odpovídaly vašim požadavkům na výkon. 

## <a name="next-steps"></a>Další postup

- [Optimalizace propustnosti sítě pro operační systém virtuálního počítače](virtual-network-optimize-network-bandwidth.md)
- [Propustnost sítě testovací](virtual-network-bandwidth-testing.md) pro virtuální počítač.
