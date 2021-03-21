---
title: Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek – Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s tabulkovým úložištěm při vývoji vysoce výkonných aplikací.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004700"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Kontrolní seznam pro výkon a škálovatelnost pro úložiště tabulek

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s využitím tabulkového úložiště. Tento kontrolní seznam popisuje klíčové postupy, které můžou vývojáři použít k optimalizaci výkonu. Při navrhování aplikace a celého procesu mějte na paměti tyto postupy.

Azure Storage má cíle škálovatelnosti a výkonu pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech Azure Storage škálovatelnosti najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) a [škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek organizuje osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace pro úložiště tabulek.

| Hotovo | Kategorie | Aspekt návrhu |
| --- | --- | --- |
| &nbsp; |Cíle škálovatelnosti |[Můžete navrhnout aplikaci tak, aby nepoužívala více než maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cíle škálovatelnosti |[Nechcete se vyhnout přístupu k kapacitě a omezením transakcí?](#capacity-and-transaction-targets) |
| &nbsp; |Cíle škálovatelnosti |[Blížíte se k cílům škálovatelnosti pro entity za sekundu?](#targets-for-data-operations) |
| &nbsp; |Sítě |[Mají zařízení na straně klienta dostatečně velkou šířku pásma a nízkou latenci pro dosažení potřebného výkonu?](#throughput) |
| &nbsp; |Sítě |[Má zařízení na straně klienta vysoce kvalitní síťový odkaz?](#link-quality) |
| &nbsp; |Sítě |[Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; |Přímý přístup klienta |[Používáte k tomu přímý přístup k Azure Storage pomocí sdílených přístupových podpisů (SAS) a sdílení prostředků mezi zdroji (CORS)?](#sas-and-cors) |
| &nbsp; |Dávkování |[Provádí vaše aplikace dávkové aktualizace pomocí transakcí skupin entit?](#batch-transactions) |
| &nbsp; |Konfigurace .NET |[Používáte pro optimální výkon .NET Core 2,1 nebo novější?](#use-net-core) |
| &nbsp; |Konfigurace .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; |Konfigurace .NET |[Pro aplikace .NET jste nakonfigurovali .NET pro použití dostatečného počtu vláken?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismu |[Měli byste zajistit, aby byl paralelismu správně ohraničený, takže nebudete přetěžovat možnosti svého klienta nebo se přiblížíte cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; |Nástroje |[Používáte nejnovější verze klientských knihoven a nástrojů od společnosti Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Opakování |[Používáte zásady opakování s exponenciálním omezení rychlosti pro omezení chyb a časových limitů?](#timeout-and-server-busy-errors) |
| &nbsp; |Opakování |[Vyloučí vaše aplikace opakované pokusy o neopakující se chyby?](#non-retryable-errors) |
| &nbsp; |Konfigurace |[Používáte pro žádosti o tabulku JSON?](#use-json) |
| &nbsp; |Konfigurace |[Vypnuli jste Nagle algoritmus, aby se zlepšil výkon malých požadavků?](#disable-nagle) |
| &nbsp; |Tabulky a oddíly |[Správně jste rozdělili data?](#schema) |
| &nbsp; |Aktivní oddíly |[Nechcete se vyhnout pouze připojeným vzorům jenom pro připojení a k předřazení?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Aktivní oddíly |[Jsou vložení nebo aktualizace rozloženy mezi mnoho oddílů?](#high-traffic-data) |
| &nbsp; |Rozsah dotazu |[Navrhli jste schéma, aby bylo možné používat dotazy bodů ve většině případů, a dotazy tabulek, které se mají používat zřídka?](#query-scope) |
| &nbsp; |Hustota dotazů |[Budou dotazy obvykle prohledávat a vracet pouze řádky, které bude aplikace používat?](#query-density) |
| &nbsp; |Omezení vrácených dat |[Používáte filtrování, abyste se vyhnuli vrácení nepotřebných entit?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Omezení vrácených dat |[Používáte projekci k tomu, abyste se vyhnuli vrácení vlastností, které nejsou potřeba?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizace |[Vyzkoušeli jste Denormalizovaná data, abyste se vyhnuli neefektivním dotazům nebo vícenásobným požadavkům na čtení při pokusu o získání dat?](#denormalization) |
| &nbsp; |Vložení, aktualizace a odstranění |[Vytváříte dávkování požadavků, které musí být transakční, nebo je lze provést ve stejnou dobu, abyste snížili zpáteční cesty?](#batching) |
| &nbsp; |Vložení, aktualizace a odstranění |[Nemůžete načítat entitu jenom k určení toho, jestli se má volat vložení nebo aktualizace?](#upsert) |
| &nbsp; |Vložení, aktualizace a odstranění |[Měli jste v potaz ukládání řady dat, které se často načítají v jedné entitě jako vlastnosti místo více entit?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Vložení, aktualizace a odstranění |[Pro entity, které se vždycky načítají společně a můžou být napsané v dávkách (například data časových řad), jste se domnívali používat objekty blob místo tabulek?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje k některým cílům škálovatelnosti nebo překročí, může dojít ke zvýšené latenci transakcí nebo omezování. Když aplikace Azure Storage omezí vaši aplikaci, začne služba vracet kódy chyb 503 (zaneprázdněný serverem) nebo 500 (časový limit operace). Zamezení těchto chyb zachováním omezení cílů škálovatelnosti je důležitou součástí zvýšení výkonu aplikace.

Další informace o cílech škálovatelnosti pro Table service najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se přiblížíte k maximálnímu počtu účtů úložiště povolených pro určitou kombinaci předplatného nebo oblasti, budete k horizontálních oddílů používat víc účtů úložiště, abyste mohli zvýšit počet vstupně-výstupních operací za sekundu (IOPS) nebo kapacitu? V tomto scénáři Microsoft doporučuje, abyste využili vyšší omezení pro účty úložiště, abyste snížili počet účtů úložiště potřebných pro vaše zatížení, pokud je to možné. Kontaktujte [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšená omezení pro váš účet úložiště. Další informace najdete v tématu [oznamujeme větší, vyšší škálování účtů úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cíle kapacity a transakce

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte použití jednoho z následujících přístupů:  

- Převezměte v úvahu zatížení, které způsobí, že vaše aplikace bude přistupovat k cíli škálovatelnosti nebo nad něj. Můžete ji navrhovat odlišně, abyste mohli používat menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte několik účtů úložiště a vytvořte oddíly dat aplikací napříč těmito různými účty úložiště. Pokud použijete tento model, nezapomeňte navrhnout aplikaci, abyste mohli v budoucnu přidat další účty úložiště pro vyrovnávání zatížení. Účty úložiště samy nemají žádné náklady jiné než vaše využití v závislosti na uložených datech, provedených transakcích nebo přenesených datech.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte komprimaci dat na straně klienta, aby se snížila šířka pásma nutná k odeslání dat do Azure Storage.
    I když komprimace dat může ušetřit šířku pásma a zvýšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad dalších požadavků na zpracování pro kompresi a dekompresi dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může řešit obtíže, protože může být náročnější na zobrazení dat pomocí standardních nástrojů.
- Pokud se vaše aplikace blíží cílům škálovatelnosti, ujistěte se, že používáte exponenciální omezení rychlosti pro opakované pokusy. Doporučujeme, abyste se vyhnuli dosažení cílů škálovatelnosti, a to implementací doporučení popsaných v tomto článku. Použití exponenciálního omezení rychlosti pro opakování však zabrání aplikaci v rychlém opakování, což by mohlo způsobit horší omezení. Další informace najdete v části s názvem [timeout a chyby zaneprázdněnosti serveru](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Cíle pro datové operace

Azure Storage se vyrovnává zatížení při zvýšení provozu do účtu úložiště, ale pokud provoz vykazuje náhlé shluky, nemusí být možné okamžitě získat tento objem propustnosti. V případě, že Azure Storage automaticky načítá rovnováhu mezi tabulkami, očekává se, že se v průběhu shluku zobrazuje omezení nebo časové limity. Rozkládání pomalu obecně poskytuje lepší výsledky, protože systém má odpovídající čas na Vyrovnávání zatížení.

#### <a name="entities-per-second-storage-account"></a>Entity za sekundu (účet úložiště)

Omezení škálovatelnosti pro přístup k tabulkám je až 20 000 entit (1 KB každý) za sekundu pro účet. Obecně platí, že každou entitu, která je vložená, aktualizovaná, Odstraněná nebo se vyhledá na tomto cíli. Dávková vložení obsahující 100 entit by tedy bylo možné počítat jako jednotky 100. Dotaz, který kontroluje 1000 entit a vrátí hodnotu 5, by byl počet entit 1000.

#### <a name="entities-per-second-partition"></a>Entity za sekundu (oddíl)

V rámci jednoho oddílu je cíl škálovatelnosti pro přístup k tabulkám 2 000 entit (1 KB každý) za sekundu, a to za použití stejného počítání, jak je popsáno v předchozí části.

## <a name="networking"></a>Sítě

Omezení fyzické sítě aplikace mohou mít významný dopad na výkon. Následující části popisují některá omezení, která se mohou vyskytnout pro uživatele.  

### <a name="client-network-capability"></a>Schopnost klientské sítě

Šířka pásma a kvalita síťového propojení hrají důležité role v výkonu aplikace, jak je popsáno v následujících částech.

#### <a name="throughput"></a>Propustnost

V případě šířky pásma je problém často funkcemi klienta. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší omezení sítě z jednoho počítače. Pokud přistupujete Azure Storage z místní aplikace, platí stejné pravidlo: Pochopte síťové možnosti klientského zařízení a připojení k síti pro Azure Storage umístění a buď je Vylepšete podle potřeby, nebo Navrhněte svou aplikaci tak, aby fungovala v jejich schopnostech.

#### <a name="link-quality"></a>Kvalita propojení

Stejně jako u všech využití sítě Pamatujte na to, že síťové podmínky, které mají za následek chyby a ztráty paketů, budou zpomalit efektivní propustnost.  K diagnostice tohoto problému může využít nástroj WireShark nebo NetMon.  

### <a name="location"></a>Umístění

V jakémkoli distribuovaném prostředí je umístění klienta poblíž serveru k dispozici nejlepšího výkonu. Pro přístup k Azure Storage s nejnižší latencí je nejlepší umístění pro vašeho klienta ve stejné oblasti Azure. Například pokud máte webovou aplikaci Azure, která používá Azure Storage, vyhledáte je v rámci jedné oblasti, jako je například USA – západ nebo Asie – jihovýchod. Společné umístění prostředků snižuje latenci a náklady, protože využití šířky pásma v rámci jedné oblasti je zdarma.  

Pokud budou klientské aplikace přistupovat k Azure Storage, ale nejsou hostovány v rámci Azure, jako jsou aplikace pro mobilní zařízení nebo místní podnikové služby, pak hledání účtu úložiště v oblasti blízko těchto klientů může snížit latenci. Pokud jsou vaši klienti široce distribuované (například některé v Severní Amerika a některé v Evropě), zvažte použití jednoho účtu úložiště v každé oblasti. Tento přístup je snazší implementovat, pokud jsou data, která aplikace ukládá, specifická pro jednotlivé uživatele a nevyžadují replikaci dat mezi účty úložiště.

## <a name="sas-and-cors"></a>SAS a CORS

Předpokládejme, že je nutné autorizovat kód, jako je například JavaScript, který je spuštěn ve webovém prohlížeči uživatele nebo v mobilním telefonu pro přístup k datům v aplikaci Azure Storage. Jednou z možností je vytvořit aplikaci služby, která funguje jako proxy server. Zařízení uživatele se ověřuje pomocí služby, což zase autorizuje přístup k prostředkům Azure Storage. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tento přístup však přináší značnou režii na aplikaci služby, protože všechna data přenesená mezi zařízením uživatele a Azure Storage musí projít aplikací služby.

Pomocí sdíleného přístupového podpisu (SAS) se můžete vyhnout použití aplikace služby jako proxy serveru pro Azure Storage. Pomocí SAS můžete zařízení uživatele povolit, aby se požadavky přímo na Azure Storage pomocí omezeného přístupového tokenu. Pokud třeba uživatel chce do aplikace nahrát fotografii, aplikace služby může vygenerovat SAS a odeslat ji do zařízení uživatele. Token SAS může udělit oprávnění k zápisu do prostředku Azure Storage v zadaném časovém intervalu, po jehož uplynutí vyprší platnost tokenu SAS. Další informace o SAS najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).  

Webový prohlížeč obvykle nepovoluje JavaScript na stránce, která je hostována webem v jedné doméně k provádění určitých operací, například operací zápisu, do jiné domény. Tato zásada se označuje jako zásada stejného zdroje, a proto zabraňuje škodlivému skriptu na jedné stránce získat přístup k datům na jiné webové stránce. Zásady stejného původce ale můžou být omezením při sestavování řešení v cloudu. Sdílení prostředků mezi zdroji (CORS) je funkce prohlížeče, která umožňuje cílové doméně komunikovat s prohlížečem, který důvěřuje žádostem pocházejících z zdrojové domény.

Předpokládejme například, že webová aplikace spuštěná v Azure vytvoří požadavek na prostředek na účet Azure Storage. Webová aplikace je zdrojová doména a účet úložiště je cílová doména. CORS můžete nakonfigurovat pro jakoukoli službu Azure Storage Services, která bude komunikovat s webovým prohlížečem, který požadavky ze zdrojové domény důvěřuje Azure Storage. Další informace o CORS najdete v tématu [Podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS i CORS vám můžou přispět k tomu, abyste se vyhnuli zbytečnému zatížení vaší webové aplikace.  

## <a name="batch-transactions"></a>Transakce Batch

Table service podporuje transakce Batch u entit, které jsou ve stejné tabulce a patří do stejné skupiny oddílů. Další informace najdete v tématu [provádění transakcí skupin entit](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Konfigurace .NET

Pokud používáte .NET Framework, v této části najdete několik nastavení rychlých konfigurací, pomocí kterých můžete provádět významná vylepšení výkonu.  Pokud používáte jiné jazyky, podívejte se, jestli se ve zvoleném jazyce použijí podobné koncepty.  

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

### <a name="increase-minimum-number-of-threads"></a>Zvýšení minimálního počtu vláken

Pokud používáte synchronní volání spolu s asynchronními úkoly, můžete zvýšit počet vláken ve fondu vláken:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete v tématu metoda [fondu vláken. SetMinThreads –](/dotnet/api/system.threading.threadpool.setminthreads) .  

## <a name="unbounded-parallelism"></a>Neohraničené paralelismus

I když paralelismus může být ideální pro výkon, buďte opatrní v používání neohraničené paralelismu, což znamená, že není nijak omezen počet vláken nebo paralelních požadavků. Nezapomeňte omezit paralelní požadavky na nahrávání nebo stahování dat, pro přístup k více oddílům ve stejném účtu úložiště nebo pro přístup k několika položkám ve stejném oddílu. Pokud je paralelismus neohraničený, může vaše aplikace přesáhnout možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezování.  

## <a name="client-libraries-and-tools"></a>Klientské knihovny a nástroje

Nejlepšího výkonu dosáhnete, když budete vždycky používat nejnovější klientské knihovny a nástroje poskytované Microsoftem. Azure Storage klientské knihovny jsou k dispozici pro nejrůznější jazyky. Azure Storage podporuje taky PowerShell a Azure CLI. Microsoft aktivně vyvíjí tyto klientské knihovny a nástroje s ohledem na výkon, udržuje je aktuální s nejnovějšími verzemi služby a zajišťuje interní zpracování mnoha osvědčených postupů výkonu.

## <a name="handle-service-errors"></a>Zpracování chyb služby

Azure Storage vrátí chybu, pokud služba nemůže zpracovat požadavek. Porozumění chybám, které mohou být vráceny Azure Storage v daném scénáři, je užitečné pro optimalizaci výkonu.

### <a name="timeout-and-server-busy-errors"></a>Vypršení časového limitu a chyb zaneprázdněnosti serveru

Azure Storage může aplikaci omezit, pokud se blíží omezením škálovatelnosti. V některých případech nemusí Azure Storage být schopen zpracovat požadavek z důvodu přechodné podmínky. V obou případech může služba vracet chybu 503 (zaneprázdněný serverem) nebo 500 (časový limit). K těmto chybám může dojít také v případě, že je služba vyrovnávající datové oddíly, aby bylo možné dosáhnout vyšší propustnosti. Klientská aplikace by obvykle měla operaci zopakovat, což způsobí jednu z těchto chyb. Pokud však Azure Storage omezuje vaši aplikaci, protože je nad rámec škálovatelnosti, nebo i v případě, že služba nemohla tuto žádost z nějakého důvodu nedokázala zpracovat, agresivní pokusy mohou problém vyřešit. Doporučuje se použít exponenciální back-mailové zásady opakování, přičemž klientské knihovny jsou pro toto chování výchozí. Vaše aplikace se může například opakovat po 2 sekundách, potom 4 sekundy, 10 sekund, 30 sekund a potom zcela. Tímto způsobem vaše aplikace významně snižuje zatížení služby, spíše než exacerbating chování, které by mohlo vést k omezení.  

Chyby připojení se dají opakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že budou přechodné.  

### <a name="non-retryable-errors"></a>Neopakující se chyby

Knihovny klienta zpracovává opakované pokusy s vědomím, které chyby lze opakovat a které nemohou. Nicméně pokud voláte Azure Storage REST API přímo, dojde k chybám, které byste neměli opakovat. Například Chyba 400 (chybný požadavek) indikuje, že klientská aplikace odeslala požadavek, který nebylo možné zpracovat, protože nebyl v očekávané podobě. Při opakovaném odeslání této žádosti se pokaždé pošle stejná odpověď, takže nedojde k žádnému bodu v dalším pokusu. Pokud voláte Azure Storage REST API přímo, pamatujte na potenciální chyby a určete, zda se mají opakovat.

Další informace o kódech chyb Azure Storage naleznete v tématu [stav a chybové kódy](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Konfigurace

V této části jsou uvedena několik nastavení rychlých konfigurací, která můžete použít k výraznému zlepšení výkonu Table service:

### <a name="use-json"></a>Použít JSON

Počínaje službou Storage verze 2013-08-15 podporuje Table service pro přenos dat tabulky místo formátu AtomPub založeného na jazyce XML použití JSON. Použití formátu JSON může snížit velikost datových vytížení až o 75% a může významně zlepšit výkon aplikace.

Další informace najdete v [tabulkách post Microsoft Azure: Úvod](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) do formátu JSON a [datové části pro operace služby Table Service](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Zakázat Nagle

Algoritmus Nagle se v sítích TCP/IP široce implementuje jako prostředek pro zlepšení výkonu sítě. Není ale optimální pro všechny okolnosti (například vysoce interaktivní prostředí). Nagle algoritmus má negativní dopad na výkon požadavků na Azure Table service a pokud je to možné, měli byste ho zakázat.

## <a name="schema"></a>Schéma

Způsob reprezentace a dotazování dat je největší jednoduchý faktor, který ovlivňuje výkon Table service. I když je každá aplikace odlišná, Tato část popisuje některé obecné osvědčené postupy, které se týkají:

- Návrh tabulky
- Efektivní dotazy
- Efektivní aktualizace dat

### <a name="tables-and-partitions"></a>Tabulky a oddíly

Tabulky jsou rozděleny na oddíly. Každá entita uložená v oddílu sdílí stejný klíč oddílu a má jedinečný klíč řádku k identifikaci v rámci tohoto oddílu. Oddíly poskytují výhody, ale také zavádí omezení škálovatelnosti.

- Výhody: entity ve stejném oddílu můžete aktualizovat v jedné, atomické transakci dávky, která obsahuje až 100 samostatných operací úložiště (limit 4 MB celkové velikosti). Za předpokladu, že stejný počet entit, které se mají načíst, můžete také data v rámci jednoho oddílu efektivněji pokládat rychleji než data, která zahrnují oddíly (i když si přečtete další doporučení k dotazování na data tabulky).
- Omezení škálovatelnosti: přístup k entitám uloženým v jednom oddílu nelze vyrovnávat, protože oddíly podporují atomické transakce dávky. Z tohoto důvodu je cílem škálovatelnosti pro jednotlivé oddíly tabulky nižší než u Table service jako celku.

Z důvodu těchto vlastností tabulek a oddílů byste měli přijmout následující principy návrhu:

- Vyhledá data, která klientská aplikace často aktualizuje nebo odesílá dotazy ve stejné logické jednotce v rámci stejného oddílu. Můžete například vyhledat data ve stejném oddílu, pokud vaše aplikace agreguje zápisy nebo provádíte atomické operace dávkového zpracování. Data v jednom oddílu taky můžou být efektivněji dotazována v jednom dotazu než data napříč oddíly.
- Vyhledejte data, která klientská aplikace nevloží, neaktualizuje ani nedotazují do stejné logické jednotky práce (tj. v jednom dotazu nebo v dávkové aktualizaci) v samostatných oddílech. Mějte na paměti, že počet klíčů oddílů v jedné tabulce není omezený, takže miliony klíčů oddílů nepředstavuje problém a nebude mít vliv na výkon. Například pokud je vaše aplikace oblíbeným webem s přihlašovacími údaji uživatele, můžete použít ID uživatele, protože klíč oddílu může být dobrou volbou.

#### <a name="hot-partitions"></a>Aktivní oddíly

Aktivní oddíl je jeden, který přijímá neúměrné procento provozu k účtu a nedá se vyrovnávat zatížení, protože se jedná o jeden oddíl. Obecně platí, že aktivní oddíly jsou vytvořeny jedním ze dvou způsobů:

#### <a name="append-only-and-prepend-only-patterns"></a>Jenom připojit a předřadit jenom vzory

Vzor "pouze pro připojení" je ten, ve kterém se zvyšuje a snižuje počet přenosů na daný klíč oddílu (nebo téměř všech) na základě aktuálního času. Předpokládejme například, že vaše aplikace používá aktuální datum jako klíč oddílu pro data protokolu. Výsledkem tohoto návrhu jsou všechna vložení na poslední oddíl v tabulce a systém nemůže správně vyrovnávat zatížení. Pokud objem provozu do tohoto oddílu překročí cíl škálovatelnosti na úrovni oddílu, výsledkem bude omezení. Je lepší zajistit, aby se provoz odesílal do několika oddílů, aby bylo možné vyrovnávat zatížení u požadavků v tabulce.

#### <a name="high-traffic-data"></a>Data s vysokým provozem

Pokud vaše schéma vytváření oddílů vede k jednomu oddílu, který má pouze data, která jsou mnohem více používána než ostatní oddíly, může se také zobrazit omezení, protože tento oddíl přistupuje k cíli škálovatelnosti pro jeden oddíl. Je lepší, abyste se ujistili, že vaše schéma oddílu nedosahuje žádného jednoho oddílu, který se blíží cílům škálovatelnosti.

### <a name="querying"></a>Dotazování

Tato část popisuje osvědčené postupy pro dotazování na Table service.

#### <a name="query-scope"></a>Rozsah dotazu

Existuje několik způsobů, jak zadat rozsah entit pro dotaz. Následující seznam popisuje jednotlivé možnosti pro obor dotazů.

- **Dotazy na body:**– dotaz na bod načte přesně jednu entitu zadáním klíče oddílu a klíče řádku entity, která se má načíst. Tyto dotazy jsou efektivní a měli byste je používat všude, kde je to možné.
- **Dotazy na oddíly:** Dotaz na oddíl je dotaz, který načte sadu dat, která sdílí společný klíč oddílu. Obvykle dotaz určuje rozsah hodnot klíčů řádků nebo rozsah hodnot pro některé vlastnosti entity kromě klíče oddílu. Tyto dotazy jsou méně efektivní než dotazy na body a měly by se používat jenom zřídka.
- **Dotazy tabulky:** Dotaz na tabulku je dotaz, který načte sadu entit, které nesdílejí společný klíč oddílu. Tyto dotazy nejsou efektivní a je třeba se jim vyhnout, pokud je to možné.

Obecně se vyhněte kontrolám (dotazy větší než jedna entita), ale pokud je potřeba je zkontrolovat, zkuste uspořádat data, aby vaše prohledávání načetla potřebná data bez kontroly a vrácení důležitých množství entit, které nepotřebujete.

#### <a name="query-density"></a>Hustota dotazů

Dalším klíčovým faktorem v efektivitě dotazu je počet vrácených entit ve srovnání s počtem zkontrolovaných entit, které prohledaly vrácenou sadu. Pokud vaše aplikace provede dotaz na tabulku s filtrem pro hodnotu vlastnosti, která je jenom 1% sdílených dat, dotaz vyhledá entity 100 pro každou entitu, kterou vrátí. Cíle škálovatelnosti tabulky popsané dříve se vztahují na počet prohledaných entit a nikoli na počet vrácených entit: nízká hustota dotazu může snadno způsobit, že Table service omezí vaši aplikaci, protože musí prověřit, aby se hledaná entita načetla v mnoha entitách. Další informace o tom, jak se vyhnout omezování, najdete v části s názvem [denormalizace](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Omezení množství vrácených dat

Pokud víte, že dotaz vrátí entity, které v klientské aplikaci nepotřebujete, zvažte použití filtru ke snížení velikosti vrácené sady. I když se entitám, které nejsou vraceny klientovi, stále počítá s omezeními škálovatelnosti, výkon aplikace se zvýší z důvodu snížené velikosti datové části sítě a omezeného počtu entit, které musí klientská aplikace zpracovat. Mějte na paměti, že cíle škálovatelnosti se vztahují na počet prověřených entit, takže dotaz, který filtruje mnoho entit, může i nadále omezovat omezení i v případě, že se vrátí několik entit. Další informace o tom, jak dotazy efektivně provádět, najdete v části s názvem [hustota dotazů](#query-density).

Pokud vaše klientská aplikace potřebuje jenom omezenou sadu vlastností z entit v tabulce, můžete použít projekci k omezení velikosti vrácené datové sady. Stejně jako u filtrování pomáhá projekce snížit zatížení sítě a zpracování klientů.

#### <a name="denormalization"></a>Denormalizace

Na rozdíl od práce s relačními databázemi osvědčené postupy pro efektivní dotazování dat tabulek vedou k denormalizaci vašich dat. To znamená, že duplikuje stejná data v několika entitách (jeden pro každý klíč, který můžete použít k vyhledání dat), abyste minimalizovali počet entit, které musí dotaz vyhledat, aby bylo možné najít data, která potřebuje, a ne kontrolovat velký počet entit, aby bylo možné najít data, která vaše aplikace potřebuje. Například na webu elektronického obchodování můžete chtít najít objednávku podle ID zákazníka (dát mi objednávky tohoto zákazníka) a podle data (dát mi do objednávky datum). V Table Storage je nejlepší ukládat entitu (nebo odkaz na ni) dvakrát – jednou s názvem tabulky, PK a vých, aby se usnadnilo hledání podle zákaznického ID, a to jednou, aby bylo snazší ho najít podle data.  

### <a name="insert-update-and-delete"></a>Vložení, aktualizace a odstranění

Tato část popisuje osvědčené postupy pro úpravu entit uložených v Table service.  

#### <a name="batching"></a>Dávkování

Transakce Batch se označují jako transakce skupin entit v Azure Storage. Všechny operace v rámci transakce skupiny entit musí být v jednom oddílu v jedné tabulce. Pokud je to možné, použijte transakce skupin entit k provádění operací vložení, aktualizace a odstranění v dávkách. Použití transakcí skupin entit snižuje počet fakturovatelných cest z klientské aplikace na server, snižuje počet fakturovaných transakcí (transakce skupiny entit se počítá jako jediná transakce pro účely fakturace a může obsahovat až 100 operací úložiště) a umožňuje atomické aktualizace (všechny operace jsou úspěšné nebo všechny selžou v transakci skupiny entit). Prostředí s vysokou latencí, jako jsou například mobilní zařízení, budou významně těžit z použití transakcí skupin entit.  

#### <a name="upsert"></a>Upsert

Pokud je to možné, používejte **Upsert** operace tabulky. Existují dva typy **Upsert**, z nichž obě můžou být efektivnější než tradiční operace **vkládání** a **aktualizace** :  

- **InsertOrMerge**: tuto operaci použijte, pokud chcete nahrát podmnožinu vlastností entity, ale nevíte, zda entita již existuje. Pokud entita existuje, toto volání aktualizuje vlastnosti zahrnuté v operaci **Upsert** a ponechá všechny existující vlastnosti tak, jak jsou, pokud entita neexistuje, vloží novou entitu. To se podobá použití projekce v dotazu, v tom, že potřebujete pouze nahrát vlastnosti, které se mění.
- **InsertOrReplace**: tuto operaci použijte, chcete-li nahrát zcela novou entitu, ale nejste si jisti, zda již existuje. Tuto operaci použijte, pokud víte, že nově nahraná entita je zcela správná, protože zcela přepíše starou entitu. Například chcete aktualizovat entitu, která uchovává aktuální umístění uživatele bez ohledu na to, zda aplikace dříve uložila data o umístění pro daného uživatele. nová entita umístění je dokončena a z žádné předchozí entity nepotřebujete žádné informace.

#### <a name="storing-data-series-in-a-single-entity"></a>Ukládání datových řad do jedné entity

V některých případech aplikace ukládá řadu dat, která často potřebují k načtení všech najednou: aplikace může například sledovat využití CPU v čase, aby bylo možné vykreslit postupný graf dat za posledních 24 hodin. Jedním z možností je mít jednu entitu tabulky za hodinu, přičemž každá entita představuje určitou hodinu a za tuto hodinu bude ukládat využití CPU. Aby bylo možné tato data vykreslit, musí aplikace načíst entity, které mají data v posledních 24 hodinách.  

Alternativně může vaše aplikace uložit využití CPU pro každou hodinu jako samostatnou vlastnost jedné entity: Chcete-li aktualizovat každou hodinu, může aplikace použít jedno volání **Upsert InsertOrMerge** k aktualizaci hodnoty pro poslední hodinu. Aby bylo možné sestavovat data, aplikace potřebuje získat jedinou entitu místo 24 a vytvořit tak efektivní dotaz. Další informace o efektivitě dotazů naleznete v části s názvem [obor dotazu](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Ukládání strukturovaných dat v objektech blob

Pokud provádíte dávkové vkládání a pak načítáte rozsahy entit dohromady, zvažte použití objektů BLOB místo tabulek. Dobrým příkladem je soubor protokolu. Můžete dávkovat několik minut protokolů a vkládat je a pak najednou načíst několik minut protokolů. V takovém případě je výkon lepší při použití objektů BLOB místo tabulek, protože můžete významně snížit počet objektů zapsaných nebo čtených a také případně i počet požadavků, které je potřeba provést.  

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)