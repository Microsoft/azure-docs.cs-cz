---
title: Zabezpečený přístup k účtům Azure Cosmos DB pomocí koncového bodu služby virtuální sítě
description: Tento dokument popisuje informace o virtuální síti a řízení přístupu k podsíti pro účet Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 10f2b44d00361885778a523109a8fcb05dabe9dd
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574343"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Přístup ke službě Azure Cosmos DB z virtuálních sítí

Účet Azure Cosmos můžete nakonfigurovat tak, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Povolením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro přístup k Azure Cosmos DB v podsíti ve virtuální síti se přenos z této podsítě pošle Azure Cosmos DB s identitou podsítě a Virtual Network. Po povolení koncového bodu služby Azure Cosmos DB můžete omezit přístup k podsíti tím, že ji přidáte do svého účtu Azure Cosmos.

Ve výchozím nastavení je účet Azure Cosmos přístupný z libovolného zdroje, pokud je žádost doprovázena platným autorizačním tokenem. Když do virtuální sítě přidáte jednu nebo víc podsítí, budou se v rámci požadavků, které pocházejí z těchto podsítí, získat platná odpověď. Žádosti pocházející z jakéhokoliv jiného zdroje obdrží odpověď 403 (zakázáno). 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tady jsou některé nejčastější dotazy týkající se konfigurace přístupu z virtuálních sítí:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Jsou poznámkové bloky a prostředí Mongo/Cassandra aktuálně kompatibilní s účty s povoleným Virtual Network?

V současné době se [Mongo prostředí PowerShellu](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) a [prostředí Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) ve službě Cosmos DB Průzkumník dat a [Služba Jupyter poznámkových bloků](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks)nepodporuje s přístupem VNET. Toto je aktuálně aktivní vývoj.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Můžu v účtu Azure Cosmos zadat jak koncový bod služby virtuální sítě, tak i zásady řízení přístupu IP? 

V účtu Azure Cosmos můžete povolit koncový bod služby virtuální sítě a zásadu řízení přístupu IP (neboli bránu firewall). Tyto dvě funkce jsou doplňující a společně zajišťují izolaci a zabezpečení účtu Azure Cosmos. Použití brány firewall protokolu IP zajišťuje, že ke svému účtu budou mít přístup statické IP adresy. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Návody omezit přístup k podsíti v rámci virtuální sítě? 

K omezení přístupu k účtu Azure Cosmos z podsítě se vyžadují dva kroky. Nejdřív povolíte provoz z podsítě, aby se mohla Azure Cosmos DBa identita vaší podsítě a virtuální sítě. To se provádí povolením koncového bodu služby pro Azure Cosmos DB v podsíti. V dalším kroku se přidá pravidlo do účtu Azure Cosmos, který určuje tuto podsíť jako zdroj, ze kterého se dá dostat k účtu.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Budou seznamy řízení přístupu k virtuální síti a žádosti o připojení brány firewall protokolu IP odmítnuty? 

Když se přidají pravidla přístupu k bránám firewall nebo k virtuální síti, žádosti z povolených zdrojů získají platné odpovědi. Jiné požadavky se odmítnou s 403 (zakázáno). Bránu firewall účtu Azure Cosmos je důležité odlišit od brány firewall na úrovni připojení. Zdroj se stále může ke službě připojit a samotné připojení se neodmítlo.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Když se povolí koncový bod služby Azure Cosmos DB v podsíti, začaly se zablokovat moje žádosti. Co se stalo?

Jakmile je koncový bod služby pro Azure Cosmos DB v podsíti povolený, zdroj provozu, který přiblíží účtu, se přepne z veřejné IP adresy do virtuální sítě a podsítě. Pokud má váš účet Azure Cosmos jenom bránu firewall založenou na protokolu IP, provoz z podsítě s povolenými službami už nebude odpovídat pravidlům brány firewall protokolu IP a proto se odmítne. Přečtěte si postup plynule migrace z brány firewall založené na protokolu IP na řízení přístupu na základě virtuální sítě.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Jsou pro účty Azure Cosmos s koncovými body služby virtuální sítě potřeba další oprávnění RBAC?

Po přidání koncových bodů služby virtuální sítě do účtu Azure Cosmos budete potřebovat přístup k `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` akci pro všechny virtuální sítě nakonfigurované na vašem účtu Azure Cosmos. Toto oprávnění je povinné, protože proces autorizace ověřuje přístup k prostředkům (například k prostředkům databáze a virtuální sítě) před vyhodnocením jakýchkoli vlastností.
 
Autorizace ověřuje oprávnění prostředku virtuální sítě i v případě, že uživatel nezadá seznamy ACL virtuální sítě pomocí rozhraní příkazového řádku Azure CLI. V současné době řídicí plocha účtu Azure Cosmos podporuje nastavení kompletního stavu účtu Azure Cosmos. Jeden z parametrů pro volání řídicí roviny je `virtualNetworkRules` . Pokud tento parametr nezadáte, Azure CLI vytvoří volání metody Get Database, které načte `virtualNetworkRules` a použije tuto hodnotu v volání aktualizace.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Mají partnerské virtuální sítě taky přístup k účtu Azure Cosmos? 
Jenom virtuální síť a jejich podsítě přidávané k účtu Azure Cosmos mají přístup. Jejich partnerský virtuální sítě nemá přístup k účtu, dokud nebudou do účtu přidány podsítě v rámci partnerských virtuálních sítí.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaký je maximální počet podsítí s povoleným přístupem k jednomu účtu Cosmos? 
V současné době můžete mít pro účet Azure Cosmos povolený maximálně 256 podsítí.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Můžu povolit přístup z sítě VPN a Express Route? 
Pro přístup k účtu Azure Cosmos přes Express Route z místního prostředí budete muset povolit partnerské vztahy Microsoftu. Po umístění brány firewall protokolu IP nebo pravidla přístupu k virtuální síti můžete přidat veřejné IP adresy používané pro partnerský vztah Microsoftu na svém účtu brány firewall Azure Cosmos, aby místní služby mohly přistupovat k účtu Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Potřebuji aktualizovat pravidla skupin zabezpečení sítě (NSG)? 
Pravidla NSG se používají k omezení připojení k podsíti s virtuální sítí a z ní. Když do podsítě přidáte koncový bod služby pro Azure Cosmos DB, není nutné otevírat odchozí připojení v NSG pro váš účet Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Jsou k dispozici koncové body služby pro všechny virtuální sítě?
Ne, u koncového bodu služby můžou být povolené jenom Azure Resource Manager virtuální sítě. Klasické virtuální sítě nepodporují koncové body služby.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Můžu přijmout připojení z veřejných datacenter Azure, když je povolený koncový bod služby Access pro Azure Cosmos DB?  
To se vyžaduje jenom v případě, že chcete, aby k účtu Azure Cosmos DB přistupovaly jiné služby Azure, jako je Azure Data Factory, Azure Kognitivní hledání nebo jakákoli služba nasazená v dané oblasti Azure.


## <a name="next-steps"></a>Další kroky

* [Omezení přístupu účtu Azure Cosmos k podsítím v rámci virtuálních sítí](how-to-configure-vnet-service-endpoint.md)
* [Jak nakonfigurovat bránu firewall protokolu IP pro účet Azure Cosmos](how-to-configure-firewall.md)

