---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175365"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Virtuální počítače](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na jednu cloudovou službu<sup>1</sup> |50 |50 |
| Vstupní koncové body za cloudové služby<sup>2</sup> |150 |150 |

<sup>1</sup>virtuální počítače vytvořené pomocí modelu nasazení classic místo Azure Resource Manageru se automaticky ukládají v cloudové službě. Můžete přidat další virtuální počítače do cloudové služby pro vyrovnávání zatížení a dostupnost. 

<sup>2</sup>vstupní koncové body umožňují komunikaci virtuálním počítačem mimo virtuální počítač cloudové služby. Virtuální počítače ve stejné cloudové službě nebo virtuální sítě můžou automaticky komunikovat mezi sebou. Další informace najdete v tématu [postup nastavení koncových bodů pro virtuální počítač](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
