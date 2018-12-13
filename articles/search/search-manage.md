---
title: Správa služby Azure Search na portálu – Azure Search
description: Správa služby Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure pomocí webu Azure portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 70343c0d66eb2a00ff2245b7e4876c2e94a96855
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314478"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Správa služby Azure Search na webu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search je plně spravované cloudové vyhledávací služby používá pro sestavení bohaté možnosti vyhledávání do vlastních aplikací. Tento článek popisuje úkoly pro správu služby, které můžete provádět v [webu Azure portal](https://portal.azure.com) pro vyhledávací službu, která jste zřídili. Správa služby je nenáročná záměrné, omezené jenom na následující úlohy:

* Správa přístupu k *klíče api Key* používá pro čtení nebo zápis do vaší služby.
* Upravte kapacitu služby změnou rozdělení oddílů a replik.
* Monitorovat využití prostředků relativně k maximálními limity vaší úrovně služby.

Všimněte si, že *upgradovat* není uvedena jako úlohu správy. Protože prostředky se přidělují, když je služba zřízená, přechod na jinou úroveň vyžaduje novou službu. Podrobnosti najdete v tématu [vytvoření služby Azure Search](search-create-service-portal.md).

> [!Tip]
> Hledáte nápovědu o tom, jak analyzovat výkon vyhledávání provoz nebo dotaz? Můžete sledovat množství dotazů, které uživatelé podmínky hledání a jak úspěšné hledání, které jsou výsledky v hlavních zákazníků na konkrétní dokumenty v indexu. Další informace najdete v tématu [prohledání analýzy provozu pro službu Azure Search](search-traffic-analytics.md), [monitorovat metriky využití a dotaz](search-monitor-usage.md), a [výkon a optimalizace](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Oprávnění správce
Zřizování a vyřazování z provozu službu samotnou může provést správce předplatného Azure nebo spolupracujícího správce.

V rámci služby každý, kdo má přístup k adresu URL služby a api-key správce má přístup pro čtení a zápis do služby. Přístup pro čtení a zápis umožňuje přidat, odstranit nebo upravit objekty serveru, včetně klíče api Key, indexy, indexery, zdroje dat, plány a přiřazení rolí, jak je implementován prostřednictvím [role RBAC definované](search-security-rbac.md).

Všechny interakce uživatele s Azure Search spadá do jedné z těchto režimů: přístup pro čtení a zápis ke službě (oprávnění správce), nebo jen pro čtení přístup ke službě (práva k dotazu). Další informace najdete v tématu [spravovat klíče api Key](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Protokolování a informací o systému
Služba Azure Search nevystavuje soubory protokolu pro jednotlivé služby buď na portálu nebo programových rozhraní. Na úrovni Basic a vyšší, Microsoft sleduje všechny služby Azure Search pro 99,9 % dostupnost podle smlouvy o úrovni služeb (SLA). Pokud služba je pomalé nebo propustnost žádostí nedosahuje prahové hodnoty SLA, týmy podpory najdete v souborech protokolu jsou pro ně dostupné a vyřešení příslušného problému.

Informace z hlediska obecné informace o službě, můžete získat následujícími způsoby:

* Na portálu, na řídicím panelu služby, prostřednictvím oznámení, vlastnosti a stavové zprávy.
* Pomocí [PowerShell](search-manage-powershell.md) nebo [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) k [získat vlastnosti služby](https://docs.microsoft.com/rest/api/searchmanagement/services), nebo stav ve využití prostředků index.
* Prostřednictvím [Analýza provozu vyhledávání](search-traffic-analytics.md), jak jste si poznamenali dříve.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorování využití prostředků
Na řídicím panelu monitorování prostředků je omezený na informace zobrazené v řídicím panelu služby a několik metrik, které můžete získat pomocí dotazu na službu. Na řídicím panelu služby, v části využití můžete rychle zjistit, zda úrovní prostředků oddílu jsou dostatečné pro vaši aplikaci.

Pomocí rozhraní REST API služby Search, můžete získat počet v dokumentech a indexech prostřednictvím kódu programu: 

* [Získání statistik indexu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Počet dokumentů](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Výpadky obnovení a služby po havárii

I když jsme můžete zachránit vaše data, Azure Search neposkytuje rychlé převzetí služeb při selhání služby, pokud dojde k výpadku na úrovni centra clusteru nebo data. Pokud cluster selže v datovém centru, provozní tým rozpozná a práce je, aby obnovení služby. Výpadek nastane během obnovení služby, ale můžete požádat o kredity ke kompenzaci spojeným s nedostupností služby za [smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Pokud byly služby nepřetržitě se vyžaduje při katastrofických selhání mimo ovládací prvek od Microsoftu, může [zřídit další službu](search-create-service-portal.md) v různých oblastech a implementovat strategii geografické replikace k zajištění indexy jsou plně redundantní napříč všemi službami.

Zákazníci, kteří používají [indexery](search-indexer-overview.md) vyplnit a aktualizovat indexy zvládne zotavení po havárii pomocí indexerů konkrétní geografické využívat stejný zdroj dat. Dvě služby v různých oblastech, každé spuštění indexeru, by mohla indexu stejného zdroje dat k dosažení geografickou redundancí. Pokud se indexování ze zdroje dat, které jsou geograficky redundantní, mějte na paměti, že indexerů Azure Search lze provést pouze v přírůstkové indexování z primární repliky. V případě převzetí služeb při selhání nezapomeňte znovu přejděte indexer na novou primární repliku. 

Pokud je velmi riskantní používat indexery, můžete využít kódu aplikace za účelem nabízených objektů a dat do služby různé vyhledávací paralelně. Další informace najdete v tématu [výkon a optimalizace do služby Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Zálohování a obnovení

Protože Azure Search je řešení primární datové úložiště, neposkytujeme formální mechanismus pro samoobslužné služby zálohování a obnovení. Pokud omylem odstraníte indexu, je kódu aplikace, která je vhodná k vytváření a naplňování indexu, de facto stane možnost obnovení. 

Opětovné sestavení indexu, by odstranit (za předpokladu, že existuje), znovu vytvořte index ve službě a znovu načíst načtením dat z primárního datového úložiště.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Vertikálně navýšit nebo snížit kapacitu
Každá služba search začíná minimálně jednu repliku a jeden oddíl. Pokud jste [úroveň, která zajišťují vyhrazené prostředky](search-limits-quotas-capacity.md), klikněte na tlačítko **ŠKÁLOVÁNÍ** dlaždici na řídicím panelu služby a upravte využití prostředků.

Když přidáte kapacitu prostřednictvím prostředku, služba používá je automaticky. Nevyžaduje žádnou další akci z vaší strany, ale neexistuje dojde k mírnému zpoždění, než začne vyplácet dopadu nový prostředek. Může trvat 15 minut nebo déle zřízení dalších prostředků.

 ![][10]

### <a name="add-replicas"></a>Přidat repliky
Zvýšení dotazů za sekundu (QPS) nebo dosažení vysoké dostupnosti se provádí přidáním repliky. Každou repliku má jednu kopii tohoto indexu, takže přidání jeden další repliky se přeloží na jednu další index k dispozici pro zpracování požadavků na dotazy služby. Minimálně 3 repliky jsou požadovány pro zajištění vysoké dostupnosti (viz [plánování kapacity](search-capacity-planning.md) podrobnosti).

Vyhledávací služba s víc replik můžete načíst z žádostí vyvažovat mezi dotazu nad větší počet indexů. S ohledem úrovni množství dotazů, propustnost dotazů se to být rychlejší, pokud existuje více kopií index k dispozici ke zpracování požadavku. Pokud dochází k latence dotazu, můžete očekávat pozitivní dopad na výkon po další repliky jsou online.

I když propustnost dotazů přejde při přidávání repliky, nikoli přesně dvakrát nebo ztrojnásobit při přidávání repliky do vaší služby. Všechny aplikace hledání jsou v souladu s vnějším faktorům, které můžete dotýkat výkon dotazů. Složité dotazy a latence sítě jsou dva faktory, které přispívají k odchylky v pomalejší doby odezvy.

### <a name="add-partitions"></a>Přidat oddíly
Většina aplikací service má integrovanou potřebu další repliky spíše než oddíly. Pro případy, ve kterém jsou vyžadována počet vyšší dokumentů můžete přidat oddíly, pokud jste se zaregistrovali pro standardní služby. Úroveň Basic se neposkytuje pro další oddíly.

Na úrovni Standard se přidají oddíly v násobcích 12 (konkrétně, 1, 2, 3, 4, 6 nebo 12). To je horizontální dělení. Index se vytvoří v 12 horizontální oddíly, které lze vše uložit na 1 oddílu nebo rovnoměrně rozdělí na 2, 3, 4, 6 nebo 12 oddílů (jeden horizontální oddíl na oddíl).

### <a name="remove-replicas"></a>Odebrat repliky
Po období vysoké dotazu svazků můžete pomocí posuvníku ke snížení repliky po načtení dotazu hledání mají normalizované (například po sváteční prodeje jsou přes). Nejsou žádné další kroky z vaší strany. Snížit počet replik ztratí virtuální počítače v datovém centru. Na méně než virtuální počítače před virtuálním se teď spustí dotaz a data operace příjmu. Požadavek na minimální je jedna replika.

### <a name="remove-partitions"></a>Odebrat oddíly
Rozdíl od odebrání repliky, který vyžaduje žádná práce navíc z vaší strany, může mít některé pracovní postup, pokud používáte další úložiště, než je možné snížit. Například pokud vaše řešení používá tři oddíly, downsizing na jeden nebo dva oddíly dojde k chybě pokud nového prostoru úložiště je menší než se požaduje pro hostování vašeho indexu. Jak byste asi očekávali, jsou vaše volby odstranění indexů nebo dokumentů v indexu přidružené k uvolnit nějaké místo nebo zachovat aktuální konfiguraci.

Neexistuje žádná metoda zjišťování, který říká, které horizontální oddíly indexu jsou uložené na konkrétní oddíly. Každý oddíl poskytuje přibližně 25 GB v úložišti, budete muset snížit úložiště o velikosti, která může být obslouženo počet oddílů, ke kterým máte. Pokud chcete vrátit do jednoho oddílu, všemi horizontálními oddíly 12 muset přizpůsobit.

Smyslem budoucí plánování, můžete chtít zkontrolovat úložiště (pomocí [získání statistik indexu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) Chcete-li zobrazit, kolik skutečně spotřebujete. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Doporučených postupech ohledně škálování a nasazení
Toto víkend na 30minutové video obsahuje přehled osvědčené postupy pro pokročilé scénáře nasazení, včetně geograficky distribuované úlohy. Můžete také zobrazit [výkon a optimalizace do služby Azure Search](search-performance-optimization.md) pro stránek nápovědy, které se týkají stejné body.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Další postup
Až porozumíte konceptům za správu služby, zvažte použití [Powershellu](search-manage-powershell.md) pro automatizaci úloh.

Doporučujeme také prohlédnout si [výkon a optimalizace článku](search-performance-optimization.md).

Další doporučení je ke zhlédnutí tohoto videa, které jste si poznamenali v předchozí části. Poskytuje lepší pokrytí z postupů uvedených v této části.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



