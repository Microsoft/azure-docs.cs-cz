---
title: Nejčastější dotazy týkající se použití Azure Database Migration Service | Dokumentace Microsoftu
description: Přečtěte si nejčastější dotazy o použití k provedení migrace databází Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/19/2018
ms.openlocfilehash: b8001729c85c8447ab22bafffe24a32524a0662a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714691"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Nejčastější dotazy týkající se použití Azure Database Migration Service
Tento článek obsahuje nejčastější dotazy o použití Azure Database Migration Service společně se související odpovědi.

### <a name="q-what-is-azure-database-migration-service"></a>Otázka: Co je Azure Database Migration Service?
Azure Database Migration Service je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Služba je aktuálně všeobecně dostupná, s probíhající vývojové činnosti, zaměřuje na:
- Spolehlivost a výkon.
- Sčítání iterativní párů zdroj cíl.
- Další investice do bezproblémové migrace.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Otázka: Jaké párů zdroj cíl Azure Database Migration Service aktuálně podporuje?
Služba aktuálně podporuje širokou škálu scénářů migrace. Úplný seznam všech stav jednotlivých scénářů migrace k dispozici, najdete v článku [stavový migrační scénáře podporované službou Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status). Další scénáře migrace jsou ve verzi limited preview a vyžadovat odeslání nominace prostřednictvím webu DMS ve verzi Preview. Úplný seznam všech scénáře ve verzi limited preview a zaregistrovat se a particpate v jednom z těchto nabídek, najdete v článku [DMS ve verzi Preview webu](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Otázka: Jak Azure Database Migration Service se dá porovnat s ostatními nástroji Migrace databáze Microsoftu jako jsou databáze Migration Assistant (DMA) nebo SQL asistent migrace Server (SSMA)?
Azure Database Migration Service je upřednostňovanou metodou pro migraci databází do Microsoft Azure ve velkém měřítku. Další podrobnosti o jak si Azure Database Migration Service vede jiné společnosti Microsoft databázových nástrojů pro migraci a doporučení týkající se používání služby pro různé scénáře, naleznete v tématu příspěvky blogu [odlišení těchto Microsoft databáze Nástroje pro migraci a služby](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Otázka: Jak Azure Database Migration Service se dá porovnat s nabídkou Azure Migrate?
Služba Azure Migrate pomáhá při migraci místních virtuálních počítačů Azure IaaS. Služba posuzuje vhodnost k migraci a velikost na základě výkonu a poskytuje odhady nákladů pro provoz vašich místních virtuálních počítačů v Azure. Azure Migrate je užitečné pro lift and shift migrace místních virtuálních počítačů na základě úloh virtuálních počítačů Azure IaaS. Ale na rozdíl od Azure Database Migration Service, Azure Migrate není specializovaná služba database migration service nabízí pro relační databázi platformy Azure PaaS, jako je Azure SQL Database nebo SQL Azure nebo Azure SQL Database Managed Instance.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>Otázka: Jaké verze systému SQL Server Azure Database Migration Service podporuje jako zdroj?
Při migraci z SQL serveru Azure Database Migration Service podporuje SQL Server 2005 do SQL serveru 2017.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Otázka: Co je uveden seznam kroky potřebné k Azure Database Migration Service můžete provést migraci databáze?
Při migraci databáze typické, jednoduché můžete:
1.  Vytvořte cílové databáze.
2.  Migraci schématu databáze s použitím [pomocníka s migrací databáze](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Vytvoření instance služby Azure Database Migration Service
4.  Vytvořte projekt migrace zadáním zdrojových databází, cílové databáze a tabulky k migraci.
5.  Spustit úplné načtení.
6.  Vyberte následné ověřování.
7.  Proveďte ruční přepínání provozního prostředí do nové databáze založené na cloudu. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Otázka: Jaké jsou požadavky pro používání Azure Database Migration Service?
Existuje několik předpokladů, které jsou potřeba k tomu, že Azure Database Migration Service běží bez problémů při provádění migrace databází. Některé z požadovaných součástí ve všech scénářích (párů zdroj cíl) podporované službou, zatímco jiné požadavky, které jsou jedinečné pro konkrétní scénář uplatnit.
Požadavky na Azure Database Migration Service, které jsou společné pro všechny podporované scénáře migrace patří:
- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Zajistěte, aby pravidla skupiny zabezpečení virtuální sítě Azure neblokovala následující komunikační porty: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
 
Seznam všech požadovaných součástí pro soutěžit konkrétní migrační scénáře pomocí Azure Database Migration Service najdete v tématu souvisejících kurzů v Azure Database Migration Service [dokumentaci](https://docs.microsoft.com/azure/dms/dms-overview) na Web docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Otázka: Jak najdu IP adresu pro službu Azure Database Migration Service tak, že seznam povolených pro pravidla brány firewall pro přístup k databázi zdroje pro migraci můžete vytvořit?
Budete muset přidat pravidla brány firewall umožňující Azure Database Migration Service přístup k vaší zdrojové databáze pro migraci. Je dynamická IP adresa pro službu, ale pokud používáte Expressroute, tato adresa se přiřadí soukromě ve vaší podnikové síti. Nejjednodušší způsob, jak určit správnou IP adresu, se vás pod rouškou ve stejné skupině prostředků jako vaše se zřídil prostředek Azure Database Migration Service k vyhledání přidružené síťové rozhraní. Název prostředku síťového rozhraní obvykle začíná příslušnou předponou síťovou kartu a za nímž následuje jedinečný znak a čísla pořadí, například síťová karta jj6tnztnmarpsskr82rbndyp. Pokud vyberete tento prostředek síťové rozhraní, uvidíte IP adresu, musí být zahrnuty do seznamu povolených na webu Azure portal stránka Přehled prostředků.

Také budete muset zahrnout zdroje port, který SQL Server naslouchá na seznam povolených tříd. Ve výchozím nastavení to je port 1433, ale požadovaný zdroj SQL Server může být nakonfigurovaná k naslouchání na i další porty. V takovém případě musíte zahrnout tyto porty na seznamu povolených. Můžete určit port, který SQL Server naslouchá na pomocí zobrazení dynamické správy dotazu:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Můžete také určit port, který SQL Server naslouchá dotazováním v protokolu chyb systému SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Otázka: Existují žádná doporučení pro optimalizaci výkonu služby Azure Database Migration Service?
Můžete provést několik věcí, ke zrychlení migrace databází pomocí služby:
- Použití více procesorů obecné účely cenová úroveň, při vytváření vaší instance služby, aby byla povolena využívat více virtuálních procesorů pro paralelizaci a rychlejší přenos dat.
- Dočasně vertikálního navýšení vaše cílová instance Azure SQL Database na úrovni Premium SKU během operace migrace dat. Chcete-li minimalizovat Azure SQL Database, omezování, která může mít vliv na činnosti přenosu dat při použití SKU nižší úrovně.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Otázka: Jak můžu nastavit Azure Virtual Network?
Při více kurzy Microsoftu, které může vás provede procesem vytvoření virtuální síť Azure, se zobrazí v oficiální dokumentaci v článku [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Otázka: Proč je můj Azure Database Migration Service není k dispozici nebo zastavena?
Pokud uživatel explicitně zastaví Azure Database Migration Service (DMS) nebo pokud je služba neaktivní po dobu 24 hodin, služba bude v zastavený nebo auto pozastaveného stavu. V obou případech bude služba není k dispozici a je v zastaveném stavu.  Chcete-li pokračovat s migracemi aktivní, restartujte službu.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>Otázka: Kde je můžete nechat názor na Azure Database Migration Service?
Chceme znát váš názor. Pošlete svůj názor a / nápady, budete mít o Azure Database Migration Service přes User Voice [tady](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md). 
