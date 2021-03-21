---
title: Nejčastější dotazy – Azure Database Migration Service
description: Nejčastější dotazy týkající se použití Azure Database Migration Service k provedení migrace databáze.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4d65c97bfccd223453583b25ee0586c5bc0b1ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091451"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Nejčastější dotazy týkající se použití Azure Database Migration Service

V tomto článku jsou uvedené Nejčastější dotazy týkající se použití Azure Database Migration Service spolu se souvisejícími odpověďmi.

## <a name="overview"></a>Přehled

**Č. Co je Azure Database Migration Service?**
Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky. Služba je aktuálně obecně dostupná a průběžné vývojové úsilí se zaměřuje na:

* Spolehlivost a výkon.
* Iterativní přidání párů zdroj-cíl.
* Pokračující investice do migrace bez problémů.

**Č. Které páry zdroj/cíl Azure Database Migration Service aktuálně podporují?**
Služba aktuálně podporuje nejrůznější páry zdroj/cíl nebo scénáře migrace. Úplný seznam stavů každého dostupného scénáře migrace najdete v článku [o stavu scénářů migrace podporovaných Azure Database Migration Service](./resource-scenario-status.md).

Další scénáře migrace jsou ve verzi Preview a vyžadují odeslání jmenování prostřednictvím webu DMS Preview. Úplný seznam scénářů ve verzi Preview a o tom, jak se zaregistrovat k účasti v jedné z těchto nabídek, najdete na [webu DMS Preview](https://aka.ms/dms-preview/).

**Č. Jaké verze SQL Server Azure Database Migration Service podporují jako zdroj?**
Při migraci z SQL Server jsou podporované zdroje pro Azure Database Migration Service SQL Server 2005 až SQL Server 2019.

**Otázka: při použití Azure Database Migration Service, jaký je rozdíl mezi offline a online migrací?**
Pomocí Azure Database Migration Service můžete provádět offline a online migrace. Při *offline* migraci se spustí výpadek aplikace při spuštění migrace. V případě *online* migrace se doba výpadku na konci migrace omezí na čas, který se má vyjímat. Doporučujeme otestovat offline migraci a určit, jestli je výpadek přijatelný. Pokud není, proveďte online migraci.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/database-migration/) Azure Database Migration Service.

**Č. Jak Azure Database Migration Service porovnat s jinými nástroji pro migraci databáze Microsoft, jako je třeba databáze Pomocník s migrací (DMA) nebo Pomocník s migrací SQL Serveru (SSMA)?**
Azure Database Migration Service je upřednostňovanou metodou migrace databáze pro Microsoft Azure ve velkém měřítku. Podrobnější informace o tom, jak Azure Database Migration Service porovnává s dalšími nástroji pro migraci databáze Microsoft a doporučení týkající se používání služby pro různé scénáře, najdete v příspěvku na blogu, který [rozlišuje služby a nástroje pro migraci databáze od Microsoftu](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**Č. Jak Azure Database Migration Service porovnat s nabídkou Azure Migrate?**
Azure Migrate pomáhá s migrací místních virtuálních počítačů do Azure IaaS. Služba posuzuje vhodnost migrace a velikost na základě výkonu a poskytuje odhad nákladů na provoz místních virtuálních počítačů v Azure. Azure Migrate je užitečná pro migrace z místních virtuálních počítačů na virtuální počítače Azure s IaaS do virtuálních počítačů s využitím virtuálních počítačů. Ale na rozdíl od Azure Database Migration Service Azure Migrate není specializovaná služba migrace databáze pro platformy Azure PaaS relačních databází, jako je například Azure SQL Database nebo spravovaná instance Azure SQL.

**Č. Ukládá Database Migration Service zákaznická data?**
No. Database Migration Service neukládají zákaznická data.

## <a name="setup"></a>Nastavení

**Č. Jaké jsou požadavky na používání Azure Database Migration Service?**
K zajištění plynulého chodu Azure Database Migration Service při provádění migrace databáze je nutné provést několik požadovaných požadavků. Některé požadavky platí ve všech scénářích (páry zdroj-cíl) podporované službou, zatímco jiné požadavky jsou jedinečné pro konkrétní scénář.

Mezi všechny podporované scénáře migrace Azure Database Migration Service společné předpoklady, které zahrnují nutnost:

* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala port 443 pro ServiceTags ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.

Seznam všech požadavků požadovaných k konkurenci konkrétních scénářů migrace pomocí Azure Database Migration Service najdete v souvisejících kurzech v [dokumentaci](./dms-overview.md) k Azure Database Migration Service na docs.Microsoft.com.

**Č. Návody najít IP adresu pro Azure Database Migration Service, aby bylo možné vytvořit seznam povolených pravidel brány firewall používaných pro přístup ke zdrojové databázi pro migraci?**
Možná budete muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojové databázi pro migraci. IP adresa pro tuto službu je dynamická, ale pokud používáte ExpressRoute, tato adresa je soukromá a přiřazuje ji podnikovou síť. Nejjednodušší způsob, jak identifikovat příslušnou IP adresu, je vyhledat ve stejné skupině prostředků jako zřízené Azure Database Migration Service prostředku a najít tak přidružené síťové rozhraní. Obvykle název prostředku síťového rozhraní začíná předponou síťové karty a následuje jedinečným znakem a sekvencí, například síťový adaptér jj6tnztnmarpsskr82rbndyp. Výběrem tohoto prostředku síťového rozhraní můžete zobrazit IP adresu, která musí být zahrnutá do seznamu povolených položek na stránce Přehled prostředků Azure Portal.

Je také možné, že budete muset zahrnout zdroj portu, který SQL Server naslouchá na seznamu povolených. Ve výchozím nastavení je to port 1433, ale zdrojová SQL Server může být nakonfigurována tak, aby naslouchala i na dalších portech. V takovém případě je potřeba zahrnout tyto porty i na seznam povolených. Můžete určit port, na kterém SQL Server naslouchá, pomocí dotazu zobrazení dynamické správy:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Můžete také určit port, který SQL Server naslouchá, pomocí dotazování protokolu chyb SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Č. Návody nastavit Microsoft Azure Virtual Network?**
I když máte několik kurzů Microsoftu, které vás provedou procesem nastavení virtuální sítě, zobrazí se oficiální dokumentace v článku [Virtual Network Azure](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Využití

**Č. Co je souhrn kroků vyžadovaných k provedení migrace databáze pomocí Azure Database Migration Service?**
Během typické migrace databáze postupujte takto:

1. Vytvořte cílové databáze (y).
2. Vyhodnoťte vaše zdrojové databáze.
    * Pro homogenní migrace vyhodnoťte existující databáze pomocí [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Pro heterogenní migrace (z konkurenčních zdrojů) vyhodnoťte existující databáze pomocí [SSMA](/sql/ssma/sql-server-migration-assistant). K převodu databázových objektů a migraci schématu na cílovou platformu použijete také SSMA.
3. Vytvořte instanci služby Azure Database Migration Service.
4. Vytvořte projekt migrace určující zdrojové databáze, cílové databáze (y) a tabulky, které chcete migrovat.
5. Spusťte úplné načtení.
6. Vyberte následné ověření.
7. Proveďte ruční přepnutí produkčního prostředí do nové cloudové databáze.

## <a name="troubleshooting-and-optimization"></a>Řešení potíží a optimalizace

**Č. Jsem v DMS nastavil (a) projekt migrace a mám potíže s připojením ke zdrojové databázi. Co mám dělat?**
Pokud máte při práci na migraci problémy s připojením k vašemu zdrojovému databázovému systému, vytvořte virtuální počítač ve stejné podsíti virtuální sítě, se kterou jste nastavili instanci DMS. Na virtuálním počítači byste měli být schopni spustit test připojení, například použít soubor UDL k otestování připojení k SQL Server nebo stažení 3Tu Robo pro testování připojení MongoDB. Pokud je test připojení úspěšný, neměli byste mít problém s připojením ke zdrojové databázi. Pokud se test připojení nezdařil, obraťte se na správce sítě.

**Č. Proč je můj Azure Database Migration Service nedostupný nebo zastavený?**
Pokud uživatel explicitně zastaví Azure Database Migration Service (DMS) nebo pokud je služba neaktivní po dobu 24 hodin, služba bude v zastaveném nebo automaticky pozastaveném stavu. V každém případě nebude služba k dispozici a je ve stavu Zastaveno.  Chcete-li obnovit aktivní migrace, restartujte službu.

**Č. Existují nějaká doporučení pro optimalizaci výkonu Azure Database Migration Service?**
K urychlení migrace databáze pomocí služby můžete použít několik věcí:

* Při vytváření instance služby použijte cenovou úroveň Pro obecné účely více PROCESORů, aby služba mohla využívat více vCPU k paralelnímu a rychlejšímu přenosu dat.
* Dočasné navýšení kapacity Azure SQL Database cílovou instanci na SKU úrovně Premium během operace migrace dat, aby se minimalizovalo Azure SQL Database omezení, které může ovlivnit aktivity přenosu dat při použití SKU nižší úrovně.

## <a name="next-steps"></a>Další kroky

Přehled Azure Database Migration Service a regionální dostupnosti najdete v článku [co je to Azure Database Migration Service](dms-overview.md).
