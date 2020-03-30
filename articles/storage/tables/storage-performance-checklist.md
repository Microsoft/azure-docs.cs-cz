---
title: Kontrolní seznam výkonu a škálovatelnosti pro úložiště tabulek – Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s table storage při vývoji vysoce výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749548"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Kontrolní seznam výkonu a škálovatelnosti pro úložiště tabulek

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s table storage. Tento kontrolní seznam identifikuje klíčové postupy, které mohou vývojáři sledovat pro optimalizaci výkonu. Mějte tyto postupy na paměti při navrhování aplikace a v průběhu celého procesu.

Azure Storage má škálovatelnost a výkonnostní cíle pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech škálovatelnosti úložiště Azure najdete v tématu [Škálovatelnost a cíle výkonu pro standardní účty úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) a [škálovatelnost a výkonnostní cíle pro table storage](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek uspořádá osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace úložiště tabulky.

| Hotovo | Kategorie | Posouzení návrhu |
| --- | --- | --- |
| &nbsp; |Cíle škálovatelnosti |[Můžete navrhnout aplikaci tak, aby používala maximálně maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cíle škálovatelnosti |[Vyhýbáte se blížícím se kapacitním a transakčním limitům?](#capacity-and-transaction-targets) |
| &nbsp; |Cíle škálovatelnosti |[Blížíte se k cílům škálovatelnosti entit za sekundu?](#targets-for-data-operations) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta dostatečně vysokou šířku pásma a nízkou latenci, aby dosáhla potřebného výkonu?](#throughput) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta vysoce kvalitní síťové spojení?](#link-quality) |
| &nbsp; |Síťové služby |[Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; |Přímý přístup klienta |[Používáte sdílené přístupové podpisy (SAS) a sdílení prostředků mezi zdroji (CORS) k povolení přímého přístupu k Azure Storage?](#sas-and-cors) |
| &nbsp; |Dávkování |[Je vaše aplikace dávkování aktualizací pomocí transakcí skupiny entit?](#batch-transactions) |
| &nbsp; |Konfigurace rozhraní .NET |[Používáte rozhraní .NET Core 2.1 nebo novější pro optimální výkon?](#use-net-core) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste rozhraní .NET pro použití dostatečného počtu podprocesů v aplikacích .NET?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelnost |[Ujistili jste se, že paralelismus je vhodně ohraničen, abyste nepřetížili schopnosti klienta nebo nepřiblížili se k cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; |Nástroje |[Používáte nejnovější verze klientských knihoven a nástrojů poskytovaných společností Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Opakování |[Používáte zásadu opakování s exponenciálním zpětným omezením pro omezení chyb a časového nastavení?](#timeout-and-server-busy-errors) |
| &nbsp; |Opakování |[Vyhýbá se vaše aplikace opakování neopakovatelných chyb?](#non-retryable-errors) |
| &nbsp; |Konfigurace |[Používáte JSON pro vaše požadavky na tabulka?](#use-json) |
| &nbsp; |Konfigurace |[Vypnuli jste algoritmus Nagle, abyste zlepšili výkon malých požadavků?](#disable-nagle) |
| &nbsp; |Tabulky a oddíly |[Už jste správně rozdělili svá data?](#schema) |
| &nbsp; |Horké oddíly |[Vyhýbáte se pouze přídavek a prepend-jen vzory?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Horké oddíly |[Jsou vaše vložky / aktualizace rozloženy do mnoha oddílů?](#high-traffic-data) |
| &nbsp; |Rozsah dotazu |[Navrhli jste schéma tak, aby umožňovalo použití bodových dotazů ve většině případů a dotazů na tabulky střídmě?](#query-scope) |
| &nbsp; |Hustota dotazu |[Do vaše dotazy obvykle pouze skenování a vrácení řádků, které vaše aplikace bude používat?](#query-density) |
| &nbsp; |Omezení vrácených dat |[Používáte filtrování, abyste se vyhnuli vrácení entit, které nejsou potřeba?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Omezení vrácených dat |[Používáte projekci, abyste se vyhnuli vrácení vlastností, které nejsou potřeba?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizace |[Došlo k denormalizování dat tak, že se při pokusu o získání dat vyhnete neefektivním dotazům nebo více násobným požadavkům na čtení?](#denormalization) |
| &nbsp; |Vložení, aktualizace a odstranění |[Dávkujete požadavky, které musí být transakční nebo mohou být provedeny současně, aby se snížily zpáteční lety?](#batching) |
| &nbsp; |Vložení, aktualizace a odstranění |[Vyhýbáte se načítání entity jen proto, abyste zjistili, zda chcete volat insert nebo update?](#upsert) |
| &nbsp; |Vložení, aktualizace a odstranění |[Zvažovali jste ukládání řady dat, která budou často načtena společně v jedné entitě jako vlastnosti namísto více entit?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Vložení, aktualizace a odstranění |[U entit, které budou vždy načteny společně a mohou být zapsány v dávkách (například data časových řad), jste zvažovali použití objektů BLOB namísto tabulek?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje nebo překračuje některý z cílů škálovatelnosti, může dojít ke zvýšení latence transakcí nebo omezení. Když Azure Storage omezí vaši aplikaci, služba začne vracet kódy chyb 503 (Server zaneprázdněn) nebo 500 (časový limit operace). Vyhnout se tyto chyby tím, že zůstane v mezích cíle škálovatelnosti je důležitou součástí zvýšení výkonu vaší aplikace.

Další informace o cílech škálovatelnosti pro službu Table service naleznete v tématu [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se blížíte maximálnímu počtu účtů úložiště povoleným pro určitou kombinaci předplatného nebo oblasti, používáte více účtů úložiště k vytvoření oddílu pro zvýšení příchozího přenosu dat, odchozího přenosu dat, vstupně-výstupních operací za sekundu (IOPS) nebo kapacity? V tomto scénáři společnost Microsoft doporučuje využít zvýšené limity pro účty úložiště snížit počet účtů úložiště požadovaných pro vaše úlohy, pokud je to možné. Obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšení limitů pro váš účet úložiště. Další informace najdete [v tématu Oznámení větší, vyšší škálování účty úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitní a transakční cíle

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte přijetí jednoho z následujících přístupů:  

- Znovu zvažte zatížení, které způsobí, že vaše aplikace přístup nebo překročit cíl škálovatelnosti. Můžete jej navrhnout jinak, abyste využili menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte více účtů úložiště a rozdělte data aplikace mezi tyto účty více úložišť. Pokud použijete tento vzor, nezapomeňte navrhnout aplikaci tak, aby v budoucnu bylo možné přidat další účty úložiště pro vyrovnávání zatížení. Samotné účty úložiště nemají žádné jiné náklady než vaše použití, pokud jde o uložená data, provedené transakce nebo přenesená data.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte kompresi dat na straně klienta, abyste snížili šířku pásma potřebnou k odeslání dat do Služby Azure Storage.
    Zatímco komprese dat může ušetřit šířku pásma a zlepšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad na výkon dalších požadavků na zpracování komprese a dekomprese dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může ztížit řešení potíží, protože zobrazení dat pomocí standardních nástrojů může být náročnější.
- Pokud se vaše aplikace blíží cíle škálovatelnosti, ujistěte se, že používáte exponenciální backoff pro opakování. Je nejlepší se pokusit vyhnout dosažení cílů škálovatelnosti implementací doporučení popsaných v tomto článku. Použití exponenciálního backoff pro opakování však zabrání vaší aplikaci z opakování rychle, což by mohlo zhoršit omezení. Další informace naleznete v části s názvem [Chyby časového nastavení a zaneprázdnění serveru](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Cíle pro datové operace

Azure Storage vyrovnává zatížení jako provoz na váš účet úložiště zvyšuje, ale pokud provoz vykazuje náhlé shluky, nemusí být možné získat tento objem propustnost okamžitě. Očekávejte omezení nebo časové to-outběhem burst jako Azure Storage automaticky vyrovnává zatížení vaší tabulky. Zrychlování pomalu obecně poskytuje lepší výsledky, protože systém má čas na vyvážení zatížení odpovídajícím způsobem.

#### <a name="entities-per-second-storage-account"></a>Entity za sekundu (účet úložiště)

Limit škálovatelnosti pro přístup k tabulkám je až 20 000 entit (po 1 KB) za sekundu pro účet. Obecně platí, že každá entita, která je vložena, aktualizována, odstraněna nebo naskenována, se počítá k tomuto cíli. Dávková vložka, která obsahuje 100 entit, by se tedy počítala jako 100 entit. Dotaz, který prohledá 1000 entit a vrátí 5 by se počítat jako 1000 entit.

#### <a name="entities-per-second-partition"></a>Entity za sekundu (oddíl)

V rámci jednoho oddílu cíl škálovatelnostpro přístup k tabulkám je 2 000 entit (1 KB každý) za sekundu, pomocí stejné inventury, jak je popsáno v předchozí části.

## <a name="networking"></a>Síťové služby

Fyzická síťová omezení aplikace může mít významný dopad na výkon. V následujících částech jsou popsána některá omezení, se kterými se mohou uživatelé setkat.  

### <a name="client-network-capability"></a>Možnosti klientské sítě

Šířka pásma a kvalita síťového propojení hrají důležitou roli ve výkonu aplikace, jak je popsáno v následujících částech.

#### <a name="throughput"></a>Propustnost

Pro šířku pásma, problém je často možnosti klienta. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší limity sítě z jednoho počítače. Pokud přistupujete k Azure Storage z místní aplikace, platí stejné pravidlo: porozumět síťovým možnostem klientského zařízení a síťovému připojení k umístění Azure Storage a buď je podle potřeby vylepšit, nebo navrhnout své aplikace pro práci v rámci svých schopností.

#### <a name="link-quality"></a>Kvalita propojení

Stejně jako u všech síťových použití mějte na paměti, že síťové podmínky, které vedou k chybám a ztrátě paketů, zpomalí efektivní propustnost.  Použití WireShark nebo NetMon může pomoci při diagnostice tohoto problému.  

### <a name="location"></a>Umístění

V jakémkoli distribuovaném prostředí poskytuje umístění klienta v blízkosti serveru nejlepší výkon. Pro přístup k Azure Storage s nejnižší latencí, nejlepší umístění pro vašeho klienta je ve stejné oblasti Azure. Například pokud máte webovou aplikaci Azure, která používá Azure Storage, pak vyhledejte oba v rámci jedné oblasti, jako je například USA – západ nebo Asie – jihovýchod. Co-locating prostředky snižuje latenci a náklady, protože využití šířky pásma v rámci jedné oblasti je zdarma.  

Pokud klientské aplikace budou přistupovat k Azure Storage, ale nejsou hostované v rámci Azure, jako jsou aplikace pro mobilní zařízení nebo místní podnikové služby, pak umístění účtu úložiště v oblasti v blízkosti těchto klientů může snížit latenci. Pokud jsou vaši klienti široce distribuováni (například někteří v Severní Americe a někteří v Evropě), zvažte použití jednoho účtu úložiště pro každý region. Tento přístup je jednodušší implementovat, pokud data, která aplikace ukládá, jsou specifická pro jednotlivé uživatele a nevyžaduje replikaci dat mezi účty úložiště.

## <a name="sas-and-cors"></a>SAS a CORS

Předpokládejme, že potřebujete autorizovat kód, jako je JavaScript, který běží ve webovém prohlížeči uživatele nebo v aplikaci pro mobilní telefon pro přístup k datům ve službě Azure Storage. Jedním z přístupů je vytvoření aplikace služby, která funguje jako proxy server. Zařízení uživatele se ověřuje pomocí služby, která zase autorizuje přístup k prostředkům Azure Storage. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tento přístup však klade významné režie na aplikaci služby, protože všechna data přenesená mezi zařízením uživatele a Azure Storage musí projít aplikací služby.

Můžete se vyhnout použití aplikace služby jako proxy pro Azure Storage pomocí sdílených přístupových podpisů (SAS). Pomocí SAS můžete povolit zařízení uživatele, aby požadavky přímo do Služby Azure Storage pomocí tokenu omezený přístup. Například pokud uživatel chce nahrát fotografii do vaší aplikace, pak aplikace služby můžete vygenerovat SAS a odeslat do zařízení uživatele. Token SAS můžete udělit oprávnění k zápisu do prostředku úložiště Azure pro zadaný časový interval, po kterém vyprší platnost tokenu SAS. Další informace o SAS najdete [v tématu udělení omezeného přístupu k prostředkům Úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../common/storage-sas-overview.md)  

Webový prohlížeč obvykle nepovolí JavaScript na stránce, která je hostována webem v jedné doméně, provádět určité operace, například operace zápisu, do jiné domény. Tato zásada, známá jako zásady stejného původu, zabraňuje škodlivému skriptu na jedné stránce získat přístup k datům na jiné webové stránce. Zásady stejného původu však může být omezení při vytváření řešení v cloudu. Sdílení prostředků mezi zdroji (CORS) je funkce prohlížeče, která umožňuje cílové doméně komunikovat s prohlížečem, kterému důvěřuje požadavkům pocházejícím ze zdrojové domény.

Předpokládejme například, že webová aplikace spuštěná v Azure vytvoří požadavek na prostředek pro účet úložiště Azure. Webová aplikace je zdrojová doména a účet úložiště je cílová doména. Cors můžete nakonfigurovat pro kteroukoli službu Azure Storage tak, aby komunikovala s webovým prohlížečem, že požadavky ze zdrojové domény jsou službou Azure Storage důvěryhodné. Další informace o CORS najdete v [tématu podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS i CORS vám mohou pomoci vyhnout se zbytečnému zatížení webové aplikace.  

## <a name="batch-transactions"></a>Dávkové transakce

Služba Table podporuje dávkové transakce u entit, které jsou ve stejné tabulce a patří do stejné skupiny oddílů. Další informace naleznete [v tématu Provádění transakcí skupiny entit](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Konfigurace rozhraní .NET

Pokud používáte rozhraní .NET Framework, tato část obsahuje několik nastavení rychlé konfigurace, které můžete použít k významnému zlepšení výkonu.  Pokud používáte jiné jazyky, zkontrolujte, zda podobné koncepty platí ve zvoleném jazyce.  

### <a name="use-net-core"></a>Použití jádra rozhraní .NET

Vyvíjejte aplikace azure storage s rozhraním .NET Core 2.1 nebo novějším a využijte výhod vylepšení výkonu. Pokud je to možné, doporučujeme použít rozhraní .NET Core 3.x.

Další informace o vylepšení výkonu v .NET Core najdete v následujících příspěvcích blogu:

- [Vylepšení výkonu v rozhraní .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Vylepšení výkonu v .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zvýšení výchozího limitu připojení

V rozhraní .NET následující kód zvyšuje výchozí limit připojení (což je obvykle 2 v klientském prostředí nebo 10 v prostředí serveru) na 100. Obvykle byste měli nastavit hodnotu přibližně počet podprocesů používaných vaší aplikací.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Před otevřením připojení nastavte limit připojení.  

Další programovací jazyky naleznete v dokumentaci k tomuto jazyce a zjistěte, jak nastavit limit připojení.  

Další informace naleznete v příspěvku blogu [Web Services: Souběžná připojení](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Zvýšení minimálního počtu vláken

Pokud používáte synchronní volání společně s asynchronními úlohami, můžete zvýšit počet vláken ve fondu vláken:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete [threadpool.setminthreads](/dotnet/api/system.threading.threadpool.setminthreads) metoda.  

## <a name="unbounded-parallelism"></a>Neohraničený paralelismus

Zatímco paralelismus může být skvělé pro výkon, buďte opatrní při použití neohraničené paralelismu, což znamená, že neexistuje žádné omezení vynuceno na počet podprocesů nebo paralelní požadavky. Nezapomeňte omezit paralelní požadavky na nahrávání nebo stahování dat, přístup k více oddílů ve stejném účtu úložiště nebo přístup k více položek ve stejném oddílu. Pokud je paralelismus neomezený, vaše aplikace může překročit možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezení.  

## <a name="client-libraries-and-tools"></a>Klientské knihovny a nástroje

Pro dosažení nejlepšího výkonu vždy používejte nejnovější klientské knihovny a nástroje poskytované společností Microsoft. Klientské knihovny Azure Storage jsou k dispozici pro různé jazyky. Azure Storage taky podporuje PowerShell a Azure CLI. Společnost Microsoft aktivně vyvíjí tyto klientské knihovny a nástroje s ohledem na výkon, udržuje je v aktuálním stavu s nejnovějšími verzemi služeb a zajišťuje, že interně zpracovávají mnoho osvědčených postupů výkonu. Další informace najdete v [referenční dokumentaci k úložišti Azure](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Zpracování chyb služby

Azure Storage vrátí chybu, když služba nemůže zpracovat požadavek. Pochopení chyb, které mohou být vráceny službou Azure Storage v daném scénáři, je užitečné pro optimalizaci výkonu.

### <a name="timeout-and-server-busy-errors"></a>Chyby časového oběhu a zaneprázdnění serveru

Azure Storage může omezit vaši aplikaci, pokud se blíží omezení škálovatelnosti. V některých případech Azure Storage nemusí být schopen zpracovat požadavek z důvodu některých přechodný stav. V obou případech může služba vrátit chybu 503 (Server Busy) nebo 500 (Timeout). Tyto chyby může dojít také v případě, že služba je vyvážení datových oddílů, aby vyšší propustnost. Klientská aplikace by měla obvykle opakovat operaci, která způsobí jednu z těchto chyb. Pokud však Azure Storage je omezení vaší aplikace, protože překračuje cíle škálovatelnosti, nebo i v případě, že služba nebyla schopna obsluhovat požadavek z nějakého jiného důvodu, agresivní opakování může problém zhoršit. Pomocí exponenciální zpět vypnout zásady opakování se doporučuje a klientské knihovny výchozí toto chování. Aplikace může například opakovat po 2 sekundách, pak 4 sekundách, pak 10 sekundách, pak 30 sekundách a pak se úplně vzdát. Tímto způsobem aplikace výrazně snižuje jeho zatížení služby, spíše než zhoršuje chování, které by mohly vést k omezení.  

Chyby připojení lze zopakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že přechodné.  

### <a name="non-retryable-errors"></a>Neopakovatelné chyby

Klientské knihovny zpracovávají opakování s vědomím, které chyby lze opakovat a které nelze. Pokud však voláte rozhraní API azure storage REST, existují některé chyby, které byste neměli opakovat. Například chyba 400 (Chybný požadavek) označuje, že klientská aplikace odeslala požadavek, který nemohl být zpracován, protože nebyl v očekávané podobě. Opětovné odeslání tohoto požadavku výsledky stejnou odpověď pokaždé, takže nemá smysl při jeho opakování. Pokud voláte rozhraní API azure storage REST api přímo, uvědomte si potenciální chyby a zda by měly být opakovány.

Další informace o chybových kódech Služby Azure Storage najdete v [tématu Stavové a chybové kódy](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Konfigurace

V této části je uvedeno několik rychlých nastavení konfigurace, která lze použít k významným vylepšením výkonu ve službě Table Service:

### <a name="use-json"></a>Použití JSON

Počínaje verzí služby úložiště 2013-08-15 podporuje služba Table použití json namísto formátu AtomPub založeného na XML pro přenos dat tabulky. Použití JSON můžete snížit velikost datové části až o 75 % a může výrazně zlepšit výkon vaší aplikace.

Další informace najdete v příspěvku [Microsoft Azure Tables: Introducing JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) and [Payload Format for Table Service Operations](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Zakázat Nagle

Algoritmus Společnosti Nagle je široce implementován v sítích TCP/IP jako prostředek ke zlepšení výkonu sítě. Není však optimální za všech okolností (například vysoce interaktivní prostředí). Nagle algoritmus má negativní dopad na výkon požadavků na azure table služby a měli byste zakázat, pokud je to možné.

## <a name="schema"></a>Schéma

Jak představují a dotaz na data je největší jediný faktor, který ovlivňuje výkon table service. Zatímco každá aplikace je jiná, tato část popisuje některé obecné osvědčené postupy, které se týkají:

- Návrh tabulky
- Efektivní dotazy
- Efektivní aktualizace dat

### <a name="tables-and-partitions"></a>Tabulky a oddíly

Tabulky jsou rozděleny do oddílů. Každá entita uložená v oddílu sdílí stejný klíč oddílu a má jedinečný klíč řádku k jeho identifikaci v rámci tohoto oddílu. Oddíly poskytují výhody, ale také zavést omezení škálovatelnosti.

- Výhody: Entity ve stejném oddílu můžete aktualizovat v jedné atomické, dávkové transakci, která obsahuje až 100 samostatných operací úložiště (limit celkové velikosti 4 MB). Za předpokladu, že stejný počet entit, které mají být načteny, můžete také dotaz ovat data v rámci jednoho oddílu efektivněji než data, která zahrnuje oddíly (i když čtěte dál pro další doporučení na dotazování na data tabulky).
- Omezení škálovatelnosti: Přístup k entitám uloženým v jednom oddílu nelze vyvážit zatížení, protože oddíly podporují atomické dávkové transakce. Z tohoto důvodu cíl škálovatelnostpro jednotlivé tabulky oddíl je nižší než pro table service jako celek.

Z důvodu těchto vlastností tabulek a oddílů byste měli přijmout následující principy návrhu:

- Vyhledejte data, která klientská aplikace často aktualizuje nebo dotazuje ve stejné logické jednotce práce ve stejném oddílu. Například vyhledejte data ve stejném oddílu, pokud vaše aplikace agreguje zápisy nebo provádíte atomické dávkové operace. Také data v jednom oddílu lze efektivněji dotazován v jednom dotazu než data napříč oddíly.
- Vyhledejte data, která klientská aplikace nevloží, neaktualizuje ani nedotazuje ve stejné logické jednotce práce (tj. v jednom dotazu nebo dávkové aktualizaci) v samostatných oddílech. Mějte na paměti, že neexistuje žádné omezení počtu klíčů oddílů v jedné tabulce, takže mít miliony klíčů oddílů není problém a nebude mít vliv na výkon. Například pokud vaše aplikace je populární webové stránky s přihlášením uživatele, pomocí ID uživatele jako klíč oddílu může být dobrou volbou.

#### <a name="hot-partitions"></a>Horké oddíly

Aktivní oddíl je ten, který přijímá neúměrné procento provozu na účet a nemůže být vyrovnávání zatížení, protože se jedná o jeden oddíl. Obecně platí, že horké oddíly jsou vytvořeny jedním ze dvou způsobů:

#### <a name="append-only-and-prepend-only-patterns"></a>Pouze připojit a pouze předpubertální vzory

Vzor "Připojit pouze" je ten, kde se zvyšuje a snižuje všechny (nebo téměř všechny) přenosy na daný klíč oddílu a snižuje se podle aktuálního času. Předpokládejme například, že vaše aplikace používá aktuální datum jako klíč oddílu pro data protokolu. Výsledkem tohoto návrhu je, že všechny vloží se do posledního oddílu v tabulce a systém nemůže správně vyvážit zatížení. Pokud objem provozu na tento oddíl překročí cíl škálovatelnosti na úrovni oddílu, bude mít za následek omezení. Je lepší zajistit, aby byl provoz odeslán do více oddílů, aby bylo možné vyrovnat zatížení požadavků v celé tabulce.

#### <a name="high-traffic-data"></a>Údaje o vysokém provozu

Pokud vaše schéma dělení má za následek jeden oddíl, který má pouze data, která je mnohem více než jiné oddíly, můžete také zobrazit omezení jako tento oddíl přístup y cíl škálovatelnostpro jeden oddíl. Je lepší se ujistit, že vaše schéma oddílů má za následek žádný jediný oddíl blížící se cílům škálovatelnosti.

### <a name="querying"></a>Dotazování

Tato část popisuje osvědčené postupy pro dotazování na službu Table Service.

#### <a name="query-scope"></a>Rozsah dotazu

Existuje několik způsobů, jak určit rozsah entit dotaz. Následující seznam popisuje každou možnost pro obor dotazu.

- **Bodové dotazy:**- Bodový dotaz načte přesně jednu entitu zadáním klíče oddílu a klíče řádku entity, kterou chcete načíst. Tyto dotazy jsou efektivní a měli byste je použít, kdykoli je to možné.
- **Dotazy na oddíly:** Dotaz na oddíl je dotaz, který načítá sadu dat, která sdílí společný klíč oddílu. Dotaz obvykle určuje rozsah hodnot klíče řádku nebo rozsah hodnot pro některé vlastnosti entity kromě klíče oddílu. Tyto dotazy jsou méně efektivní než bodové dotazy a měly by být používány střídmě.
- **Dotazy na tabulky:** Dotaz na tabulku je dotaz, který načítá sadu entit, které nesdílejí společný klíč oddílu. Tyto dotazy nejsou efektivní a měli byste se jim vyhnout, pokud je to možné.

Obecně se vyhněte prohledávání (dotazy větší než jedna entita), ale pokud je nutné skenovat, pokuste se uspořádat data tak, aby skeny načítaly data, která potřebujete, bez skenování nebo vrácení významného množství entit, které nepotřebujete.

#### <a name="query-density"></a>Hustota dotazu

Dalším klíčovým faktorem účinnosti dotazu je počet vrácených entit ve srovnání s počtem entit naskenovaných za účelem nalezení vrácené sady. Pokud aplikace provede dotaz na tabulku s filtrem pro hodnotu vlastnosti, která pouze 1 % datových podílů, dotaz prohledá 100 entit pro každou entitu, kterou vrátí. Cíle škálovatelnosti tabulky popsané dříve se týkají počtu naskenovaných entit a nikoli počtu vrácených entit: nízká hustota dotazu může snadno způsobit, že služba Table omezí vaši aplikaci, protože musí skenovat tolik entit, aby entitu, kterou hledáte. Další informace o tom, jak se vyhnout škrcení, naleznete v části s názvem [Denormalizace](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Omezení množství vrácených dat

Pokud víte, že dotaz vrátí entity, které nepotřebujete v klientské aplikaci, zvažte použití filtru ke zmenšení velikosti vrácené sady. Zatímco entity, které nejsou vráceny klientovi, se stále počítají do omezení škálovatelnosti, výkon aplikace se zlepší z důvodu snížené velikosti datové části sítě a sníženého počtu entit, které musí klientská aplikace zpracovat. Mějte na paměti, že cíle škálovatelnosti se vztahují k počtu naskenovaných entit, takže dotaz, který filtruje mnoho entit, může stále vést k omezení, i když je vráceno několik entit. Další informace o efektivním dotazování naleznete v části s názvem [Hustota dotazu](#query-density).

Pokud vaše klientská aplikace potřebuje pouze omezenou sadu vlastností z entit v tabulce, můžete použít projekci k omezení velikosti vrácené sady dat. Stejně jako u filtrování, projekce pomáhá snížit zatížení sítě a zpracování klienta.

#### <a name="denormalization"></a>Denormalizace

Na rozdíl od práce s relačními databázemi vedou osvědčené postupy pro efektivní dotazování na data tabulky k denormalizaci dat. To znamená duplikovat stejná data ve více entitách (jeden pro každý klíč, který můžete použít k vyhledání dat), abyste minimalizovali počet entit, které musí dotaz prohledat, aby našel data, která klient potřebuje, místo toho, abyste museli skenovat velký počet entit, abyste našli data, která vaše potřeby aplikace. Například na webových stránkách elektronického obchodu můžete chtít najít objednávku jak podle ID zákazníka (dejte mi objednávky tohoto zákazníka), tak podle data (dejte mi objednávky k datu). V table storage je nejlepší uložit entitu (nebo odkaz) dvakrát – jednou s názvem tabulky, PK a RK, aby se usnadnilo hledání podle ID zákazníka, jednou pro usnadnění jeho nalezení k datu.  

### <a name="insert-update-and-delete"></a>Vložení, aktualizace a odstranění

Tato část popisuje osvědčené postupy pro úpravu entit uložených ve službě Table Service.  

#### <a name="batching"></a>Dávkování

Dávkové transakce se ve službě Azure Storage označují jako transakce skupiny entit. Všechny operace v rámci transakce skupiny entit musí být na jednom oddílu v jedné tabulce. Pokud je to možné, použijte transakce skupiny entit k provádění vložení, aktualizací a odstranění v dávkách. Použití transakcí skupiny entit snižuje počet zpátečních cest z klientské aplikace na server, snižuje počet fakturovatelných transakcí (transakce skupiny entit se pro účely fakturace počítá jako jedna transakce a může obsahovat až 100 operace úložiště) a umožňuje atomické aktualizace (všechny operace úspěšné nebo všechny selhání v rámci transakce skupiny entit). Prostředí s vysokou latencí, jako jsou mobilní zařízení, budou mít velký prospěch z použití transakcí skupiny entit.  

#### <a name="upsert"></a>Upsert

Pokud je to možné, používejte stolní operace **Upsert.** Existují dva typy **Upsert**, z nichž oba mohou být efektivnější než tradiční **vložení** a **aktualizace** operace:  

- **InsertOrMerge**: Tuto operaci použijte, pokud chcete nahrát podmnožinu vlastností entity, ale nejste si jisti, zda entita již existuje. Pokud entita existuje, toto volání aktualizuje vlastnosti zahrnuté v operaci **Upsert** a ponechá všechny existující vlastnosti tak, jak jsou, pokud entita neexistuje, vloží novou entitu. To je podobné jako pomocí projekce v dotazu, v tom, že stačí nahrát vlastnosti, které se mění.
- **InsertOrReplace**: Tuto operaci použijte, pokud chcete nahrát zcela novou entitu, ale nejste si jisti, zda již existuje. Tuto operaci použijte, pokud víte, že nově nahraná entita je zcela správná, protože zcela přepíše starou entitu. Chcete například aktualizovat entitu, která ukládá aktuální umístění uživatele bez ohledu na to, zda aplikace dříve uložila data o poloze pro uživatele; nová entita skladového místa je dokončena a nepotřebujete žádné informace od žádné předchozí entity.

#### <a name="storing-data-series-in-a-single-entity"></a>Ukládání datových řad do jedné entity

Aplikace někdy ukládá řadu dat, která často potřebuje načíst všechny najednou: například aplikace může sledovat využití procesoru v průběhu času za účelem vykreslení průběžného grafu dat z posledních 24 hodin. Jedním z přístupů je mít jednu entitu tabulky za hodinu, přičemž každá entita představuje určitou hodinu a ukládá využití procesoru pro tuto hodinu. Chcete-li vykreslit tato data, aplikace musí načíst entity, které drží data z posledních 24 hodin.  

Alternativně aplikace může uložit využití procesoru pro každou hodinu jako samostatnou vlastnost jedné entity: aktualizovat každou hodinu, aplikace můžete použít jeden **InsertOrMerge Upsert** volání aktualizovat hodnotu pro poslední hodinu. Chcete-li vykreslit data, aplikace potřebuje pouze načíst jednu entitu namísto 24, což pro efektivní dotaz. Další informace o efektivitě dotazu naleznete v části s názvem [Obor dotazu](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Ukládání strukturovaných dat do objektů BLOB

Pokud provádíte dávkové vkládání a potom načítáte rozsahy entit společně, zvažte použití objektů BLOB namísto tabulek. Dobrým příkladem je soubor protokolu. Můžete dávkové několik minut protokolů a vložit je a potom načíst několik minut protokolů najednou. V tomto případě výkon je lepší, pokud používáte objekty BLOB namísto tabulek, protože můžete výrazně snížit počet objektů zapsaných nebo přečtených a také případně počet požadavků, které je třeba provést.  

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty standardního úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)
