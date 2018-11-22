---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279532"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Virtuální počítače](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na jednu cloudovou službu<sup>1</sup> |50 |50 |
| Vstupní koncové body za cloudové služby<sup>2</sup> |150 |150 |

<sup>1</sup>virtuální počítače vytvořené ve službě Service Management (místo Resource Manager) se automaticky ukládají v cloudové službě. Můžete přidat další virtuální počítače do cloudové služby pro vyrovnávání zatížení a dostupnost. 

<sup>2</sup>vstupní koncové body umožňují komunikaci virtuálním počítačem mimo virtuální počítač cloudové služby. Virtuální počítače ve stejné cloudové službě nebo virtuální sítě můžou automaticky komunikovat mezi sebou. Zobrazit [jak nastavit koncové body k virtuálnímu počítači](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
