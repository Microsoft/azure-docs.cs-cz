---
title: Co je delegování podsítě ve službě Azure Virtual Network?
description: Další informace o delegování podsítě ve službě Azure Virtual Network
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: 1fbb683754aed5b2a2e6e9c022713b7e87ad9ba9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329203"
---
# <a name="what-is-subnet-delegation"></a>Co je delegování podsítě?

Delegování podsítě umožňuje určit konkrétní podsíť pro službu Azure PaaS podle vašeho výběru, kterou je potřeba vložit do vaší virtuální sítě. Delegování podsítě poskytuje zákazníkům plnou kontrolu nad správou integrace služeb Azure do jejich virtuálních sítí.

Při delegování podsítě na službu Azure povolíte, aby tato služba navázala některá základní pravidla konfigurace sítě pro tuto podsíť, což pomůže stabilní službě Azure provozovat jejich instance stabilním způsobem. V důsledku toho může služba Azure zřídit některé podmínky před nebo po nasazení, jako například:
- Nasaďte službu ve sdílené a vyhrazené podsíti.
- Přidejte do služby sadu zásad záměru sítě po nasazení, které jsou potřebné ke správnému fungování služby.

##  <a name="advantages-of-subnet-delegation"></a>Výhody delegování podsítí

Delegování podsítě na konkrétní služby přináší následující výhody:

- pomáhá určit podsíť pro jednu nebo více služeb Azure a spravovat instance v podsíti podle požadavků. Vlastník virtuální sítě může například definovat následující pro delegovanou podsíť pro lepší správu prostředků a přístupu následujícím způsobem:
    - zásady provozu filtrování sítě se skupinami zabezpečení sítě.
    - zásady směrování s uživatelsky definovanými trasami.
    - integrace služeb s konfiguracemi koncových bodů služby
- pomáhá vloženým službám lepší integraci s virtuální sítí, a to definováním jejich předběžných podmínek nasazení v podobě zásad záměrů sítě. Tím se zajistí, že všechny akce, které můžou ovlivnit fungování vložené služby, se můžou zablokovat v umístění.


## <a name="who-can-delegate"></a>Kdo může delegovat?
Delegování podsítě je cvičení, že vlastníci virtuální sítě potřebují provést k určení jedné z podsítí pro určitou službu Azure. Služba Azure v nástroji následně nasadí instance do této podsítě, aby je mohla spotřebovat pracovní zatížení.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Dopad delegování podsítě na vaši podsíť
Každá služba Azure definuje svůj vlastní model nasazení, kde může definovat, jaké vlastnosti mají nebo nepodporují v delegované podsíti pro účely injektáže, například následující:
- sdílená podsíť s dalšími službami Azure nebo sadou škálování virtuálních počítačů/virtuálních počítačů ve stejné podsíti, nebo podporuje jenom vyhrazenou podsíť s pouze instancemi této služby.
- podporuje přidružení NSG k delegované podsíti.
- Podpora NSG přidružených k delegované podsíti se dá taky přidružit k jakékoli jiné podsíti.
- umožňuje přidružení směrovací tabulky k delegované podsíti.
- povolí přidružení směrovací tabulky přidružené k delegované podsíti k jakékoli jiné podsíti.
- Určuje minimální počet IP adres v delegované podsíti.
- Určuje adresní prostor IP adres v delegované podsíti, který bude z privátního adresního prostoru IP adres (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- Určuje, že vlastní konfigurace DNS má položku Azure DNS.
- vyžaduje odebrání delegování, aby bylo možné odstranit podsíť nebo virtuální síť.

Vložené služby mohou také přidat vlastní zásady následujícím způsobem:
- **Zásady zabezpečení**: kolekce pravidel zabezpečení potřebných pro fungování dané služby.
- **Zásady směrování**: kolekce tras potřebných pro fungování dané služby.

## <a name="what-subnet-delegation-does-not-do"></a>Co delegování podsítě nedělá

Služby Azure vložené do delegované podsítě mají stále základní sadu vlastností, které jsou k dispozici pro nedelegované podsítě, například:
-  Služby Azure můžou vkládat instance do podsítí zákazníků, ale nemůžou mít vliv na stávající úlohy.
-  Zásady nebo trasy, které tyto služby používají, jsou flexibilní a můžou je přepsat zákazníkem.

## <a name="next-steps"></a>Další kroky

- [Delegování podsítě](manage-subnet-delegation.md)
