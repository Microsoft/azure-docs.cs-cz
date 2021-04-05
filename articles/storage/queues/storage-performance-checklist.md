---
title: Kontrolní seznam pro výkon a škálovatelnost Queue Storage-Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s Queue Storage při vývoji vysoce výkonných aplikací.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585662"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Kontrolní seznam pro výkon a škálovatelnost pro Queue Storage

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s Queue Storage. Tento kontrolní seznam popisuje klíčové postupy, které můžou vývojáři použít k optimalizaci výkonu. Při navrhování aplikace a celého procesu mějte na paměti tyto postupy.

Azure Storage má cíle škálovatelnosti a výkonu pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech Azure Storage škálovatelnosti najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) a [škálovatelnost a výkonnostní cíle pro Queue Storage](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek organizuje osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace Queue Storage.

| Hotovo | Kategorie | Aspekt návrhu |
|--|--|--|
| &nbsp; | Cíle škálovatelnosti | [Můžete navrhnout aplikaci tak, aby nepoužívala více než maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; | Cíle škálovatelnosti | [Nechcete se vyhnout přístupu k kapacitě a omezením transakcí?](#capacity-and-transaction-targets) |
| &nbsp; | Sítě | [Mají zařízení na straně klienta dostatečně velkou šířku pásma a nízkou latenci pro dosažení potřebného výkonu?](#throughput) |
| &nbsp; | Sítě | [Má zařízení na straně klienta vysoce kvalitní síťový odkaz?](#link-quality) |
| &nbsp; | Sítě | [Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; | Přímý přístup klienta | [Používáte k tomu přímý přístup k Azure Storage pomocí sdílených přístupových podpisů (SAS) a sdílení prostředků mezi zdroji (CORS)?](#sas-and-cors) |
| &nbsp; | Konfigurace .NET | [Používáte pro optimální výkon .NET Core 2,1 nebo novější?](#use-net-core) |
| &nbsp; | Konfigurace .NET | [Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; | Konfigurace .NET | [Pro aplikace .NET jste nakonfigurovali .NET pro použití dostatečného počtu vláken?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Paralelismu | [Měli byste zajistit, aby byl paralelismu správně ohraničený, takže nebudete přetěžovat možnosti svého klienta nebo se přiblížíte cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; | nástroje | [Používáte nejnovější verze klientských knihoven a nástrojů od společnosti Microsoft?](#client-libraries-and-tools) |
| &nbsp; | Opakování | [Používáte zásady opakování s exponenciálním omezení rychlosti pro omezení chyb a časových limitů?](#timeout-and-server-busy-errors) |
| &nbsp; | Opakování | [Vyloučí vaše aplikace opakované pokusy o neopakující se chyby?](#non-retryable-errors) |
| &nbsp; | Konfigurace | [Vypnuli jste algoritmus Nagle, aby se zlepšil výkon malých požadavků?](#disable-nagles-algorithm) |
| &nbsp; | Velikost zpráv | [Mají vaše zprávy kompresi pro zlepšení výkonu fronty?](#message-size) |
| &nbsp; | Hromadné načtení | [Načítáte více zpráv v rámci jedné operace Get?](#batch-retrieval) |
| &nbsp; | Frekvence cyklického dotazování | [Je dotazování na často dostatečně časté, aby se snížila zjištěná latence vaší aplikace?](#queue-polling-interval) |
| &nbsp; | Zpráva aktualizace | [Provádíte operaci aktualizace zprávy, která ukládá průběh zpracování zpráv, takže se můžete vyhnout nutnosti opakovanému zpracování celé zprávy, pokud dojde k chybě?](#perform-an-update-message-operation) |
| &nbsp; | Architektura | [Používáte fronty k zajištění větší škálovatelnosti celé aplikace tím, že zachováte dlouhodobě spuštěné úlohy ze kritické cesty a škálování pak nezávisle na sobě?](#application-architecture) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje k některým cílům škálovatelnosti nebo překročí, může dojít ke zvýšené latenci transakcí nebo omezování. Když aplikace Azure Storage omezí vaši aplikaci, začne služba vracet kódy chyb 503 ( `Server Busy` ) nebo 500 ( `Operation Timeout` ). Zamezení těchto chyb zachováním omezení cílů škálovatelnosti je důležitou součástí zvýšení výkonu aplikace.

Další informace o cílech škálovatelnosti pro Queue Storage najdete v tématu [Azure Storage cíle škálovatelnosti a výkonu](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se přiblížíte k maximálnímu počtu účtů úložiště povolených pro určitou kombinaci předplatného nebo oblasti, budete k horizontálních oddílů používat víc účtů úložiště, abyste mohli zvýšit počet vstupně-výstupních operací za sekundu (IOPS) nebo kapacitu? V tomto scénáři Microsoft doporučuje, abyste využili vyšší omezení pro účty úložiště, abyste snížili počet účtů úložiště potřebných pro vaše zatížení, pokud je to možné. Kontaktujte [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšená omezení pro váš účet úložiště. Další informace najdete v tématu [oznamujeme větší, vyšší škálování účtů úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cíle kapacity a transakce

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte použití jednoho z následujících přístupů:

- Pokud cíle škálovatelnosti pro fronty nejsou pro vaši aplikaci dostatečné, použijte více front a distribuujte mezi nimi zprávy.
- Převezměte v úvahu zatížení, které způsobí, že vaše aplikace bude přistupovat k cíli škálovatelnosti nebo nad něj. Můžete ji navrhovat odlišně, abyste mohli používat menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte několik účtů úložiště a vytvořte oddíly dat aplikací napříč těmito různými účty úložiště. Pokud použijete tento model, nezapomeňte navrhnout aplikaci, abyste mohli v budoucnu přidat další účty úložiště pro vyrovnávání zatížení. Účty úložiště samy nemají žádné náklady jiné než vaše využití v závislosti na uložených datech, provedených transakcích nebo přenesených datech.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte komprimaci dat na straně klienta, aby se snížila šířka pásma nutná k odeslání dat do Azure Storage. I když komprimace dat může ušetřit šířku pásma a zvýšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad dalších požadavků na zpracování pro kompresi a dekompresi dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může řešit obtíže, protože může být náročnější na zobrazení dat pomocí standardních nástrojů.
- Pokud se vaše aplikace blíží cílům škálovatelnosti, ujistěte se, že používáte exponenciální omezení rychlosti pro opakované pokusy. Doporučujeme, abyste se vyhnuli dosažení cílů škálovatelnosti, a to implementací doporučení popsaných v tomto článku. Použití exponenciálního omezení rychlosti pro opakování však zabrání aplikaci v rychlém opakování, což by mohlo způsobit horší omezení. Další informace najdete v části [timeout a chyby zaneprázdněnosti serveru](#timeout-and-server-busy-errors) .

## <a name="networking"></a>Sítě

Omezení fyzické sítě aplikace mohou mít významný dopad na výkon. Následující části popisují některá omezení, která se mohou vyskytnout pro uživatele.

### <a name="client-network-capability"></a>Schopnost klientské sítě

Šířka pásma a kvalita síťového propojení hrají důležité role v výkonu aplikace, jak je popsáno v následujících částech.

#### <a name="throughput"></a>Propustnost

V případě šířky pásma je problém často funkcemi klienta. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší omezení sítě z jednoho počítače. Pokud přistupujete Azure Storage z místní aplikace, platí stejné pravidlo: Pochopte síťové možnosti klientského zařízení a připojení k síti pro Azure Storage umístění a buď je Vylepšete podle potřeby, nebo Navrhněte aplikaci, která bude fungovat v rámci svých schopností.

#### <a name="link-quality"></a>Kvalita propojení

Stejně jako u všech využití sítě Pamatujte na to, že síťové podmínky, které mají za následek chyby a ztráty paketů, budou zpomalit efektivní propustnost. K diagnostice tohoto problému může použít Nástroj Wireshark nebo Sledování sítě.

### <a name="location"></a>Umístění

V jakémkoli distribuovaném prostředí je umístění klienta poblíž serveru k dispozici nejlepšího výkonu. Pro přístup k Azure Storage s nejnižší latencí je nejlepší umístění pro vašeho klienta ve stejné oblasti Azure. Například pokud máte webovou aplikaci Azure, která používá Azure Storage, vyhledáte je v rámci jedné oblasti, jako je například Západní USA nebo jihovýchodní Asie. Společné umístění prostředků snižuje latenci a náklady, protože využití šířky pásma v rámci jedné oblasti je zdarma.

Pokud budou klientské aplikace přistupovat k Azure Storage, ale nejsou hostovány v rámci Azure, jako jsou aplikace pro mobilní zařízení nebo místní podnikové služby, pak hledání účtu úložiště v oblasti poblíž těchto klientů může snížit latenci. Pokud jsou vaši klienti široce distribuované (například některé v Severní Amerika a některé v Evropě), zvažte použití jednoho účtu úložiště v každé oblasti. Tento přístup je snazší implementovat, pokud jsou data, která aplikace ukládá, specifická pro jednotlivé uživatele a nevyžadují replikaci dat mezi účty úložiště.

## <a name="sas-and-cors"></a>SAS a CORS

Předpokládejme, že je nutné autorizovat kód, jako je například JavaScript, který je spuštěn ve webovém prohlížeči uživatele nebo v mobilním telefonu pro přístup k datům v aplikaci Azure Storage. Jednou z možností je vytvořit aplikaci služby, která funguje jako proxy server. Zařízení uživatele se ověřuje pomocí služby, což zase autorizuje přístup k prostředkům Azure Storage. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tento přístup však přináší značnou režii na aplikaci služby, protože všechna data přenesená mezi zařízením uživatele a Azure Storage musí projít aplikací služby.

Pomocí sdíleného přístupového podpisu (SAS) se můžete vyhnout použití aplikace služby jako proxy serveru pro Azure Storage. Pomocí SAS můžete zařízení uživatele povolit, aby se požadavky přímo na Azure Storage pomocí omezeného přístupového tokenu. Pokud třeba uživatel chce do aplikace nahrát fotografii, aplikace služby může vygenerovat SAS a odeslat ji do zařízení uživatele. Token SAS může udělit oprávnění k zápisu do prostředku Azure Storage v zadaném časovém intervalu, po jehož uplynutí vyprší platnost tokenu SAS. Další informace o SAS najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).

Webový prohlížeč obvykle nepovoluje JavaScript na stránce, která je hostována webem v jedné doméně k provádění určitých operací, například operací zápisu, do jiné domény. Tato zásada se označuje jako zásada stejného zdroje, a proto zabraňuje škodlivému skriptu na jedné stránce získat přístup k datům na jiné webové stránce. Zásady stejného původce ale můžou být omezením při sestavování řešení v cloudu. Sdílení prostředků mezi zdroji (CORS) je funkce prohlížeče, která umožňuje cílové doméně komunikovat s prohlížečem, který důvěřuje žádostem pocházejících z zdrojové domény.

Předpokládejme například, že webová aplikace spuštěná v Azure vytvoří požadavek na prostředek na účet Azure Storage. Webová aplikace je zdrojová doména a účet úložiště je cílová doména. CORS můžete nakonfigurovat pro jakoukoli službu Azure Storage Services, která bude komunikovat s webovým prohlížečem, který požadavky ze zdrojové domény důvěřuje Azure Storage. Další informace o CORS najdete v tématu [Podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

SAS i CORS vám můžou přispět k tomu, abyste se vyhnuli zbytečnému zatížení vaší webové aplikace.

## <a name="net-configuration"></a>Konfigurace .NET

Pokud používáte .NET Framework, v této části najdete několik nastavení rychlých konfigurací, pomocí kterých můžete provádět významná vylepšení výkonu. Pokud používáte jiné jazyky, zkontrolujte, zda se v zvoleném jazyce použijí podobné koncepty.

### <a name="use-net-core"></a>Použití .NET Core

Vývoj aplikací pro Azure Storage s využitím .NET Core 2,1 nebo novějším vám umožní využít vylepšení výkonu. Pokud je to možné, doporučuje se použít .NET Core 3. x.

Další informace o vylepšení výkonu v .NET Core najdete v následujících blogových příspěvcích:

- [Vylepšení výkonu v .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Vylepšení výkonu v .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zvýšit výchozí limit připojení

V rozhraní .NET zvyšuje následující kód výchozí limit připojení (obvykle 2 v klientském prostředí nebo 10 v prostředí serveru) na 100. Obvykle byste měli nastavit hodnotu na přibližně počet vláken používaných vaší aplikací.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Před otevřením připojení nastavte limit připojení.

Další programovací jazyky najdete v dokumentaci k příslušnému jazyku, kde zjistíte, jak nastavit limit připojení.

Další informace najdete v blogovém příspěvku [webové služby: souběžná připojení](/archive/blogs/darrenj/web-services-concurrent-connections).

### <a name="increase-the-minimum-number-of-threads"></a>Zvýšení minimálního počtu vláken

Pokud používáte synchronní volání spolu s asynchronními úkoly, můžete zvýšit počet vláken ve fondu vláken:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete v [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) metodě.

## <a name="unbounded-parallelism"></a>Neohraničené paralelismus

I když paralelismus může být ideální pro výkon, buďte opatrní v používání neohraničené paralelismu, což znamená, že není nijak omezen počet vláken nebo paralelních požadavků. Nezapomeňte omezit paralelní požadavky na nahrávání nebo stahování dat, pro přístup k více oddílům ve stejném účtu úložiště nebo pro přístup k několika položkám ve stejném oddílu. Pokud je paralelismus neohraničený, může vaše aplikace přesáhnout možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezování.

## <a name="client-libraries-and-tools"></a>Klientské knihovny a nástroje

Nejlepšího výkonu dosáhnete, když budete vždycky používat nejnovější klientské knihovny a nástroje poskytované Microsoftem. Azure Storage klientské knihovny jsou k dispozici pro nejrůznější jazyky. Azure Storage podporuje taky PowerShell a Azure CLI. Microsoft aktivně vyvíjí tyto klientské knihovny a nástroje s ohledem na výkon, udržuje je aktuální s nejnovějšími verzemi služby a zajišťuje interní zpracování mnoha osvědčených postupů výkonu. Další informace najdete v [dokumentaci Azure Storage reference](./reference.md).

## <a name="handle-service-errors"></a>Zpracování chyb služby

Azure Storage vrátí chybu, pokud služba nemůže zpracovat požadavek. Porozumění chybám, které mohou být vráceny Azure Storage v daném scénáři, je užitečné pro optimalizaci výkonu.

### <a name="timeout-and-server-busy-errors"></a>Vypršení časového limitu a chyb zaneprázdněnosti serveru

Azure Storage může aplikaci omezit, pokud se blíží omezením škálovatelnosti. V některých případech nemusí Azure Storage být schopen zpracovat požadavek z důvodu přechodné podmínky. V obou případech může služba vracet chybu 503 ( `Server Busy` ) nebo 500 ( `Timeout` ). K těmto chybám může dojít také v případě, že je služba vyrovnávající datové oddíly, aby bylo možné dosáhnout vyšší propustnosti. Klientská aplikace by obvykle měla operaci zopakovat, což způsobí jednu z těchto chyb. Pokud však Azure Storage omezuje vaši aplikaci, protože je nad rámec škálovatelnosti, nebo i v případě, že služba nemohla tuto žádost z nějakého důvodu nedokázala zpracovat, agresivní pokusy mohou problém vyřešit. Doporučuje se použít exponenciální back-mailové zásady opakování, přičemž klientské knihovny jsou pro toto chování výchozí. Vaše aplikace se může například opakovat po 2 sekundách, potom 4 sekundy, 10 sekund, 30 sekund a potom zcela. Tímto způsobem vaše aplikace významně snižuje zatížení služby, spíše než exacerbating chování, které by mohlo vést k omezení.

Chyby připojení se dají opakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že budou přechodné.

### <a name="non-retryable-errors"></a>Neopakující se chyby

Knihovny klienta zpracovává opakované pokusy s vědomím, které chyby lze opakovat a které nemohou. Nicméně pokud voláte Azure Storage REST API přímo, dojde k chybám, které byste neměli opakovat. Například Chyba 400 ( `Bad Request` ) označuje, že klientská aplikace odeslala požadavek, který nebylo možné zpracovat, protože nebyl v očekávané podobě. Při opakovaném odeslání této žádosti se pokaždé pošle stejná odpověď, takže nedojde k žádnému bodu v dalším pokusu. Pokud voláte Azure Storage REST API přímo, pamatujte na potenciální chyby a určete, zda se mají opakovat.

Další informace o kódech chyb Azure Storage naleznete v tématu [stav a chybové kódy](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagles-algorithm"></a>Zakázat algoritmus Nagle

Algoritmus Nagle se v sítích TCP/IP široce implementuje jako prostředek pro zlepšení výkonu sítě. Není ale optimální pro všechny okolnosti (například vysoce interaktivní prostředí). Nagle algoritmus má negativní dopad na výkon požadavků do Azure Table Storage a pokud je to možné, měli byste ho deaktivovat.

## <a name="message-size"></a>Velikost zpráv

Zmenšení výkonu fronty a škálovatelnosti při zvýšení velikosti zprávy Dejte jenom informace, které přijímač potřebuje ve zprávě.

## <a name="batch-retrieval"></a>Dávkové načítání

V jedné operaci můžete načíst až 32 zpráv z fronty. Dávkové načítání může snížit počet zpátečních cest z klientské aplikace, což je zvláště užitečné pro prostředí, jako jsou například mobilní zařízení s vysokou latencí.

## <a name="queue-polling-interval"></a>Interval cyklického dotazování fronty

Většina aplikací se dotazuje na zprávy z fronty, což může být jeden z největších zdrojů transakcí pro danou aplikaci. Výběr intervalu cyklického dotazování v případě, že by dotaz příliš často mohl způsobit, že vaše aplikace bude přistupovat k cílům škálovatelnosti pro danou frontu. V 200 000 transakcích pro $0,01 (v době psaní) se ale jeden procesor dotazuje jednou za měsíc za měsíc, takže náklady nejsou obvykle faktorem, který ovlivňuje zvolený interval cyklického dotazování.

Aktuální informace o nákladech najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).

## <a name="perform-an-update-message-operation"></a>Provést operaci aktualizace zprávy

Operaci aktualizace zprávy můžete použít ke zvýšení časového limitu inviditelnosti nebo k aktualizaci informací o stavu zprávy. Tento přístup může být efektivnější než pracovní postup, který projde úlohu z jedné fronty do další, protože je dokončený každý krok úlohy. Vaše aplikace může uložit stav úlohy do zprávy a potom pokračovat v práci, místo aby se zpráva znovu zařadila do fronty pro další krok úlohy pokaždé, když se krok dokončí. Mějte na paměti, že každá operace zprávy o aktualizaci se počítá směrem k cíli škálovatelnosti.

## <a name="application-architecture"></a>Architektura aplikace

Pomocí front můžete nastavit škálovatelnost architektury aplikace. Následující seznam uvádí některé způsoby, jak můžete používat fronty pro lepší škálovatelnost aplikace:

- Fronty můžete použít k vytvoření nevyřízených položek práce pro zpracování a vyhlazení úloh ve vaší aplikaci. Můžete například zařadit požadavky uživatelů do fronty, aby prováděly práci náročné na procesor, jako je například změna velikosti nahraných obrázků.
- Pomocí front můžete oddělit části aplikace, abyste je mohli škálovat nezávisle. Webový front-end může například umístit výsledky průzkumu z uživatelů do fronty pro pozdější analýzu a úložiště. V případě potřeby můžete přidat další instance role pracovního procesu pro zpracování dat fronty.

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro Queue Storage](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)
