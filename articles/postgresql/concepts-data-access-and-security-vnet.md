---
title: Pravidla virtuální sítě – databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak používat koncové body služby virtuální sítě (vnet) pro připojení k Azure Database for PostgreSQL – single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 512ad8f93da53afb618491cd1769645d8edb0b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965841"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Použití koncových bodů a pravidel služby Virtuální síť pro Azure Database for PostgreSQL – jeden server

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která řídí, zda váš server Azure Database for PostgreSQL přijímá komunikaci odesílanou z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč je funkce pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s vaším serverem Azure Database for PostgreSQL.

Chcete-li vytvořit pravidlo virtuální sítě, musí nejprve existovat [virtuální síť][vm-virtual-network-overview] (VNet) a koncový [bod služby virtuální sítě,][vm-virtual-network-service-endpoints-overview-649d] na který má pravidlo odkazovat. Následující obrázek ilustruje, jak koncový bod služby virtuální sítě funguje s Azure Database for PostgreSQL:

![Příklad fungování koncového bodu služby virtuální sítě](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech veřejného cloudu Azure, kde se Azure Database for PostgreSQL nasadí pro servery optimalizované pro obecné účely a paměť.
> V případě partnerského vztahu virtuální sítě, pokud provoz protéká společnou bránou virtuální sítě s koncovými body služby a má tok do druhé strany, vytvořte pravidlo ACL/Virtuální sítě, které umožní virtuálním počítačům Azure ve virtuální síti brány přístup k databázi Azure pro postgreSQL server.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální síť:** Můžete mít virtuální sítě přidružené k vašemu předplatnému Azure.

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), které jste přiřadili k podsítím. Jedna podsíť může obsahovat více virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k vaší virtuální síti, pokud nenakonfigurujete zabezpečení tak, aby přístup umožňovalo.

**Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť][vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. V tomto článku nás zajímá název typu **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro azure databáze pro postgreSQL a mysql služby. Je důležité si uvědomit, při použití značky služby **Microsoft.Sql** na koncový bod služby virtuální sítě bude konfigurovat provoz koncového bodu služby pro všechny Azure SQL Database, Azure Database pro PostgreSQL a Azure Database pro mySQL servery v podsíti. 

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro váš server Azure Database for PostgreSQL je podsíť, která je uvedená v seznamu řízení přístupu (ACL) vašeho serveru Azure Database for PostgreSQL. Chcete-li být v acl pro azure databáze pro postgreSQL server, podsíť musí obsahovat název typu **Microsoft.Sql.**

Pravidlo virtuální sítě říká, že databáze Azure pro postgreSQL server přijímá komunikaci z každého uzlu, který je v podsíti.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidla virtuální sítě

Dokud nepodniknete akci, virtuální počítače ve vašich podsítích nemohou komunikovat s vaším serverem Azure Database for PostgreSQL. Jedna akce, která vytvoří komunikaci je vytvoření pravidla virtuální sítě. Důvody pro výběr přístupu pravidla virtuální sítě vyžaduje porovnání a kontrast diskuse zahrnující konkurenční možnosti zabezpečení, které nabízí brána firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

Podokno zabezpečení Připojení má tlačítko **ZAPNUTO/VYPNUTO** s označením **Povolit přístup ke službám Azure**. Nastavení **ON** umožňuje komunikaci ze všech IP adres Azure a všech podsítí Azure. Tyto IP adresy nebo podsítě Azure nemusí být vlastněny vámi. Toto nastavení **ON** je pravděpodobně otevřenější, než chcete, aby databáze Azure pro PostgreSQL databáze byla. Funkce pravidla virtuální sítě nabízí mnohem jemnější podrobné ovládání.

### <a name="b-ip-rules"></a>B. Pravidla IP

Brána firewall Azure Database for PostgreSQL umožňuje určit rozsahy IP adres, ze kterých se přijímá komunikace do databáze Azure Database for PostgreSQL Database. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale mnoho uzlů uvnitř privátní sítě Azure je nakonfigurovaných s *dynamickými* IP adresami. Dynamické IP adresy se mohou změnit, například při restartování virtuálního počítače. Bylo by pošetilé zadat dynamickou adresu IP v pravidle brány firewall v produkčním prostředí.

Možnost IP můžete zachránit získáním *statické* IP adresy pro váš virtuální počítač. Podrobnosti najdete [v tématu Konfigurace privátních IP adres pro virtuální počítač pomocí portálu Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Statický přístup IP však může být obtížné spravovat a je nákladné při práci ve velkém měřítku. Pravidla virtuální sítě se snadněji vytvářejí a spravují.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Zatím nelze mít Azure Database pro PostgreSQL v podsíti bez definování koncového bodu služby

Pokud váš **Microsoft.Sql** server byl uzel v podsíti ve vaší virtuální síti, všechny uzly ve virtuální síti může komunikovat s azure databáze pro postgreSQL server. V takovém případě mohou virtuální počítače komunikovat s Azure Database for PostgreSQL bez nutnosti jakýchkoli pravidel virtuální sítě nebo pravidel IP.

Nicméně od srpna 2018 služba Azure Database for PostgreSQL ještě není mezi službami, které lze přiřadit přímo k podsíti.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Pouze jedna zeměpisná oblast

Každý koncový bod služby virtuální sítě se vztahuje pouze na jednu oblast Azure. Koncový bod neumožňuje jiné oblasti přijímat komunikaci z podsítě.

Všechna pravidla virtuální sítě je omezena na oblast, která jeho základní koncový bod se vztahuje.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, nikoli na úrovni databáze

Každé pravidlo virtuální sítě se vztahuje na celou databázi Azure pro postgreSQL server, nejen na jednu konkrétní databázi na serveru. Jinými slovy pravidlo virtuální sítě platí na úrovni serveru, nikoli na úrovni databáze.

#### <a name="security-administration-roles"></a>Role správy zabezpečení

V správě koncových bodů služby virtuální sítě dochází k oddělení rolí zabezpečení. Akce je vyžadována z každé z následujících rolí:

- **Správce sítě:** &nbsp; Zapněte koncový bod.
- **Správce databáze:** &nbsp; Aktualizujte seznam řízení přístupu (ACL) a přidejte danou podsíť do databáze Azure pro postgreSQL server.

*Alternativa RBAC:*

Role správce sítě a správce databáze mají více funkcí, než je potřeba ke správě pravidel virtuální sítě. Je potřeba pouze podmnožinu jejich schopností.

Máte možnost pomocí [řízení přístupu na základě rolí (RBAC)][rbac-what-is-813s] v Azure vytvořit jednu vlastní roli, která má pouze potřebnou podmnožinu možností. Vlastní role lze použít namísto zapojení správce sítě nebo správce databáze. Plocha expozice zabezpečení je nižší, pokud přidáte uživatele do vlastní role, oproti přidání uživatele do dalších dvou hlavních rolí správce.

> [!NOTE]
> V některých případech databáze Azure pro PostgreSQL a podsítě virtuální sítě jsou v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Obě předplatná musí být ve stejném tenantovi služby Azure Active Directory.
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě na daný server.
> - Ujistěte se, že obě předplatná má registrovaný zprostředkovatel prostředků **Microsoft.Sql.** Další informace naleznete v odkazech [na registraci správce prostředků][resource-manager-portal]

## <a name="limitations"></a>Omezení

Pro Azure Database for PostgreSQL má funkce pravidel virtuální sítě následující omezení:

- Webovou aplikaci lze namapovat na privátní IP adresu ve virtuální síti nebo podsíti. I v případě, že koncové body služby jsou zapnuté z dané virtuální sítě/podsítě, připojení z webové aplikace na server budou mít zdroj IP Azure veřejný, ne zdroj virtuální sítě/podsítě. Chcete-li povolit připojení z webové aplikace k serveru, který má pravidla brány firewall virtuální sítě, musíte povolit přístup služeb Azure k přístupu k serveru na serveru.

- V bráně firewall pro databázi Azure pro PostgreSQL odkazuje každé pravidlo virtuální sítě na podsíť. Všechny tyto odkazované podsítě musí být hostované ve stejné geografické oblasti, která je hostitelem databáze Azure pro PostgreSQL.

- Každá databáze Azure pro PostgreSQL server může mít až 128 položek ACL pro danou virtuální síť.

- Pravidla virtuální sítě platí jenom pro virtuální sítě Azure Resource Manager; a nikoli do klasických sítí [modelů nasazení.][arm-deployment-model-568f]

- Zapnutí koncových bodů virtuální síťové služby do Databáze Azure pro PostgreSQL pomocí značky služby **Microsoft.Sql** také umožňuje koncové body pro všechny služby Azure Database: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database a Azure SQL Data Warehouse.

- Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.

- V bráně firewall se rozsahy adres IP vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
    - [Virtuální privátní síť site-to-site (S2S) (VPN)][vpn-gateway-indexmd-608y]
    - Místní přes [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Pokud je vaše síť připojena k síti Azure pomocí [ExpressRoute][expressroute-indexmd-744v], každý okruh je nakonfigurován se dvěma veřejnými IP adresami v Microsoft Edge. Dvě IP adresy se používají k připojení ke službám Microsoftu, jako je azure storage, pomocí Azure Public Peering.

Chcete-li povolit komunikaci z vašeho okruhu do databáze Azure pro PostgreSQL, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašich okruhů. Chcete-li najít veřejné IP adresy okruhu ExpressRoute, otevřete lístek podpory s ExpressRoute pomocí portálu Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány firewall virtuální sítě na server bez zapnutí koncových bodů služby Virtuální sítě

Pouhé nastavení pravidla brány firewall nepomůže zabezpečit server do virtuální sítě. Musíte také zapnout koncové body služby virtuální **sítě,** aby se zabezpečení projevilo. Když zapnete koncové body **služby zapnuto**, vaše podsíť virtuální sítě zažije prostoje, dokud nedokončí přechod z **Vypnuto** **na Zapnuto**. To platí zejména v kontextu velkých virtuálních sítí. Příznak **IgnoreMissingServiceEndpoint** můžete použít ke snížení nebo odstranění prostojů během přechodu.

Příznak **IgnoreMissingServiceEndpoint** můžete nastavit pomocí příkazového příkazu k onomu Azure nebo portálu.

## <a name="related-articles"></a>Související články
- [Virtuální sítě Azure][vm-virtual-network-overview]
- [Koncové body virtuální síťové služby Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel virtuální sítě najdete v tématu:
- [Vytváření a správa pravidel Azure Database for PostgreSQL VNet pomocí portálu Azure](howto-manage-vnet-using-portal.md)
- [Vytváření a správa pravidel Azure Database for PostgreSQL VNet pomocí rozhraní příkazového příkazového příkazu Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
