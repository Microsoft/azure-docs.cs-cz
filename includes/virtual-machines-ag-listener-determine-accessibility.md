---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174960"
---
Je důležité si uvědomit, že existují dva způsoby konfigurace naslouchací proces skupiny dostupnosti v Azure. Způsoby se liší v typu Azure vyrovnávání zatížení, které používáte při vytváření naslouchací proces. Následující tabulka popisuje rozdíly:

| Typ pro vyrovnávání zatížení | Implementace | Použijte, když: |
| --- | --- | --- |
| **Externí** |Používá *veřejnou virtuální IP adresu* cloudové služby, která hostuje virtuální počítače (VMs). |Je třeba získat přístup k naslouchací proces z mimo virtuální síť, včetně z Internetu. |
| **Vnitřní** |Používá *interní vyrovnávání zatížení* s privátní adresu pro naslouchací proces. |K naslouchací procesu můžete přistupovat pouze ze stejné virtuální sítě. Tento přístup zahrnuje síť VPN mezi lokalitami v hybridních scénářích. |

> [!IMPORTANT]
> Pro naslouchací proces, který používá veřejné VIP cloudové služby (externí vyrovnávání zatížení), tak dlouho, dokud klient, naslouchací proces a databáze jsou ve stejné oblasti Azure, nebude vám vzniknout poplatky za odchozí přenos. V opačném případě všechna data vrácená prostřednictvím naslouchací proces je považován za odchozí a je účtován a normální přenos dat. 
> 
> 

ILB lze nakonfigurovat pouze ve virtuálních sítích s regionálním rozsahem. Existující virtuální sítě, které byly nakonfigurovány pro skupinu spřažení, nemohou používat službu ILB. Další informace naleznete [v tématu Interní přehled nástroje pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internal-overview.md).

