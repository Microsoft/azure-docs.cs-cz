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
ms.date: 06/24/2020
ms.openlocfilehash: 814a5afbde548891a30d941365cdd71d227b4767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674395"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Správa služeb pro Azure Kognitivní hledání v Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Azure Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba, která se používá k vytváření bohatě vyhledávaného prostředí pro vlastní aplikace. Tento článek se zabývá úlohami správy služby, které můžete provádět v [Azure Portal](https://portal.azure.com) pro vyhledávací službu, kterou jste už zřídili. Správa služeb je odlehčená podle návrhu a je omezená na následující úlohy:

* Pomocí odkazu **použití** střední stránky se podívejte na úložiště.
* Ověřte svazky dotazů a latenci pomocí odkazu **sledování** střední stránky a zda byly požadavky omezeny.
* Spravujte přístup pomocí stránky **klíče** vlevo.
* Nastavte kapacitu pomocí stránky **škála** vlevo.

Stejné úlohy, které se provádějí na portálu, můžete také zpracovat programově prostřednictvím [rozhraní API pro správu](/rest/api/searchmanagement/) a [modulu PowerShellu AZ. Search](search-manage-powershell.md). Úlohy správy jsou plně reprezentovány napříč portálem a programovým rozhraním. Neexistuje žádný konkrétní úkol správy, který je k dispozici pouze v jedné z nich.

Azure Kognitivní hledání využívá další služby Azure pro hlubší monitorování a správu. Samostatně platí, že jediná data uložená ve vyhledávací službě jsou obsah (indexy, indexer a definice zdrojů dat a další objekty). Metriky hlášené na stránkách portálu jsou načítány z interních protokolů na více než 30 dní cyklů. V případě uchovávání protokolů řízených uživatelem a dalších událostí budete potřebovat [Azure monitor](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Vlastnosti pevné služby

Při zřizování služby je určeno několik aspektů služby vyhledávání a nelze je později změnit:

* Název služby (nelze přejmenovat službu)
* Umístění služby (aktuálně nemůžete přesunout nedotčenou službu do jiné oblasti)
* Maximální počet replik a oddílů (určených vrstvou, Basic nebo Standard)

Pokud jste spustili Basic s maximálním počtem jednoho oddílu a teď potřebujete další oddíly, budete muset [vytvořit novou službu](search-create-service-portal.md) na vyšší úrovni a znovu vytvořit obsah nové služby. 

## <a name="administrator-rights"></a>Práva správce

Zřizování nebo vyřazení samotné služby může udělat Správce předplatného Azure nebo spolusprávce.

Pro přístup ke koncovému bodu má přístup kdokoli s přístupem k adrese URL služby a klíčem API-Key k obsahu. Další informace o klíčích najdete v tématu [Správa klíčů rozhraní API](search-security-api-keys.md).

* Přístup k této službě je jen pro čtení. Jedná se o oprávnění, která jsou obvykle udělena klientské aplikaci, a to tak, že jí přidělíte adresu URL a klíč rozhraní API pro dotazování.
* Přístup pro čtení i zápis nabízí možnost přidávat, odstraňovat a upravovat serverové objekty, včetně klíčů rozhraní API, indexů, indexerů, zdrojů dat a plánů. Přístup pro čtení i zápis je udělen poskytnutím adresy URL, klíče rozhraní API pro správu.

Práva na zařízení pro zřizování služby se udělují prostřednictvím přiřazení rolí. [Řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) je autorizační systém založený na [Azure Resource Manager](../azure-resource-manager/management/overview.md) pro zřizování prostředků Azure. 

V kontextu Azure Kognitivní hledání určí [přiřazení rolí Azure](search-security-rbac.md) , kdo může provádět úlohy bez ohledu na to, jestli používají [portál](search-manage.md), [PowerShell](search-manage-powershell.md)nebo [rozhraní REST API pro správu](/rest/api/searchmanagement/search-howto-management-rest-api):

* Vytvoření nebo odstranění služby
* Škálování služby
* Odstranit nebo znovu vygenerovat klíče rozhraní API
* Povolit protokolování diagnostiky (vytvořit služby)
* Povolit analýzu provozu (vytvořit služby)

> [!TIP]
> Pomocí mechanismů pro práci v rámci Azure můžete uzamknout předplatné nebo prostředek, abyste zabránili nechtěnému nebo neautorizovanému odstranění služby vyhledávání uživatelů s právy správce. Další informace najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávanému odstranění](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Protokolování a systémové informace

Na úrovni Basic a vyšší Microsoft sleduje všechny služby Azure Kognitivní hledání Services pro 99,9% dostupnost podle smluv o úrovni služeb (SLA). Pokud je služba pomalá nebo propustnost žádosti klesne pod prahové hodnoty smlouvy SLA, Prohlédněte si všechny soubory protokolů, které jsou pro ně k dispozici, a vyřešte problém.

Azure Kognitivní hledání využívá [Azure monitor](../azure-monitor/index.yml) ke shromažďování a ukládání aktivity indexování a dotazů. Vyhledávací služba sám o sobě ukládá jenom svůj obsah (indexy, definice indexerů, definice zdrojů dat, definice dovednosti a mapování synonym). Informace o ukládání do mezipaměti a protokolu se ukládají mimo službu, často v účtu Azure Storage. Další informace o protokolování indexování a úlohách dotazů najdete v tématu [shromažďování a analýza dat protokolu](search-monitor-logs.md).

V souvislosti s obecnými informacemi o vaší službě se pomocí jenom těch zařízení integrovaných do Azure Kognitivní hledání sám získat informace následujícími způsoby:

* Pomocí stránky s **přehledem** služby prostřednictvím oznámení, vlastností a zpráv o stavu.
* Použití [PowerShellu](search-manage-powershell.md) nebo [REST API správy](/rest/api/searchmanagement/) k [získání vlastností služby](/rest/api/searchmanagement/services). V programové vrstvě nejsou k dispozici žádné nové informace ani operace. Rozhraní existují, abyste mohli psát skripty.

## <a name="monitor-resource-usage"></a>Monitorování využití prostředků

V řídicím panelu je monitorování prostředků omezené na informace zobrazené na řídicím panelu služby a na několik metrik, které můžete získat dotazem na službu. Na řídicím panelu služby můžete v části využití rychle zjistit, jestli jsou pro vaši aplikaci adekvátní úrovně prostředků oddílu. Pokud chcete zachytit a zachovat protokolované události, můžete zřídit externí prostředky, jako je monitorování Azure. Další informace najdete v tématu [monitorování Azure kognitivní hledání](search-monitor-usage.md).

Pomocí REST API vyhledávací služby můžete získat počet dokumentů a indexů programově: 

* [Získat statistiku indexu](/rest/api/searchservice/Get-Index-Statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Zotavení po havárii a výpadky služeb

I když můžeme data vyřazením z provozu, služba Azure Kognitivní hledání neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku na úrovni clusteru nebo datového centra. Pokud dojde v datovém centru k chybě clusteru, bude provozní tým zjišťovat a fungovat na obnovení služby. Během obnovování služby dojde k výpadku, ale můžete požádat o kredity služby na nedostupnost služby na [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Pokud se v případě závažných selhání mimo kontrolu Microsoftu vyžaduje nepřetržitá služba, můžete [zřídit další službu](search-create-service-portal.md) v jiné oblasti a implementovat strategii geografické replikace, abyste zajistili, že indexy budou plně redundantní napříč všemi službami.

Zákazníci, kteří používají [indexery](search-indexer-overview.md) k naplnění a aktualizaci indexů, mohou zvládnout zotavení po havárii pomocí geograficky specifických indexerů využívajících stejný zdroj dat. Dvě služby v různých oblastech, z nichž každý spouští indexer, můžou indexovat stejný zdroj dat, aby bylo možné geografickou redundanci. Pokud provádíte indexování ze zdrojů dat, které jsou taky geograficky redundantní, uvědomte si, že služby Azure Kognitivní hledání indexery můžou provádět přírůstkové indexování (sloučení aktualizací z nových, upravených nebo odstraněných dokumentů) z primárních replik. V případě události převzetí služeb při selhání nezapomeňte indexer znovu nasměrovat na novou primární repliku. 

Pokud nepoužíváte indexery, použijete kód aplikace k paralelnímu nabízení objektů a dat do různých vyhledávacích služeb. Další informace najdete v tématu o [výkonu a optimalizaci v Azure kognitivní hledání](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Zálohování a obnovení

Vzhledem k tomu, že Azure Kognitivní hledání není primárním řešením úložiště dat, neposkytujeme formální mechanismus pro samoobslužné zálohování a obnovu. K zálohování definice a snímku indexu do řady souborů JSON ale můžete použít ukázkový kód **index-Backup-Restore** v tomto [úložišti Azure kognitivní hledání .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) a pak tyto soubory použít k obnovení indexu v případě potřeby. Tento nástroj může také přesouvat indexy mezi úrovněmi služeb.

V opačném případě váš kód aplikace použitý k vytvoření a naplnění indexu je možnost de facto Restore, pokud index omylem odstraníte. Pokud chcete index znovu sestavit, odstraňte ho (za předpokladu, že existuje), znovu vytvořte index ve službě a znovu ho načtěte z primárního úložiště dat.

## <a name="scale-up-or-down"></a>Vertikální navýšení nebo snížení kapacity

Každá vyhledávací služba začíná minimálně jednou replikou a jedním oddílem. Pokud jste se zaregistrovali do [úrovně, která podporuje větší kapacitu](search-limits-quotas-capacity.md), klikněte na tlačítko **škálovat** v levém navigačním podokně a upravte využití prostředků.

Když přidáváte kapacitu prostřednictvím kteréhokoli prostředku, služba je automaticky používá. V této části se nevyžaduje žádná další akce, ale dojde k mírnému zpoždění před tím, než se dopustí dopad nového prostředku. Zřizování dalších prostředků může trvat 15 minut nebo déle.

### <a name="add-replicas"></a>Přidat repliky

Zvýšení počtu dotazů za sekundu (QPS) nebo dosažení vysoké dostupnosti se provádí přidáním replik. Každá replika má jednu kopii indexu, takže další replika je k dispozici pro zpracování požadavků na dotazy služby na jeden další index. Pro zajištění vysoké dostupnosti se vyžadují minimálně 3 repliky (podrobnosti najdete v tématu [Nastavení kapacity](search-capacity-planning.md) ).

Vyhledávací služba, která má více replik, může vyrovnávat zatížení požadavků na dotazy přes větší počet indexů. V případě určité úrovně svazku dotazu bude propustnost dotazů rychlejší, pokud jsou k dispozici více kopií indexu pro obsluhu žádosti. Pokud máte latenci dotazů, můžete očekávat kladný dopad na výkon, když jsou další repliky online.

I když při přidávání replik funguje propustnost dotazů, při přidávání replik do služby není přesně dvojnásobná ani trojnásobná. Všechny vyhledávací aplikace podléhají externím faktorům, které mohou při výkonu dotazů Netestovat jejich test. Složité dotazy a latence sítě jsou dva faktory, které přispívají k variacím v časech odezvy dotazů.

### <a name="add-partitions"></a>Přidat oddíly

Je běžnější přidat repliky, ale pokud je úložiště omezené, můžete přidat oddíly, abyste získali větší kapacitu. Úroveň, na které jste zřídili službu, určuje, jestli je možné přidat oddíly. Úroveň Basic je uzamčena v jednom oddílu. Úrovně Standard a vyšší podporují další oddíly.

Oddíly se přidávají v děliteli 12 (konkrétně 1, 2, 3, 4, 6 nebo 12). Toto je artefakt horizontálního dělení. Index se vytvoří v 12 horizontálních oddílů, který se dá uložit na 1 oddíl nebo rovnoměrně rozdělit do 2, 3, 4, 6 nebo 12 oddílů (jeden horizontálních oddílů na oddíl).

### <a name="remove-replicas"></a>Odebrat repliky

Po obdobích s vysokými objemy dotazů můžete pomocí posuvníku snížit počet replik, které jsou po načtení vyhledávacích dotazů normalizovány (například po dobu nedovoleného prodeje). V této části se nevyžadují žádné další kroky. Snížení počtu replik vzhodnotí virtuální počítače v datovém centru. Operace přijímání dotazů a příjmu dat se teď spustí na méně virtuálních počítačích než dřív. Minimální požadavek je jedna replika.

### <a name="remove-partitions"></a>Odebrat oddíly

Na rozdíl od odebrání replik, které nevyžadují žádné další úsilí na vaši část, můžete nějakou práci udělat, pokud používáte víc úložišť, než je možné snížit. Pokud například vaše řešení používá tři oddíly, možnost do jednoho nebo dvou oddílů vygeneruje chybu, pokud je nový prostor úložiště menší než vyžadovaný pro hostování vašeho indexu. Jak byste mohli očekávat, vaše volby budou odstraňovat indexy nebo dokumenty v rámci přidruženého indexu, aby se uvolnilo místo, nebo můžete zachovat aktuální konfiguraci.

Neexistuje žádná metoda detekce, která oznamuje, že se horizontálních oddílů indexů ukládají na konkrétní oddíly. Každý oddíl poskytuje úložiště přibližně 25 GB, takže budete muset omezit úložiště na velikost, kterou můžete přizpůsobit podle počtu oddílů, které máte. Pokud se chcete vrátit k jednomu oddílu, bude nutné, aby se všechny 12 horizontálních oddílůy.

Pro pomoc s budoucím plánováním můžete chtít ověřit úložiště (pomocí funkce [získat statistiku indexu](/rest/api/searchservice/Get-Index-Statistics)) a zjistit, kolik jste skutečně používali. 

## <a name="next-steps"></a>Další kroky

* Automatizace pomocí [PowerShellu](search-manage-powershell.md) nebo rozhraní příkazového [řádku Azure](search-manage-azure-cli.md)

* Kontrola [výkonu a technik optimalizace](search-performance-optimization.md)

* Kontrola [funkcí zabezpečení](search-security-overview.md) pro ochranu obsahu a operací

* Povolení [protokolování diagnostiky](search-monitor-logs.md) pro monitorování úloh dotazů a indexování