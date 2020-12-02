---
title: Pokyny k výkonu pro SQL Server v Azure | Microsoft Docs
description: Poskytuje pokyny pro optimalizaci výkonu SQL Server v Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cff67dde7cfe9e015cd25b26811410ce6e686e9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462547"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Průvodce výkonem SQL Serveru ve službě Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje pokyny pro optimalizaci SQL Serverho výkonu v Microsoft Azure Virtual Machines.

## <a name="overview"></a>Přehled

Při spouštění SQL Server v Azure Virtual Machines doporučujeme, abyste dál používali stejné možnosti optimalizace výkonu databáze, které platí pro SQL Server v místních serverových prostředích. Výkon relační databáze ve veřejném cloudu však závisí na řadě faktorů, jako je velikost virtuálního počítače nebo konfigurace datových disků.

[SQL Server Image zřízené v Azure Portal](sql-vm-create-portal-quickstart.md) postupují podle obecných osvědčených [postupů konfigurace](storage-configuration.md)úložiště. Po zřízení zvažte použití dalších optimalizací popsaných v tomto článku. Založte své možnosti na vaše úlohy a ověřte je prostřednictvím testování.

> [!TIP]
> Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tento článek se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud vaše úloha je méně náročná, možná nebudete potřebovat každou níže uvedenou optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

## <a name="quick-checklist"></a>Rychlý kontrolní seznam

Následuje rychlý kontrolní seznam pro optimální výkon SQL Server v Azure Virtual Machines:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) | – Používejte velikosti virtuálních počítačů 4 nebo více vCPU, jako je [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)nebo [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) nebo vyšší. <br/><br/> – Použijte [paměťově optimalizované](../../../virtual-machines/sizes-memory.md) velikosti virtuálních počítačů pro dosažení optimálního výkonu SQL Server úloh. <br/><br/> – [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) Series, [M-](../../../virtual-machines/m-series.md)a [Mv2-](../../../virtual-machines/mv2-series.md) Series nabízí optimální poměr mezi pamětí a Vcore potřebný pro úlohy OLTP. Oba virtuální počítače řady M nabízejí nejvyšší poměr paměti k vCore, který je potřeba pro důležité úlohy, a je taky ideální pro úlohy datového skladu. <br/><br/> – Pro důležité úlohy a úlohy datového skladu může být vyžadován vyšší poměr vCore paměti. <br/><br/> – Využití imagí virtuálních počítačů Azure Marketplace jako nastavení SQL Server a možnosti úložiště jsou nakonfigurovány pro optimální výkon SQL Server. <br/><br/> – Shromážděte charakteristiky výkonu cílového zatížení a použijte je k určení vhodné velikosti virtuálních počítačů pro vaši firmu.|
| [Storage](#storage-guidance) | – Podrobné testování výkonu SQL Server v Azure Virtual Machines pomocí srovnávacích testů TPC-E a TPC_C najdete v blogu věnovaném [optimalizaci výkonu OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Použijte [prémiové SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) pro nejlepší ceny a výkonnostní výhody. Nakonfigurujte [mezipaměť jen pro čtení](../../../virtual-machines/premium-storage-performance.md#disk-caching) pro datové soubory a žádnou mezipaměť pro soubor protokolu. <br/><br/> – Použijte [disky Ultra](../../../virtual-machines/disks-types.md#ultra-disk) , pokud zatížení vyžaduje méně než 1 – MS úložiště. Další informace najdete v tématu [migrace na disk s Ultra](storage-migrate-to-ultradisk.md) . <br/><br/> – Shromažďování požadavků na latenci úložiště pro soubory SQL Server dat, protokolů a dočasné databáze [monitorováním aplikace](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) před volbou typu disku. Pokud se vyžadují < 1 – MS úložiště, použijte Ultra disks, v opačném případě použijte disk SSD úrovně Premium. Pokud se pro soubor protokolu a ne pro datové soubory vyžadují nízké latence, [zajistěte, aby byl Ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md) v požadovaném IOPS a úrovně propustnosti jenom pro soubor protokolu. <br/><br/>  – Standardní úložiště se doporučuje jenom pro účely vývoje a testování nebo pro záložní soubory a neměla by se používat pro produkční úlohy. <br/><br/> – Udržujte [účet úložiště](../../../storage/common/storage-account-create.md) a SQL Server virtuální počítač ve stejné oblasti.<br/><br/> – Zakažte v účtu úložiště [geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) Azure (geografickou replikaci).  |
| [Disky](#disks-guidance) | – Použijte minimálně 2 [disky SSD úrovně Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 pro soubor protokolu a 1 pro datové soubory). <br/><br/> – Pro úlohy, které vyžadují < 1-MS v/v/v, povolte akcelerátor zápisu pro řady M a zvažte použití SSD úrovně Ultra disků pro řady ES a DS. <br/><br/> -Povolit [ukládání do mezipaměti jen pro čtení](../../../virtual-machines/premium-storage-performance.md#disk-caching) na discích, které hostují datové soubory.<br/><br/> – Přidání dalších 20% kapacity IOPS/propustnosti, než kolik jich vyžaduje při [konfiguraci úložiště pro SQL Server dat, protokolů a souborů tempdb](storage-configuration.md) <br/><br/> – Nepoužívejte operační systém nebo dočasné disky pro úložiště databáze nebo protokolování.<br/><br/> – Nepovolujte ukládání do mezipaměti na discích hostujících soubor protokolu.  **Důležité**: při změně nastavení mezipaměti pro disk Azure Virtual Machines zastavte službu SQL Server.<br/><br/> – Proložením několika datových disků Azure získáte vyšší propustnost úložiště.<br/><br/> – Formát s dokumentovanými velikostmi přidělení. <br/><br/> Na místní jednotku SSD umístěte databázi TempDB `D:\` pro klíčové SQL Server úlohy (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z Azure Portal nebo šablon Azure pro rychlý Start a [umístíte dočasnou databázi na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), pak nebudete potřebovat žádnou další akci. pro všechny ostatní případy postupujte podle pokynů v blogu pro  [použití SSD k uložení databáze tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , aby nedocházelo k chybám po restartování. Pokud kapacita místního disku není dostatečná pro velikost dočasné databáze, umístěte dočasnou databázi [do fondu úložiště](../../../virtual-machines/premium-storage-performance.md) , který je umístěn na discích SSD úrovně Premium s [ukládáním do mezipaměti jen pro čtení](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |-Povolit kompresi stránky databáze.<br/><br/> – Povolí okamžitou inicializaci souborů pro datové soubory.<br/><br/> – Omezuje automatické zvětšování databáze.<br/><br/> -Zakázat automatického zmenšení databáze.<br/><br/> – Přesuňte všechny databáze na datové disky, včetně systémových databází.<br/><br/> – SQL Server přesunutí adresářů protokolů chyb a trasovacích souborů do datových disků.<br/><br/> – Nakonfigurujte výchozí zálohu a umístění souborů databáze.<br/><br/> - [Povolí uzamčené stránky v paměti](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> – Vyhodnoťte a nainstalujte [nejnovější kumulativní aktualizace](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) pro nainstalovanou verzi SQL Server. |
| [Specifické pro jednotlivé funkce](#feature-specific-guidance) | – Zálohování přímo do Azure Blob Storage.<br/><br/>– Použijte [zálohy snímků souborů](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) pro databáze větší než 12 TB. <br/><br/>– Použijte více souborů dočasné databáze, 1 soubor na jádro a až 8 souborů.<br/><br/>-Nastavit maximální velikost paměti serveru v 90% nebo až 50 GB zbývajících pro operační systém. <br/><br/>– Povolte měkký NUMA. |


<br/>
Další informace o *tom, jak* a *Proč* provádět tyto optimalizace, najdete v podrobnostech a pokynech uvedených v následujících částech.
<br/><br/>

## <a name="getting-started"></a>Začínáme

Pokud vytváříte novou SQL Server na virtuálním počítači Azure a nemigrujete aktuální zdrojový systém, vytvořte nový virtuální počítač s SQL Server na základě požadavků dodavatele.  Požadavky na dodavatele pro SQL Server virtuální počítač jsou stejné jako ty, které byste nasadili místně. 

Pokud vytváříte nový virtuální počítač SQL Server s novou aplikací vytvořenou pro Cloud, můžete snadno nastavit velikost vašich SQL Server virtuálních počítačů jako vývoj vašich požadavků na data a využití.
Spusťte vývojová prostředí s nižší vrstvou D-Series, B-Series nebo Av2-Series a rozšiřte prostředí v čase. 

Doporučené minimum pro produkční prostředí OLTP je 4 vCore, 32 GB paměti a poměr paměti až vCore 8. U nových prostředí začněte se 4 vCore počítači a škálujte na 8, 16, 32 virtuální jádra nebo víc, když se změní vaše požadavky na data a výpočetní prostředky. U OLTP propustnosti cílí na cílové SQL Server virtuální počítače, které mají 5000 vstupně-výstupních operací pro každou vCore. 

Pomocí SQL Server imagí virtuálního počítače Marketplace s konfigurací úložiště na portálu. Díky tomu bude snazší vytvořit fondy úložiště potřebné k získání velikosti, IOPS a propustnosti potřebných pro vaše úlohy. Je důležité zvolit SQL Server virtuální počítače, které podporují Storage úrovně Premium a Storage úrovně Premium. Další informace najdete v části [úložiště](#storage-guidance) . 

SQL Server datový sklad a kritická prostředí se často musí škálovat nad rámec 8 paměti až do vCore poměru. Pro střední prostředí můžete zvolit poměr 16 jader k paměti a poměr od 32 do paměti pro rozsáhlejší prostředí datového skladu. 

SQL Server prostředí datového skladu často využívají paralelní zpracování větších počítačů. Z tohoto důvodu jsou řady M-Series a Mv2-Series silné možnosti pro rozsáhlejší prostředí datového skladu.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Použijte konfiguraci vCPU a paměti ze zdrojového počítače jako základní hodnotu pro migraci aktuální místní SQL Server databáze do SQL Server na virtuálních počítačích Azure. Přepněte si základní licenci do Azure, abyste mohli využít výhod [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) a ušetřit náklady na SQL Server licencování.

**Microsoft doporučuje pro produkční SQL Server zátěžový poměr paměti a 8 jako výchozí bod.** Pro neprodukční úlohy jsou přijatelné i menší poměry. 

Vyberte [paměťově optimalizovanou](../../../virtual-machines/sizes-memory.md), [obecné účely](../../../virtual-machines/sizes-general.md), [optimalizované úložiště](../../../virtual-machines/sizes-storage.md)nebo velikost virtuálního počítače s [omezením vCore](../../../virtual-machines/constrained-vcpu.md) , která je nejoptimální pro SQL Server výkon na základě vaší úlohy (OLTP nebo datového skladu). 

### <a name="memory-optimized"></a>Optimalizované z hlediska paměti

[Paměťově optimalizované velikosti virtuálních počítačů](../../../virtual-machines/sizes-memory.md) jsou primárním cílem pro SQL Server virtuální počítače a doporučené volby od Microsoftu. Paměťově optimalizované virtuální počítače nabízí silnější poměry paměti k procesoru a možnosti střední až velké mezipaměti. 

#### <a name="m-and-mv2-series"></a>Řady M a Mv2

[Řada M-Series](../../../virtual-machines/m-series.md) nabízí počty Vcore a paměť pro některé z největších SQL Server úloh.  

[Mv2-Series](../../../virtual-machines/mv2-series.md) má nejvyšší počet vCoreů a paměť a doporučuje se pro důležité úlohy datového skladu. Instance Mv2-Series jsou paměťově optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu velkých databází v paměti a úloh s vysokým poměrem paměti na procesor, který je ideální pro servery relačních databází, velké mezipaměti a analýzu v paměti.

[Standard_M64ms](../../../virtual-machines/m-series.md) má například poměr 28 paměti k vCore.

Některé funkce řady M a Mv2-Series atraktivní pro SQL Server výkon zahrnují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md) a podporu pro [ukládání do mezipaměti služby Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) , podpora [Ultra disků](../../../virtual-machines/disks-enable-ultra-ssd.md) a [akcelerace zápisu](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Edsv4-Series

[Edsv4-Series](../../../virtual-machines/edv4-edsv4-series.md) je určená pro aplikace náročné na paměť. Tyto virtuální počítače mají rozsáhlou kapacitu místního úložiště SSD, silný počet IOPS na místní disk, až 504 GiB paměti RAM a vylepšený výpočetní výkon v porovnání s předchozími Ev3/Esv3 formáty s virtuálními počítači Gen2. U těchto virtuálních počítačů je skoro konzistentní poměr paměti k vCore, který je ideální pro standardní SQL Server úlohy. 

Tato série virtuálních počítačů je ideální pro podnikové aplikace náročné na paměť a aplikace, které využívají nízkou latenci, vysoce rychlé místní úložiště.

Virtuální počítače řady Edsv4-series podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md)a [ukládání do mezipaměti služby Storage úrovně Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2-Series 11-15

[DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) má stejnou konfiguraci paměti a disku jako předchozí řada D-Series. Tato série má konzistentní poměr paměti k procesoru 7 ve všech virtuálních počítačích. 

[DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) podporuje [úložiště Premium Storage](../../../virtual-machines/premium-storage-performance.md) a [Storage úrovně Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), což se důrazně doporučuje pro optimální výkon.

### <a name="general-purpose"></a>Pro obecné účely

[Velikosti virtuálních počítačů pro obecné účely](../../../virtual-machines/sizes-general.md) jsou navržené tak, aby poskytovaly vyvážené poměry paměti až Vcore pro menší úlohy na úrovni vstupu, jako je vývoj a testování, webové servery a menší databázové servery. 

Z důvodu menšího poměru paměti k vCore s virtuálními počítači pro obecné účely je důležité pečlivě monitorovat čítače výkonu založené na paměti, abyste zajistili, že SQL Server dokáže získat paměť vyrovnávací paměti, kterou potřebuje. Další informace najdete v části [směrný plán výkonu paměti](#memory) . 

Vzhledem k tomu, že počáteční doporučení pro produkční úlohy je poměr vCore k paměti 8, minimální doporučená konfigurace pro virtuální počítač s obecným účelem se systémem SQL Server je 4 vCPU a 32 GB paměti. 

#### <a name="ddsv4-series"></a>Řada Ddsv4

[Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) nabízí uspokojivou kombinaci vCPU, paměti a dočasného disku, ale s menší podporou Vcore paměti. 

Virtuální počítače s Ddsv4 zahrnují nižší latenci a vyšší rychlost místního úložiště.

Tyto počítače jsou ideální pro souběžná nasazení SQL a aplikace, která vyžadují rychlý přístup k dočasnému úložišti a relačním databázím oddělení. Na všech virtuálních počítačích v této sérii je standardní vCore poměr paměti a 4. 

Z tohoto důvodu se doporučuje využít D8ds_v4 jako počáteční virtuální počítač v této sérii s 8 virtuální jádra a 32 GB paměti. Největším počítačem je D64ds_v4, který má 64 virtuální jádra a 256 GB paměti.

Virtuální počítače [řady Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md) a [úložiště Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) nemá poměr paměti až Vcore, který se doporučuje pro SQL Server úlohy. V takovém případě zvažte použití těchto virtuálních počítačů pouze pro menší úlohy aplikací a vývoje.

#### <a name="b-series"></a>Řady B-Series

Velikosti virtuálních počítačů s [nízkou](../../../virtual-machines/sizes-b-series-burstable.md) zátěží jsou ideální pro úlohy, které nepotřebují konzistentní výkon, jako je například kontrola konceptu a velmi malé aplikace a vývojové servery. 

Většina velikostí virtuálních počítačů [řady B-Series](../../../virtual-machines/sizes-b-series-burstable.md) má Vcore poměr paměti 4. Největší z těchto počítačů je [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) s 20 virtuální jádra a 80 GB paměti.

Tato řada je jedinečná, protože aplikace mají možnost rozrůznit se v pracovní době s rozdílnými kredity **, které se** liší podle velikosti počítačů. 

Po vyčerpání kreditů se virtuální počítač vrátí do výkonu základního počítače.

Výhoda B-Series je úspory výpočtů, které byste mohli dosáhnout v porovnání s jinými velikostmi virtuálních počítačů v jiných řadách, zejména pokud potřebujete výpočetní výkon v průběhu dne.

Tato série podporuje [Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale **nepodporuje** [mezipaměť Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> [Řady B-Series](../../../virtual-machines/sizes-b-series-burstable.md) neobsahují poměr Vcore k paměti 8, který je doporučený pro úlohy SQL Server. V takovém případě zvažte použití těchto virtuálních počítačů pro menší aplikace, webové servery a vývojové úlohy.

#### <a name="av2-series"></a>Av2-series

Virtuální počítače [řady Av2-Series](../../../virtual-machines/av2-series.md) jsou nejvhodnější pro úlohy na úrovni vstupu, jako je vývoj a testování, webové servery s nízkým provozem, malé až středně střední databáze aplikací a testování konceptů.

Pro tyto tři virtuální počítače mají dobrý poměr paměti k virtuální jádra (virtuální jádra a 16GBs paměti), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuální jádra a 32GBs paměti) a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 64GBs a vCore paměti). [Standard_A2m_v2](../../../virtual-machines/av2-series.md) 

Tyto virtuální počítače jsou osvědčenými možnostmi pro menší vývojové a testovací SQL Server počítače. 

[Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 Vcore může být vhodnou možností pro malé aplikační a webové servery.

> [!NOTE] 
> Av2 Series nepodporuje Prémiové úložiště, a proto se nedoporučuje pro produkční SQL Server úlohy ani s virtuálními počítači, které mají poměr paměti k vCore 8.

### <a name="storage-optimized"></a>Optimalizované z hlediska úložiště

[Velikosti virtuálních počítačů optimalizované pro úložiště](../../../virtual-machines/sizes-storage.md) jsou určené pro konkrétní případy použití. Tyto virtuální počítače jsou speciálně navržené pomocí optimalizované propustnosti disku a vstupně-výstupních operací. Tato série virtuálních počítačů je určená pro scénáře s velkými objemy dat, datové sklady a velké transakční databáze. 

#### <a name="lsv2-series"></a>Řada Lsv2

[Lsv2-Series](../../../virtual-machines/lsv2-series.md) nabízí vysokou propustnost, nízkou latenci a místní úložiště NVMe. Virtuální počítače řady Lsv2-Series jsou optimalizované tak, aby používaly místní disk na uzlu připojeném přímo k virtuálnímu počítači namísto použití trvalých datových disků. 

Tyto virtuální počítače jsou silné možnosti pro úlohy s velkým objemem dat, datového skladu, generování sestav a ETL. Vysoká propustnost a IOPs místního úložiště NVMe je vhodný případ pro zpracování souborů, které se načtou do vaší databáze, a dalších scénářů, ve kterých se zdrojová data dají znovu vytvořit ze zdrojového systému nebo jiných úložišť, jako je Azure Blob Storage nebo Azure Data Lake. [Lsv2-Series](../../../virtual-machines/lsv2-series.md) Virtuální počítače můžou také zvýšit výkon svého disku po dobu až 30 minut.

Tyto virtuální počítače mají velikost od 8 do 80 vCPU s 8 GiB paměti na vCPU a pro každých 8 vCPU, 1,92 TB z NVMe SSD. To znamená, že pro největší virtuální počítač této řady je [L80s_v2](../../../virtual-machines/lsv2-series.md), že je k dispozici 80 vCPU a 640 BIB paměti s 10x 1.92 TB NVMe úložiště.  Mezi všemi těmito virtuálními počítači je konzistentní vCore poměr paměti a 8.

Úložiště NVMe je dočasné, což znamená, že při restartování virtuálního počítače dojde ke ztrátě dat na těchto discích.

Lsv2 a ls series podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md), ale úložiště Premium Storage nepatří do mezipaměti. Vytvoření místní mezipaměti pro zvýšení IOPs není podporováno. 

> [!WARNING]
> Ukládání datových souborů na dočasné úložiště NVMe může způsobit ztrátu dat, když se virtuální počítač oddělí. 

### <a name="constrained-vcores"></a>Omezené virtuální jádra

Vysoce výkonné SQL Server úlohy často potřebují větší množství paměti, vstupně-výstupních operací a propustnosti bez vyššího počtu vCore. 

Většina úloh OLTP jsou aplikační databáze řízené velkým počtem menších transakcí. U úloh OLTP je čtení nebo úpravy pouze malého množství dat, ale objem transakcí řízený počty uživatelů je mnohem vyšší. Je důležité mít k dispozici SQL Server paměť pro plány ukládání do mezipaměti, ukládat nedávno dostupná data a zajistit, aby fyzické čtení bylo možné rychle načíst do paměti. 

Tato OLTP prostředí vyžadují větší množství paměti, rychlé úložiště a šířku pásma I/O, která je nutná k optimálnímu výkonu. 

Azure nabízí velikosti virtuálních počítačů s [omezenými vCPU počty](../../../virtual-machines/constrained-vcpu.md), aby se zachovala Tato úroveň výkonu bez vyšších licenčních nákladů na SQL Server. 

Tato operace pomáhá řídit náklady na licencování tím, že snižuje dostupné virtuální jádra a současně zachovává stejnou šířku pásma, úložiště a I/O v nadřazeném virtuálním počítači.

Počet vCPU může být omezený na jednu čtvrtinu původní velikosti virtuálního počítače. Omezení virtuální jádra k dispozici pro virtuální počítač, dosáhne vyššího poměru paměti až vCore.

Tyto nové velikosti virtuálních počítačů mají příponu, která určuje počet aktivních vCPU, aby je bylo snazší identifikovat. 

Například [M64-32ms](../../../virtual-machines/constrained-vcpu.md) vyžaduje licencování pouze 32 SQL Server virtuální jádra s pamětí, v/v a propustností [M64ms](../../../virtual-machines/m-series.md) a [M64-16MS](../../../virtual-machines/constrained-vcpu.md) vyžaduje licencování pouze 16 virtuální jádra.  I když má [M64-16MS](../../../virtual-machines/constrained-vcpu.md) čtvrtinu SQL Server licenčních nákladů na M64ms, budou náklady na výpočetní výkon virtuálního počítače stejné.

> [!NOTE] 
> - Středně velké zátěže datového skladu můžou mít stále nárok na [omezené virtuální počítače s Vcore](../../../virtual-machines/constrained-vcpu.md), ale úlohy datového skladu se běžně charakterizují menším počtem uživatelů a procesy, které řeší větší objemy dat prostřednictvím plánů dotazů, které běží paralelně. 
> - Náklady na výpočetní výkon, včetně licencování operačního systému, budou zůstat stejné jako u nadřazeného virtuálního počítače. 

## <a name="storage-guidance"></a>Pokyny k ukládání

Podrobné testování výkonu SQL Server v Azure Virtual Machines pomocí srovnávacích testů TPC-E a TPC-C najdete v blogu věnovaném [optimalizaci OLTP výkonu](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure Blob cache s Premium SSD se doporučuje pro všechny produkční úlohy. 

> [!WARNING]
> Standardní HDD a SSD mají různou latenci a šířku pásma a jsou doporučovány jenom pro úlohy pro vývoj a testování. Produkční úlohy by měly používat Premium SSD.

Kromě toho doporučujeme vytvořit účet služby Azure Storage ve stejném datovém centru jako virtuální počítače s SQL Server, abyste snížili zpoždění přenosu. Při vytváření účtu úložiště není zaručená geografická replikace jako konzistentní pořadí zápisu na více discích. Místo toho zvažte konfiguraci SQL Server technologie zotavení po havárii mezi dvěma datovými centry Azure. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Doprovodné materiály pro disky

Na virtuálních počítačích Azure existují tři hlavní typy disků:

* **Disk s operačním** systémem: Když vytváříte virtuální počítač Azure, bude platforma k virtuálnímu počítači pro disk s operačním systémem připojovat aspoň jeden disk (označený jako jednotka **C** ). Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasný disk**: virtuální počítače Azure obsahují další disk nazvaný dočasný disk (označený jako jednotka **D**:). Toto je disk na uzlu, který lze použít pro pomocné místo.
* **Datové disky**: k virtuálnímu počítači můžete připojit také další disky jako datové disky a ty budou uloženy v úložišti jako objekty blob stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který můžete spustit a připojit jako běžící verzi operačního systému a který je označený jako jednotka **C** .

Výchozí zásady ukládání do mezipaměti na disku s operačním systémem jsou **jen pro čtení a zápis**. Pro aplikace s citlivým výkonem doporučujeme místo disku operačního systému používat datové disky. Viz část na datových discích níže.

### <a name="temporary-disk"></a>Dočasný disk

Dočasná Úložná jednotka označená jako jednotka **D** není trvale nastavená na službu Azure Blob Storage. Neukládejte soubory uživatelské databáze ani soubory protokolů transakcí uživatele na jednotce **D**:.

Pro nejdůležitější SQL Server úlohy umístěte databázi TempDB na místní `D:\` jednotku SSD (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z Azure Portal nebo šablon Azure pro rychlý Start a [umístíte dočasnou databázi na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), pak nebudete potřebovat žádnou další akci. pro všechny ostatní případy postupujte podle pokynů v blogu pro  [použití SSD k uložení databáze tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , aby nedocházelo k chybám po restartování. Pokud kapacita místního disku není dostatečná pro velikost dočasné databáze, umístěte dočasnou databázi [do fondu úložiště](../../../virtual-machines/premium-storage-performance.md) , který je umístěn na discích SSD úrovně Premium s [ukládáním do mezipaměti jen pro čtení](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Pro virtuální počítače, které podporují prémiové SSD, můžete databázi TempDB taky ukládat na disk, který podporuje Premium SSD s povoleným ukládáním do mezipaměti pro čtení.


### <a name="data-disks"></a>Datové disky

* **Použít disky SSD úrovně Premium pro data a soubory protokolu**: Pokud nepoužíváte diskové svazky, použijte dva disky SSD úrovně Premium, kde jeden disk obsahuje soubor protokolu a druhý obsahuje data. Každý disk SSD úrovně Premium poskytuje množství vstupně-výstupních operací a šířky pásma (MB/s) v závislosti na velikosti, jak je znázorněno v článku, a [Vyberte typ disku](../../../virtual-machines/disks-types.md). Pokud používáte techniku rozložení disku, například prostory úložiště, dosáhnete optimálního výkonu tím, že máte dva fondy, jeden pro soubory protokolu a druhý pro datové soubory. Pokud ale plánujete použít SQL Server instance clusterů s podporou převzetí služeb při selhání (FCI), musíte místo toho nakonfigurovat jeden fond nebo využít [prémiové sdílené soubory](failover-cluster-instance-premium-file-share-manually-configure.md) .

   > [!TIP]
   > - Výsledky testů různých konfigurací disků a úloh najdete v tomto blogovém příspěvku: [pokyny pro konfiguraci úložiště pro SQL Server v Azure Virtual Machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - Pro zajištění důležitého výkonu pro SQL servery, které potřebují ~ 50 000 IOPS, zvažte nahrazení 10 P30 disků pomocí SSD úrovně Ultra. Další informace najdete v tomto blogovém příspěvku: [rozhodující výkon s SSD úrovně Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Když zřizujete SQL Server virtuální počítač na portálu, máte možnost upravit si konfiguraci úložiště. V závislosti na konfiguraci Azure nakonfiguruje jeden nebo víc disků. Více disků je sloučeno do jednoho fondu úložiště s použitím Stripe. Data i soubory protokolů se v této konfiguraci nacházejí společně. Další informace najdete v tématu [Konfigurace úložiště pro virtuální počítače s SQL Server](storage-configuration.md).

* **Diskové svazky**: pro větší propustnost můžete přidat další datové disky a použít diskové svazky. Chcete-li zjistit počet datových disků, je nutné analyzovat počet vstupně-výstupních operací a šířky pásma požadované pro soubory protokolu a pro vaše data a soubory databáze TempDB. Všimněte si, že různé velikosti virtuálních počítačů mají různá omezení počtu vstupně-výstupních operací za sekundu a šířky pásma, viz tabulky v IOPS podle [velikosti virtuálního počítače](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Postupujte takto:

  * U systémů Windows 8/Windows Server 2012 a novějších používejte [prostory úložiště](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) s následujícími pokyny:

      1. Nastavte prokládání (velikost Stripe) na 64 KB (65 536 bajtů) pro úlohy OLTP a 256 KB (262 144 bajty) pro úlohy datového skladu, aby se předešlo dopadu na výkon v důsledku chybného zarovnání oddílu. Tato nastavení se musí nastavit pomocí PowerShellu.
      2. Nastavte počet sloupců = počet fyzických disků. Použijte PowerShell při konfiguraci více než 8 disků (ne Správce serveru uživatelského rozhraní). 

    Například následující PowerShell vytvoří nový fond úložiště s velikostí prokládání na 64 KB a počet sloupců rovný velikosti fyzického disku ve fondu úložiště:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pro Windows 2008 R2 nebo starší můžete použít dynamické disky (svazky Stripe OS) a velikost Stripe je vždycky 64 KB. Tato možnost je zastaralá od Windows 8/Windows Serveru 2012. Informace najdete v tématu věnovaném podpoře na [virtuální diskové službě přechod na rozhraní API pro správu úložiště systému Windows](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Pokud pro [SQL Server instancí clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-storage-spaces-direct-manually-configure.md)používáte [prostory úložiště s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) , musíte nakonfigurovat jeden fond. I když se na tomto jediném fondu dají vytvořit různé svazky, budou všechny sdílet stejné charakteristiky, například stejné zásady pro ukládání do mezipaměti.

  * Určete počet disků přidružených k vašemu fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace najdete v tématu [velikosti pro virtuální počítače](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte Premium SSD (scénáře pro vývoj a testování), doporučujeme přidat maximální počet datových disků, které podporuje [Velikost virtuálního počítače](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , a použít diskové svazky.

* **Zásady ukládání do mezipaměti**: Všimněte si následujících doporučení pro ukládání zásad do mezipaměti v závislosti na konfiguraci úložiště.

  * Pokud používáte samostatné disky pro data a soubory protokolů, povolte ukládání do mezipaměti pro čtení na datových discích hostujících datové soubory a datové soubory TempDB. To může mít za následek značný přínos na výkon. Nepovolujte ukládání do mezipaměti na disku, který soubor protokolu uchovává, protože to způsobí menší snížení výkonu.

  * Pokud používáte diskové obložení na jednom fondu úložiště, většina úloh bude využívat ukládání do mezipaměti při čtení. Pokud máte samostatné fondy úložiště pro soubory protokolů a dat, povolte ukládání do mezipaměti pro čtení jenom pro datové soubory ve fondu úložiště. V některých těžkých zátěžích pro zápis se může dosáhnout lepšího výkonu bez ukládání do mezipaměti. To lze určit pouze pomocí testování.

  * Předchozí doporučení se vztahují na SSD Premium. Pokud nepoužíváte prémiové SSD, nepovolujte žádné datové disky do mezipaměti.

  * Pokyny ke konfiguraci ukládání do mezipaměti disku najdete v následujících článcích. Pro model nasazení Classic (ASM) viz: [set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) a [set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Model nasazení Azure Resource Manager naleznete v tématu: [set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) a [set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Při změně nastavení mezipaměti disků Azure Virtual Machines zastavte službu SQL Server, abyste se vyhnuli možnosti poškození databáze.

* **Velikost alokační jednotky NTFS**: při formátování datového disku se doporučuje použít velikost alokační jednotky 64-KB pro data a soubory protokolů i pro databázi tempdb. Pokud je databáze TempDB umístěná na dočasném disku (D:\ jednotka) výkon získaný využitím této jednotky převyšuje nutnost velikosti alokační jednotky 64-KB. 

* **Osvědčené postupy správy disků**: při odebrání datového disku nebo změně jeho typu mezipaměti zastavte službu SQL Server během změny. Při změně nastavení ukládání do mezipaměti na disku s operačním systémem Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Když se změní nastavení mezipaměti datového disku, virtuální počítač se neukončí, ale datový disk se během změny odpojí z virtuálního počítače a pak se znovu připojí.

  > [!WARNING]
  > Nepovedlo se zastavit službu SQL Server během těchto operací může způsobit poškození databáze.


## <a name="io-guidance"></a>Doprovodné materiály k v/v

* Nejlepší výsledky s Premium SSD se dosáhnou při paralelizovat své aplikace a požadavků. Premium SSD jsou navržené pro scénáře, ve kterých je hloubka front/v v/v větší než 1, takže se vám pro sériové požadavky s jedním vláknem zobrazí malý nebo žádný nárůst výkonu (i v případě, že se jedná o náročné úložiště). To může mít vliv na výsledky testů nástroje pro analýzu výkonu, jako je například SQLIO, na jeden podproces.

* Zvažte použití [Komprese stránky databáze](/sql/relational-databases/data-compression/data-compression) , protože může pomoci zlepšit výkon úloh náročných na vstupně-výstupní operace. Komprese dat ale může zvýšit spotřebu procesoru na databázovém serveru.

* Zvažte možnost Povolit okamžitou inicializaci souborů, aby se zkrátila doba potřebná k počátečnímu přidělení souborů. Pokud chcete využít výhod okamžité inicializace souborů, udělíte účtu služby SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME a přidáte ho do zásad zabezpečení **provádět úlohy údržby multilicence** . Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (NT Service\MSSQLSERVER) se nepřidá do zásad zabezpečení **provádět úlohy údržby multilicence** . Jinými slovy, okamžitá inicializace souborů není povolená v SQL Server Image platformy Azure. Po přidání účtu služby SQL Server do zásad zabezpečení **úlohy údržby multilicence proveďte** restart služby SQL Server. Při použití této funkce se mohly vycházet z bezpečnostních důvodů. Další informace najdete v tématu [inicializace souboru databáze](/sql/relational-databases/databases/database-instant-file-initialization).

* Mějte na paměti, že **autogrow** se považuje za neočekávaný nárůst. Nespravujte svoje data a protokolujte na každodenní bázi pomocí autogrow. Pokud se používá autogrow, soubor předem Rozšiřte pomocí přepínače Size.

* Ujistěte se, že je možnost automaticky **zmenšit** zakázaná, aby nedocházelo k zbytečné režii, která může negativně ovlivnit výkon

* Přesuňte všechny databáze na datové disky, včetně systémových databází. Další informace najdete v tématu [přesunutí systémových databází](/sql/relational-databases/databases/move-system-databases).

* Přesuňte protokoly chyb SQL Server a trasovací soubory do datových disků. To se dá udělat v SQL Server Configuration Manager tak, že kliknete pravým tlačítkem na instanci SQL Server a vyberete vlastnosti. Nastavení protokolu chyb a trasovacího souboru můžete změnit na kartě **parametry spuštění** . Adresář výpisu se zadává na kartě **Upřesnit** . Následující snímek obrazovky ukazuje, kde vyhledat parametr spuštění protokolu chyb.

    ![Snímek obrazovky s chybou SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Nastavte výchozí zálohu a umístění souborů databáze. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazení nebo změna výchozích umístění pro data a soubory protokolů (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Následující snímek obrazovky ukazuje, kde provést tyto změny.

    ![Soubory protokolu a zálohování dat SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčené stránky, aby se snížila vstupně-výstupní operace a jakékoli aktivity stránkování. Další informace najdete v tématu [Povolení možnosti Uzamknout stránky v paměti (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Pokud používáte SQL Server 2012, nainstalujte aktualizaci Service Pack 1 – kumulativní aktualizace 10. Tato aktualizace obsahuje opravu špatného výkonu při vstupu a výstupu při spuštění příkazu SELECT INTO dočasnou tabulku v SQL Server 2012. Informace najdete v tomto [článku znalostní báze](https://support.microsoft.com/kb/2958012).

* Při převádění do Azure zvažte komprimaci všech datových souborů.

## <a name="feature-specific-guidance"></a>Doprovodné materiály k jednotlivým funkcím

Některá nasazení mohou dosáhnout dalších výhod výkonu s využitím pokročilejších technik konfigurace. V následujícím seznamu se vysvětlují některé funkce SQL Server, které vám pomůžou dosáhnout lepšího výkonu:

### <a name="back-up-to-azure-storage"></a>Zálohování do Azure Storage
Při provádění zálohování pro SQL Server běžící v Azure Virtual Machines můžete použít [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučuje se pro zálohování na připojené datové disky. Když provádíte zálohování nebo obnovení do nebo z Azure Storage, postupujte podle doporučení uvedených v článku [SQL Server zálohování pro osvědčené postupy a řešení potíží a obnovení ze záloh uložených v Azure Storage](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Tyto zálohy můžete také automatizovat pomocí [automatizovaného zálohování pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Před SQL Server 2012 můžete použít [nástroj SQL Server Backup do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může přispět ke zvýšení propustnosti záloh pomocí více cílů záložního Stripe.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datových souborů v Azure

Tato nová funkce, [SQL Server datových souborů v Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure), je k dispozici od SQL Server 2014. Spuštění SQL Server s datovými soubory v Azure ukazuje srovnatelné charakteristiky výkonu jako při použití datových disků Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance clusteru s podporou převzetí služeb při selhání a prostory úložiště

Pokud používáte prostory úložiště, při přidávání uzlů do clusteru na stránce **potvrzení** zrušte zaškrtnutí políčka **Přidat do clusteru veškeré oprávněné úložiště**. 

![Zrušit kontrolu oprávněného úložiště](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Pokud používáte prostory úložiště a nechcete zrušit kontrolu **Přidat do clusteru všechny opravňující úložiště**, systém Windows virtuální disky během procesu clusteringu odpojí. V důsledku toho se neobjeví ve Správci disků nebo v Průzkumníkovi, dokud se prostory úložiště z clusteru neodstraní a znovu nepřipojí přes PowerShell. Prostory úložiště seskupují více disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Několik instancí 

Při nasazování více instancí SQL Server do jednoho virtuálního počítače Vezměte v úvahu následující osvědčené postupy: 

- Nastavte maximální velikost paměti serveru pro každou instanci SQL Server a zajistěte, aby existovala zbývající paměť pro operační systém. Nezapomeňte aktualizovat omezení paměti pro instance SQL Server, pokud změníte, kolik paměti je přiděleno virtuálnímu počítači. 
- Mít samostatné logické jednotky (LUN) pro data, protokoly a databázi TempDB, protože všechny mají různé vzory úloh a nechcete je navzájem ovlivňovat. 
- Důkladně otestujte své prostředí v rámci velkých produkčních úloh, abyste se ujistili, že dokáže zvládnout kapacitu zátěže ve špičce v rámci vaší aplikace jako SLA. 

Příznaky přetížených systémů mohou zahrnovat, ale nejsou omezeny na vyčerpání pracovního vlákna, pomalých dob odezvy a/nebo v systémové paměti dispečera. 



## <a name="collect-performance-baseline"></a>Shromažďování standardních hodnot výkonu

Pro účely pokročilejšího přístupu můžete shromáždit čítače výkonu pomocí programu PerfMon/LogMan a zachytit statistiku SQL Server čekání a lépe porozumět obecným tlakům a potenciálním kritickým bodům zdrojového prostředí. 

Začněte shromažďováním výkonu procesoru, paměti, [vstupně](../../../virtual-machines/premium-storage-performance.md#iops)-výstupních operací, [propustnosti](../../../virtual-machines/premium-storage-performance.md#throughput)a [latence](../../../virtual-machines/premium-storage-performance.md#latency) zdrojové úlohy v době špičky podle [kontrolního seznamu výkonu aplikace](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Shromažďovat data během špičky, jako jsou úlohy v průběhu běžného pracovního dne, ale také i jiné procesy vysokého zatížení, jako je například každodenní zpracování, a víkendové úlohy ETL. Zvažte horizontální navýšení kapacity prostředků pro velmi náročné úlohy, jako je například zpracování na konci čtvrtletí, a po dokončení úlohy se pak provede škálování. 

Pomocí analýzy výkonu můžete vybrat [Velikost virtuálního počítače](../../../virtual-machines/sizes-memory.md) , která se může škálovat na požadavky na výkon vašich úloh.


### <a name="iops-and-throughput"></a>IOPS a propustnost

SQL Server výkon závisí silně na vstupně-výstupním subsystému. Pokud se vaše databáze nevejde do fyzické paměti, SQL Server trvale přinášejí stránky databáze do fondu vyrovnávacích pamětí a z něj. Datové soubory pro SQL Server by měly být zpracovány jinak. Přístup k souborům protokolu je sekvenční s výjimkou případů, kdy je potřeba transakci vrátit, kde se náhodně přistupuje k datovým souborům, včetně TempDB. Pokud máte pomalý vstupně-výstupní podsystém, mohou uživatelé zaznamenat problémy s výkonem, jako jsou například pomalé doby odezvy a úlohy, které nejsou dokončeny kvůli časovým limitům. 

Virtuální počítače s Azure Marketplace mají soubory protokolu na fyzickém disku, který je ve výchozím nastavení oddělený od datových souborů. Počet a velikost datových souborů databáze TempDB vyhovují osvědčeným postupům a jsou zaměřeny na dočasný D:/ jednotka... 

Následující čítače výkonu mohou napomoci ověření propustnosti vstupně-výstupních operací vyžadovaných vaším SQL Server: 
* **\LogicalDisk\Disk čtení za sekundu** (čtení a zápis IOPS)
* **\LogicalDisk\Disk zápisy na disk/s** (čtení a zápis IOPS) 
* **\LogicalDisk\Disk bajty/s** (požadavky na propustnost pro soubory dat, protokoly a soubory tempdb)

Pomocí požadavků na vstupně-výstupní operace a propustnosti na úrovních špičky vyhodnoťte velikosti virtuálních počítačů, které odpovídají kapacitě z vašich měření. 

Pokud vaše úloha vyžaduje 20 000 vstupně-výstupních vstupně-výstupních operací a 10 000 vstupně-výstupních operací za sekundu, můžete buď zvolit E16s_v3 (s až 32 KB a 25600 neuložených vstupně-výstupních operací), nebo M16_s (s až 20 KB a 10 000 IOPS) a 2 P30 disky vykládané pomocí prostorů úložiště. 

Ujistěte se, že rozumíte požadavkům na propustnost a IOPS zatížení, protože virtuální počítače mají jiné limity škálování pro vstupně-výstupní operace a propustnost.

### <a name="memory"></a>Paměť

Sledujte jak externí paměť použitou v operačním systému, tak i paměť, kterou používá interně SQL Server. Identifikace přítlaku pro jednu z komponent bude mít za problém s velikostí virtuálních počítačů a k identifikaci příležitostí pro optimalizaci. 

Následující čítače výkonu vám pomůžou ověřit stav paměti virtuálního počítače s SQL Server: 
* [\Memory\Available MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: paměť Manager\Target serveru paměti (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: paměť Manager\Total serveru paměti (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy zápisy paměti/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer \ přečtené stránky Life očekávané](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Výpočty/zpracování

Výpočetní prostředí v Azure se spravuje jinak než v místním prostředí. Místní servery jsou během několika let sestaveny bez upgradu z důvodu režie správy a nákladů na získání nového hardwaru. Virtualizace zmírnit některé z těchto problémů, ale aplikace jsou optimalizované tak, aby využívaly největší výhody základního hardwaru, což znamená, že jakákoli významná změna spotřeby prostředků vyžaduje nové vyrovnávání zatížení celého fyzického prostředí. 

Nejedná se o výzvu v Azure, kde je snadné dosáhnout nového virtuálního počítače na jiné sérii hardwaru a dokonce i v jiné oblasti. 

V Azure chcete využít výhod co nejvíc prostředků virtuálních počítačů, proto by měly být virtuální počítače Azure nakonfigurované tak, aby co nejvíce ovlivnily co největší procesor, aniž by to mělo vliv na zatížení. 

Následující čítače výkonu vám pomůžou ověřit výpočetní stav SQL Server virtuálního počítače:
* **Čas procesoru \Processor Information (_Total) \%**
* **Čas procesoru \Process (soubor sqlservr) \%**

> [!NOTE] 
> V ideálním případě se snažte vyzkoušet používání 80% vašich výpočetních prostředků s špičkami vyššími než 90%, ale nedosáhnou 100% za jakékoli trvalé časové období. V podstatě budete chtít jenom zřídit výpočetní výkon aplikací a potom naplánovat horizontální nebo nižší navýšení kapacity podle potřeb firmy. 

## <a name="next-steps"></a>Další kroky

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
