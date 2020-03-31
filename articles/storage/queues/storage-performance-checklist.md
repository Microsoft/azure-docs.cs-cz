---
title: Kontrolní seznam výkonu a škálovatelnosti pro úložiště front – Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s úložištěm fronty při vývoji vysoce výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75750510"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Kontrolní seznam výkonu a škálovatelnosti pro úložiště front

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s úložištěm front. Tento kontrolní seznam identifikuje klíčové postupy, které mohou vývojáři sledovat pro optimalizaci výkonu. Mějte tyto postupy na paměti při navrhování aplikace a v průběhu celého procesu.

Azure Storage má škálovatelnost a výkonnostní cíle pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech škálovatelnosti úložiště Azure najdete v [tématu Škálovatelnost a cíle výkonu pro standardní účty úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) a [škálovatelnost a cíle výkonu pro úložiště fronty](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek uspořádá osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace úložiště fronty.

| Hotovo | Kategorie | Posouzení návrhu |
| --- | --- | --- |
| &nbsp; |Cíle škálovatelnosti |[Můžete navrhnout aplikaci tak, aby používala maximálně maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cíle škálovatelnosti |[Vyhýbáte se blížícím se kapacitním a transakčním limitům?](#capacity-and-transaction-targets) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta dostatečně vysokou šířku pásma a nízkou latenci, aby dosáhla potřebného výkonu?](#throughput) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta vysoce kvalitní síťové spojení?](#link-quality) |
| &nbsp; |Síťové služby |[Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; |Přímý přístup klienta |[Používáte sdílené přístupové podpisy (SAS) a sdílení prostředků mezi zdroji (CORS) k povolení přímého přístupu k Azure Storage?](#sas-and-cors) |
| &nbsp; |Konfigurace rozhraní .NET |[Používáte rozhraní .NET Core 2.1 nebo novější pro optimální výkon?](#use-net-core) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste rozhraní .NET pro použití dostatečného počtu podprocesů v aplikacích .NET?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelnost |[Ujistili jste se, že paralelismus je vhodně ohraničen, abyste nepřetížili schopnosti klienta nebo nepřiblížili se k cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; |Nástroje |[Používáte nejnovější verze klientských knihoven a nástrojů poskytovaných společností Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Opakování |[Používáte zásadu opakování s exponenciálním zpětným omezením pro omezení chyb a časového nastavení?](#timeout-and-server-busy-errors) |
| &nbsp; |Opakování |[Vyhýbá se vaše aplikace opakování neopakovatelných chyb?](#non-retryable-errors) |
| &nbsp; |Konfigurace |[Vypnuli jste algoritmus Nagle, abyste zlepšili výkon malých požadavků?](#disable-nagle) |
| &nbsp; |Velikost zpráv |[Jsou vaše zprávy kompaktní pro zlepšení výkonu fronty?](#message-size) |
| &nbsp; |Hromadné načítání |[Načítáte více zpráv v jedné operaci GET?](#batch-retrieval) |
| &nbsp; |Frekvence dotazování |[Jste dotazování dostatečně často snížit vnímanou latenci vaší aplikace?](#queue-polling-interval) |
| &nbsp; |Aktualizovat zprávu |[Používáte operaci Aktualizovat zprávu k ukládání průběhu zpracování zpráv, takže se můžete vyhnout nutnosti znovu zpracovat celou zprávu, pokud dojde k chybě?](#use-update-message) |
| &nbsp; |Architektura |[Používáte fronty, aby se celá aplikace škálovatelnější udržováním dlouhotrvající úlohy z kritické cesty a škálování pak nezávisle?](#application-architecture) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje nebo překračuje některý z cílů škálovatelnosti, může dojít ke zvýšení latence transakcí nebo omezení. Když Azure Storage omezí vaši aplikaci, služba začne vracet kódy chyb 503 (Server zaneprázdněn) nebo 500 (časový limit operace). Vyhnout se tyto chyby tím, že zůstane v mezích cíle škálovatelnosti je důležitou součástí zvýšení výkonu vaší aplikace.

Další informace o cílech škálovatelnosti pro službu Frontnajdete v [tématu Škálovatelnost úložiště Azure a cíle výkonu](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se blížíte maximálnímu počtu účtů úložiště povoleným pro určitou kombinaci předplatného nebo oblasti, používáte více účtů úložiště k vytvoření oddílu pro zvýšení příchozího přenosu dat, odchozího přenosu dat, vstupně-výstupních operací za sekundu (IOPS) nebo kapacity? V tomto scénáři společnost Microsoft doporučuje využít zvýšené limity pro účty úložiště snížit počet účtů úložiště požadovaných pro vaše úlohy, pokud je to možné. Obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšení limitů pro váš účet úložiště. Další informace najdete [v tématu Oznámení větší, vyšší škálování účty úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitní a transakční cíle

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte přijetí jednoho z následujících přístupů:  

- Pokud cíle škálovatelnosti pro fronty jsou nedostatečné pro vaši aplikaci, použijte více front a distribuovat zprávy mezi nimi.
- Znovu zvažte zatížení, které způsobí, že vaše aplikace přístup nebo překročit cíl škálovatelnosti. Můžete jej navrhnout jinak, abyste využili menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte více účtů úložiště a rozdělte data aplikace mezi tyto účty více úložišť. Pokud použijete tento vzor, nezapomeňte navrhnout aplikaci tak, aby v budoucnu bylo možné přidat další účty úložiště pro vyrovnávání zatížení. Samotné účty úložiště nemají žádné jiné náklady než vaše použití, pokud jde o uložená data, provedené transakce nebo přenesená data.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte kompresi dat na straně klienta, abyste snížili šířku pásma potřebnou k odeslání dat do Služby Azure Storage.
    Zatímco komprese dat může ušetřit šířku pásma a zlepšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad na výkon dalších požadavků na zpracování komprese a dekomprese dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může ztížit řešení potíží, protože zobrazení dat pomocí standardních nástrojů může být náročnější.
- Pokud se vaše aplikace blíží cíle škálovatelnosti, ujistěte se, že používáte exponenciální backoff pro opakování. Je nejlepší se pokusit vyhnout dosažení cílů škálovatelnosti implementací doporučení popsaných v tomto článku. Použití exponenciálního backoff pro opakování však zabrání vaší aplikaci z opakování rychle, což by mohlo zhoršit omezení. Další informace naleznete v části s názvem [Chyby časového nastavení a zaneprázdnění serveru](#timeout-and-server-busy-errors).

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

## <a name="disable-nagle"></a>Zakázat Nagle

Algoritmus Společnosti Nagle je široce implementován v sítích TCP/IP jako prostředek ke zlepšení výkonu sítě. Není však optimální za všech okolností (například vysoce interaktivní prostředí). Nagle algoritmus má negativní dopad na výkon požadavků na azure table služby a měli byste zakázat, pokud je to možné.

## <a name="message-size"></a>Velikost zpráv

Výkon fronty a škálovatelnost se s růstem velikosti zprávy zmenšují. Do zprávy vložte pouze informace, které příjemce potřebuje.  

## <a name="batch-retrieval"></a>Dávkové načítání

Můžete načíst až 32 zpráv z fronty v jedné operaci. Dávkové načítání může snížit počet zpátečních cest z klientské aplikace, což je užitečné zejména pro prostředí, jako jsou mobilní zařízení, s vysokou latencí.  

## <a name="queue-polling-interval"></a>Interval dotazování fronty

Většina aplikací dotazování pro zprávy z fronty, což může být jedním z největších zdrojů transakcí pro tuto aplikaci. Vyberte interval dotazování moudře: dotazování příliš často může způsobit, že aplikace přístup k cíle škálovatelnosti pro frontu. Však na 200.000 transakcí za 0,01 USD (v době psaní), jeden procesor dotazování jednou za sekundu za měsíc by stálo méně než 15 centů, takže náklady není obvykle faktorem, který ovlivňuje výběr intervalu dotazování.  

Aktuální informace o nákladech najdete v [tématu Ceny za úložiště Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Použít aktualizační zprávu

Operaci Aktualizovat **zprávu** můžete použít ke zvýšení časového limitu neviditelnosti nebo k aktualizaci informací o stavu zprávy. Použití **aktualizační zprávy** může být efektivnější přístup než mít pracovní postup, který předává úlohu z jedné fronty do další, protože každý krok úlohy je dokončen. Aplikace může uložit stav úlohy do zprávy a potom pokračovat v práci, namísto requeuing zprávy pro další krok úlohy pokaždé, když krok dokončí. Mějte na paměti, že každá operace **aktualizační zprávy** se započítává do cíle škálovatelnosti.

## <a name="application-architecture"></a>Architektura aplikace

Pomocí front můžete architekturu aplikace škálovat. V následujícím seznamu jsou uvedeny některé způsoby, jak pomocí front vytvořit škálovatelnější aplikaci:  

- Fronty můžete použít k vytvoření nevyřízených položek práce pro zpracování a vyhlazení úloh ve vaší aplikaci. Můžete například zařadit do fronty požadavky uživatelů na provádění práce náročné na procesor, jako je změna velikosti nahraných obrázků.
- Fronty můžete použít k oddělení částí aplikace, abyste je mohli škálovat nezávisle. Například webový front-end může umístit výsledky průzkumu od uživatelů do fronty pro pozdější analýzu a úložiště. Můžete přidat další instance role pracovního procesu pro zpracování dat fronty podle potřeby.  

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště front](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty standardního úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)
