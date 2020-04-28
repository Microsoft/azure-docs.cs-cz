---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174960"
---
Je důležité si uvědomit, že existují dva způsoby konfigurace naslouchacího procesu skupiny dostupnosti v Azure. Způsoby se liší v typu nástroje pro vyrovnávání zatížení Azure, který používáte při vytváření naslouchacího procesu. Rozdíly jsou popsány v následující tabulce:

| Typ nástroje pro vyrovnávání zatížení | Implementace | Použijte, když: |
| --- | --- | --- |
| **Zahraničních** |Používá *veřejnou virtuální IP adresu* cloudové služby, která hostuje virtuální počítače (VM). |Musíte získat přístup k naslouchacímu procesu mimo virtuální síť, včetně z Internetu. |
| **Vnitřních** |Používá *interní nástroj pro vyrovnávání zatížení* s privátní adresou pro naslouchací proces. |K naslouchacího procesu můžete přistupovat jenom ze stejné virtuální sítě. Tento přístup zahrnuje síť Site-to-Site VPN v hybridních scénářích. |

> [!IMPORTANT]
> Pro naslouchací proces, který používá veřejnou virtuální IP adresu cloudové služby (externí nástroj pro vyrovnávání zatížení), pokud je klient, naslouchací proces a databáze ve stejné oblasti Azure, nebudete mít poplatky za odchozí data. Jinak se veškerá data vrácená naslouchacím procesem považují za výstup a účtují se za běžné sazby za přenos dat. 
> 
> 

INTERNÍHO nástroje se dá nakonfigurovat jenom v rámci virtuálních sítí s regionálním rozsahem. Existující virtuální sítě, které jsou nakonfigurované pro skupinu vztahů, nemůžou používat interního nástroje. Další informace najdete v tématu [Přehled interního nástroje pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internal-overview.md).

