---
title: Přehled zásad Azure Firewall Manager U verze Preview
description: Informace o zásadách Správce azure firewall
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77445018"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Přehled zásad Azure Firewall Manager U verze Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Zásada brány firewall je prostředek Azure, který obsahuje kolekce pravidel NAT, sítě a aplikace a také nastavení Threat Intelligence. Jedná se o globální prostředek, který lze použít ve více instancích Azure Firewall v zabezpečených virtuálních rozbočovačích a virtuálních sítích rozbočovače. Zásady fungují napříč oblastmi a předplatnými.

![Zásady Správce azure firewall](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Vytváření a přidružení zásad

Zásady lze vytvořit a spravovat několika způsoby, včetně portálu Azure, rozhraní REST API, šablon, Azure PowerShellu a rozhraní příkazového příkazového příkazu.

Můžete také migrovat existující pravidla z Azure Firewall pomocí portálu nebo Azure PowerShell u vytvořit zásady. Další informace najdete v tématu [Jak migrovat konfigurace Azure Firewall do zásad Azure Firewall (preview).](migrate-to-policy.md) 

Zásady lze přidružit k jednomu nebo více virtuálních rozbočovačů nebo virtuálních sítí. Brána firewall může být v libovolném předplatném přidruženém k vašemu účtu a v libovolné oblasti.

## <a name="hierarchical-policies"></a>Hierarchické zásady

Nové zásady lze vytvořit od začátku nebo zděděné z existujících zásad. Dědičnost umožňuje devOps vytvořit místní firewall zásady nad organizací nařízené základní zásady.

Zásady vytvořené pomocí neprázdných nadřazených zásad dědí všechny kolekce pravidel z nadřazené zásady. Kolekce síťových pravidel zděděné z nadřazené zásady jsou vždy upřednostněny nad kolekcemi síťových pravidel definovanými jako součást nové zásady. Stejná logika platí také pro kolekce pravidel aplikace. Kolekce síťových pravidel jsou však vždy zpracovány před kolekcemi pravidel aplikace bez ohledu na dědičnost.

Režim Threat Intelligence je také zděděn z nadřazené zásady. Režim inteligence hrozeb můžete nastavit na jinou hodnotu, abyste toto chování přepsali, ale nemůžete ho vypnout. Je možné přepsat pouze s přísnější hodnotou. Pokud je například nadřazená zásada nastavena pouze na **možnost Výstrahy**, můžete tuto místní zásadu nakonfigurovat tak, aby **byla výstraha a odepření**.

Kolekce pravidel NAT nejsou zděděny, protože jsou specifické pro danou bránu firewall.

S dědičností jsou všechny změny nadřazené zásady automaticky použity pro přidružené podřízené zásady brány firewall.

## <a name="traditional-rules-and-policies"></a>Tradiční pravidla a politiky

Azure Firewall podporuje tradiční pravidla i zásady. Následující tabulka porovnává zásady a pravidla:


|         |Zásada  |Pravidla  |
|---------|---------|---------|
|Contains     |Nastavení NAT, sítě, pravidel aplikací a analýzy hrozeb|Pravidla NAT, sítě a aplikací |
|Chrání     |Virtuální rozbočovače a virtuální sítě|Pouze virtuální sítě|
|Prostředí portálu     |Centrální správa pomocí Správce brány firewall|Samostatné prostředí brány firewall|
|Podpora více bran firewall     |Zásady brány firewall jsou samostatným zdrojem prostředků, který lze použít v rámci brány firewall.|Ručně exportovat a importovat pravidla nebo pomocí řešení pro správu třetích stran |
|Ceny     |Fakturováno na základě přidružení brány firewall. Viz [Ceny](#pricing).|Free|
|Podporované mechanismy nasazení     |Portál, rozhraní REST API, šablony, Azure PowerShell a ROZHRANÍ PŘÍKAZOVÉHO PŘÍKAZU|Portál, rozhraní REST API, šablony, PowerShell a CLI. |
|Stav vydání     |Veřejná verze Preview|Obecná dostupnost|

## <a name="pricing"></a>Ceny

Zásady se účtují na základě přidružení brány firewall. Zásady s nulovým nebo jedním přidružením brány firewall jsou zdarma. Zásada s více přidruženími brány firewall se účtuje pevnou sazbou. Další informace najdete v tématu [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Další kroky

Informace o nasazení brány Azure Firewall najdete [v tématu Kurz: Zabezpečení cloudové sítě pomocí Azure Firewall Manager Preview pomocí portálu Azure Portal](secure-cloud-network.md).
