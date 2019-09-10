---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "67175365"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Virtuální počítače](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na cloudovou službu<sup>1</sup> |50 |50 |
| Vstupní koncové body na cloudové služby<sup>2</sup> |150 |150 |

<sup>1</sup> Virtuální počítače vytvořené pomocí modelu nasazení Classic místo Azure Resource Manager se automaticky ukládají do cloudové služby. Do této cloudové služby můžete přidat více virtuálních počítačů pro vyrovnávání zatížení a dostupnost. 

<sup>2</sup> . Vstupní koncové body umožňují komunikaci s virtuálním počítačem mimo cloudovou službu virtuálního počítače. Virtuální počítače ve stejné cloudové službě nebo virtuální síti můžou vzájemně komunikovat automaticky. Další informace najdete v tématu [nastavení koncových bodů na virtuální počítač](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
