---
title: Zabezpečený přístup k účtu služby Azure Cosmos DB s využitím koncového bodu služby virtuální sítě Azure
description: Tento dokument popisuje, o virtuální síť a podsíť řízení přístupu k účtu Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 25a05df42029fe444b8d5ceddb2972f779f1b232
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358724"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Přístup k prostředkům služby Azure Cosmos DB z virtuální sítě

Můžete nakonfigurovat účet Azure Cosmos, který chcete povolit přístup pouze z konkrétní podsítě virtuální sítě (VNET). Povolením [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro přístup ke službě Azure Cosmos DB na podsítě v rámci virtuální sítě, provoz z této podsítě ke službě Azure Cosmos DB se neposílají identity podsítě a virtuální sítě. Po povolení koncového bodu služby Azure Cosmos DB můžete omezit přístup k podsíti tak, že přidáte ke svému účtu Azure Cosmos.

Ve výchozím nastavení je přístupný z jakéhokoli zdroje účet služby Azure Cosmos, když žádosti je přiložený platný autorizační token. Při přidání jedné nebo několika podsítí v rámci virtuální sítě, se zobrazí pouze požadavky pocházejících z těchto podsítí platnou odpověď. Žádosti pocházející z jakéhokoli jiného zdroje přijetí odpovědi 403 (zakázáno). 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tady jsou některé nejčastější dotazy týkající se konfigurace přístupu z virtuální sítě:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Můžete zadat koncový bod služby virtuální sítě a zásad řízení přístupu IP v rámci účtu Azure Cosmos? 

Koncový bod služby virtuální sítě a zásad řízení přístupu IP (označuje se také jako brána firewall) můžete povolit na vašem účtu Azure Cosmos. Tyto dvě funkce se doplňují a souhrnně zajištění izolace a zabezpečení vašeho účtu Azure Cosmos. Pomocí IP adresy brány firewall zajistí, že statické IP adresy můžete přístup k vašemu účtu. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak můžu omezit přístup k podsíti ve virtuální síti? 

Existují dva kroky potřebné k omezení přístupu k účtu Azure Cosmos z podsítě. Nejprve je povolený provoz z podsítě provádět její podsítě a identitu virtuální sítě ke službě Azure Cosmos DB. Je to tím, že koncový bod služby pro službu Azure Cosmos DB v podsíti. Dále je přidání pravidla v účtu Azure Cosmos zadání této podsítě jako zdroj, ze kterého můžete přístupu k účtu.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Se seznamy ACL virtuální sítě a brány Firewall protokolu IP odmítnout žádosti o připojení nebo připojení? 

Při přístupu k brány firewall protokolu IP nebo virtuální sítě přidána pravidla, pouze žádosti z povolené zdroje get platné odpovědi. Další požadavky byly zamítnuty s 403 (zakázáno). Je důležité odlišení od připojení brány firewall na úrovni firewall účet Azure Cosmos. Zdroj může pořád připojit ke službě a nejsou odmítl připojení sami.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje žádosti o spuštění blokován, pokud mám povolený koncový bod služby Azure Cosmos DB v podsíti. Co se stalo?

Po povolení koncového bodu služby pro službu Azure Cosmos DB v podsíti zdroje přenosů, obraťte se účtu se přepne z veřejné IP adresy na virtuální síť a podsíť. Pokud má váš účet Azure Cosmos založené na protokolu IP brány firewall pouze provoz z podsítě povolenou službu už neodpovídá pravidla firewallu protokolu IP a proto zamítne. Projít kroky bezproblémově migrovat z založené na protokolu IP brány firewall pro řízení přístupu na virtuální síti.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Partnerské virtuální sítě také mají přístup k účtu Azure Cosmos? 
Mít přístup pouze virtuální sítě a podsítě přidán k účtu Azure Cosmos. Jejich partnerských virtuálních sítích nelze přístup k účtu, dokud nebudou přidány podsítě v rámci partnerských virtuálních sítích na účet.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Co je povolen maximální počet podsítí pro přístup k jednomu účtu Cosmos? 
V současné době může mít maximálně 64 podsítí povolené pro účet Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Můžete povolit přístup z VPN a Expressroute? 
Pro přístup k účtu Azure Cosmos přes Express route z místní, musíte povolit partnerský vztah Microsoftu. Po přepnutí brány firewall protokolu IP nebo pravidla přístupu k virtuální síti, můžete přidat veřejné IP adresy používané pro partnerský vztah Microsoftu na bráně firewall IP účtu Azure Cosmos umožňuje v místním prostředí služby přístup k účtu Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Je potřeba aktualizovat pravidla skupiny zabezpečení sítě (NSG)? 
Pravidla skupiny zabezpečení sítě umožňují omezit připojení do a z podsítě virtuální sítě. Když přidáte koncový bod služby pro službu Azure Cosmos DB k podsíti, není nutné otevřít odchozí připojení do skupiny zabezpečení sítě pro váš účet Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Jsou k dispozici koncové body služby pro všechny virtuální sítě?
Ne, virtuální sítě pouze Azure Resource Manageru můžete mít povolený koncový bod služby. Klasické virtuální sítě nepodporují koncových bodů služby.


## <a name="next-steps"></a>Další postup

* [Jak omezit přístup k účtu Azure Cosmos pro tento počet podsítí: v rámci virtuálních sítí](how-to-configure-vnet-service-endpoint.md)
* [Postup konfigurace brány firewall protokolu IP pro váš účet Azure Cosmos](how-to-configure-firewall.md)

