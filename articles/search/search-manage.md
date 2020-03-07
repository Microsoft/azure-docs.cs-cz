---
title: Správa služeb na portálu
titleSuffix: Azure Cognitive Search
description: Pomocí Azure Portalu Spravujte službu Azure Kognitivní hledání hostovaná cloudová vyhledávací služba na Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379600"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Správa služeb pro Azure Kognitivní hledání v Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Azure Portal](search-manage.md)
> * > [Pythonu](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0) 

Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba, která se používá k vytváření bohatě vyhledávaného prostředí pro vlastní aplikace. Tento článek se zabývá úlohami správy služby, které můžete provádět v [Azure Portal](https://portal.azure.com) pro vyhledávací službu, kterou jste už zřídili. Správa služeb je odlehčená podle návrhu a je omezená na následující úlohy:

> [!div class="checklist"]
> * Spravujte přístup k *klíčům API* , které se používají pro přístup pro čtení nebo zápis ke službě.
> * Změnou přidělení oddílů a replik upravte kapacitu služby.
> * Monitorujte využití prostředků relativně s maximálními limity vaší úrovně služby.

Všimněte si, že *upgrade* není uveden jako úloha správy. Vzhledem k tomu, že prostředky jsou přiděleny při zřizování služby, přesun na jinou úroveň vyžaduje novou službu. Podrobnosti najdete v tématu [Vytvoření služby Azure kognitivní hledání](search-create-service-portal.md).

Můžete monitorovat svazky dotazů a další metriky a pomocí těchto přehledů upravovat službu pro rychlejší doby odezvy. Další informace najdete v tématech [monitorování využití a metriky dotazů](search-monitor-usage.md) a [výkonu a optimalizace](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Práva správce
Zřizování nebo vyřazení samotné služby může udělat Správce předplatného Azure nebo spolusprávce.

V rámci služby má kdokoli, kdo má přístup k adrese URL služby a klíč rozhraní API pro správu, ke službě přístup pro čtení i zápis. Přístup pro čtení i zápis nabízí možnost přidávat, odstraňovat a upravovat serverové objekty, včetně klíčů rozhraní API, indexů, indexerů, zdrojů dat, plánů a přiřazení rolí, jak je implementováno prostřednictvím [rolí definovaných pomocí RBAC](search-security-rbac.md).

Veškerá interakce uživatele s Azure Kognitivní hledání spadá do jednoho z těchto režimů: přístup pro čtení i zápis ke službě (oprávnění správce) nebo přístup ke službě jen pro čtení (dotazovaná oprávnění). Další informace najdete v tématu [Správa klíčů rozhraní API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Protokolování a systémové informace
Azure Kognitivní hledání nezveřejňuje soubory protokolů pro jednotlivé služby, a to prostřednictvím portálu nebo programových rozhraní. Na úrovni Basic a vyšší Microsoft sleduje všechny služby Azure Kognitivní hledání Services pro 99,9% dostupnost podle smluv o úrovni služeb (SLA). Pokud je služba pomalá nebo propustnost žádosti klesne pod prahové hodnoty smlouvy SLA, Prohlédněte si všechny soubory protokolů, které jsou pro ně k dispozici, a vyřešte problém.

V souvislosti s obecnými informacemi o vaší službě můžete získat informace následujícími způsoby:

* Na portálu na řídicím panelu služby prostřednictvím oznámení, vlastností a zpráv o stavu.
* Použití [PowerShellu](search-manage-powershell.md) nebo [REST API správy](https://docs.microsoft.com/rest/api/searchmanagement/) k [získání vlastností služby](https://docs.microsoft.com/rest/api/searchmanagement/services)nebo stavu využití prostředků indexu.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorování využití prostředků
V řídicím panelu je monitorování prostředků omezené na informace zobrazené na řídicím panelu služby a na několik metrik, které můžete získat dotazem na službu. Na řídicím panelu služby můžete v části využití rychle zjistit, jestli jsou pro vaši aplikaci adekvátní úrovně prostředků oddílu. Pokud chcete zachytit a zachovat protokolované události, můžete zřídit externí prostředky, jako je monitorování Azure. Další informace najdete v tématu [monitorování Azure kognitivní hledání](search-monitor-usage.md).

Pomocí REST API vyhledávací služby můžete získat počet dokumentů a indexů programově: 

* [Získat statistiku indexu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Počet dokumentů](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Zotavení po havárii a výpadky služeb

I když můžeme data vyřazením z provozu, služba Azure Kognitivní hledání neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku na úrovni clusteru nebo datového centra. Pokud dojde v datovém centru k chybě clusteru, bude provozní tým zjišťovat a fungovat na obnovení služby. Během obnovování služby dojde k výpadku, ale můžete požádat o kredity služby na nedostupnost služby na [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Pokud se v případě závažných selhání mimo kontrolu Microsoftu vyžaduje nepřetržitá služba, můžete [zřídit další službu](search-create-service-portal.md) v jiné oblasti a implementovat strategii geografické replikace, abyste zajistili, že indexy budou plně redundantní napříč všemi službami.

Zákazníci, kteří používají [indexery](search-indexer-overview.md) k naplnění a aktualizaci indexů, mohou zvládnout zotavení po havárii pomocí geograficky specifických indexerů využívajících stejný zdroj dat. Dvě služby v různých oblastech, z nichž každý spouští indexer, můžou indexovat stejný zdroj dat, aby bylo možné geografickou redundanci. Pokud provádíte indexování ze zdrojů dat, které jsou taky geograficky redundantní, uvědomte si, že služby Azure Kognitivní hledání indexery můžou provádět přírůstkové indexování (sloučení aktualizací z nových, upravených nebo odstraněných dokumentů) z primárních replik. V případě události převzetí služeb při selhání nezapomeňte indexer znovu nasměrovat na novou primární repliku. 

Pokud nepoužíváte indexery, použijete kód aplikace k paralelnímu nabízení objektů a dat do různých vyhledávacích služeb. Další informace najdete v tématu o [výkonu a optimalizaci v Azure kognitivní hledání](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Zálohování a obnovení

Vzhledem k tomu, že Azure Kognitivní hledání není primárním řešením úložiště dat, neposkytujeme formální mechanismus pro samoobslužné zálohování a obnovu. K zálohování definice a snímku indexu do řady souborů JSON ale můžete použít ukázkový kód **index-Backup-Restore** v tomto [úložišti Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) a pak tyto soubory použít k obnovení indexu v případě potřeby. Tento nástroj může také přesouvat indexy mezi úrovněmi služeb.

V opačném případě váš kód aplikace použitý k vytvoření a naplnění indexu je možnost de facto Restore, pokud index omylem odstraníte. Pokud chcete index znovu sestavit, odstraňte ho (za předpokladu, že existuje), znovu vytvořte index ve službě a znovu ho načtěte z primárního úložiště dat.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Vertikální navýšení nebo snížení kapacity
Každá vyhledávací služba začíná minimálně jednou replikou a jedním oddílem. Pokud jste se zaregistrovali do [úrovně, která poskytuje vyhrazené prostředky](search-limits-quotas-capacity.md), klikněte na dlaždici **škálování** na řídicím panelu služby a upravte využití prostředků.

Když přidáváte kapacitu prostřednictvím kteréhokoli prostředku, služba je automaticky používá. V této části se nevyžaduje žádná další akce, ale dojde k mírnému zpoždění před tím, než se dopustí dopad nového prostředku. Zřizování dalších prostředků může trvat 15 minut nebo déle.

 ![][10]

### <a name="add-replicas"></a>Přidat repliky
Zvýšení počtu dotazů za sekundu (QPS) nebo dosažení vysoké dostupnosti se provádí přidáním replik. Každá replika má jednu kopii indexu, takže další replika je k dispozici pro zpracování požadavků na dotazy služby na jeden další index. Pro zajištění vysoké dostupnosti se vyžadují minimálně 3 repliky (podrobnosti najdete v tématu [plánování kapacity](search-capacity-planning.md) ).

Vyhledávací služba, která má více replik, může vyrovnávat zatížení požadavků na dotazy přes větší počet indexů. V případě určité úrovně svazku dotazu bude propustnost dotazů rychlejší, pokud jsou k dispozici více kopií indexu pro obsluhu žádosti. Pokud máte latenci dotazů, můžete očekávat kladný dopad na výkon, když jsou další repliky online.

I když při přidávání replik funguje propustnost dotazů, při přidávání replik do služby není přesně dvojnásobná ani trojnásobná. Všechny vyhledávací aplikace podléhají externím faktorům, které mohou při výkonu dotazů Netestovat jejich test. Složité dotazy a latence sítě jsou dva faktory, které přispívají k variacím v časech odezvy dotazů.

### <a name="add-partitions"></a>Přidat oddíly
Většina aplikací služeb má vestavěnou potřebu více replik, nikoli oddílů. V případech, kdy je potřeba zvýšit počet dokumentů, můžete přidat oddíly, pokud jste se zaregistrovali ke standardní službě. Úroveň Basic neposkytuje další oddíly.

Na úrovni Standard se oddíly přidávají v násobcích 12 (konkrétně 1, 2, 3, 4, 6 nebo 12). Toto je artefakt horizontálního dělení. Index se vytvoří v 12 horizontálních oddílů, který se dá uložit na 1 oddíl nebo rovnoměrně rozdělit do 2, 3, 4, 6 nebo 12 oddílů (jeden horizontálních oddílů na oddíl).

### <a name="remove-replicas"></a>Odebrat repliky
Po obdobích s vysokými objemy dotazů můžete pomocí posuvníku snížit počet replik, které jsou po načtení vyhledávacích dotazů normalizovány (například po dobu nedovoleného prodeje). V této části se nevyžadují žádné další kroky. Snížení počtu replik vzhodnotí virtuální počítače v datovém centru. Operace přijímání dotazů a příjmu dat se teď spustí na méně virtuálních počítačích než dřív. Minimální požadavek je jedna replika.

### <a name="remove-partitions"></a>Odebrat oddíly
Na rozdíl od odebrání replik, které nevyžadují žádné další úsilí na vaši část, můžete nějakou práci udělat, pokud používáte víc úložišť, než je možné snížit. Pokud například vaše řešení používá tři oddíly, možnost do jednoho nebo dvou oddílů vygeneruje chybu, pokud je nový prostor úložiště menší než vyžadovaný pro hostování vašeho indexu. Jak byste mohli očekávat, vaše volby budou odstraňovat indexy nebo dokumenty v rámci přidruženého indexu, aby se uvolnilo místo, nebo můžete zachovat aktuální konfiguraci.

Neexistuje žádná metoda detekce, která oznamuje, že se horizontálních oddílů indexů ukládají na konkrétní oddíly. Každý oddíl poskytuje úložiště přibližně 25 GB, takže budete muset omezit úložiště na velikost, kterou můžete přizpůsobit podle počtu oddílů, které máte. Pokud se chcete vrátit k jednomu oddílu, bude nutné, aby se všechny 12 horizontálních oddílůy.

Pro pomoc s budoucím plánováním můžete chtít ověřit úložiště (pomocí funkce [získat statistiku indexu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) a zjistit, kolik jste skutečně používali. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Další kroky
Jakmile porozumíte konceptům za správu služeb, zvažte použití [prostředí PowerShell](search-manage-powershell.md) pro automatizaci úloh.

Doporučujeme také zkontrolovat článek o [výkonu a optimalizaci](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



