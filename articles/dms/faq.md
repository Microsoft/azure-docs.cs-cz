---
title: Nejčastější dotazy – služba migrace databáze Azure
description: Nejčastější dotazy týkající se použití služby Migrace databáze Azure k provádění migrací databáze.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650977"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Nejčastější dotazy týkající se používání služby Migrace databáze Azure

Tento článek uvádí běžně kladené otázky týkající se používání služby Migrace databáze Azure spolu se souvisejícími odpověďmi.

## <a name="overview"></a>Přehled

**Otázka: Co je služba migrace databáze Azure?**
Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů na datové platformy Azure s minimálními prostoji. Služba je v současné době v obecné dostupnosti, s pokračujícím úsilím o rozvoj zaměřených na:

* Spolehlivost a výkon.
* Iterativní přidání párů zdroj-cíl.
* Pokračující investice do migrace bez tření.

**Otázka: Jaké páry zdroje a cíle služba Migrace databáze Azure aktuálně podporuje?**
Služba aktuálně podporuje různé páry zdroj/cíl nebo scénáře migrace. Úplný seznam stavu každého dostupného scénáře migrace najdete v článku [Stav scénářů migrace podporovaných službou Migrace databáze Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Další scénáře migrace jsou ve verzi Preview a vyžadují odeslání nominace prostřednictvím webu DMS Preview. Úplný seznam scénářů ve verzi Preview a přihlášení k účasti v jedné z těchto nabídek najdete na [webu DMS Preview](https://aka.ms/dms-preview/).

**Otázka: Jaké verze SQL Serveru služba Azure Database Migration Service podporuje jako zdroj?**
Při migraci z SQL Serveru jsou podporované zdroje pro službu Migrace databáze Azure SQL Server 2005 až SQL Server 2019.

**Otázka: Jaký je rozdíl mezi offline a migrací online, když používáte Službu migrace databáze Azure?**
Službu migrace databáze Azure můžete použít k provádění offline a online migrací. Při *offline* migraci se prostoje aplikací spustí při spuštění migrace. Při migraci *online* je prostoje omezena na dobu, kterou je třeba na konci migrace zkrátit. Doporučujeme otestovat offline migraci a určit, jestli je výpadek přijatelný. Pokud není, proveďte online migraci.

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce [s cenami](https://azure.microsoft.com/pricing/details/database-migration/) služby Azure Database Migration Service.

**Otázka: Jak azure database migration service porovnání s jinými nástroji pro migraci databáze Microsoft, jako je například Pomocník pro migraci databáze (DMA) nebo SQL Server Migration Assistant (SSMA)?**
Služba Migrace databáze Azure je upřednostňovanou metodou pro migraci databáze do Microsoft Azure ve velkém měřítku. Další podrobnosti o tom, jak azure database migration service porovnává s jinými nástroji pro migraci databáze Microsoftu a doporučení ohledně používání služby pro různé scénáře, najdete v blogu, který [zveřejňuje různé nástroje a služby pro migraci databází microsoftu](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**Otázka: Jak azure database migration service porovnání s nabídkou Migrace Azure?**
Migrace Azure pomáhá s migrací místních virtuálních počítačů do Azure IaaS. Služba vyhodnocuje vhodnost migrace a velikost na základě výkonu a poskytuje odhady nákladů pro spouštění místních virtuálních počítačů v Azure. Migrace Azure je užitečná pro migrace místních úloh založených na virtuálních počítačích na azure iaas do virtuálních aplikací Azure IaaS. Na rozdíl od služby Migrace databáze Azure však Migrace Azure není specializovaná nabídka služby migrace databáze pro relační databázové platformy Azure PaaS, jako je Azure SQL Database nebo Azure SQL Database Managed Instance.

## <a name="setup"></a>Nastavení

**Otázka: Jaké jsou předpoklady pro použití služby migrace databáze Azure?**
Existuje několik předpokladů, které jsou nutné k zajištění, že služba Migrace databáze Azure běží hladce při provádění migrace databáze. Některé požadavky platí pro všechny scénáře (páry cíl zdroj) podporované službou, zatímco jiné požadavky jsou jedinečné pro konkrétní scénář.

Mezi požadavky služby Azure Database Migration Service, které jsou společné ve všech podporovaných scénářích migrace, patří:

* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.

Seznam všech předpokladů požadovaných k soutěži konkrétních scénářů migrace pomocí služby Migrace databáze Azure najdete v souvisejících kurzech v [dokumentaci](https://docs.microsoft.com/azure/dms/dms-overview) ke službě Migrace databáze Azure v docs.microsoft.com.

**Otázka: Jak najdu IP adresu pro službu Migrace databáze Azure, abych mohl vytvořit seznam povolených pravidel brány firewall používaných pro přístup ke zdrojové databázi pro migraci?**
Možná budete muset přidat pravidla brány firewall, která povolují službě Migrace databáze Azure přístup ke zdrojové databázi pro migraci. IP adresa služby je dynamická, ale pokud používáte ExpressRoute, je tato adresa soukromě přiřazena vaší podnikovou sítí. Nejjednodušší způsob, jak identifikovat příslušnou IP adresu, je podívat se ve stejné skupině prostředků jako zřízený prostředek služby Azure Database Migration Service a najít přidružené síťové rozhraní. Obvykle název prostředku síťového rozhraní začíná předponou síťové ho sponou a následuje jedinečný znak a číselná řada, například NIC-jj6tnztnmarpsskr82rbndyp. Výběrem tohoto prostředku síťového rozhraní uvidíte IP adresu, která musí být zahrnuta do seznamu povolených na stránce portálu Azure S přehledem prostředků.

Může být také nutné zahrnout zdroj portu, který SQL Server naslouchá v seznamu povolených položek. Ve výchozím nastavení je port 1433, ale zdrojový SQL Server může být nakonfigurován tak, aby naslouchal i na jiných portech. V takovém případě je třeba zahrnout tyto porty do seznamu povolených také. Port, na který sql server naslouchá, můžete určit pomocí dotazu dynamického zobrazení správy:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Můžete také určit port, který SQL Server naslouchá dotazem na protokol chyb serveru SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Otázka: Jak nastavím virtuální síť Microsoft Azure?**
Zatímco několik výukových programů společnosti Microsoft, které vás mohou procházet procesem nastavení virtuální sítě, se v článku [Virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)zobrazí oficiální dokumentace.

## <a name="usage"></a>Využití

**Otázka: Jaký je souhrn kroků potřebných k použití služby Migrace databáze Azure k provedení migrace databáze?**
Během typické jednoduché migrace databáze:

1. Vytvořte cílovou databázi( databáze).
2. Vyhodnoťte zdrojovou databázi(y).
    * U homogenních migrací vyhodnoťte stávající databáze pomocí [dma](https://www.microsoft.com/download/details.aspx?id=53595).
    * U heterogenních migrací (ze zdrojů konkurence) vyhodnoťte stávající databáze pomocí [ssma](https://aka.ms/get-ssma). SSMA také použít k převodu databázových objektů a migraci schématu do cílové platformy.
3. Vytvořte instanci služby Azure Database Migration Service.
4. Vytvořte projekt migrace určující zdrojové databáze, cílové databáze a tabulky, které chcete migrovat.
5. Spusťte plné zatížení.
6. Vyberte následné ověření.
7. Proveďte ruční přepnutí produkčního prostředí do nové cloudové databáze.

## <a name="troubleshooting-and-optimization"></a>Řešení potíží a optimalizace

**Otázka: Nastavuji projekt migrace v systému DMS a mám potíže s připojením ke zdrojové databázi. Co mám dělat?**
Pokud máte potíže s připojením ke zdrojovému databázovému systému při práci na migraci, vytvořte virtuální počítač ve virtuální síti, se kterou nastavíte instanci DMS. Ve virtuálním počítači byste měli být schopni spustit test připojení, například pomocí souboru UDL k testování připojení k SERVERU SQL nebo stahování Robo 3T pro testování připojení MongoDB. Pokud test připojení proběhne úspěšně, neměli byste mít problém s připojením ke zdrojové databázi. Pokud test připojení není úspěšný, obraťte se na správce sítě.

**Otázka: Proč je moje služba migrace databáze Azure nedostupná nebo zastavená?**
Pokud uživatel explicitně zastaví službu Migrace databáze Azure (DMS) nebo pokud je služba neaktivní po dobu 24 hodin, bude služba v zastaveném nebo automaticky pozastaveném stavu. V každém případě bude služba nedostupná a ve stavu zastaveno.  Chcete-li obnovit aktivní migrace, restartujte službu.

**Otázka: Existují nějaká doporučení pro optimalizaci výkonu služby Migrace databáze Azure?**
Pomocí služby můžete provést několik věcí, které urychlí migraci databáze:

* Při vytváření instance služby použijte úroveň oceňování pro univerzální využití procesoru, která službě umožní využívat více virtuálních procesorů pro paralelizaci a rychlejší přenos dat.
* Dočasně vertikálně navýšit kapacitu vaší azure SQL database cílinstance na úroveň Premium Skladové položky během operace migrace dat minimalizovat omezení Azure SQL Database, které mohou mít vliv na aktivity přenosu dat při použití nižší úrovně skladové jednotky.

## <a name="next-steps"></a>Další kroky

Přehled služby migrace databáze Azure a místní dostupnosti najdete v článku [Co je služba migrace databáze Azure](dms-overview.md).
