---
title: Propustnost sítě virtuálního počítače Azure | Microsoft Docs
description: Další informace o propustnost sítě virtuálního počítače Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: f22b6f361f0c5bea547721309bb0f75b62f18d92
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2018
ms.locfileid: "27778951"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuální počítač šířku pásma sítě

Azure nabízí celou řadu velikosti virtuálních počítačů a typy, každý s jinou kombinaci možnosti výkonu. Jeden funkce je sítě propustnost (nebo šířky pásma), měří v MB za sekundu (Mbps). Vzhledem k tomu, že jsou virtuální počítače hostované na sdílených hardwaru, musí být dostatečnou kapacitu sítě sdílená poměrně mezi virtuálními počítači, který sdílí stejný hardware. Větší virtuální počítače jsou přiděleny relativně větší šířku pásma než menší virtuální počítače.
 
Každý virtuální počítač přidělenou šířku pásma sítě je – měření podle objemu na výchozí přenos (odchozí) z virtuálního počítače. Veškerý přenos v síti a virtuální počítač se počítá směrem k přidělené limit, bez ohledu na cílový. Například pokud má virtuální počítač je omezena na 1 000 MB/s, tento limit platí jak pro odchozí provoz je určené pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
 
Příjem příchozích dat není – měření podle objemu nebo jsou omezena přímo. Existují však dalších faktorech, například procesoru a úložiště omezení, což může mít vliv na virtuální počítač není schopen zpracovávat příchozí data.

Zrychlený sítě je funkce, navržená tak, aby zlepšit výkon sítě, včetně latence, propustnosti a využití procesoru. Zrychlený sítě můžete zlepšit propustnost virtuálního počítače, je to udělat jen až virtuálního počítače přidělené šířky pásma. Další informace o možnostech Accelerated sítě najdete v tématu Accelerated sítě pro [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) virtuálních počítačů.
 
Virtuální počítače Azure musí mít jeden, ale může mít několik síťových rozhraní připojených k nim. Šířka pásma přidělená k virtuálnímu počítači je součet hodnot všechny odchozí přenosy mezi všech síťových rozhraní připojených k virtuálnímu počítači. Jinými slovy je přidělené šířky pásma na virtuální počítač, bez ohledu na to, kolik síťových rozhraní připojených k virtuálnímu počítači. Informace o tom, kolik síťových rozhraní různých podporu velikosti virtuálního počítače Azure, najdete v části Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. 

## <a name="expected-network-throughput"></a>Propustnost očekávané sítě

Očekávaný odchozí propustnost a počet síťových rozhraní nepodporuje velikost každého virtuálního počítače je podrobně popsán v Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. Vyberte typ, jako je například obecné účely, pak vyberte velikost řady na stránce výsledné, jako je například Dv2-series. Každé série obsahuje tabulku s sítě specifikace v posledním sloupci s názvem, **maximální počet síťových adaptérů / očekávaný výkon sítě (Mbps)**. 

Omezení propustnosti se vztahuje k virtuálnímu počítači. Propustnost je nemá vliv následující faktory:
- **Počet síťových rozhraní**: limit šířky pásma je kumulativní z všechny odchozí přenosy z virtuálního počítače.
- **Accelerated sítě**: Přestože funkce můžou být užitečné v dosažení limit publikované, nezmění limit.
- **Provoz cílové**: všech cílů započítávat odchozí limit.
- **Protokol**: všechny odchozí přenosy přes všechny protokoly, se započítává limit.

## <a name="next-steps"></a>Další postup

- [Optimalizovat propustnost sítě pro operační systém virtuálního počítače](virtual-network-optimize-network-bandwidth.md)
- [Propustnost sítě testovací](virtual-network-bandwidth-testing.md) pro virtuální počítač.
