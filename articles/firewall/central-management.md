---
title: Azure Firewall centrální Správa
description: Další informace o centrální správě Azure Firewall Manageru
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084666"
---
# <a name="azure-firewall-central-management"></a>Azure Firewall centrální Správa

Pokud spravujete více bran firewall, víte, že neustále se měnící pravidla brány firewall obtížně udržují, aby byla synchronizovaná. Centrální týmy IT potřebují způsob, jak definovat základní zásady brány firewall a vymáhat je napříč různými obchodními jednotkami. Týmy DevOps ve stejnou chvíli chtějí vytvořit své vlastní místní odvozené zásady brány firewall pro lepší flexibilitu.

Azure Firewall Manager vám může tyto problémy vyřešit.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager je služba pro správu zabezpečení sítě, která poskytuje centrální zásady zabezpečení a správu směrování pro hraniční zabezpečení na základě cloudu. Podnikoví IT týmy usnadňují centrálně definování pravidel na úrovni sítě a aplikací pro filtrování přenosů napříč několika instancemi Azure Firewall. Pro zásady správného řízení provozu a ochrany můžete v architektuře hub a paprsků zasahovat do různých oblastí a předplatných Azure. Poskytuje taky DevOps lepší flexibilitu s odvozenými zásadami zabezpečení místní brány firewall, které jsou implementované v organizacích.

### <a name="firewall-policy"></a>Zásady brány firewall

Zásada brány firewall je prostředek Azure, který obsahuje kolekce pravidel NAT, síť a aplikace a nastavení analýzy hrozeb. Jedná se o globální prostředek, který se dá použít napříč několika Azure Firewall instancemi v *zabezpečených virtuálních rozbočovačích* a *virtuálních sítích rozbočovačů*. Nové zásady je možné vytvářet od začátku nebo zděděné z existujících zásad. Dědičnost umožňuje DevOps vytvářet místní zásady brány firewall nad základními zásadami pověřenými organizací. Zásady fungují napříč oblastmi a předplatnými.
 
Pomocí nástroje Azure Firewall Manager můžete vytvořit zásady a přidružení brány firewall. Můžete ale také vytvořit a spravovat zásady pomocí REST API, šablon, Azure PowerShell a CLI. Jakmile zásadu vytvoříte, můžete ji přidružit k bráně firewall ve virtuálním rozbočovači WAN a vytvořit tak *zabezpečený virtuální rozbočovač* a/nebo bránu firewall ve virtuální síti, která *Virtual Network centrum* IT.

### <a name="pricing"></a>Ceny

Zásady se účtují na základě přidružení brány firewall. Zásada s žádným nebo jedním přidružením brány firewall je bezplatná. Zásada s více přidruženími brány firewall se účtuje pevnou sazbou. Další informace najdete v tématu [ceny Azure firewall Manageru](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Azure Firewall partneři pro správu

Následující špičková řešení třetích stran podporují Azure Firewall centrální správu pomocí standardních rozhraní Azure REST API. Každé z těchto řešení má vlastní jedinečné charakteristiky a funkce:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security stráže](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Další kroky

Další informace o Azure Firewall Manageru najdete v tématu [co je Azure firewall Manager?](../firewall-manager/overview.md) .