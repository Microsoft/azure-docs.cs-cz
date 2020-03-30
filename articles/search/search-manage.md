---
title: Správa služeb na portálu
titleSuffix: Azure Cognitive Search
description: Spravujte službu Azure Cognitive Search, hostovkou služby cloudového vyhledávání v Microsoft Azure, pomocí portálu Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282923"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Správa služeb pro Azure Cognitive Search na webu Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Sada SDK rozhraní .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portál](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search je plně spravovaná cloudová vyhledávací služba používaná k vytváření bohatého vyhledávacího prostředí do vlastních aplikací. Tento článek popisuje úlohy správy služeb, které můžete provádět na [webu Azure Portal](https://portal.azure.com) pro vyhledávací službu, kterou jste už zřídit. Správa služeb je zjednodušená, je omezena na následující úkoly:

> [!div class="checklist"]
> * Spravujte přístup ke *klíčům rozhraní api používaným* pro přístup pro čtení nebo zápis do vaší služby.
> * Upravte kapacitu služby změnou přidělení oddílů a replik.
> * Sledujte využití prostředků vzhledem k maximálním limitům úrovně služeb.

Všimněte si, že *upgrade* není uveden jako úkol správy. Vzhledem k tomu, že prostředky jsou přiděleny při zřízení služby, přesunutí na jinou úroveň vyžaduje novou službu. Podrobnosti najdete [v tématu Vytvoření služby Azure Cognitive Search](search-create-service-portal.md).

Můžete sledovat objem dotazů a další metriky a pomocí těchto přehledů upravit službu pro rychlejší dobu odezvy. Další informace naleznete v [tématu Sledování metrik využití a dotazů](search-monitor-usage.md) a [výkonu a optimalizace](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Práva správce
Zřizování nebo vyřazení samotné služby zřazené z provozu může provádět správce předplatného Azure nebo spolusprávce.

V rámci služby má každý, kdo má přístup k adrese URL služby a klíč rozhraní API správce, přístup ke službě pro čtení a zápis. Přístup pro čtení a zápis umožňuje přidávat, odstraňovat nebo upravovat objekty serveru, včetně klíčů api, indexů, indexerů, zdrojů dat, plánů a přiřazení rolí implementovaných prostřednictvím [rolí definovaných rbac](search-security-rbac.md).

Veškerá interakce uživatele s Azure Cognitive Search spadá do jednoho z těchto režimů: přístup pro čtení a zápis ke službě (práva správce) nebo přístup ke službě jen pro čtení (práva dotazu). Další informace naleznete [v tématu Správa klíčů rozhraní API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Protokolování a systémové informace
Azure Cognitive Search nezveřejňuje soubory protokolu pro jednotlivé služby prostřednictvím portálu nebo programová rozhraní. Na úrovni Basic a vyšší, Microsoft monitoruje všechny služby Azure Cognitive Search pro 99,9% dostupnost na úroveň smlouvy (SLA). Pokud je služba pomalá nebo propustnost požadavku klesne pod prahové hodnoty s la, týmy podpory zkontrolují soubory protokolu, které mají k dispozici, a řeší problém.

Pokud jde o obecné informace o vaší službě, můžete získat následující způsoby:

* Na portálu, na řídicím panelu služby, prostřednictvím oznámení, vlastností a stavových zpráv.
* Použití [prostředí PowerShell](search-manage-powershell.md) nebo [rozhraní REST pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) k [získání vlastností služby](https://docs.microsoft.com/rest/api/searchmanagement/services)nebo stavu využití prostředků indexu.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Sledování využití prostředků
Na řídicím panelu je monitorování prostředků omezeno na informace zobrazené na řídicím panelu služby a několik metrik, které můžete získat dotazem na službu. Na řídicím panelu služby v části Využití můžete rychle určit, zda jsou úrovně prostředků oddílu pro vaši aplikaci dostatečné. Můžete zřídit externí prostředky, jako je například monitorování Azure, pokud chcete zachytit a zachovat protokolované události. Další informace naleznete [v tématu Monitoring Azure Cognitive Search](search-monitor-usage.md).

Pomocí vyhledávací služby REST API můžete získat přehled o dokumentech a indexech programově: 

* [Získat statistiku rejstříku](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Počet dokumentů](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Zotavení po havárii a výpadky služeb

I když můžeme zachránit vaše data, Azure Cognitive Search neposkytuje okamžité převzetí služeb při selhání služby, pokud je výpadek na úrovni clusteru nebo datového centra. Pokud cluster selže v datovém centru, operační tým zjistí a bude pracovat na obnovení služby. Během obnovení služby dojde k prostojům, ale můžete požádat o kredity služby, které kompenzují nedostupnost služeb podle [smlouvy o úrovni služeb (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 

Pokud je vyžadována nepřetržitá služba v případě katastrofických selhání mimo kontrolu společnosti Microsoft, můžete [zřídit další službu](search-create-service-portal.md) v jiné oblasti a implementovat strategii geografické replikace, abyste zajistili, že indexy jsou plně redundantní ve všech službách.

Zákazníci, kteří používají [indexery](search-indexer-overview.md) k naplnění a aktualizaci indexů, mohou zpracovávat zotavení po havárii prostřednictvím geograficky specifických indexerů využívajících stejný zdroj dat. Dvě služby v různých oblastech, z nichž každá běží indexer, může indexovat stejný zdroj dat k dosažení geografické redundance. Pokud indexujete ze zdrojů dat, které jsou také geograficky redundantní, uvědomte si, že indexery Azure Cognitive Search můžou provádět pouze přírůstkové indexování (slučování aktualizací z nových, upravených nebo odstraněných dokumentů) z primárních replik. V případě převzetí služeb při selhání nezapomeňte indexer znovu nasměrovat na novou primární repliku. 

Pokud nepoužíváte indexery, použijete kód aplikace k nabízení objektů a dat do různých vyhledávacích služeb paralelně. Další informace najdete [v tématu Výkon a optimalizace v Azure Cognitive Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Zálohování a obnovení

Vzhledem k tomu, že Azure Cognitive Search není primární řešení pro ukládání dat, neposkytujeme formální mechanismus pro samoobslužné zálohování a obnovení. Ukázkový kód **obnovení zálohy indexu** v tomto [ukázkovém úložiště Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) však můžete použít k zálohování definice indexu a snímku na řadu souborů JSON a v případě potřeby použít tyto soubory k obnovení indexu. Tento nástroj může také přesunout indexy mezi úrovněmi služby.

V opačném případě je kód aplikace použitý pro vytvoření a vyplnění indexu de facto možností obnovení, pokud omylem odstraníte index. Chcete-li obnovit index, odstraníte jej (za předpokladu, že existuje), znovu vytvořte index ve službě a znovu načtěte načtením dat z primárního úložiště dat.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Vertikální navýšení nebo snížení kapacity
Každá vyhledávací služba začíná minimálně jednou replikou a jedním oddílem. Pokud jste se zaregistrovali k [vrstvě, která poskytuje vyhrazené prostředky](search-limits-quotas-capacity.md), klikněte na dlaždici **MĚŘÍTKO** na řídicím panelu služby a upravte využití prostředků.

Když přidáte kapacitu prostřednictvím jednoho z prostředků, služba je používá automaticky. Z vaší strany není nutná žádná další akce, ale před realizací dopadu nového zdroje dochází k mírnému zpoždění. Zřízení dalších prostředků může trvat 15 minut nebo déle.

 ![][10]

### <a name="add-replicas"></a>Přidání replik
Zvýšení dotazů za sekundu (QPS) nebo dosažení vysoké dostupnosti se provádí přidáním repliky. Každá replika má jednu kopii indexu, takže přidání další repliky se přeloží do dalšího indexu, který je k dispozici pro zpracování požadavků na servisní dotazy. Pro vysokou dostupnost jsou vyžadovány minimálně 3 repliky (podrobnosti najdete [v tématu Plánování kapacity).](search-capacity-planning.md)

Vyhledávací služba s více replikami může požadavky na vyrovnávání zatížení dotazu přes větší počet indexů. Vzhledem k úrovni objemu dotazu bude propustnost dotazů rychlejší, pokud je k dispozici více kopií indexu pro zpracování požadavku. Pokud dochází k latenci dotazu, můžete očekávat pozitivní dopad na výkon, jakmile jsou další repliky online.

Přestože propustnost dotazu se zvýší při přidávání replik, není přesně dvojité nebo trojité při přidávání replik do služby. Všechny vyhledávací aplikace podléhají externím faktorům, které mohou zasahovat do výkonu dotazu. Komplexní dotazy a latence sítě jsou dva faktory, které přispívají k odchylkám v době odezvy dotazu.

### <a name="add-partitions"></a>Přidání oddílů
Většina aplikací služby mají vestavěnou potřebu více replik spíše než oddíly. V případech, kdy je vyžadován zvýšený počet dokumentů, můžete přidat oddíly, pokud jste se zaregistrovali ke službě Standard. Základní úroveň neposkytuje další oddíly.

Na úrovni Standard oddíly jsou přidány v násobcích 12 (konkrétně 1, 2, 3, 4, 6 nebo 12). Tohle je artefakt střepů. Index je vytvořen ve 12 úlomcích, které mohou být všechny uloženy na 1 oddíl nebo rovnoměrně rozděleny do 2, 3, 4, 6 nebo 12 oddílů (jeden oddíl na oddíl).

### <a name="remove-replicas"></a>Odebrání replik
Po obdobích vysokých objemů dotazů můžete pomocí posuvníku snížit repliky po normalizaci zatížení vyhledávacího dotazu (například po skončení prodeje svátků). Z vaší strany nejsou vyžadovány žádné další kroky. Snížení počtu replik se vzdává virtuálních počítačů v datovém centru. Operace při přijímání dotazů a dat se teď budou spouštět na méně virtuálních počítačích než dříve. Minimální požadavek je jedna replika.

### <a name="remove-partitions"></a>Odebrání oddílů
Na rozdíl od odebrání repliky, které nevyžaduje žádné další úsilí z vaší strany, můžete mít nějakou práci, pokud používáte více úložiště, než lze snížit. Například pokud vaše řešení používá tři oddíly, zmenšení na jeden nebo dva oddíly vygeneruje chybu, pokud je nový úložný prostor menší, než je požadováno pro hostování indexu. Jak můžete očekávat, vaše volby jsou odstranit indexy nebo dokumenty v rámci přidruženého indexu uvolnit místo nebo zachovat aktuální konfiguraci.

Neexistuje žádná metoda zjišťování, která vám řekne, které indexové šřepy jsou uloženy na konkrétních oddílech. Každý oddíl poskytuje přibližně 25 GB v úložišti, takže budete muset snížit úložiště na velikost, která může být přizpůsobena počtu oddílů, které máte. Pokud se chcete vrátit k jednomu oddílu, bude nutné přizpůsobit všech 12 úlomků.

Chcete-li pomoci s budoucím plánováním, můžete zkontrolovat úložiště (pomocí [statistiky získat index)](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)chcete-li zjistit, kolik jste skutečně použili. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Další kroky
Až pochopíte koncepty správy služeb, zvažte použití [Prostředí PowerShell](search-manage-powershell.md) k automatizaci úloh.

Doporučujeme také zkontrolovat [výkon a optimalizace článku](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



