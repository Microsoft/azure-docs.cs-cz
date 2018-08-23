---
title: Přehled koncových bodů služby Azure Database for virtuální sítě serveru PostgreSQL | Dokumentace Microsoftu
description: Popisuje, jak fungují koncové body služby virtuální sítě pro váš server Azure Database for PostgreSQL.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/20/2018
ms.openlocfilehash: 4f488128b3f7a9aa06be9358439536d78615430e
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060552"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Použití koncové body služeb virtuální sítě a pravidel pro službu Azure Database for PostgreSQL

*Pravidla virtuální sítě* jsou jednu funkci brány firewall na zabezpečení, která určuje, jestli váš server Azure Database for PostgreSQL přijímá komunikaci, kterou jsou odesílány z konkrétní podsítě ve virtuálních sítích. Tento článek vysvětluje, proč funkce pravidlo virtuální sítě je někdy nejlepší možnost povolení zabezpečené komunikace pro váš server Azure Database for PostgreSQL.

Chcete-li vytvořit pravidlo virtuální sítě, nejprve musí být [virtuální sítě] [ vm-virtual-network-overview] (VNet) a [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] pro pravidlo pro odkaz. Následující obrázek ukazuje, jak funguje koncový bod služby virtuální sítě s využitím Azure Database for PostgreSQL:

![Příklad toho, jak funguje koncového bodu služby virtuální sítě](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure veřejného cloudu, ve kterém je nasazená – Azure Database for PostgreSQL pro servery pro obecné účely a optimalizovaný pro paměť.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální síť:** může mít virtuální sítě přidružený k vašemu předplatnému Azure.

**Podsíť:** virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), ke kterým máte jsou přidruženy k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlech. Výpočetní uzly, které jsou mimo virtuální síť nemůže přistupovat k vaší virtuální sítě, pokud konfiguraci zabezpečení pro povolení přístupu.

**Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. V tomto článku jsme se zajímat název typu **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro služby Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě nakonfiguruje provoz koncový bod služby pro Azure SQL Database, Azure Database for PostgreSQL a – Azure Database for MySQL servery v podsíti. 

**Pravidlo virtuální sítě:** pravidlo virtuální sítě pro váš server Azure Database for PostgreSQL je podsíť, která je uvedena v seznamu řízení přístupu (ACL) z vašeho serveru Azure Database for PostgreSQL. Bude v seznamu ACL pro váš server Azure Database for PostgreSQL a musí obsahovat podsítě **Microsoft.Sql** název typu.

Pravidlo virtuální sítě sdělí váš server Azure Database for postgresql – tak, aby přijímal komunikaci od každý uzel, který je v podsíti.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidlo virtuální sítě

Dokud je provést akce, virtuální počítače na podsítě nemůže komunikovat s vašeho serveru Azure Database for PostgreSQL. Jednu akci, která vytváří komunikace je vytvoření pravidla virtuální sítě. Důvody pro výběr přístup pravidlo virtuální sítě vyžaduje diskuzi porovnání a kontrast zahrnující konkurenční možnosti zabezpečení nabízí bránou firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

V podokně zabezpečení připojení **ON/OFF** tlačítko, které je označené jako **povolit přístup ke službám Azure**. **ON** nastavení umožňuje komunikaci ze všech IP adres Azure a všech podsítí Azure. Tyto IP adresy Azure nebo podsítě nemusí být vlastníte. To **ON** nastavení je pravděpodobně otevřenější než se vaše databáze Azure Database for postgresql – bude. Funkce pravidlo virtuální sítě nabízí mnohem lepší kontrolu.

### <a name="b-ip-rules"></a>B. Pravidla protokolu IP

Azure Database for postgresql – brány firewall můžete zadat rozsahy IP adres, ze kterých komunikace jsou přijaty do databáze Azure pro databázi PostgreSQL. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale počet uzlů v rámci Azure privátní sítě jsou nakonfigurovány s *dynamické* IP adresy. Dynamické IP adresy můžou změnit, například když váš virtuální počítač se restartuje. Bylo by pošetilost dynamickou IP adresu určit v pravidlu brány firewall, v produkčním prostředí.

Možnosti IP můžete zachránit získáním *statické* IP adresu vašeho virtuálního počítače. Podrobnosti najdete v tématu [konfigurace privátních IP adres pro virtuální počítač pomocí webu Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Ale statické IP přístup může být obtížné spravovat a je nákladná provádět ve velkém měřítku. Pravidla virtuální sítě je snazší, vytvořit a spravovat.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Nelze ještě – Azure Database for PostgreSQL v podsíti bez definování koncového bodu služby

Pokud vaše **Microsoft.Sql** server byl uzel v podsíti ve virtuální síti, všechny uzly v rámci virtuální sítě může komunikovat s vašeho serveru Azure Database for PostgreSQL. V takovém případě vašich virtuálních počítačů může komunikovat s využitím Azure Database for PostgreSQL bez nutnosti jakékoli pravidla virtuální sítě nebo IP.

Nicméně od srpna 2018 služba Azure Database for PostgreSQL ještě není mezi službami, které je možné přiřadit přímo do podsítě.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidel virtuální sítě

Tato část popisuje několik podrobností o pravidel virtuální sítě.

### <a name="only-one-geographic-region"></a>Pouze jedné zeměpisné oblasti

Každý koncový bod služby virtuální sítě se týká pouze jedné oblasti Azure. Koncový bod neumožňuje jiných oblastech tak, aby přijímal komunikaci z podsítě.

Žádné pravidlo virtuální sítě je omezená na oblasti, která se vztahuje na jeho základní koncový bod.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, ne databáze na úrovni

Každé pravidlo virtuální sítě se vztahuje k vaší celý server Azure Database for PostgreSQL, nejen pro jednu konkrétní databázi na serveru. Jinými slovy pravidlo virtuální sítě se vztahuje na úrovni serveru, ne na úrovni databáze.

#### <a name="security-administration-roles"></a>Role zabezpečení správy

Je oddělení rolí zabezpečení ve správě koncových bodů služby virtuální sítě. Akce je zapotřebí ve směru z každé z následujících rolí:

- **Správce sítě:** &nbsp; zapnout koncový bod.
- **Správce databáze:** &nbsp; aktualizujte seznam řízení přístupu (ACL) pro přidání dané podsíti serveru Azure Database for PostgreSQL.

*Ve zkratce RBAC:*

Role správce sítě a správce databáze mají další funkce, než jsou potřeba ke správě pravidel virtuální sítě. Je potřeba pouze podmnožinu jejich schopnosti.

Máte možnost použití [řízení přístupu na základě role (RBAC)] [ rbac-what-is-813s] v Azure k vytvoření jedné vlastní roli, která obsahuje pouze nezbytné dílčí sadu možností. Vlastní role může použít namísto správce sítě nebo správce databáze. Oblast povrchu napadení zabezpečení je nižší, pokud přidáte vlastní roli, a přidejte tohoto uživatele do další dvě hlavní správce role uživatele.

> [!NOTE]
> V některých případech jsou Azure Database for PostgreSQL a podsítěmi virtuálních sítí v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Obě předplatná musí být ve stejném tenantovi Azure Active Directory.
> - Uživatel nemá potřebná oprávnění k zahájení operace, např. povolují se koncové body služby a přidáte k podsíti virtuální sítě na daném serveru.

## <a name="limitations"></a>Omezení

Pro službu Azure Database for PostgreSQL funkci pravidla virtuální sítě má následující omezení:

- Každé pravidlo virtuální sítě v bráně firewall pro Azure Database for PostgreSQL, odkazuje na podsíť. Všechny odkazované podsítě musí být hostovaný ve stejné zeměpisné oblasti, který je hostitelem Azure Database for PostgreSQL.

- Každý server Azure Database for PostgreSQL můžete mít až 128 položky seznamů ACL pro jakékoli dané virtuální síti.

- Virtuální síť pravidla se vztahují pouze k virtuálním sítím Azure Resource Manageru; a nikoli k [modelu nasazení classic] [ arm-deployment-model-568f] sítě.

- Zapnutí na virtuální síť koncových bodů služby do služby Azure Database for PostgreSQL pomocí **Microsoft.Sql** značka služby také umožňuje koncové body pro všechny služby Azure Database: Azure Database for MySQL, Azure Database for PostgreSQL , Azure SQL Database a Azure SQL Data Warehouse.

- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

- V bráně firewall rozsahy IP adres se vztahují na následující síťové položky, ale nepodporují pravidla virtuální sítě:
    - [Site-to-Site (S2S) virtuální privátní sítě (VPN)][vpn-gateway-indexmd-608y]
    - On-premises prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Pokud vaše síť připojená k síti Azure pomocí [ExpressRoute][expressroute-indexmd-744v], každý okruh se nakonfigurují dvě veřejné IP adresy v Microsoft Edge. Dvě IP adresy se používají pro připojení k Microsoft Services, například ke službě Azure Storage s použitím veřejné partnerské vztahy Azure.

Povolit komunikaci z váš okruh ke službě Azure Database for PostgreSQL, musíte vytvořit pravidla sítě protokolu IP pro veřejné IP adresy vaší obvody. Pokud chcete zjistit veřejné IP adresy okruhů ExpressRoute, otevřete lístek podpory s využitím ExpressRoute pomocí webu Azure portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány Firewall virtuální sítě k vašemu serveru neaktivuje na koncové body služby virtuální sítě

Pouze nastavení pravidla brány Firewall nepomůže zabezpečení serveru k virtuální síti. Musíte také zapnout koncové body služby virtuální sítě **na** pro zabezpečení, než se projeví. Když povolíte koncové body služby **na**, VNet subnet vyskytne výpadek až do dokončení přechodu z **vypnout** k **na**. To platí zejména v souvislosti s velké virtuální sítě. Můžete použít **IgnoreMissingServiceEndpoint** příznak ke snížení nebo eliminaci výpadek během přechodu.

Můžete nastavit **IgnoreMissingServiceEndpoint** příznak pomocí rozhraní příkazového řádku Azure nebo portálu.

## <a name="related-articles"></a>Související články
- [Virtuální sítě Azure][vm-virtual-network-overview]
- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Další postup
Články o vytvoření pravidla virtuální sítě naleznete v tématu:
- [Vytvoření a správě Azure Database for PostgreSQL VNet pravidla pomocí webu Azure portal](howto-manage-vnet-using-portal.md)
- [Vytvoření a správě Azure Database for PostgreSQL VNet pravidla pomocí Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml