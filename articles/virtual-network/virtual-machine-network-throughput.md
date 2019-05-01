---
title: Propustnost sítě virtuálních počítačů Azure | Dokumentace Microsoftu
description: Další informace o propustnosti sítě virtuálních počítačů Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 182b3b7dad828e67d006391e00986406729c959d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689255"
---
# <a name="virtual-machine-network-bandwidth"></a>Šířka pásma sítě virtuálních počítačů

Azure nabízí širokou škálu velikostí virtuálních počítačů a typy, každý s různou kombinaci možností výkonu. Jedna funkce je sítě propustnost (nebo šířky pásma) měří v MB za sekundu (MB/s). Vzhledem k tomu virtuální počítače hostují na sdíleném hardwaru, musí být kapacita šířky sdílené poměrně mezi virtuálními počítači, které sdílení stejného hardwaru. Větší virtuální počítače mají při přidělování relativně větší šířku pásma než menších virtuálních počítačích.
 
Šířka pásma sítě, které jsou přidělené na každý virtuální počítač se měří na výchozí přenos (odchozí) z virtuálního počítače. Veškerý přenos v síti byste museli opustit virtuální počítač se počítá směrem k přidělené limit, bez ohledu na cílový. Například pokud virtuální počítač má limit 1 000 MB/s, toto omezení platí, zda je odchozí provoz určený pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
 
Příchozí přenos dat se měří nebo jsou omezena přímo. Existují však dalších faktorů, jako jsou omezení úložiště a procesoru, které můžou ovlivnit virtuálního počítače není schopen zpracovávat příchozí data.

Akcelerované síťové služby se funkci navrženou pro zvýšení výkonu sítě, včetně latence, propustnosti a využití procesoru. Akcelerované síťové služby může zlepšit propustnost virtuálních počítačů, je to tak, jen až k virtuálnímu počítači přidělená šířky pásma. Další informace o akcelerovaných síťových službách najdete v tématu Accelerated networking pro [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) virtuálních počítačů.
 
Virtuální počítače Azure musí mít jeden, ale může mít několik, síťová rozhraní připojená k nim. Šířka pásma přidělená k virtuálnímu počítači je součet veškerého odchozího provozu ve všech síťových rozhraní připojených k virtuálnímu počítači. Jinými slovy přidělené šířky pásma je na virtuální počítač, bez ohledu na to, kolik síťových rozhraní, které jsou připojené k virtuálnímu počítači. Informace o tom, kolik síťová rozhraní různé podporu velikosti virtuálního počítače Azure, podívejte se na Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. 

## <a name="expected-network-throughput"></a>Propustnost sítě očekávané

Očekávaná výstupní propustnost a počet síťových rozhraní, které podporuje všechny velikosti virtuálních počítačů je podrobně popsán v Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů. Vyberte typ, například pro obecné účely, pak vyberte velikost series na výsledný stránky, například řady Dv2-series. Každé datové řady obsahuje tabulku s sítě specifikace v poslední sloupec s názvem, **maximální počet síťových karet / očekávaný výkon sítě (MB/s)**. 

Omezení propustnosti platí pro virtuální počítač. Propustnost je tato akce vliv následující faktory:
- **Počet síťových rozhraní**: Limit šířky pásma je kumulativní z veškerého odchozího provozu z virtuálního počítače.
- **Akcelerované síťové služby**: I když tato funkce může být užitečné při dosažení limitu publikované, nezmění limit.
- **Určení provozu**: Všechny cíle počítají odchozí limit.
- **Protokol**: Veškerý odchozí provoz přes všechny protokoly započítává limitu.

## <a name="next-steps"></a>Další postup

- [Optimalizace propustnosti sítě pro operační systém virtuálního počítače](virtual-network-optimize-network-bandwidth.md)
- [Propustnost sítě testovací](virtual-network-bandwidth-testing.md) pro virtuální počítač.
