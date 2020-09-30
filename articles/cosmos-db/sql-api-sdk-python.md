---
title: Azure Cosmos DB SQL Python API, SDK & prostředky
description: Seznamte se se všemi informacemi o rozhraních API SQL Pythonu a sadě SDK, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: 03c8f5acfc10738401f61de099f946c33497d705
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569817"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK pro SQL API: poznámky k verzi a prostředky

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Jarní data v2](sql-api-sdk-java-spring-v2.md)
> * [Jarní data V3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Stáhnout sadu SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní Python API](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python&preserve-view=true)|
|**Pokyny k instalaci sady SDK**|[Pokyny k instalaci sady Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Začínáme**|[Začínáme se sadou Python SDK](create-sql-api-python.md)|
|**Aktuální podporovaná platforma**|[Python 2,7](https://www.python.org/downloads/) a [Python 3.5.3 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Historie verzí

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10)

- Přidání upozornění na zastaralost pro režim indexování "opožděné". Back-end už neumožňuje vytvářet kontejnery s tímto režimem a bude je místo toho nastavit na konzistentní.

**Nové funkce**
- Byla přidána možnost nastavit hodnotu TTL analytického úložiště při vytváření nového kontejneru.

**Opravy chyb**
- Pevná podpora pro Dicts jako vstupní hodnoty pro rozhraní API pro get_client.
- Opravená kompatibilita Pythonu 2/3 ve iterátorech dotazů.
- Došlo k chybě pomocného parametru pro pevný typ (problém #12570).
- Opravená chyba, kdy se záhlaví možností do funkce upsert_item nepřidala. Problém #11791 – Děkujeme vám @aalapatirvbd .
- Při použití neřetězcového ID v položce byla vyvolána Opravená chyba. Nyní vyvolává TypeError místo AttributeError (problém #11793).

### <a name="400"></a>4.0.0

* Stabilní verze.
* Přidali jsme HttpLoggingPolicy do kanálu, abyste umožnili předávání vlastního protokolovacího nástroje pro hlavičky požadavků a odpovědí.

### <a name="400b6"></a>4.0.0 B6

* Opravená chyba v synchronized_request pro rozhraní API pro multimédia
* Odebrané MediaReadMode a MediaRequestTimeout z ConnectionPolicy nejsou podporované požadavky na média.

### <a name="400b5"></a>4.0.0 B5

* modul Azure. Cosmos. Errors se nepoužívá a nahradil Azure. Cosmos. Exceptions.
* Parametry podmínky přístupu ( `access_condition` , `if_match` ,) se už `if_none_match` nepoužívají jako samostatné `match_condition` `etag` parametry a.
* Opravila se chyba ve zprostředkovateli mapy směrování.
* Přidala se podpora jedinečnosti, posunu a omezení dotazu.
* Výchozí kontext spuštění dotazu dokumentu, který se teď používá pro

  * Dotazy ChangeFeed
  * dotazy s jedním oddílem (partitionkey, partitionKeyRangeId je k dispozici v možnostech)
  * Jiné než dokumentové dotazy

* Chyby pro agregace na více oddílech, přičemž dotaz povolit mezioddíl je nastaven na hodnotu true, ale neexistuje žádné klíčové slovo Value.
* Počet přístupů do koncového bodu plánu dotazů pro další scénáře načtení plánu dotazů
* Přidání `__repr__` podpory pro objekty entit Cosmos
* Aktualizovala se dokumentace.

### <a name="400b4"></a>4.0.0 B4

* Přidání podpory `timeout` argumentu klíčového slova do všech operací pro zadání absolutního časového limitu v sekundách, ve kterém musí být operace dokončena. Pokud dojde k překročení časového limitu, `azure.cosmos.errors.CosmosClientTimeoutError` bude vyvolána hodnota.

* Přidání nového `ConnectionRetryPolicy` pro správu chování opakování během chyb připojení HTTP.

* Přidání nových argumentů konstruktoru a v rámci operace konfigurace:

  * `retry_total` – Maximální počet opakovaných pokusů.
  * `retry_backoff_max` – Maximální doba čekání na opakování v sekundách.
  * `retry_fixed_interval` – Pevný interval opakování v milisekundách
  * `retry_read` – Maximální počet soketů pro čtení a pokusů o opakování čtení
  * `retry_connect` – Maximální počet opakovaných pokusů o připojení.
  * `retry_status` – Maximální počet opakovaných pokusů u stavových kódů chyb.
  * `retry_on_status_codes` – Seznam specifických stavových kódů, které se mají opakovat.
  * `retry_backoff_factor` -Faktor pro výpočet čekací doby mezi opakovanými pokusy.

### <a name="400b3"></a>4.0.0 B3

* Přidání `create_database_if_not_exists()` a `create_container_if_not_exists` funkce v CosmosClient a databázi.

### <a name="400b2"></a>4.0.0 B2

* 4.0.0 B2 je druhá iterace v naší snaze vytvořit klientskou knihovnu, která bude vyhovovat osvědčeným postupům jazyka Python.

**Změny způsobující chyby**

* Připojení klienta bylo upraveno za účelem využívání kanálu HTTP definovaného v `azure.core.pipeline` .

* Interaktivní objekty se teď přejmenovaly jako proxy. Sem patří:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Byl aktualizován konstruktor `CosmosClient` :

  * `auth`Parametr byl přejmenován na `credential` a bude nyní přebírat typ ověřování přímo. To znamená, že hodnota primárního klíče, slovník tokenů prostředků nebo seznam oprávnění, se dají předat. Starý formát slovníku je však stále podporován.

  * `connection_policy`Parametr byl vytvořen jako parametr pouze klíčová slova a i když je stále podporován, každý z jednotlivých atributů zásady lze nyní předat jako explicitní argumenty klíčového slova:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Byl přidán nový konstruktor, aby bylo `CosmosClient` možné vytvořit pomocí připojovacího řetězce načteného z Azure Portal.

* Některé `read_all` operace byly přejmenovány na `list` operace:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Všechny operace, které převezmou `request_options` nebo jsou `feed_options` , se přesunuly na parametry jenom pro klíčové slovo. I když jsou tyto slovníky možností pořád podporované, každá z jednotlivých možností v rámci slovníku se teď podporuje jako explicitní argumenty klíčového slova.

* Hierarchie chyb je nyní děděna z `azure.core.AzureError` :

  * Přejmenování `HTTPFailure` na `CosmosHttpResponseError`
  * `JSONParseFailure` byl odebrán a nahrazen `azure.core.DecodeError`
  * Přidání dalších chyb pro konkrétní kódy odpovědí:
    * `CosmosResourceNotFoundError` pro stav 404
    * `CosmosResourceExistsError` pro stav 409
    * `CosmosAccessConditionFailedError` pro stav 412

* `CosmosClient` Nyní se můžete spustit ve Správci kontextu, aby bylo možné zpracovat ukončení připojení klienta.

* Odpovědi iterované (například odpovědi na dotazy a odpovědi na seznam) jsou nyní typu `azure.core.paging.ItemPaged` . Metoda byla `fetch_next_block` nahrazena sekundárním iterátorem, ke kterému přistupovala `by_page` metoda.

### <a name="400b1"></a>4.0.0 B1

Verze 4.0.0 B1 je první náhled našeho úsilí o vytvoření uživatelsky přívětivé klientské knihovny, která vyhovuje doporučeným postupům jazyka Python. Další informace o tomto a verzi Preview dalších knihoven Azure SDK najdete na stránce https://aka.ms/azure-sdk-preview1-python .

**Průlomové změny: nový návrh rozhraní API**

* Operace jsou nyní vymezeny na konkrétního klienta:

  * `CosmosClient`: Tento klient zpracovává operace na úrovni účtu. Patří sem Správa vlastností služby a výpis databází v rámci účtu.
  * `Database`: Tento klient zpracovává operace na úrovni databáze. To zahrnuje vytváření a odstraňování kontejnerů, uživatelů a uložených procedur. Dá se k ní přistupovat z instance cosmosClient podle názvu.
  * `Container`: Tento klient zpracovává operace pro konkrétní kontejner. To zahrnuje dotazování a vkládání položek a správu vlastností.
  * `User`: Tento klient zpracovává operace pro konkrétního uživatele. To zahrnuje přidávání a odstraňování oprávnění a správu vlastností uživatele.

    Tito klienti jsou k dispozici tak, že přejdou dolů hierarchii klienta pomocí `get_<child>_client` metody. Úplné podrobnosti o novém rozhraní API najdete v [referenční dokumentaci](https://aka.ms/azsdk-python-cosmos-ref).

* K klientům se přistupuje pomocí názvu a nikoli podle ID. Pro vytváření odkazů není nutné zřetězit řetězce.

* Není nutné importovat typy a metody z jednotlivých modulů. Plocha veřejného rozhraní API je k dispozici přímo v `azure.cosmos` balíčku.

* Jednotlivé vlastnosti žádosti lze zadat jako argumenty klíčového slova namísto vytvoření samostatné `RequestOptions` instance.

### <a name="302"></a>3.0.2

* Přidání podpory pro datový typ s více mnohoúhelníky
* Oprava chyby v zásadách opakování při čtení relace
* Oprava chyb pro nesprávné problémy s odsazením při dekódování základních řetězců 64

### <a name="301"></a>3.0.1

* Oprava chyb v LocationCache
* Oprava chyby koncovým bodem – logika opakování
* Pevná dokumentace

### <a name="300"></a>3.0.0

* Přidaná podpora zápisu pro více oblastí
* Změny názvů
  * DocumentClient na CosmosClient
  * Kolekce do kontejneru
  * Dokument k položce
  * Název balíčku se aktualizoval na Azure-Cosmos.
  * Obor názvů se aktualizoval na Azure. Cosmos.

### <a name="233"></a>2.3.3

* Přidání podpory pro proxy server
* Přidání podpory pro čtení kanálu změn
* Přidání podpory pro záhlaví kvóty kolekce
* Opravu chyb pro problémy s tokeny velkých relací
* Opravu chyb pro rozhraní API pro ReadMedia
* Opravu chyb v mezipaměti rozsahu klíčů oddílu

### <a name="232"></a>2.3.2

* Přidání podpory pro výchozí opakování při potížích s připojením

### <a name="231"></a>kládají

* Aktualizovaná dokumentace, aby odkazovala Azure Cosmos DB místo Azure DocumentDB.

### <a name="230"></a>2.3.0

* Tato verze sady SDK vyžaduje k dispozici nejnovější verzi nástroje Azure Cosmos DB emulátor pro stažení z https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* opravu chyb pro agregované dict –y
* opravu chyb pro ořezávání lomítek v odkazu na prostředek
* testuje kódování Unicode

### <a name="220"></a>2.2.0

* Byla přidána podpora funkce jednotek žádosti za minutu (RU/m).
* Přidání podpory pro novou úroveň konzistence s názvem ConsistentPrefix.

### <a name="210"></a>2.1.0

* Přidání podpory agregačních dotazů (COUNT, MIN, MAX, SUM a AVG).
* Přidání možnosti pro zakázání ověřování SSL při spuštění v emulátoru DocumentDB.
* Odebralo se omezení modulu závislých požadavků, aby byl přesně 2.10.0.
* Byla snížena minimální propustnost u dělených kolekcí z 10 100 RU/s na 2500 RU/s.
* Přidání podpory pro povolení protokolování skriptu během provádění uložených procedur.
* V této verzi je REST APIá verze předaná na 2017-01-19.

### <a name="201"></a>2.0.1

* Byly provedeny redakční změny dokumentačních komentářů.

### <a name="200"></a>2.0.0

* Byla přidána podpora Pythonu 3,5.
* Přidala se podpora sdružování připojení pomocí modulu požadavky.
* Byla přidána podpora konzistence relace.
* Přidání podpory pro dotazy TOP/ORDERBY pro dělené kolekce.

### <a name="190"></a>1.9.0

* Přidala se podpora zásad opakování pro omezené požadavky. (Omezení požadavků obdrží příliš velkou výjimku, kód chyby 429.) Ve výchozím nastavení se DocumentDB opakuje devět časů pro každý požadavek, pokud se zjistil kód chyby 429, který respektuje čas retryAfter v hlavičce odpovědi.
  Pevný čas intervalu opakování se teď dá nastavit jako součást vlastnosti RetryOptions objektu ConnectionPolicy, pokud chcete ignorovat dobu retryAfter vrácenou serverem mezi opakovanými pokusy.
  DocumentDB nyní čeká maximálně 30 sekund pro každý požadavek, který je omezený (bez ohledu na počet opakování), a vrátí odpověď s kódem chyby 429.
  Tuto dobu lze také přepsat ve vlastnosti RetryOptions objektu ConnectionPolicy.

* DocumentDB nyní vrací x-MS-Throttle-Return-Count a x-MS-Throttle-Retry-Time-MS jako hlavičky odpovědi v každé žádosti, aby se poznamenalo, že počet opakování omezení a kumulativní čas, který požadavek mezi opakovanými pokusy čekal.

* Odebrali jsme třídu RetryPolicy a odpovídající vlastnost (retry_policy) zveřejněnou ve třídě document_client a místo toho jste zavedli RetryOptions třídu, která vystavuje vlastnost RetryOptions ve třídě ConnectionPolicy, kterou lze použít k přepsání některých výchozích možností opakování.

### <a name="180"></a>1.8.0

* Přidání podpory pro geografické replikované databázové účty.
* Testovací opravy pro přesunutí globálního hostitele a masterKey do jednotlivých testovacích tříd.

### <a name="170"></a>1.7.0

* Přidali jsme podporu funkce Time to Live (TTL) pro dokumenty.

### <a name="161"></a>1.6.1

* Opravy chyb související s vytvářením oddílů na straně serveru pro povolení speciálních znaků v cestě klíče oddílu.

### <a name="160"></a>1.6.0

* Přidání podpory funkce dělené kolekce na straně serveru

### <a name="150"></a>1.5.0

* Do sady SDK se přidala horizontálního dělení architektura na straně klienta. Implementované třídy HashPartionResolver a RangePartitionResolver.

### <a name="142"></a>1.4.2

* Implementujte Upsert. Přidaly se nové metody UpsertXXX, které podporují funkci Upsert.
* Implementujte směrování na základě ID. Žádné změny veřejného rozhraní API, všechny změny jsou interní.

### <a name="130"></a>1.3.0

* Vynechává se vydaná verze, která přináší číslo verze v zarovnání s jinými sadami SDK.

### <a name="120"></a>1.2.0

* Podporuje geoprostorové indexy.
* Ověří vlastnost ID pro všechny prostředky. ID pro prostředky nesmí obsahovat `?, /, #, \\` znaky ani končit mezerou.
* Přidá novou hlavičku průběh transformace indexu do ResourceResponse.

### <a name="110"></a>1.1.0

* Implementuje zásadu indexování v2.

### <a name="101"></a>1.0.1

* Podporuje připojení proxy

## <a name="release--retirement-dates"></a>Data vyřazení & vydání

Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi. Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK.

> [!WARNING]
> Po 31. srpna 2022 už Azure Cosmos DB neprovádí opravy chyb, přidávají nové funkce a poskytuje podporu pro verze 1. x nebo 2. x sady Azure Cosmos DB Python SDK pro SQL API. Pokud nechcete upgradovat, požadavky odeslané z verze 1. x a 2. x sady SDK budou nadále obsluhovány službou Azure Cosmos DB.

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [4.0.0](#400) |20. května 2020 |--- |
| [3.0.2](#302) |15. listopadu 2018 |--- |
| [3.0.1](#301) |Říjen 04, 2018 |--- |
| [2.3.3](#233) |Září 08, 2018 |30. srpna 2020 |
| [2.3.2](#232) |8. května 2018 |30. srpna 2020 |
| [kládají](#231) |21. prosince 2017 |30. srpna 2020 |
| [2.3.0](#230) |10. listopadu 2017 |30. srpna 2020 |
| [2.2.1](#221) |Září 29, 2017 |30. srpna 2020 |
| [2.2.0](#220) |10. května 2017 |30. srpna 2020 |
| [2.1.0](#210) |Květen 01, 2017 |30. srpna 2020 |
| [2.0.1](#201) |30. října 2016 |30. srpna 2020 |
| [2.0.0](#200) |29. září 2016 |30. srpna 2020 |
| [1.9.0](#190) |7. července 2016 |30. srpna 2020 |
| [1.8.0](#180) |14. června 2016 |30. srpna 2020 |
| [1.7.0](#170) |26. dubna 2016 |30. srpna 2020 |
| [1.6.1](#161) |08 8. dubna 2016 |30. srpna 2020 |
| [1.6.0](#160) |29. března 2016 |30. srpna 2020 |
| [1.5.0](#150) |Leden 03, 2016 |30. srpna 2020 |
| [1.4.2](#142) |06 6. října 2015 |30. srpna 2020 |
| 1.4.1 |06 6. října 2015 |30. srpna 2020 |
| [1.2.0](#120) |6. srpna 2015 |30. srpna 2020 |
| [1.1.0](#110) |9. července 2015 |30. srpna 2020 |
| [1.0.1](#101) |25. května 2015 |30. srpna 2020 |
| 1.0.0 |7. dubna 2015 |30. srpna 2020 |
| 0.9.4 – zapůjčení |14. ledna 2015 |29. února 2016 |
| 0.9.3 – zapůjčení |9. prosince 2014 |29. února 2016 |
| 0.9.2 – zapůjčení |25. listopadu 2014 |29. února 2016 |
| 0.9.1 – zapůjčení |23. září 2014 |29. února 2016 |
| 0.9.0 – zapůjčení |21. srpna 2014 |29. února 2016 |

## <a name="faq"></a>Časté otázky

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky

Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) . 
