---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335016"
---
| Prostředek | Omezení |
| --- | --- |
| [Virtuální počítače](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na cloudovou službu<sup>1</sup> |50 |
| Vstupní koncové body na cloudovou službu<sup>2</sup> |150 |

<sup>1.</sup> Virtuální počítače vytvořené pomocí klasického modelu nasazení namísto Azure Resource Manager ukládají se automaticky v cloudové službě. Do této cloudové služby můžete přidat další virtuální počítače pro vyrovnávání zatížení a dostupnost. 

<sup>2.</sup> Vstupní koncové body umožňují komunikaci s virtuálním počítačem mimo cloudovou službu virtuálního počítače. Virtuální počítače ve stejné cloudové službě nebo virtuální síti mohou automaticky komunikovat mezi sebou. Další informace najdete v tématu [Jak nastavit koncové body pro virtuální počítač](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
