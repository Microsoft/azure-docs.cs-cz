---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335016"
---
| Prostředek | Omezení |
| --- | --- |
| [Virtuální počítače](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na cloudovou službu<sup>1</sup> |50 |
| Vstupní koncové body na cloudové služby<sup>2</sup> |150 |

<sup>1</sup> Virtuální počítače vytvořené pomocí modelu nasazení Classic místo Azure Resource Manager se automaticky ukládají do cloudové služby. Do této cloudové služby můžete přidat více virtuálních počítačů pro vyrovnávání zatížení a dostupnost. 

<sup>2</sup> . Vstupní koncové body umožňují komunikaci s virtuálním počítačem mimo cloudovou službu virtuálního počítače. Virtuální počítače ve stejné cloudové službě nebo virtuální síti můžou vzájemně komunikovat automaticky. Další informace najdete v tématu [nastavení koncových bodů na virtuální počítač](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
