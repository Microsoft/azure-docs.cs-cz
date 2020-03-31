---
title: Zabezpečený přístup k účtům Azure Cosmos DB pomocí koncového bodu virtuální síťové služby
description: Tento dokument popisuje o virtuální síti a řízení přístupu podsítě pro účet Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af1032de9aabac45ad7a86cfe1f36ed2c04c0f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444620"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Přístup ke službě Azure Cosmos DB z virtuálních sítí

Účet Azure Cosmos můžete nakonfigurovat tak, aby umožňoval přístup jenom z určité podsítě virtuální sítě (VNet). Povolením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro přístup k Azure Cosmos DB v podsíti v rámci virtuální sítě se provoz z této podsítě odesílá do Azure Cosmos DB s identitou podsítě a virtuální sítě. Jakmile je koncový bod služby Azure Cosmos DB povolen, můžete omezit přístup k podsíti přidáním do vašeho účtu Azure Cosmos.

Ve výchozím nastavení je účet Azure Cosmos přístupný z libovolného zdroje, pokud je požadavek doprovázen platným tokenem autorizace. Když přidáte jednu nebo více podsítí v rámci virtuálních sítí, pouze požadavky pocházející z těchto podsítí obdrží platnou odpověď. Požadavky pocházející z jiného zdroje obdrží odpověď 403 (Zakázáno). 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tady jsou některé nejčastější dotazy týkající se konfigurace přístupu z virtuálních sítí:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Můžu určit koncový bod virtuální sítě i zásady řízení přístupu k IP adresám na účtu Azure Cosmos? 

Můžete povolit koncový bod virtuální síťové služby a zásady řízení přístupu k IP (aka firewall) na vašem účtu Azure Cosmos. Tyto dvě funkce jsou doplňkové a společně zajistit izolaci a zabezpečení vašeho účtu Azure Cosmos. Použití brány firewall IP zajišťuje, že statické IP adresy mají přístup k vašemu účtu. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak omezím přístup k podsíti ve virtuální síti? 

Existují dva kroky potřebné k omezení přístupu k účtu Azure Cosmos z podsítě. Nejprve povolíte přenosy z podsítě přenášet jeho podsítě a identity virtuální sítě do Azure Cosmos DB. To se provádí povolením koncového bodu služby pro Azure Cosmos DB v podsíti. Další je přidání pravidla v účtu Azure Cosmos určující tuto podsíť jako zdroj, ze kterého je přístupk účtu.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Odmítnou seznamy AKL virtuální sítě a bránu FIREWALL IP požadavky nebo připojení? 

Při přidání brány firewall IP nebo pravidel přístupu k virtuální síti získají platné odpovědi pouze požadavky z povolených zdrojů. Ostatní požadavky jsou odmítnuty s 403 (Zakázáno). Je důležité rozlišovat bránu firewall účtu Azure Cosmos od brány firewall na úrovni připojení. Zdroj se stále může připojit ke službě a samotná připojení nejsou odmítnuta.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje požadavky začaly být blokovány, když jsem povolil koncový bod služby azure cosmos DB v podsíti. Co se stalo?

Jakmile je koncový bod služby pro Azure Cosmos DB povolen v podsíti, zdroj provozu, který dosáhne účtu, se přepne z veřejné IP adresy do virtuální sítě a podsítě. Pokud váš účet Azure Cosmos má jenom bránu firewall založenou na PROTOKOLU IP, provoz z podsítě s povolenou službou by již neodpovídal pravidlům brány firewall IP, a proto by byl odmítnut. Projděte si postup, jak plynule přejít z brány firewall založené na protokolu IP na řízení přístupu založené na virtuální síti.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Jsou další oprávnění RBAC potřebná pro účty Azure Cosmos s koncovými body služby Virtuální sítě?

Po přidání koncových bodů služby virtuální sítě do účtu Azure Cosmos, chcete-li `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` provést změny nastavení účtu, budete potřebovat přístup k akci pro všechny virtuální sítě nakonfigurované na vašem účtu Azure Cosmos. Toto oprávnění je vyžadováno, protože proces autorizace ověřuje přístup k prostředkům (například k prostředkům databáze a virtuální sítě) před vyhodnocením všech vlastností.
 
Autorizace ověří oprávnění pro akci prostředku virtuální sítě i v případě, že uživatel nezadává seznamy ACL virtuální sítě pomocí rozhraní příkazového příkazového příkazu Azure. V současné době rozhraní řízení účtu Azure Cosmos podporuje nastavení úplného stavu účtu Azure Cosmos. Jedním z parametrů volání roviny `virtualNetworkRules`ovládacího prvku je . Pokud tento parametr není zadán, Azure CLI provede volání `virtualNetworkRules` databáze get načte a použije tuto hodnotu ve volání aktualizace.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Mají partnerské virtuální sítě taky přístup k účtu Azure Cosmos? 
Přístup mají jenom virtuální síť a jejich podsítě přidané k účtu Azure Cosmos. Jejich partnerské virtuální sítě nemají přístup k účtu, dokud se k účtu nepřidají podsítě v partnerských virtuálních sítích.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaký je maximální počet podsítí povolených pro přístup k jednomu účtu Cosmos? 
V současné době můžete mít maximálně 64 podsítí povolených pro účet Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Mohu povolit přístup z VPN a Express Route? 
Pro přístup k účtu Azure Cosmos přes express trasy z místních, budete muset povolit partnerský vztah Microsoftu. Jakmile vložíte ip bránu firewall nebo pravidla přístupu k virtuální síti, můžete přidat veřejné IP adresy používané pro partnerský vztah Microsoftu na ip firewall účtu Azure Cosmos, abyste místním službám povolili přístup k účtu Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Musím aktualizovat pravidla skupin zabezpečení sítě (NSG)? 
Pravidla skupiny zabezpečení sítě se používají k omezení připojení k podsíti a z podsítě s virtuální sítí. Když přidáte koncový bod služby pro Azure Cosmos DB do podsítě, není potřeba otevírat odchozí připojení v nsg pro váš účet Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Jsou koncové body služby dostupné pro všechny virtuální sítě?
Ne, pouze virtuální sítě Azure Resource Manager umějí mít povolený koncový bod služby. Klasické virtuální sítě nepodporují koncové body služby.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Můžu "Přijímat připojení z veřejných datových center Azure", když je přístup ke koncovému bodu služby povolený pro Azure Cosmos DB?  
To se vyžaduje jenom v případě, že chcete, aby k vašemu účtu Azure Cosmos DB měly přístup jiné služby první strany Azure, jako je Azure Data Factory, Azure Cognitive Search nebo jakákoli služba nasazená v dané oblasti Azure.


## <a name="next-steps"></a>Další kroky

* [Jak omezit přístup k účtu Azure Cosmos na podsítě v rámci virtuálních sítí](how-to-configure-vnet-service-endpoint.md)
* [Jak nakonfigurovat bránu firewall IP pro váš účet Azure Cosmos](how-to-configure-firewall.md)

