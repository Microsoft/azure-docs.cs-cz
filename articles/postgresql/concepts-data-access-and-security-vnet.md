---
title: Přehled koncový bod služby services databáze Azure pro virtuální síť PostgreSQL serveru | Microsoft Docs
description: Popisuje, jak fungují koncové body služby virtuální sítě pro vaši databázi Azure pro PostgreSQL server.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2018
ms.openlocfilehash: a832f45027fc5337d9e76ec9cc4898286121278c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654870"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Použijte koncové body služby virtuální sítě a pravidla pro databázi Azure pro PostgreSQL

*Pravidla pro virtuální sítě* jsou jeden funkce zabezpečení brány firewall, která určuje, zda databáze Azure pro PostgreSQL server přijímá komunikaci, kterou jsou odesílány z konkrétní podsítě ve virtuálních sítích. Tento článek vysvětluje, proč funkci pravidlo virtuální sítě se někdy nejlepší možnost pro bezpečně povolení komunikace k vaší databázi Azure pro PostgreSQL server.

K vytvoření pravidla, virtuální sítě, nejprve musí být [virtuální sítě] [ vm-virtual-network-overview] (VNet) a [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] pro pravidlo pro odkaz. Následující obrázek ukazuje, jak koncový bod služby virtuální sítě funguje s databází Azure pro PostgreSQL:

![Příklad toho, jak funguje koncového bodu služby virtuální sítě](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Pro databázi Azure pro PostgreSQL tato funkce je dostupná ve verzi public preview ve všech oblastech Azure veřejného cloudu, kde je nasazená databáze Azure pro PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální sítě:** může mít virtuální sítě, které jsou spojené s předplatným Azure.

**Podsítě:** virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), které máte přiřazené k podsítím. Jedna podsíť může obsahovat více virtuálních počítačů nebo dalších výpočetních uzlů. Výpočetní uzly, které jsou mimo virtuální síť nemůže přistupovat k virtuální síti, pokud konfigurace vaší zabezpečení pro povolení přístupu.

**Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejichž hodnoty vlastností obsahovat jeden nebo více názvy typů formální služby Azure. V tomto článku jsme mají zájem o název typu **Microsoft.Sql**, které odkazuje na službu Azure s názvem databáze SQL. Tato značka služby platí také pro Azure databázi PostgreSQL a MySQL služby. Je důležité si uvědomit, při použití **Microsoft.Sql** služby značky pro koncový bod služby virtuální sítě nakonfiguruje přenosů koncový bod služby pro všechny databáze SQL Azure, Azure databáze PostgreSQL a databáze MySQL servery Azure v podsíti. 

**Pravidlo virtuální sítě:** pravidlo virtuální sítě pro vaši databázi Azure pro PostgreSQL server je podsíť, která je uvedena v seznamu řízení přístupu (ACL) Azure databáze pro PostgreSQL server. Chcete-li být v seznamu ACL pro vaši databázi Azure pro PostgreSQL server, musí obsahovat podsíť **Microsoft.Sql** název typu.

Pravidlo pro virtuální sítě informuje Azure databáze PostgreSQL serveru tak, aby přijímal komunikaci od každému uzlu, který je v podsíti.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidlo virtuální sítě

Dokud provedení akce, virtuální počítače na podsítě nemůže komunikovat s Azure databáze pro PostgreSQL server. Jedna akce, který stanoví komunikace je vytvoření pravidla virtuální sítě. Při výběru virtuální sítě pravidlo přístupu vyžaduje se zabývat porovnání a kontrast zahrnující konkurenční možnosti zabezpečení nabízené bránou firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

V podokně zabezpečení připojení **zapnout nebo vypnout** tlačítko, které je označeno **povolit přístup ke službám Azure**. **ON** nastavení umožňuje komunikaci od všech Azure IP adres a všech Azure podsítí. Tyto Azure IP adresy nebo podsítě nemusí být vlastníte. To **ON** nastavení je pravděpodobně více otevřete než chcete Azure databáze pro databázi PostgreSQL být. Funkce pravidlo virtuální sítě nabízí mnohem přesnější přesná kontrola.

### <a name="b-ip-rules"></a>B. Pravidla IP

Databáze Azure pro brány firewall PostgreSQL vám umožní určit rozsahy IP adres, ze kterých přijata komunikace do databáze Azure pro databázi PostgreSQL. Tento přístup je vhodná pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale velký počet uzlů v rámci Azure privátní sítě jsou nakonfigurovány s *dynamické* IP adresy. Dynamické IP adresy mohou změnit, například když je virtuální počítač restartovat. Je pošetilost k určení dynamickou IP adresu v pravidle brány firewall v produkčním prostředí.

IP adresy můžete vyřazení získáním *statické* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [nakonfigurovat privátní IP adresy pro virtuální počítač pomocí portálu Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Ale statické IP přístupu se může stát obtížné spravovat a je nákladná, pokud provádí ve velkém měřítku. Pravidla pro virtuální sítě se snadněji můžete vytvořit a spravovat.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Ještě nemají databáze Azure pro PostgreSQL v podsíti bez definování koncového bodu služby

Pokud vaše **Microsoft.Sql** byl server uzlu na podsíť ve virtuální síti, všechny uzly v rámci virtuální sítě může komunikovat s Azure databáze pro PostgreSQL server. V takovém případě virtuální počítače může komunikovat s Azure databáze pro PostgreSQL bez nutnosti všech pravidel virtuální sítě a IP pravidla.

Ale od 2018 může databáze Azure pro službu PostgreSQL není ještě mezi služby, které lze přiřadit přímo k podsíti.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Údaje o pravidlech virtuální sítě

Tato část popisuje několik podrobnosti o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Pouze jedné zeměpisné oblasti

Každý koncový bod služby virtuální sítě se týká pouze jedna oblast Azure. Koncový bod není povolen jiných oblastí tak, aby přijímal komunikaci z podsítě.

Pravidlo pro všechny virtuální sítě je omezený na oblast, která se vztahuje na jeho základní koncového bodu.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, ne úroveň databáze

Každé pravidlo virtuální sítě se vztahují k vaší celou databázi Azure pro PostgreSQL server, nejen pro jednu konkrétní databázi na serveru. Jinými slovy virtuální sítě pravidlo se vztahuje na úrovni serveru, ne na úrovni databáze.

#### <a name="security-administration-roles"></a>Role zabezpečení správy

Je oddělení rolí zabezpečení v části Správa koncových bodů služby virtuální sítě. Akci je potřeba z každé z následujících rolí:

- **Správce sítě:** &nbsp; zapnout koncový bod.
- **Správce databáze:** &nbsp; aktualizace seznamu řízení přístupu (ACL) pro přidání do databáze Azure pro PostgreSQL server dané podsíti.

*Alternativní RBAC:*

Správce sítě a správce databáze mají další možnosti, než jsou potřeba ke správě pravidel virtuální sítě. Je potřeba jenom podmnožinu jejich možnosti.

Máte možnost použití [řízení přístupu na základě role (RBAC)] [ rbac-what-is-813s] v Azure vytvořit jednu vlastní roli, která má pouze nezbytné podmnožinu funkcí. Vlastní role může místo zahrnující správce sítě nebo správce databáze. Možnosti útoku vaší ohrožení zabezpečení je nižší, pokud přidáte vlastní roli, a přidáním uživatele do jiných dvě hlavní správce role uživatele.

> [!NOTE]
> V některých případech databáze Azure pro PostgreSQL a podsíť virtuální sítě jsou v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Oba odběry musí být ve stejném klientovi služby Azure Active Directory.
> - Uživatel nemá potřebná oprávnění k zahájení operace, například povolení koncové body služby a přidáte podsíť virtuální sítě na daný Server.

## <a name="limitations"></a>Omezení

Pro databázi Azure pro PostgreSQL funkci pravidla virtuální sítě má následující omezení:

- Každé pravidlo virtuální sítě v bráně firewall pro vaši databázi Azure pro PostgreSQL odkazuje na podsíť. Všechny tyto odkazované podsítě musí být uloženy ve stejné zeměpisné oblasti, který je hostitelem databáze Azure pro PostgreSQL.

- Každou databázi Azure pro PostgreSQL server může mít maximálně 128 položky seznamů ACL pro jakékoli dané virtuální síti.

- Pravidla virtuální sítě se vztahují pouze k virtuálním sítím Azure Resource Manager; a nikoli k [modelu nasazení classic] [ arm-deployment-model-568f] sítě.

- Vypnutí ON virtuální sítě koncové body služby do databáze Azure pro používání PostgreSQL **Microsoft.Sql** značky služba také umožňuje koncových bodů pro všechny služby Azure databáze: databáze Azure pro databázi MySQL, PostgreSQL databáze Azure , Azure SQL Database a Azure SQL Data Warehouse.

- V době verzi public preview neexistuje žádná podpora pro operace přesunutí virtuální sítě. Chcete-li přesunout pravidlo virtuální sítě, vyřaďte a znovu ji vytvořte.

- Podpora pro koncové body služby virtuální sítě je pouze pro obecné účely a paměťově optimalizované servery.

- V bráně firewall rozsahy IP adres se vztahují k následujícím položkám sítě, ale nepodporují pravidla pro virtuální sítě:
    - [Site-to-Site (S2S) virtuální privátní sítě (VPN)][vpn-gateway-indexmd-608y]
    - Místní prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Pokud vaše síť připojená k síti Azure prostřednictvím použití [ExpressRoute][expressroute-indexmd-744v], každý okruh je nakonfigurován s dvě veřejné IP adresy v Microsoft Edge. Dvě IP adresy se používají k připojení k Microsoft Services, například do služby Azure Storage, pomocí veřejného partnerského vztahu Azure.

Povolit komunikaci z okruhu do databáze Azure pro PostgreSQL, musíte vytvořit pravidla pro sítě IP pro veřejné IP adresy vaší okruhů. Aby bylo možné najít veřejné IP adresy váš okruh ExpressRoute, otevřete lístek podpory s ExpressRoute pomocí webu Azure portal.

## <a name="related-articles"></a>Související články
- [Virtuální sítě Azure][vm-virtual-network-overview]
- [Koncové body služby virtuální síť Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Další postup
Články týkající se vytváření pravidel virtuální sítě najdete v části:
- [Vytváření a Správa databáze Azure pro virtuální síť PostgreSQL pravidla pomocí portálu Azure](howto-manage-vnet-using-portal.md)
- [Vytváření a Správa databáze Azure pro virtuální síť PostgreSQL pravidla pomocí rozhraní příkazového řádku Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml