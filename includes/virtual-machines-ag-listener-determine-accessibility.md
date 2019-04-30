---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097655"
---
Je důležité si uvědomit, že existují dva způsoby, jak konfigurace naslouchacího procesu skupiny dostupnosti v Azure. Způsoby, kterými se liší v typu nástroje pro vyrovnávání zatížení Azure, které používáte při vytváření naslouchacího procesu. Následující tabulka popisuje rozdíly:

| Typ nástroje pro vyrovnávání zatížení | Implementace | Použijte, když: |
| --- | --- | --- |
| **Externí** |Používá *veřejnou virtuální IP adresu* cloudové služby, který je hostitelem virtuálního počítače (VM). |Potřebujete přístup k naslouchacího procesu od mimo virtuální síť, včetně z Internetu. |
| **Interní** |Používá *interního nástroje load balancer* s privátní adresou pro naslouchací proces. |Naslouchací proces pouze z v rámci stejné virtuální síti můžete přistupovat. Tento přístup zahrnuje site-to-site VPN v hybridních scénářích. |

> [!IMPORTANT]
> Pro naslouchací proces, který používá cloudovou službu veřejných virtuálních IP adres (externí nástroj pro vyrovnávání zatížení), pokud klient naslouchací proces a databáze jsou ve stejné oblasti Azure, vám nebudou účtovat žádné poplatky za odchozí přenos. V opačném případě žádná data vrácená přes naslouchací proces se považuje za výchozí přenos dat a se účtuje za běžné přenosu dat sazby. 
> 
> 

ILB lze nastavit pouze u virtuálních sítí s regionálním rozsahem. Existující virtuální sítě, které jsou nakonfigurované pro skupinu vztahů nejde použít ILB. Další informace najdete v tématu [interní služby load balancer – přehled](../articles/load-balancer/load-balancer-internal-overview.md).

<!-- Update_Description: update meta properties -->