---
title: Co je delegování podsítě ve virtuální síti Azure?
description: Informace o delegování podsítě ve virtuální síti Azure
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
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281334"
---
# <a name="what-is-subnet-delegation"></a>Co je delegování podsítě?

Delegování podsítě umožňuje určit konkrétní podsíť pro službu Azure PaaS podle vašeho výběru, kterou je potřeba vložit do vaší virtuální sítě. Delegování podsítě poskytuje plnou kontrolu nad zákazníkem při správě integrace služeb Azure do svých virtuálních sítí.

Když delegujete podsíť na službu Azure, povolíte této službě vytvořit některá základní pravidla konfigurace sítě pro tuto podsíť, která službě Azure pomáhají provozovat jejich instance stabilním způsobem. V důsledku toho může služba Azure vytvořit některé podmínky před nebo po nasazení, například:
- nasadit službu ve sdílené versus vyhrazené podsíti.
- přidejte do služby sadu zásad záměru sítě po nasazení, která je vyžadována pro správnou funkci služby.

##  <a name="advantages-of-subnet-delegation"></a>Výhody delegování podsítě

Delegování podsítě na konkrétní služby poskytuje následující výhody:

- pomáhá určit podsíť pro jednu nebo více služeb Azure a spravovat instance v podsíti podle požadavků. Vlastník virtuální sítě může například definovat následující pro delegovanou podsíť pro lepší správu prostředků a přístup následujícím způsobem:
    - zásady přenosu pomocí skupin zabezpečení sítě.
    - zásady směrování s uživatelem definovanými trasami.
    - integrace služeb s konfiguracemi koncových bodů služby.
- pomáhá vstřikované služby lépe integrovat s virtuální sítí definováním jejich pre-podmínky nasazení ve formě zásad záměru sítě. Tím je zajištěno, že všechny akce, které mohou ovlivnit fungování vstřikované služby může být blokován na PUT.


## <a name="who-can-delegate"></a>Kdo může delegovat?
Delegování podsítě je cvičení, které vlastníci virtuální sítě musí provést k určení jedné z podsítí pro konkrétní službu Azure. Služba Azure zase nasazuje instance do této podsítě pro využití zatížení zákazníka.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Dopad delegování podsítě na podsíť
Každá služba Azure definuje svůj vlastní model nasazení, kde mohou definovat, jaké vlastnosti mají nebo nepodporují v delegované podsíti pro účely vkládání, například:
- sdílená podsíť s jinými službami Azure Services nebo škálovací sadou virtuálních zařízení / virtuálních strojů ve stejné podsíti nebo podporuje jenom vyhrazenou podsíť s pouze instancemi této služby.
- podporuje přidružení skupiny nsg k delegované podsíti.
- podporuje skupiny nsg přidružené k delegované podsíti lze také přidružit k jakékoli jiné podsíti.
- umožňuje přidružení směrovací tabulky k delegované podsíti.
- umožňuje přidružení směrovací tabulky k delegované podsíti k jakékoli jiné podsíti.
- určuje minimální počet adres IP v delegované podsíti.
- určuje, že prostor IP adresy v delegované podsíti pochází z privátního adresního prostoru IP (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- určuje, že vlastní konfigurace DNS má položku Azure DNS.

Injected služby můžete také přidat své vlastní zásady takto:
- **Zásady zabezpečení**: Kolekce pravidel zabezpečení požadovaných pro danou službu pracovat.
- **Zásady postupu**: Kolekce tras požadovaných pro danou službu.

## <a name="what-subnet-delegation-does-not-do"></a>Co delegování podsítě neprovádí

Služby Azure, které se vkládají do delegované podsítě, mají stále základní sadu vlastností, které jsou k dispozici pro nedelegované podsítě, například:
-  Služby Azure můžete vložit instance do podsítí zákazníků, ale nemůže ovlivnit existující úlohy.
-  Zásady nebo trasy, které tyto služby platí, jsou flexibilní a mohou být přepsány zákazníkem.

## <a name="next-steps"></a>Další kroky

- [Delegování podsítě](manage-subnet-delegation.md)
