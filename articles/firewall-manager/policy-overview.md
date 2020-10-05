---
title: Přehled zásad Azure Firewall Manageru
description: Další informace o zásadách Azure Firewall Manageru
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 34134f2c790851d34db7b5327aa76350d54d137d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89075459"
---
# <a name="azure-firewall-manager-policy-overview"></a>Přehled zásad Azure Firewall Manageru

Zásada brány firewall je prostředek Azure, který obsahuje kolekce pravidel NAT, síť a aplikace a také nastavení analýzy hrozeb. Jedná se o globální prostředek, který se dá použít napříč několika Azure Firewall instancemi v zabezpečených virtuálních rozbočovačích a virtuálních sítích rozbočovačů. Zásady fungují napříč oblastmi a předplatnými.

![Zásady Azure Firewall Manageru](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Vytvoření a přidružení zásad

Zásady se dají vytvářet a spravovat několika způsoby, včetně Azure Portal, REST API, šablon, Azure PowerShell a CLI.

Stávající pravidla můžete také migrovat z Azure Firewall pomocí portálu nebo Azure PowerShell vytvořit zásady. Další informace najdete v tématu [migrace Azure firewall konfigurací na zásady Azure firewall](migrate-to-policy.md). 

Zásady je možné přidružit k jednomu nebo několika virtuálním rozbočovačům nebo virtuální sítě. Brána firewall může být v jakémkoli předplatném, které je přidružené k vašemu účtu a v jakékoli oblasti.

## <a name="hierarchical-policies"></a>Hierarchické zásady

Nové zásady je možné vytvářet od začátku nebo zděděné z existujících zásad. Dědičnost umožňuje DevOps vytvářet místní zásady brány firewall nad základními zásadami pověřenými organizací.

Zásady vytvořené pomocí neprázdných nadřazených zásad dědí všechny kolekce pravidel z nadřazené zásady. Kolekce pravidel sítě zděděné z nadřazené zásady mají vždycky přednost před kolekcemi síťových pravidel, které jsou definované jako součást nové zásady. Stejná logika platí také pro kolekce pravidel aplikace. Kolekce pravidel sítě se ale vždycky zpracovávají před kolekcemi pravidel aplikace bez ohledu na dědičnost.

Režim analýzy hrozeb se taky dědí z nadřazených zásad. Můžete nastavit režim analýzy hrozeb na jinou hodnotu, abyste toto chování přepsali, ale nemůžete ho vypnout. Je možné ji přepsat pouze přísnější hodnotou. Pokud je vaše nadřazená zásada například nastavená na **výstrahu**, můžete tuto místní zásadu nakonfigurovat na **Alert a odepřít**.

Podobně jako režim analýzy hrozeb je seznam povolených povolení analýzy hrozeb zděděný z nadřazené zásady. Podřízená zásada může do seznamu povolených adres přidat další IP adresy.

Kolekce pravidel NAT nejsou děděny, protože jsou specifické pro danou bránu firewall.

Díky dědičnosti jsou všechny změny nadřazené zásady automaticky aplikovány na související zásady přidružené brány firewall.

## <a name="traditional-rules-and-policies"></a>Tradiční pravidla a zásady

Azure Firewall podporuje tradiční pravidla a zásady. V následující tabulce jsou porovnávány zásady a pravidla:


| Předmět | Zásady  | Pravidla |
| ------- | ------- | ----- |
|Contains     |NAT, síť, pravidla použití, vlastní nastavení DNS a DNS, skupiny IP a nastavení analýzy hrozeb (včetně seznamu povolených)|Pravidla pro překlad adres (NAT), síť a aplikace, vlastní nastavení DNS a DNS, skupiny IP adres a nastavení analýzy hrozeb (včetně seznamu povolených)|
|Proti     |Virtuální rozbočovače a virtuální sítě|Pouze virtuální sítě|
|Prostředí portálu     |Centrální správa pomocí Správce brány firewall|Samostatné prostředí brány firewall|
|Podpora více bran firewall     |Zásady brány firewall jsou samostatný prostředek, který se dá použít napříč branami firewall.|Ruční export a Import pravidel nebo použití řešení pro správu třetích stran |
|Ceny     |Účtuje se podle přidružení brány firewall. Podívejte se na [ceny](#pricing).|Free|
|Podporované mechanismy nasazení     |Portál, REST API, šablony, Azure PowerShell a CLI|Portál, REST API, šablony, PowerShell a rozhraní příkazového řádku. |

## <a name="pricing"></a>Ceny

Zásady se účtují na základě přidružení brány firewall. Zásada s žádným nebo jedním přidružením brány firewall je bezplatná. Zásada s více přidruženími brány firewall se účtuje pevnou sazbou. Další informace najdete v tématu [ceny Azure firewall Manageru](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nasadit Azure Firewall, najdete v tématu [kurz: zabezpečení cloudové sítě pomocí nástroje Azure firewall Manager pomocí Azure Portal](secure-cloud-network.md).
