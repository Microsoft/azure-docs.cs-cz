---
title: 'Velikost virtuálního počítače: Doporučené postupy pro výkon & pokyny'
description: Poskytuje pokyny pro velikost virtuálního počítače a osvědčené postupy pro optimalizaci výkonu SQL Server na virtuálním počítači Azure (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572359"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Velikost virtuálního počítače: osvědčené postupy výkonu pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje pokyny pro velikost virtuálního počítače a nabízí řadu osvědčených postupů a pokynů pro optimalizaci výkonu SQL Server v Azure Virtual Machines (virtuálních počítačích).

Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tato řada osvědčených postupů pro výkon se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud je vaše úloha méně náročná, možná nebudete potřebovat při každé doporučené optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.


## <a name="checklist"></a>Kontrolní seznam

V následujícím kontrolním seznamu najdete stručný přehled osvědčených postupů pro velikost virtuálního počítače, které zbytek článku pokrývá podrobněji: 

- Používejte velikosti virtuálních počítačů 4 nebo více vCPU, jako je [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)nebo [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) nebo vyšší. 
- Využijte [paměťově optimalizované](../../../virtual-machines/sizes-memory.md) velikosti virtuálních počítačů pro dosažení optimálního výkonu SQL Server úloh. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) Series, [M-](../../../virtual-machines/m-series.md)a [Mv2-](../../../virtual-machines/mv2-series.md) Series nabízejí optimální poměr mezi pamětí a Vcore potřebný pro úlohy OLTP. Oba virtuální počítače řady M nabízejí nejvyšší poměr paměti k vCore, který je potřeba pro důležité úlohy, a je taky ideální pro úlohy datového skladu. 
- Zvažte vyšší poměr paměti k vCore pro důležité úlohy a úlohy datového skladu. 
- Využijte image virtuálních počítačů Azure na Marketplace, protože nastavení SQL Server a možnosti úložiště jsou nakonfigurovány pro optimální výkon SQL Server. 
- Shromážděte charakteristiky výkonu cílového zatížení a použijte je k určení vhodné velikosti virtuálních počítačů pro vaši firmu.

Pokud chcete porovnat kontrolní seznam velikosti virtuálního počítače s ostatními, přečtěte si podrobný [Kontrolní seznam pro osvědčené postupy výkonu](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Přehled

Při vytváření SQL Server na virtuálním počítači Azure pečlivě zvažte potřebný typ úlohy. Pokud migrujete existující prostředí, [Shromážděte základní údaje o výkonu](performance-guidelines-best-practices-collect-baseline.md) , abyste zjistili SQL Server požadavky na virtuální počítače Azure. Pokud se jedná o nový virtuální počítač, vytvořte nový virtuální počítač SQL Server na základě požadavků dodavatele. 

Pokud vytváříte nový virtuální počítač SQL Server s novou aplikací vytvořenou pro Cloud, můžete snadno nastavit velikost vašich SQL Server virtuálních počítačů jako vývoj vašich požadavků na data a využití.
Spusťte vývojová prostředí s nižší vrstvou D-Series, B-Series nebo Av2-Series a rozšiřte prostředí v čase. 

Pomocí SQL Server imagí virtuálního počítače Marketplace s konfigurací úložiště na portálu. Díky tomu bude snazší vytvořit fondy úložiště potřebné k získání velikosti, IOPS a propustnosti potřebných pro vaše úlohy. Je důležité zvolit SQL Server virtuální počítače, které podporují Storage úrovně Premium a Storage úrovně Premium. Další informace najdete v článku [úložiště](performance-guidelines-best-practices-storage.md) . 

Doporučené minimum pro produkční prostředí OLTP je 4 vCore, 32 GB paměti a poměr paměti až vCore 8. U nových prostředí začněte se 4 vCore počítači a škálujte na 8, 16, 32 virtuální jádra nebo víc, když se změní vaše požadavky na data a výpočetní prostředky. U OLTP propustnosti cílí na cílové SQL Server virtuální počítače, které mají 5000 vstupně-výstupních operací pro každou vCore. 

SQL Server datový sklad a kritická prostředí se často musí škálovat nad rámec 8 paměti až do vCore poměru. V případě středně velkých prostředí můžete zvolit 16 vCore poměru paměti a 32 paměti až vCore pro rozsáhlejší prostředí datového skladu. 

SQL Server prostředí datového skladu často využívají paralelní zpracování větších počítačů. Z tohoto důvodu jsou řady M-Series a Mv2-Series silné možnosti pro rozsáhlejší prostředí datového skladu.

Použijte konfiguraci vCPU a paměti ze zdrojového počítače jako základní hodnotu pro migraci aktuální místní SQL Server databáze do SQL Server na virtuálních počítačích Azure. Přepněte si základní licenci do Azure, abyste mohli využít výhod [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) a ušetřit náklady na SQL Server licencování.

## <a name="memory-optimized"></a>Optimalizované pro paměť.

[Paměťově optimalizované velikosti virtuálních počítačů](../../../virtual-machines/sizes-memory.md) jsou primárním cílem pro SQL Server virtuální počítače a doporučené volby od Microsoftu. Paměťově optimalizované virtuální počítače nabízí silnější poměry paměti k procesoru a možnosti střední až velké mezipaměti. 

### <a name="m-mv2-and-mdsv2-series"></a>Řady M, Mv2 a Mdsv2

[Řada M-Series](../../../virtual-machines/m-series.md) nabízí počty Vcore a paměť pro některé z největších SQL Server úloh.  

[Mv2-Series](../../../virtual-machines/mv2-series.md) má nejvyšší počet vCoreů a paměť a doporučuje se pro důležité úlohy datového skladu. Instance Mv2-Series jsou paměťově optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu velkých databází v paměti a úloh s vysokým poměrem paměti na procesor, který je ideální pro servery relačních databází, velké mezipaměti a analýzu v paměti.

[Standard_M64ms](../../../virtual-machines/m-series.md) má například poměr 28 paměti k vCore.

[Mdsv2 střední paměť Series](../../..//virtual-machines/msv2-mdsv2-series.md) je nová řada M-Series, která je aktuálně ve [verzi Preview](https://aka.ms/Mv2MedMemoryPreview) , která nabízí rozsah virtuálních počítačů Azure na úrovni M-Series s nabídkou paměti midtier. Tyto počítače jsou vhodné pro SQL Server úlohy s minimálním počtem 10 vCorech paměti až do 30.

Některé funkce řady M a Mv2-Series atraktivní pro SQL Server výkon zahrnují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md) a podporu pro [ukládání do mezipaměti služby Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) , podpora [Ultra disků](../../../virtual-machines/disks-enable-ultra-ssd.md) a [akcelerace zápisu](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4-Series

[Edsv4-Series](../../../virtual-machines/edv4-edsv4-series.md) je určená pro aplikace náročné na paměť. Tyto virtuální počítače mají rozsáhlou kapacitu místního úložiště SSD, což je silný místní diskový IOPS, až 504 GiB paměti RAM. Existuje skoro konzistentní 8 GiB paměti na vCore napříč většinou těchto virtuálních počítačů, což je ideální pro standardní SQL Server úlohy. 

V této skupině je nový virtuální počítač s [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) , který nabízí 80 virtuální jádra, 504 GB paměti, s poměrem Vcore paměti 6. Tento virtuální počítač je významný, protože je [izolovaný](../../../virtual-machines/isolation.md) , což znamená, že jde o jediný virtuální počítač běžící na hostiteli, a proto se izoluje od ostatních zákaznických úloh. To má vCore poměr, který je nižší, než je doporučeno pro SQL Server, takže by měl být použit pouze v případě, že je vyžadována izolace.

Virtuální počítače řady Edsv4-series podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md)a [ukládání do mezipaměti služby Storage úrovně Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2-Series 11-15

[DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) má stejnou konfiguraci paměti a disku jako předchozí řada D-Series. Tato série má konzistentní poměr paměti k procesoru 7 ve všech virtuálních počítačích. Toto je nejmenší z řady optimalizované pro paměť a dobrá možnost nedostatku nákladů pro úlohy SQL Server vstupní úrovně.

[DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) podporuje [úložiště Premium Storage](../../../virtual-machines/premium-storage-performance.md) a [Storage úrovně Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), což se důrazně doporučuje pro optimální výkon.

## <a name="general-purpose"></a>Obecné účely

[Velikosti virtuálních počítačů pro obecné účely](../../../virtual-machines/sizes-general.md) jsou navržené tak, aby poskytovaly vyvážené poměry paměti až Vcore pro menší úlohy na úrovni vstupu, jako je vývoj a testování, webové servery a menší databázové servery. 

Z důvodu menšího poměru paměti k vCore s virtuálními počítači pro obecné účely je důležité pečlivě monitorovat čítače výkonu založené na paměti, abyste zajistili, že SQL Server dokáže získat paměť vyrovnávací paměti, kterou potřebuje. Další informace najdete v části [směrný plán výkonu paměti](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Vzhledem k tomu, že počáteční doporučení pro produkční úlohy je poměr vCore k paměti 8, minimální doporučená konfigurace pro virtuální počítač s obecným účelem se systémem SQL Server je 4 vCPU a 32 GB paměti. 

### <a name="ddsv4-series"></a>Řada Ddsv4

[Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) nabízí uspokojivou kombinaci vCPU, paměti a dočasného disku, ale s menší podporou Vcore paměti. 

Virtuální počítače s Ddsv4 zahrnují nižší latenci a vyšší rychlost místního úložiště.

Tyto počítače jsou ideální pro souběžná nasazení SQL a aplikace, která vyžadují rychlý přístup k dočasnému úložišti a relačním databázím oddělení. Na všech virtuálních počítačích v této sérii je standardní vCore poměr paměti a 4. 

Z tohoto důvodu se doporučuje využít D8ds_v4 jako počáteční virtuální počítač v této sérii s 8 virtuální jádra a 32 GB paměti. Největším počítačem je D64ds_v4, který má 64 virtuální jádra a 256 GB paměti.

Virtuální počítače [řady Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md) a [úložiště Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) nemá poměr paměti až Vcore, který se doporučuje pro SQL Server úlohy. V takovém případě zvažte použití těchto virtuálních počítačů pouze pro menší úlohy aplikací a vývoje.

### <a name="b-series"></a>Řady B-Series

Velikosti virtuálních počítačů s [nízkou](../../../virtual-machines/sizes-b-series-burstable.md) zátěží jsou ideální pro úlohy, které nepotřebují konzistentní výkon, jako je například kontrola konceptu a velmi malé aplikace a vývojové servery. 

Většina velikostí virtuálních počítačů [řady B-Series](../../../virtual-machines/sizes-b-series-burstable.md) má Vcore poměr paměti 4. Největší z těchto počítačů je [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) s 20 virtuální jádra a 80 GB paměti.

Tato řada je jedinečná, protože aplikace mají možnost rozrůznit se v pracovní době s rozdílnými kredity **, které se** liší podle velikosti počítačů. 

Po vyčerpání kreditů se virtuální počítač vrátí do výkonu základního počítače.

Výhoda B-Series je úspory výpočtů, které byste mohli dosáhnout v porovnání s jinými velikostmi virtuálních počítačů v jiných řadách, zejména pokud potřebujete výpočetní výkon v průběhu dne.

Tato série podporuje [Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale **nepodporuje** [mezipaměť Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> [Řady B-Series](../../../virtual-machines/sizes-b-series-burstable.md) neobsahují poměr Vcore k paměti 8, který je doporučený pro úlohy SQL Server. V takovém případě zvažte použití těchto virtuálních počítačů pro menší aplikace, webové servery a vývojové úlohy.

### <a name="av2-series"></a>Av2-series

Virtuální počítače [řady Av2-Series](../../../virtual-machines/av2-series.md) jsou nejvhodnější pro úlohy na úrovni vstupu, jako je vývoj a testování, webové servery s nízkým provozem, malé až středně střední databáze aplikací a testování konceptů.

Pro tyto tři virtuální počítače mají dobrý poměr paměti k virtuální jádra (virtuální jádra a 16GBs paměti), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuální jádra a 32GBs paměti) a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 64GBs a vCore paměti). [Standard_A2m_v2](../../../virtual-machines/av2-series.md) 

Tyto virtuální počítače jsou osvědčenými možnostmi pro menší vývojové a testovací SQL Server počítače. 

[Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 Vcore může být vhodnou možností pro malé aplikační a webové servery.

> [!NOTE] 
> Av2 Series nepodporuje Prémiové úložiště, a proto se nedoporučuje pro produkční SQL Server úlohy ani s virtuálními počítači, které mají poměr paměti k vCore 8.

## <a name="storage-optimized"></a>Optimalizované pro úložiště.

[Velikosti virtuálních počítačů optimalizované pro úložiště](../../../virtual-machines/sizes-storage.md) jsou určené pro konkrétní případy použití. Tyto virtuální počítače jsou speciálně navržené pomocí optimalizované propustnosti disku a vstupně-výstupních operací. 

### <a name="lsv2-series"></a>Řada Lsv2

[Lsv2-Series](../../../virtual-machines/lsv2-series.md) nabízí vysokou propustnost, nízkou latenci a místní úložiště NVMe. Virtuální počítače řady Lsv2-Series jsou optimalizované tak, aby používaly místní disk na uzlu připojeném přímo k virtuálnímu počítači namísto použití trvalých datových disků. 

Tyto virtuální počítače jsou silné možnosti pro úlohy s velkým objemem dat, datového skladu, generování sestav a ETL. Vysoká propustnost a IOPS místního úložiště NVMe je vhodný případ pro zpracování souborů, které se načtou do vaší databáze, a dalších scénářů, ve kterých se data dají znovu vytvořit ze zdrojového systému nebo jiných úložišť, jako je Azure Blob Storage nebo Azure Data Lake. [Lsv2-Series](../../../virtual-machines/lsv2-series.md) Virtuální počítače můžou také zvýšit výkon svého disku po dobu až 30 minut.

Tyto virtuální počítače mají velikost od 8 do 80 vCPU s 8 GiB paměti na vCPU a pro každých 8 vCPU, 1,92 TB z NVMe SSD. To znamená, že pro největší virtuální počítač této řady je [L80s_v2](../../../virtual-machines/lsv2-series.md), že je k dispozici 80 vCPU a 640 BIB paměti s 10x 1.92 TB NVMe úložiště.  Mezi všemi těmito virtuálními počítači je konzistentní vCore poměr paměti a 8.

Úložiště NVMe je dočasné, což znamená, že data budou na těchto discích ztracena, pokud zrušíte přidělení virtuálního počítače nebo pokud je přesunuto na jiného hostitele pro službu opravit.

Lsv2 a ls series podporují [Prémiové úložiště](../../../virtual-machines/premium-storage-performance.md), ale úložiště Premium Storage nepatří do mezipaměti. Vytvoření místní mezipaměti pro zvýšení IOPs není podporováno. 

> [!WARNING]
> Ukládání datových souborů na dočasné úložiště NVMe může způsobit ztrátu dat, když se virtuální počítač oddělí. 

## <a name="constrained-vcores"></a>Omezené virtuální jádra

Vysoce výkonné SQL Server úlohy často potřebují větší množství paměti, vstupně-výstupních operací a propustnost bez vyššího počtu vCore. 

Většina úloh OLTP jsou aplikační databáze řízené velkým počtem menších transakcí. U úloh OLTP je čtení nebo úpravy pouze malého množství dat, ale objem transakcí řízený počty uživatelů je mnohem vyšší. Je důležité mít k dispozici SQL Server paměť pro plány ukládání do mezipaměti, ukládat nedávno dostupná data a zajistit, aby fyzické čtení bylo možné rychle načíst do paměti. 

Tato OLTP prostředí vyžadují větší množství paměti, rychlé úložiště a šířku pásma I/O, která je nutná k optimálnímu výkonu. 

Azure nabízí velikosti virtuálních počítačů s [omezenými vCPU počty](../../../virtual-machines/constrained-vcpu.md), aby se zachovala Tato úroveň výkonu bez vyšších licenčních nákladů na SQL Server. 

Tato operace pomáhá řídit náklady na licencování tím, že snižuje dostupné virtuální jádra a současně zachovává stejnou šířku pásma, úložiště a I/O v nadřazeném virtuálním počítači.

Počet vCPU může být omezený na jednu čtvrtinu původní velikosti virtuálního počítače. Snížení virtuální jádra dostupných pro virtuální počítač dosáhne vyššího poměru paměti až vCore, ale náklady na výpočetní prostředky zůstanou stejné.

Tyto nové velikosti virtuálních počítačů mají příponu, která určuje počet aktivních vCPU, aby je bylo snazší identifikovat. 

Například [M64-32ms](../../../virtual-machines/constrained-vcpu.md) vyžaduje licencování pouze 32 SQL Server virtuální jádra s pamětí, vstupně-výstupními operacemi a propustností [M64ms](../../../virtual-machines/m-series.md) a [M64-16MS](../../../virtual-machines/constrained-vcpu.md) vyžaduje licencování pouze 16 virtuální jádra.  I když má [M64-16MS](../../../virtual-machines/constrained-vcpu.md) čtvrtinu SQL Server licenčních nákladů na M64ms, budou náklady na výpočetní výkon virtuálního počítače stejné.

> [!NOTE] 
> - Středně velké zátěže datového skladu můžou mít stále nárok na [omezené virtuální počítače s Vcore](../../../virtual-machines/constrained-vcpu.md), ale úlohy datového skladu se běžně charakterizují menším počtem uživatelů a procesy, které řeší větší objemy dat prostřednictvím plánů dotazů, které běží paralelně. 
> - Náklady na výpočetní výkon, včetně licencování operačního systému, budou zůstat stejné jako u nadřazeného virtuálního počítače. 



## <a name="next-steps"></a>Další kroky

Další informace najdete v dalších článcích v této sérii:
- [Rychlý kontrolní seznam](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Shromáždit směrný plán](performance-guidelines-best-practices-collect-baseline.md)

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
