---
title: Dostupnost a kontinuita
titleSuffix: Azure Cognitive Search
description: Naučte se, jak zajistit vysokou dostupnost služby vyhledávání a odolnost proti výpadkům v obdobích nebo dokonce závažných chybách.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581529"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Dostupnost a provozní kontinuita v Azure Kognitivní hledání

V Kognitivní hledání dosahuje dostupnost prostřednictvím několika replik, zatímco provozní kontinuita (a zotavení po havárii) se dosahuje prostřednictvím několika vyhledávacích služeb. V tomto článku najdete pokyny, které můžete použít jako výchozí bod pro vývoj strategie, která splňuje vaše obchodní požadavky jak pro dostupnost, tak pro průběžné operace.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Vysoká dostupnost

V Kognitivní hledání repliky jsou kopie vašeho indexu. Pokud máte víc replik, umožníte službě Azure Kognitivní hledání restartovat počítač a provést údržbu proti jedné replice, zatímco provádění dotazů pokračuje na dalších replikách. Další informace o přidávání replik najdete v tématu [Přidání nebo zmenšení replik a oddílů](search-capacity-planning.md#adjust-capacity).

Pro každou jednotlivou vyhledávací službu společnost Microsoft garantuje minimálně 99,9% dostupnost pro konfigurace, které splňují tato kritéria: 

+ Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazů)

+ Tři nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování) 

Pro úroveň Free se neposkytuje žádná smlouva SLA. Další informace najdete v tématu [SLA pro Azure kognitivní hledání](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../availability-zones/az-overview.md) jsou schopnosti platformy Azure, které rozdělí datová centra oblasti do různých fyzických skupin umístění, aby poskytovala vysokou dostupnost v rámci stejné oblasti. Pokud používáte Zóny dostupnosti pro Kognitivní hledání, jednotlivé repliky představují jednotky pro přiřazení zóny. Vyhledávací služba běží v jedné oblasti. jeho repliky běží v různých zónách.

Zóny dostupnosti se službou Azure Kognitivní hledání můžete využít tak, že do vyhledávací služby přidáte dvě nebo víc replik. Každá replika bude umístěna v jiné zóně dostupnosti v rámci dané oblasti. Pokud máte více replik, než Zóny dostupnosti, repliky budou rozloženy mezi Zóny dostupnosti, jak je to možné. V rámci neexistují žádné konkrétní akce, s výjimkou [Vytvoření vyhledávací služby](search-create-service-portal.md) v oblasti, která poskytuje zóny dostupnosti a potom ke konfiguraci služby pro [použití více replik](search-capacity-planning.md#adjust-capacity).

Azure Kognitivní hledání aktuálně podporuje Zóny dostupnosti pro služby úrovně Standard nebo vyšší, které byly vytvořeny v jedné z následujících oblastí:

+ Austrálie – východ (vytvořeno 30. ledna 2021 nebo novější)
+ Kanada – střed (vytvořeno 30. ledna 2021 nebo novější)
+ Střed USA (vytvořeno 4. prosince 2020 nebo novější)
+ Východní USA (vytvořeno 27. ledna 2021 nebo novější)
+ Východní USA 2 (vytvořeno 30. ledna 2021 nebo novější)
+ Francie – střed (vytvořeno 23. října 2020 nebo novější)
+ Japonsko – východ (vytvořeno 30. ledna 2021 nebo novější)
+ Severní Evropa (vytvořeno 28. ledna 2021 nebo novější)
+ Jižní Východní Asie (vytvořeno 31. ledna 2021 nebo novější)
+ Velká Británie – jih (vytvořeno 30. ledna 2021 nebo novější)
+ Západní Evropa (vytvořeno 29. ledna 2021 nebo novější)
+ Západní USA 2 (vytvořeno 30. ledna 2021 nebo novější)

Zóny dostupnosti nemá vliv na [smlouva SLA Azure kognitivní hledání](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro dotaz na vysokou dostupnost stále potřebujete 3 nebo více replik.

## <a name="multiple-services-in-separate-geographic-regions"></a>Více služeb v samostatných geografických oblastech

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

+ [Provozní kontinuita a zotavení po havárii (BCDR)](../best-practices-availability-paired-regions.md) (kognitivní hledání neposkytuje okamžitý převzetí služeb při selhání v případě výpadku).
+ Globálně nasazené aplikace Pokud požadavky na dotazy a indexování pocházejí ze všech po celém světě, uživatelé, kteří jsou nejblíže k datovému centru hostitele, budou mít rychlejší výkon. Vytváření dalších služeb v oblastech s blízkou blízkosti těmto uživatelům může vymezit výkon pro všechny uživatele.
+ [Víceklientské architektury](search-modeling-multitenant-saas-applications.md) se někdy volají na dvě nebo více služeb.

Pokud potřebujete dvě další služby vyhledávání, jejich vytvoření v různých oblastech může splňovat požadavky na aplikace pro zajištění kontinuity a obnovení a také kratší dobu odezvy pro globální uživatelskou základnu.

Služba Azure Kognitivní hledání v současné době neposkytuje automatizovanou metodu hledání geografických replikovaných indexů napříč různými oblastmi, ale existuje několik postupů, které je možné použít, aby bylo možné tento proces snadno implementovat a spravovat. Ty jsou popsány v následujících částech.

Cílem geografické distribuované sady vyhledávacích služeb je mít dva nebo více indexů dostupných ve dvou nebo více oblastech, kde je uživatel směrován do služby Azure Kognitivní hledání, která poskytuje nejnižší latenci:

   ![Mezi kartami služeb podle oblasti][1]

Tuto architekturu můžete implementovat vytvořením několika služeb a návrhem strategie pro synchronizaci dat. Volitelně můžete pro žádosti o směrování zahrnout prostředek, jako je Azure Traffic Manager. Další informace najdete v tématu [Vytvoření vyhledávací služby](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Udržování dat synchronizovaných napříč více službami

Existují dvě možnosti, jak udržet synchronizaci dvou nebo více služeb distribuovaných hledání, které se skládají buď pomocí [indexeru služby Azure kognitivní hledání](search-indexer-overview.md) nebo rozhraní push API (také označovaného jako [kognitivní hledání Azure REST API](/rest/api/searchservice/)). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Možnost 1: použití indexerů pro aktualizaci obsahu ve více službách

Pokud již indexer používáte v jedné službě, můžete u druhé služby nakonfigurovat druhý indexer tak, aby používal stejný objekt zdroje dat, a nastavovat data ze stejného umístění. Každá služba v každé oblasti má svůj vlastní indexer a cílový index (váš index vyhledávání není sdílený, což znamená, že data jsou duplicitní), ale každý indexer odkazuje na stejný zdroj dat.

Tady je přehled toho, co by architektura vypadala jako.

   ![Jeden zdroj dat s kombinacemi distribuovaných indexerů a služeb][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Možnost 2: použití rozhraní REST API pro doručování aktualizací obsahu ve více službách

Pokud používáte Azure Kognitivní hledání REST API k [nabízení obsahu do indexu vyhledávání](tutorial-optimize-indexing-push-api.md), můžete uchovávat různé služby vyhledávání v synchronizaci tím, že zadáte změny ve všech vyhledávacích službách pokaždé, když se vyžaduje aktualizace. V kódu se ujistěte, že se nezdařily případy, kdy aktualizace jedné služby vyhledávání selže, ale u jiných vyhledávacích služeb je úspěšná.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Koordinace požadavků pomocí Traffic Manager Azure

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrovat požadavky na více geograficky umístěných webů, které jsou následně zajištěny několika vyhledávacími službami. Jednou z výhod Traffic Manager je to, že se dá vyzkoušet Azure Kognitivní hledání a zajistit tak, že bude k dispozici a směrovat uživatele na alternativní vyhledávací služby v případě výpadku. Kromě toho, pokud odesíláte žádosti o vyhledávání prostřednictvím webů Azure, vám Azure Traffic Manager umožňuje vyrovnávat zatížení případů, kde web není, ale není Azure Kognitivní hledání. Tady je příklad, jak architektura, která využívá Traffic Manager.

   ![Křížové karty služeb podle oblasti s centrálním Traffic Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Zotavení po havárii a výpadky služeb

I když můžeme data vyřazením z provozu, služba Azure Kognitivní hledání neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku na úrovni clusteru nebo datového centra. Pokud dojde v datovém centru k chybě clusteru, bude provozní tým zjišťovat a fungovat na obnovení služby. Během obnovování služby dojde k výpadku, ale můžete požádat o kredity služby na nedostupnost služby na [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Pokud se v případě závažných selhání mimo kontrolu Microsoftu vyžaduje nepřetržitá služba, můžete [zřídit další službu](search-create-service-portal.md) v jiné oblasti a implementovat strategii geografické replikace, abyste zajistili, že indexy budou plně redundantní napříč všemi službami.

Zákazníci, kteří používají [indexery](search-indexer-overview.md) k naplnění a aktualizaci indexů, mohou zvládnout zotavení po havárii pomocí geograficky specifických indexerů využívajících stejný zdroj dat. Dvě služby v různých oblastech, z nichž každý spouští indexer, můžou indexovat stejný zdroj dat, aby bylo možné geografickou redundanci. Pokud provádíte indexování ze zdrojů dat, které jsou taky geograficky redundantní, uvědomte si, že služby Azure Kognitivní hledání indexery můžou provádět přírůstkové indexování (sloučení aktualizací z nových, upravených nebo odstraněných dokumentů) z primárních replik. V případě události převzetí služeb při selhání nezapomeňte indexer znovu nasměrovat na novou primární repliku. 

Pokud nepoužíváte indexery, použijete kód aplikace k paralelnímu nabízení objektů a dat do různých vyhledávacích služeb. Další informace najdete v tématu [zachování synchronizovaných dat napříč více službami](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Zálohování a obnovení alternativ

Vzhledem k tomu, že Azure Kognitivní hledání není primární řešení úložiště dat, neposkytuje Microsoft formální mechanismus pro samoobslužné zálohování a obnovu. K zálohování definice a snímku indexu do řady souborů JSON ale můžete použít ukázkový kód **index-Backup-Restore** v tomto [úložišti ukázek Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) a pak tyto soubory použít k obnovení indexu v případě potřeby. Tento nástroj může také přesouvat indexy mezi úrovněmi služeb.

V opačném případě váš kód aplikace použitý k vytvoření a naplnění indexu je možnost de facto Restore, pokud index omylem odstraníte. Pokud chcete index znovu sestavit, odstraňte ho (za předpokladu, že existuje), znovu vytvořte index ve službě a znovu ho načtěte z primárního úložiště dat.

## <a name="next-steps"></a>Další kroky

Další informace o cenových úrovních a omezeních služeb pro každé z nich najdete v tématu [omezení služby](search-limits-quotas-capacity.md). Další informace o kombinacích oddílů a replik najdete v tématu [plánování kapacity](search-capacity-planning.md) .

Diskuzi o výkonu a demonstračních technikách popsaných v tomto článku najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png