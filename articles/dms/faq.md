---
title: Nejčastější dotazy týkající se použití Azure Database Migration Service | Dokumentace Microsoftu
description: Přečtěte si nejčastější dotazy o použití k provedení migrace databází Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717997"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Nejčastější dotazy týkající se použití Azure Database Migration Service

Tento článek obsahuje nejčastější dotazy o použití Azure Database Migration Service společně se související odpovědi.

## <a name="overview"></a>Přehled

**Q. Co je Azure Database Migration Service?**
Azure Database Migration Service je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Služba je aktuálně všeobecně dostupná, s probíhající vývojové činnosti, zaměřuje na:

* Spolehlivost a výkon.
* Sčítání iterativní párů zdroj cíl.
* Další investice do bezproblémové migrace.

**Q. Jaké párů zdroj/cíl Azure Database Migration Service aktuálně podporuje?**
Služba aktuálně podporuje širokou škálu párů zdroj/cíl nebo scénáře migrace. Úplný seznam všech stav jednotlivých scénářů migrace k dispozici, najdete v článku [stavový migrační scénáře podporované službou Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Další scénáře migrace jsou ve verzi preview a vyžadovat odeslání nominace prostřednictvím webu DMS ve verzi Preview. Úplný seznam všech scénáře ve verzi preview a zaregistrovat k účasti v jednom z těchto nabídek, najdete v článku [DMS ve verzi Preview webu](https://aka.ms/dms-preview/).

**Q. Jaké verze systému SQL Server Azure Database Migration Service podporuje jako zdroj?**
Při migraci z SQL serveru podporovaných zdrojů pro Azure Database Migration Service se SQL Server 2005 do SQL serveru 2017.

**Otázka: Při použití služby Azure Database Migration Service, jaký je rozdíl mezi offline a online migrace?**
Azure Database Migration Service můžete použít k provedení migrace offline a online. Pomocí *offline* migrace, aplikace výpadek nastane, jakmile migrace začne. Pomocí *online* migrace, odstávky jsou omezené na čas k přímé na konci migrace. Doporučujeme otestovat offline migraci a určit, jestli je výpadek přijatelný. Pokud není, proveďte online migraci.

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium. Další informace najdete v tématu Azure Database Migration Service [ceny](https://azure.microsoft.com/pricing/details/database-migration/) stránky.

**Q. Jak služba Azure Database Migration Service se dá porovnat s ostatními nástroji Migrace databáze Microsoftu jako jsou databáze Migration Assistant (DMA) nebo SQL asistent migrace Server (SSMA)?**
Azure Database Migration Service je upřednostňovanou metodou pro migraci databází do Microsoft Azure ve velkém měřítku. Další podrobnosti o jak služba Azure Database Migration Service porovnává jiné společnosti Microsoft databázových nástrojů pro migraci a doporučení týkající se používání služby pro různé scénáře, naleznete v tématu příspěvky blogu [odlišení těchto Microsoft migrace databáze Nástroje a služby](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**Q. Jak služba Azure Database Migration Service se dá porovnat s nabídkou Azure Migrate?**
Azure Migrate pomáhá při migraci místních virtuálních počítačů Azure IaaS. Služba posuzuje vhodnost k migraci a velikost na základě výkonu a poskytuje odhady nákladů pro provoz vašich místních virtuálních počítačů v Azure. Azure Migrate je užitečné pro lift and shift migrace místních virtuálních počítačů na základě úloh virtuálních počítačů Azure IaaS. Ale na rozdíl od Azure Database Migration Service, Azure Migrate není specializovaná služba database migration service nabízí pro relační databázi platformy Azure PaaS, jako je Azure SQL Database nebo Azure SQL Database Managed Instance.

## <a name="setup"></a>Instalace

**Q. Jaké jsou požadavky pro používání služby Azure Database Migration Service?**
Existuje několik předpokladů, které jsou potřeba k tomu, že služba Azure Database Migration Service běží bez problémů při provádění migrace databází. Některé z požadovaných součástí ve všech scénářích (párů zdroj cíl) podporované službou, zatímco jiné požadavky, které jsou jedinečné pro konkrétní scénář uplatnit.

Požadavky na Azure Database Migration Service, které jsou společné pro všechny podporované scénáře migrace patří:

* Vytvoření virtuální sítě pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Zajistěte, aby službě Azure Virtual Network (VNet) pravidla skupiny zabezpečení sítě není blokovat následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Při použití zařízení brány firewall u zdrojových databází, budete muset přidat pravidla firewallu povolující Azure Database Migration Service přístup k zdrojových databází pro migraci.

Seznam všech požadovaných součástí pro soutěžit konkrétní migrační scénáře pomocí služby Azure Database Migration Service najdete v tématu souvisejících kurzů v Azure Database Migration Service [dokumentaci](https://docs.microsoft.com/azure/dms/dms-overview) na webu docs.microsoft.com.

**Q. Jak najdu IP adresu pro Azure Database Migration Service tak, že seznam povolených pro pravidla brány firewall pro přístup k databázi zdroje pro migraci můžete vytvořit?**
Budete muset přidat pravidla brány firewall umožňující Azure Database Migration Service přístup k vaší zdrojové databáze pro migraci. Je dynamická IP adresa pro službu, ale pokud používáte Expressroute, tato adresa se přiřadí soukromě ve vaší podnikové síti. Nejjednodušší způsob, jak identifikovat příslušné IP adresy se vás pod rouškou ve stejné skupině prostředků jako vaše se zřídil prostředek Azure Database Migration Service k vyhledání přidružené síťové rozhraní. Název prostředku síťového rozhraní obvykle začíná příslušnou předponou síťovou kartu a za nímž následuje jedinečný znak a čísla pořadí, například síťová karta jj6tnztnmarpsskr82rbndyp. Pokud vyberete tento prostředek síťové rozhraní, uvidíte IP adresu, musí být zahrnuty do seznamu povolených na webu Azure portal stránka Přehled prostředků.

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

**Q. Jak můžu nastavit Azure Virtual Network?**
Při více kurzy Microsoftu, které může vás provede procesem vytvoření virtuální síť Azure, se zobrazí v oficiální dokumentaci v článku [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Použití

**Q. Co je uveden seznam kroky potřebné k Azure Database Migration Service můžete provést migraci databáze?**
Při migraci databáze typické, jednoduché můžete:

1. Vytvořte cílové databáze.
2. Vyhodnocení zdrojových databází.
    * Pro homogenní migrace vyhodnocení existující databázi/databáze s použitím [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Pro heterogenní migrace (z úplné zdrojů), posoudit vaše stávající databáze s [SSMA](https://aka.ms/get-ssma). Použijete také SSMA převést databázové objekty a migraci schématu na cílovou platformu.
3. Vytvoření instance služby Azure Database Migration Service.
4. Vytvořte projekt migrace zadáním zdrojových databází, cílové databáze a tabulky k migraci.
5. Spusťte úplné načtení.
6. Vyberte následné ověřování.
7. Proveďte ruční přepínání provozního prostředí do nové databáze založené na cloudu.

## <a name="troubleshooting-and-optimization"></a>Řešení potíží a optimalizace

**Q. Můžu jsem nastavení projektu migrace v DMS a mám potíže s připojením k databázi zdrojové. Co bych měl/a dělat?**
Pokud máte potíže s připojením k databázi zdrojového systému při práci na migraci, vytvořte virtuální počítač ve virtuální síti, pomocí kterého můžete nastavit DMS instanci. Ve virtuální počítač byste měli být schopni spustit test připojení, jako je například stahování Robo 3T k testování připojení MongoDB nebo proveďte test připojení k SQL serveru pomocí souboru UDL. Pokud je test připojení úspěšný, by neměly mít problém s připojením k vaší zdrojové databáze. Pokud se test připojení k neúspěchu, obraťte se na správce sítě.

**Q. Proč je můj Azure Database Migration Service není k dispozici nebo zastavena?**
Pokud uživatel explicitně zastaví Azure Database Migration Service (DMS) nebo jestli je služba neaktivní po dobu 24 hodin, služba bude nacházet v zastavený nebo auto pozastaveného stavu. V obou případech bude služba není k dispozici a je v zastaveném stavu.  Chcete-li pokračovat s migracemi aktivní, restartujte službu.

**Q. Existují žádná doporučení pro optimalizaci výkonu služby Azure Database Migration Service?**
Můžete provést několik věcí, ke zrychlení migrace databází pomocí služby:

* Použití více procesorů obecné účely cenová úroveň, při vytváření vaší instance služby, aby byla povolena využívat více virtuálních procesorů pro paralelizaci a rychlejší přenos dat.
* Dočasně vertikálního navýšení vaše cílová instance Azure SQL Database na úrovni Premium SKU během operace migrace dat. Chcete-li minimalizovat Azure SQL Database, omezování, která může mít vliv na činnosti přenosu dat při použití SKU nižší úrovně.

## <a name="next-steps"></a>Další postup

Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md).
