---
title: Koncové body služby virtuální sítě – Azure Database for MariaDB
description: Popisuje způsob fungování koncových bodů služby VNet pro server Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 54379c65850fa210e5523b53a64fe89705ed1f15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532106"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Použití koncových bodů služeb virtuální sítě a pravidel pro Azure Database for MariaDB

*Pravidla virtuální sítě* jsou jednou funkcí zabezpečení brány firewall, která určuje, jestli váš server Azure Database for MariaDB přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč je funkce pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s Azure Database for MariaDB serverem.

Aby bylo možné vytvořit pravidlo virtuální sítě, musí nejprve existovat [virtuální síť][vm-virtual-network-overview] (VNET) a [koncový bod služby virtuální sítě][vm-virtual-network-service-endpoints-overview-649d] pro pravidlo, které se má odkazovat. Následující obrázek ukazuje, jak koncový bod služby Virtual Network funguje s Azure Database for MariaDB:

![Příklad fungování koncového bodu služby virtuální sítě](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Tato funkce je k dispozici ve všech oblastech Azure, kde Azure Database for MariaDB nasazeny pro Pro obecné účely a paměťově optimalizované servery.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální síť:** Můžete mít virtuální sítě přidružené k vašemu předplatnému Azure.

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure, které jste přiřadili k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k virtuální síti, pokud nenastavíte zabezpečení tak, aby umožňovalo přístup.

**Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network][vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. V tomto článku se zajímá název typu **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro služby Azure Database for MariaDB, MySQL a PostgreSQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny Azure SQL Database, Azure Database for MariaDB, Azure Database for MySQL a Azure Database for PostgreSQL servery v podsíti.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro server Azure Database for MariaDB je podsíť, která je uvedená v seznamu řízení přístupu (ACL) vašeho serveru Azure Database for MariaDB. Aby byl v seznamu ACL pro váš Azure Database for MariaDB Server, podsíť musí obsahovat název typu **Microsoft. SQL** .

Pravidlo virtuální sítě přikáže serveru Azure Database for MariaDB, aby přijímal komunikaci z každého uzlu, který je v podsíti.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidla virtuální sítě

Dokud neprovedete akci, virtuální počítače v podsítích nebudou moct komunikovat se serverem Azure Database for MariaDB. Jedna akce, která stanovuje komunikaci, je vytvoření pravidla virtuální sítě. Odůvodnění výběru přístupu pravidla virtuální sítě vyžaduje diskuzi o porovnání a kontrastu zahrnující konkurenční možnosti zabezpečení nabízené bránou firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

Podokno zabezpečení připojení má tlačítko **pro zapnutí/vypnutí** , které je označeno jako **povolený přístup ke službám Azure**. Nastavení **on** umožňuje komunikaci ze všech IP adres Azure a všech podsítí Azure. Tyto IP adresy nebo podsítě Azure možná nevlastníte. Toto **Nastavení** je pravděpodobně více otevřené, než požadujete, aby byla databáze Azure Database for MariaDB. Funkce pravidla virtuální sítě nabízí mnohem přesnější kontrolu.

### <a name="b-ip-rules"></a>B. Pravidla protokolu IP

Brána Azure Database for MariaDB firewall umožňuje zadat rozsahy IP adres, ze kterých se na Azure Database for MariaDB serveru přijímají komunikace. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale mnoho uzlů v privátní síti Azure má nakonfigurovanou *dynamickou* IP adresu. Dynamické IP adresy se můžou změnit, třeba když se váš virtuální počítač restartuje. V provozním prostředí by se Folly zadat dynamickou IP adresu v pravidle brány firewall.

Možnost IP můžete vyřazením získat *statickou* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [Konfigurace privátních IP adres pro virtuální počítač pomocí Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Přístup ke statickým IP adresám se ale může obtížně spravovat a při velkém rozsahu je nákladný. Pravidla virtuální sítě je snazší vytvářet a spravovat.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. V podsíti se ještě nedá Azure Database for MariaDB bez definování koncového bodu služby.

Pokud byl váš **Microsoft. SQL** Server uzlem v podsíti ve vaší virtuální síti, můžou všechny uzly v rámci virtuální sítě komunikovat se serverem Azure Database for MariaDB. V takovém případě můžou vaše virtuální počítače komunikovat s Azure Database for MariaDB bez nutnosti používat pravidla virtuální sítě nebo pravidla protokolu IP.

Od srpna 2018 však služba Azure Database for MariaDB ještě nepatří mezi služby, které je možné přiřadit přímo do podsítě.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Jenom jedna geografická oblast

Každý koncový bod služby Virtual Network se vztahuje jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti.

Jakékoli pravidlo virtuální sítě je omezené na oblast, na kterou se vztahuje příslušný koncový bod.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, nikoli na úrovni databáze

Každé pravidlo virtuální sítě se vztahuje na celý Azure Database for MariaDB Server, nikoli jenom na jednu konkrétní databázi na serveru. Jinými slovy, pravidlo virtuální sítě se vztahuje na úrovni serveru, nikoli na úrovni databáze.

### <a name="security-administration-roles"></a>Role správy zabezpečení

V rámci správy koncových bodů služby Virtual Network je oddělení rolí zabezpečení. Pro každou z následujících rolí se vyžaduje akce:

- **Správce sítě:** &nbsp; zapněte koncový bod.
- **Správce databáze:** &nbsp; aktualizujte seznam řízení přístupu (ACL) pro přidání dané podsítě do serveru Azure Database for MariaDB.

*Alternativa RBAC:*

Role správce sítě a správce databáze mají více možností, než je nutné ke správě pravidel virtuální sítě. Je potřeba jenom podmnožina jejich schopností.

V Azure máte možnost použít [řízení přístupu na základě role (RBAC)][rbac-what-is-813s] a vytvořit jednu vlastní roli, která bude mít jenom nezbytnou podmnožinu funkcí. Vlastní roli můžete použít místo zapojení správce sítě nebo správce databáze. Pokud přidáte uživatele do vlastní role a přidáváte uživatele k ostatním dvěma hlavním rolím Správce, oblast Surface vašeho ohrožení zabezpečení je nižší.

> [!NOTE]
> V některých případech jsou Azure Database for MariaDB a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Oba odběry musí být ve stejném Azure Active Directory tenantovi.
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě do daného serveru.
> - Ujistěte se, že oba odběry mají registrovaného poskytovatele prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

## <a name="limitations"></a>Omezení

Pro Azure Database for MariaDB funkce pravidla virtuální sítě má následující omezení:

- Webová aplikace může být namapovaná na soukromou IP adresu ve virtuální síti nebo podsíti. I když jsou koncové body služby zapnuté z dané virtuální sítě nebo podsítě, budou připojení z webové aplikace k serveru mít zdroj veřejné IP adresy Azure, ne virtuální síť nebo zdroj podsítě. Pokud chcete povolit připojení z webové aplikace k serveru, který má pravidla brány firewall virtuální sítě, musíte povolit službám Azure přístup k serveru na serveru.

- V bráně firewall pro Azure Database for MariaDB se každé pravidlo virtuální sítě odkazuje na podsíť. Všechny tyto odkazované podsítě musí být hostované ve stejné geografické oblasti, která hostuje Azure Database for MariaDB.

- Každý Azure Database for MariaDB Server může mít až 128 položek seznamu řízení přístupu (ACL) pro libovolnou danou virtuální síť.

- Pravidla virtuální sítě se vztahují jenom na Azure Resource Manager virtuální sítě; a ne pro [model nasazení Classic][resource-manager-deployment-model-568f] .

- Zapnutím koncových bodů služby virtuální sítě pro Azure Database for MariaDB pomocí značky služby **Microsoft. SQL** se taky povolí koncové body pro všechny služby Azure Database services: Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database a Azure SQL Data Warehouse.

- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

- V bráně firewall se rozsahy IP adres vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
    - [Virtuální privátní síť (VPN) typu Site-to-Site (S2S)][vpn-gateway-indexmd-608y]
    - Místně prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Pokud je vaše síť připojená k síti Azure prostřednictvím použití [ExpressRoute][expressroute-indexmd-744v], každý okruh se nakonfiguruje se dvěma veřejnými IP adresami na Microsoft Edge. Tyto dvě IP adresy se používají pro připojení ke službám Microsoftu, jako je třeba Azure Storage, pomocí veřejného partnerského vztahu Azure.

Aby bylo možné Azure Database for MariaDB komunikaci z okruhu, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašich okruhů. Aby bylo možné najít veřejné IP adresy okruhu ExpressRoute, otevřete lístek podpory s ExpressRoute pomocí Azure Portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány firewall virtuální sítě na server bez zapnutí koncových bodů služby virtuální sítě

Pouze nastavení pravidla brány firewall nezabezpečuje Server do virtuální sítě. Aby se zabezpečení projevilo, musíte taky **zapnout koncové body** služby virtuální sítě. Při zapnutí koncových bodů služby **ve**vaší virtuální síti dojde k výpadku, dokud se přechod neukončí na **zapnuto**. **Off** To platí zejména v kontextu velkých virtuální sítě. Pomocí příznaku **IgnoreMissingServiceEndpoint** můžete snížit nebo odstranit výpadky během přechodu.

Příznak **IgnoreMissingServiceEndpoint** můžete nastavit pomocí Azure CLI nebo portálu.

## <a name="related-articles"></a>Související články
- [Virtuální sítě Azure][vm-virtual-network-overview]
- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel virtuální sítě najdete v těchto tématech:
- [Vytvoření a Správa pravidel Azure Database for MariaDB VNet pomocí Azure Portal](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
