---
title: Centrální správa Azure Firewall
description: Informace o centrální správě Azure Firewall Manageru
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444547"
---
# <a name="azure-firewall-central-management"></a>Centrální správa Azure Firewall

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Pokud spravujete více bran firewall, víte, že neustále se měnící pravidla brány firewall ztěžují jejich synchronizaci. Centrální IT týmy potřebují způsob, jak definovat základní zásady brány firewall a vynucovat je napříč více organizačními jednotkami. Současně devOps týmy chcete vytvořit vlastní místní odvozené zásady brány firewall pro lepší agilitu.

Azure Firewall Manager Preview může pomoci vyřešit tyto problémy.


## <a name="azure-firewall-manager-preview"></a>Azure Firewall Manager Preview

Azure Firewall Manager Preview je služba správy zabezpečení sítě, která poskytuje centrální zásady zabezpečení a správu tras pro obvody cloudového zabezpečení. Umožňuje podnikovým IT týmům centralně definovat pravidla na úrovni sítě a aplikací pro filtrování provozu ve více instancích Azure Firewall. Můžete span různých oblastí Azure a předplatných v rozbočovači a paprsku architektury pro řízení provozu a ochranu. Poskytuje také DevOps lepší flexibilitu s odvozené místní firewall zásady zabezpečení, které jsou implementovány napříč organizacemi.

### <a name="firewall-policy"></a>Zásady brány firewall

Zásada brány firewall je prostředek Azure, který obsahuje kolekce pravidel NAT, sítě a aplikace a nastavení threat intelligence. Jedná se o globální prostředek, který lze použít ve více instancích Azure Firewall v *zabezpečených virtuálních rozbočovačích* a *virtuálních sítích hubů*. Nové zásady lze vytvořit od začátku nebo zděděné z existujících zásad. Dědičnost umožňuje devOps vytvořit místní firewall zásady nad organizací nařízené základní zásady. Zásady fungují napříč oblastmi a předplatnými.
 
Zásady brány firewall a přidružení můžete vytvořit pomocí Správce brány Azure Firewall. Můžete však také vytvořit a spravovat zásady pomocí rozhraní REST API, šablony, Azure PowerShell a CLI. Jakmile vytvoříte zásadu, můžete ji přidružit k bráně firewall ve virtuálním rozbočovači WAN, což z ní činí *zabezpečené virtuální rozbočovač* a/nebo bránu firewall ve virtuální síti, což z ní činí *virtuální síť hubů*.

### <a name="pricing"></a>Ceny

Zásady se účtují na základě přidružení brány firewall. Zásady s nulovým nebo jedním přidružením brány firewall jsou zdarma. Zásada s více přidruženími brány firewall se účtuje pevnou sazbou. Další informace najdete v tématu [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Partneři pro správu Azure Firewall

Následující přední řešení třetích stran podporují centrální správu Azure Firewall pomocí standardních api Azure REST. Každé z těchto řešení má své vlastní jedinečné vlastnosti a vlastnosti:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Strážce zabezpečení cloudu Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Další kroky

Další informace o Azure Firewall Manager Preview najdete v tématu [Co je Azure Firewall Manager Preview?](../firewall-manager/overview.md)