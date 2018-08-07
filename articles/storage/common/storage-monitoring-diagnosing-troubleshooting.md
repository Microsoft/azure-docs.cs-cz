---
title: Monitorování, Diagnostika a řešení problémů s Azure Storage | Dokumentace Microsoftu
description: Pomocí funkcí, jako jsou analýzy úložiště, protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostikovat a řešit problémy související s Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.component: common
ms.openlocfilehash: e560eb9e0bbce09c541bfc66ea760ea3e636f841
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528710"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Přehled
Diagnostika a řešení potíží v distribuované aplikaci hostované v cloudovém prostředí můžou být složitější než v tradičních prostředí. Aplikace můžete nasadit v infrastruktuře PaaS nebo IaaS v místním prostředí, na mobilním zařízení, nebo určitou kombinaci těchto prostředích. Obvykle síťový provoz vaší aplikace můžou procházet veřejný a privátní sítě a vaše aplikace může používat více technologií úložišť, jako je například Microsoft Azure Storage tabulky, objekty BLOB, fronty nebo ukládá soubory kromě jiných dat, například jako relační a dokumentové databáze.

Ke správě těchto aplikací úspěšně by měl Proaktivní monitorování a pochopit, jak diagnostikovat a řešit všechny aspekty je a jejich související technologie. Jako uživatel služby Azure Storage doporučujeme průběžně monitorovat služby úložiště, které vaše aplikace používá pro všechny neočekávaným změnám v chování (například pomalejší než obvykle odezvy) a použití protokolování shromažďovat detailní data a k analýze problému v Hloubka. Diagnostické informace, které jste získali z monitorování a protokolování vám pomůže určit hlavní příčinu problému, došlo k vaší aplikace. Potom můžete tento problém vyřešit a určit příslušné kroky, které můžete provést při jeho řešení. Azure Storage je základní služby Azure a je důležitou součástí většina řešení, které zákazníci nasadí infrastrukturou Azure. Azure Storage zahrnuje funkce pro zjednodušení monitorování, Diagnostika a řešení potíží s storage ve vašich cloudových aplikacích.

> [!NOTE]
> Služba soubory Azure protokolování v tuto chvíli nepodporuje.
> 

Praktické vodítko na začátku do konce odstraňování potíží v aplikacích Azure Storage, najdete v části [začátku do konce řešení problémů pomocí metrik Azure Storage a protokolování, AzCopy a analyzátoru zpráv](../storage-e2e-troubleshooting.md).

* [Úvod]
  * [Uspořádání příručky]
* [Monitorování vaší služby úložiště]
  * [Monitorování stavu služby]
  * [Monitorování kapacity]
  * [Monitorování dostupnosti]
  * [Sledování výkonu]
* [Diagnostika problémů s úložištěm]
  * [Problémy se stavem služby]
  * [Problémy s výkonem]
  * [Diagnostika chyb]
  * [Potíže s úložištěm emulátoru]
  * [Nástroje protokolování úložiště]
  * [Pomocí nástroje protokolování sítě]
* [Trasování začátku do konce]
  * [Korelaci dat protokolu]
  * [ID žádosti klienta]
  * [ID žádosti serveru]
  * [Časová razítka]
* [Pokyny při řešení potíží]
  * [Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency]
  * [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]
  * [Metrika ukazuje vysokou hodnotu AverageServerLatency]
  * [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]
  * [Metrika ukazuje zvýšení u PercentThrottlingError]
  * [Metrika ukazuje zvýšení u PercentTimeoutError]
  * [Metrika ukazuje zvýšení u PercentNetworkError]
  * [Klient dostává zprávy HTTP 403 (zakázáno)]
  * [Klient dostává zprávy HTTP 404 (Nenalezeno)]
  * [Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]
  * [Metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]
  * [Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]
  * [Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]
  * [Narazíte na potíže s instalací sady Azure SDK pro .NET]
  * [Máte jiný problém se službou úložiště]
  * [Řešení potíží virtuální pevné disky virtuálních počítačích s Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Řešení potíží virtuální pevné disky na virtuální počítače s Linuxem](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Řešení potíží s soubory Azure s Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Řešení potíží s soubory Azure s Linuxem](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Přílohy]
  * [Dodatek 1: Pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]
  * [Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu]
  * [Dodatek 3: Použití Microsoft Message Analyzer pro zachycení síťového provozu]
  * [Dodatek 4: Zobrazení metriky a protokolovat data pomocí aplikace Excel]
  * [Dodatek 5: Monitorování pomocí služby Application Insights pro Visual Studio Team Services]

## <a name="introduction"></a>Úvod
Tento průvodce vám ukáže, jak používat funkce, jako je Azure Storage Analytics, klientské protokolování v klientské knihovně Azure Storage a další nástroje třetích stran k identifikaci, diagnostice a řešení problémů s Azure Storage související s problémy.

![][1]

Tento průvodce je určený ke čtení primárně vývojáři online služeb, které využívají služby Azure Storage a IT profesionály zodpovědného za správu těchto služeb online services. Cílem tohoto průvodce jsou:

* Chcete-li zachovat stav a výkon vašich účtů úložiště Azure.
* Kde přinášejí nezbytné procesy a nástroje, které vám pomohou rozhodnout, jestli nějaký problém v aplikaci nebo problém má vztah ke službě Azure Storage.
* Chcete-li poskytují podrobné pokyny k vyřešení problémů souvisejících s Azure Storage.

### <a name="how-this-guide-is-organized"></a>Uspořádání příručky
V části "[monitorování vaší služby úložiště]" popisuje, jak monitorovat stav a výkon služby Azure Storage pomocí Azure Storage Analytics Metrics (metriky úložiště).

V části "[Diagnostika problémů s úložištěm]" popisuje, jak diagnostikovat problémy s Azure Storage Analytics Logging (protokolování úložiště). Také popisuje, jak povolit protokolování na straně klienta pomocí zařízení v jednom z klientských knihoven například Klientská knihovna pro úložiště pro .NET a sady Azure SDK for Java.

V části "[Trasování začátku do konce]" popisuje, jak můžete porovnat informace obsažené v různých souborů protokolu a data metriky.

V části "[pokyny při řešení potíží]" poskytuje pokyny při řešení potíží pro některé nejběžnější úložiště související problémy, můžete narazit.

"[Přílohy]" zahrnují informace o použití jiných nástrojů, jako je například Wireshark nebo Netmon pro analýzu sítě paketů data, aplikace Fiddler k analýze zprávy HTTP/HTTPS a protokolování Microsoft Message Analyzer pro korelaci dat.

## <a name="monitoring-your-storage-service"></a>Monitorování vaší služby úložiště
Pokud jste se seznámili s monitorováním výkonu Windows, si představit metrik úložiště jako ekvivalent čítačů sledování výkonu Windows Azure Storage. V Storage Metrics najdete komplexní sadu metriky (čítače v, řečeno terminologií Windows Performance Monitor), jako je dostupnost služeb, celkový počet požadavků na službu nebo Procento úspěšných požadavků na službu. Úplný seznam dostupných metrik najdete v tématu [tabulkovému schématu metrik Storage Analytics](http://msdn.microsoft.com/library/azure/hh343264.aspx). Můžete určit, zda chcete službu storage ke shromažďování a agregace metrik každou hodinu nebo každou minutu. Další informace o tom, jak povolit metriky a monitorování účtů úložiště najdete v tématu [zapnutí metrik storage a zobrazení dat metrik](http://go.microsoft.com/fwlink/?LinkId=510865).

Můžete zvolit, které hodinové metriky, které chcete zobrazit v [webu Azure portal](https://portal.azure.com) a nakonfigurovat pravidla, která vždy, když hodinové metriky překročí prahovou hodnotu konkrétního upozornit správce e-mailem. Další informace najdete v tématu [přijímat oznámení o výstrahách](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). 

Služba úložiště shromažďuje metriky pomocí nezaručené, ale nelze zaznamenat všechny operace úložiště.

Na webu Azure Portal můžete zobrazit metriky, například dostupnost, celkový počet požadavků a průměrná doba vyřízení čísla pro účet úložiště. Pravidla oznámení je rovněž nastaven upozornit správce v případě dostupnosti klesne pod určitou úroveň. V zobrazení těchto dat, je možné oblastí pro zkoumání procento úspěšnosti služba tabulek je nižší než 100 % (Další informace najdete v části "[Metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]").

Nepřetržitě, měli byste sledovat vaše aplikace Azure a ověřte, že jsou v pořádku a provést podle očekávání tím:

* Vytvářejí se některé základní metriku pro aplikaci, která vám umožní porovnat aktuálních dat a zjišťuje významné změny v chování nástroje úložiště Azure a vaší aplikace. Hodnoty základní metriku budou v mnoha případech specifické pro aplikaci a je musí vytvořit, když jsou výkonu testování vaší aplikace.
* Záznam minutové metriky a jejich používání aktivně monitorovat neočekávaných chyb a anomálií, jako je například poraďte se špičkami chyba počty nebo rychlosti požadavků.
* Záznam hodinové metriky a jejich použití ke sledování průměrné hodnoty jako průměrné počty chyb a rychlosti požadavků.
* Prošetření potenciálních problémů pomocí diagnostické nástroje, jak je popsáno dále v části "[Diagnostika problémů s úložištěm]."

Grafy na následujícím obrázku ukazují, jak agregovat, ke které dochází pro hodinové metriky můžete skrýt špičkami v aktivitě. Hodinové metriky zobrazí konstantní počet požadavků, při minutu metriky odhalit kolísání, které jsou ve skutečnosti probíhající.

![][3]

Zbývající část této části popisuje, jaké metriky byste měli sledovat a proč.

### <a name="monitoring-service-health"></a>Monitorování stavu služby
Můžete použít [webu Azure portal](https://portal.azure.com) Chcete-li zobrazit stav služby úložiště (a dalších služeb Azure) ve všech oblastech Azure po celém světě. Monitorování umožňuje vám a okamžitě zjistěte, jestli problém mimo vaši kontrolu ovlivňuje službu Storage v oblasti, které používáte pro vaši aplikaci.

[Webu Azure portal](https://portal.azure.com) můžete zadat taky upozornění na incidenty, které mají vliv na různé služby Azure.
Poznámka: Tyto informace byly dříve k dispozici, spolu s využitím historických dat na [řídicího panelu služby Azure](http://status.azure.com).

Když [webu Azure portal](https://portal.azure.com) shromažďuje informace o stavu z uvnitř datových centrech Azure (zevnitř monitorování), zvažte také přijetí externí přístup ke generování syntetické transakce, které pravidelně přístup hostované v Azure webové aplikace z více míst. Služby nabízené [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) a Application Insights pro Visual Studio Team Services jsou příkladem tohoto přístupu. Další informace o Application Insights pro Visual Studio Team Services najdete v tématu dodatku "[dodatek 5: monitorování pomocí Application Insights pro Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Monitorování kapacity
Metrik úložiště pouze ukládá metriky kapacity pro službu blob service, protože objekty BLOB obvykle účet pro největší objem uložených dat (v době psaní, není možné pomocí metrik Storage můžete sledovat kapacitu tabulky a fronty). Můžete najít v těchto dat **$MetricsCapacityBlob** tabulky, pokud jste povolili monitorování služby Blob Service. Storage Metrics zaznamenává tato data jednou denně, a můžete použít hodnotu **RowKey** k určení, zda řádek obsahuje entity, která má vztah k uživatelským datům (hodnota **data**) nebo data analytics (hodnota **analytics**). Každá entita uložené obsahuje informace o velikost úložiště využitá (**kapacity** měřená v bajtech) a aktuální počet kontejnerů (**ContainerCount**) a objekty BLOB (**ObjectCount** ) používá v účtu úložiště. Další informace o metriky kapacity uložené v **$MetricsCapacityBlob** tabulky, najdete v článku [tabulkovému schématu metrik Storage Analytics](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Měli byste sledovat tyto hodnoty pro včasného varování, že se blíží do omezení kapacity účtu úložiště. Na webu Azure Portal můžete přidat pravidla výstrah, které vás upozorní, pokud je agregované úložiště použijte překročí nebo nedosahuje prahové hodnoty, které zadáte.
> 
> 

Nápovědu k odhadování množství různých objektů úložiště, jako jsou objekty BLOB, najdete v příspěvku blogu [Principy Azure Storage fakturace – šířka pásma, transakce a kapacita](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorování dostupnosti
Sledováním hodnotu v, měli byste sledovat dostupnost služeb úložiště ve vašem účtu úložiště **dostupnosti** sloupců v tabulkách hodinových nebo minutové metriky – **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. **Dostupnosti** procentuální hodnotu, která označuje dostupnost služby nebo operaci rozhraní API reprezentována řádek obsahuje sloupec ( **RowKey** zobrazí, pokud řádek obsahuje metriky pro Služba jako celek, nebo pro konkrétní operace rozhraní API).

Žádné hodnota a menší než 100 % znamená, že jsou selhání některých požadavků na úložiště. Zobrazí se, proč se nedaří prozkoumáním ostatních sloupců dat metrik, jako například zobrazit počty žádostí s typy různých chyb **ServerTimeoutError**. Měli byste očekávat zobrazíte **dostupnosti** dočasně spadají pod 100 % z důvodů, jako jsou přechodné server vypršení časového limitu při službu přesune oddíly k žádosti o lepší vyrovnávání zatížení; by Logika opakování v klientské aplikaci zpracování těchto přerušovaná podmínek. Článek [stavové zprávy a Storage Analytics protokolovanými operacemi](http://msdn.microsoft.com/library/azure/hh343260.aspx) jsou uvedeny typy transakcí, které metriky úložiště obsahuje v jeho **dostupnosti** výpočtu.

V [webu Azure portal](https://portal.azure.com), můžete přidat pravidla výstrah, které vás upozorní, pokud **dostupnosti** pro službu klesne pod zadanou mezní hodnotu.

"[Pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy služby úložiště souvisejících s dostupností.

### <a name="monitoring-performance"></a>Sledování výkonu
K monitorování výkonu služby úložiště, můžete použít následující metriky z tabulek po hodinách a minutové metriky.

* Hodnoty **AverageE2ELatency** a **hodnotu AverageServerLatency** sloupce zobrazují průměrnou dobu službu úložiště nebo některý typ operace rozhraní API pro zpracování požadavků. **Hodnotu AverageE2ELatency** je míra-celkovou latenci, která zahrnuje i čas potřebný k přečtení požadavku a odeslat odpověď kromě čas potřebný ke zpracování požadavku (tedy obsahuje latence sítě Jakmile požadavek dosáhne úložiště Služba); **Hodnotu AverageServerLatency** je určena pouze doba zpracování a proto nezahrnuje žádné latence sítě související s komunikaci s klientem. V části "[Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency]" dál v této příručce diskuzi o důvod, proč může být velký rozdíl mezi těmito dvěma hodnotami.
* Hodnoty **TotalIngress** a **TotalEgress** sloupce zobrazí celkové množství dat, v bajtech, přicházejících na a z vaší služby úložiště nebo konkrétní typ operace rozhraní API do toho pustit.
* Hodnoty **TotalRequests** sloupec zobrazit celkový počet požadavků, které přijímá službu storage operace rozhraní API. **TotalRequests** je celkový počet požadavků, které přijímá službu storage.

Budete obvykle monitorovat pro neočekávaným změnám v některém z těchto hodnot jako indikátor, že máte problém, který vyžaduje prošetření.

V [webu Azure portal](https://portal.azure.com), můžete přidat pravidla výstrah, které vás upozorní, pokud některé z metrik výkonu pro tuto službu klesnou pod nebo překročí prahovou hodnotu, která zadáte.

"[Pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy služby storage související s výkonem.

## <a name="diagnosing-storage-issues"></a>Diagnostika problémů s úložištěm
Existuje několik způsobů, že jste možná se dozvěděli o problém nebo problém ve vaší aplikaci, včetně:

* Hlavní chyby, které způsobí, že aplikace selhání nebo přestane fungovat.
* Významné změny základní hodnoty v metriky, které monitorujete, jak je popsáno v předchozí části "[monitorování vaší služby úložiště]."
* Sestavy uživatelů z vaší aplikace, že některé konkrétní operace neproběhla podle očekávání, nebo že některé funkce nefunguje.
* Vygenerované v rámci vaší aplikace, která se zobrazí chyby v souborech protokolu nebo prostřednictvím některé jiné metody oznámení.

Problémy týkající se služby Azure storage obvykle spadají do jedné ze čtyř kategorií:

* Vaše aplikace má problému s výkonem, hlášených uživatelům nebo neposkytuje změn v metrikách výkonu.
* Je nějaký problém s vaší stávající infrastrukturou Azure Storage v jedné nebo několika oblastech.
* Vaše aplikace je dojde k nějaké chybě, hlášených uživatelům nebo neposkytuje zvýšení jednu z metrik počet chyb, které monitorujete.
* Během vývoje a testování může použití emulátoru lokálního úložiště; může dojít k nějakým potížím, které se týkají speciálně pro použití emulátoru úložiště.

Následující oddíly popisují, měli byste postupovat podle pokynů pro diagnostiku a řešení potíží v každé z těchto čtyř kategorií. V části "[pokyny při řešení potíží]" dále v tomto průvodci poskytující víc podrobností pro některé běžné problémy, může dojít.

### <a name="service-health-issues"></a>Problémy se stavem služby
Problémy se stavem služby jsou obvykle mimo vaši kontrolu. [Webu Azure portal](https://portal.azure.com) poskytuje informace o všechny aktuální problémy se službami Azure, včetně služby úložiště. Když jste se rozhodli pro geograficky redundantní úložiště jen pro čtení při vytváření účtu úložiště, pak pokud data k dispozici v primárním umístění, vaše aplikace přepnout dočasně kopii jen pro čtení v sekundárním umístění. Číst ze sekundárního, musí vaše aplikace přepínat mezi pomocí umístění primárního a sekundárního úložiště a moct pracovat v režimu omezené funkčnosti se data jen pro čtení. Knihovny klienta úložiště Azure umožňují definovat zásady opakování, který může číst ze sekundárního úložiště v případě selhání pro čtení z primárního úložiště. Vaše aplikace potřebuje také mějte na paměti, že data v sekundárním umístění jsou nakonec konzistentní. Další informace naleznete v příspěvku blogu [možnosti redundance Azure Storage a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problémy s výkonem
Výkon aplikace může být subjektivní, zejména z pohledu uživatele. Proto je důležité mít k dispozici základní metriku, která vám pomůže identifikovat možné problémy s výkonem. Řada faktorů může ovlivnit výkon služby Azure storage z aplikace perspektivy klienta. Tyto faktory mohou pracovat ve službě úložiště, klienta nebo síťovou infrastrukturu; Proto je důležité mít strategii pro určení původu tyto problémy s výkonem.

Jakmile zjistíte, pravděpodobně umístění příčinu problému výkonu z metriky, pak vám pomůže protokolové soubory naleznete podrobné informace pro diagnostiku a dále řešte problém.

V části "[pokyny při řešení potíží]" dále v tomto průvodci poskytuje další informace o některé běžné související s výkonem problémy mohou nastat.

### <a name="diagnosing-errors"></a>Diagnostika chyb
Uživatelům vaší aplikace může upozorňovat na chyby oznámené službou klientské aplikace. Metriky úložiště taky zaznamenává počty typů různých chyb ze služeb úložiště, jako **NetworkError**, **ClientTimeoutError**, nebo **AuthorizationError**. Zatímco metrik úložiště pouze zaznamenává počty typů různých chyb, můžete získat více podrobností o jednotlivých požadavků prozkoumáním na straně serveru, na straně klienta a protokoly sítě. Obvykle stavový kód HTTP vrácený službou storage vám poskytne značit, proč žádost selhala.

> [!NOTE]
> Mějte na paměti, že by se měl zobrazit některé občasné chyby: například kvůli stavu sítě přechodné chyby nebo chyby aplikace.
> 
> 

V následujících zdrojích informací jsou užitečné pro pochopení souvisejících s úložištěm stavu a chybové kódy:

* [Běžné kódy chyb rozhraní API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Kódy chyb služby objektů BLOB](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Kódy chyb služby Queue](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kódy chyb služby Table](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kódy chyb služby souborů](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Potíže s úložištěm emulátoru
Sada Azure SDK obsahuje emulátor úložiště, můžete spustit na pracovní stanici vývoje. Tohoto emulátoru simuluje většinu chování služby Azure storage a se hodí při vývoji a testování, umožňuje spouštění aplikací, které používají služby Azure storage bez nutnosti mít předplatné Azure a účet úložiště Azure.

"[Pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy. došlo k pomocí emulátoru úložiště.

### <a name="storage-logging-tools"></a>Nástroje protokolování úložiště
Protokolování úložiště umožňuje protokolování na straně serveru požadavků na úložiště v účtu úložiště Azure. Další informace o tom, jak povolit protokolování na straně serveru a přístup k datům protokolů najdete v tématu [povolení protokolování úložiště a přístup k datům protokolů](http://go.microsoft.com/fwlink/?LinkId=510867).

Klientská knihovna pro úložiště pro .NET umožňuje shromažďovat data protokolu na straně klienta, která souvisí s operacemi úložiště, které provádí aplikace. Další informace najdete v článku [Protokolování na straně klienta s klientskou knihovnou pro úložiště .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> V některých případech (například selhání autorizace SAS) může uživatel nahlásit chybu, pro kterou nenajdete žádné žádosti o data v protokolech úložiště na straně serveru. Můžete použít možnosti protokolování klientskou knihovnu pro úložiště pro zjištění, zda je příčinou problém na straně klienta nebo prozkoumat sítě pomocí nástroje pro monitorování sítě.
> 
> 

### <a name="using-network-logging-tools"></a>Pomocí nástroje protokolování sítě
Můžete zaznamenávat provoz mezi klientem a serverem, aby poskytoval podrobné informace o datech, které vyměňujete klientem a serverem a podkladové síťové podmínky. Nástroje protokolování užitečné sítě patří:

* [Fiddler](http://www.telerik.com/fiddler) je bezplatný webový ladicí proxy server, který umožňuje zkoumat hlavičky a datové části zpráv požadavků a odpovědí HTTP a HTTPS. Další informace najdete v tématu [příloha 1: použití aplikace Fiddler pro zachycení provozu HTTP a HTTPS](#appendix-1).
* [Sledování sítě (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) a [Wireshark](http://www.wireshark.org/) jsou zdarma sítě protokolu analyzátory, které vám umožní zobrazit informace o podrobné paketů pro širokou škálu síťových protokolů. Další informace o Wireshark najdete v tématu "[příloha 2: použití Wireshark pro zachycení síťového provozu](#appendix-2)".
* Microsoft Message Analyzer je nástroj od Microsoftu, která nahrazuje Netmon a, která kromě zachytávání dat na síťových paketů, vám umožní zobrazit a analyzovat data protokolů zachycené z dalších nástrojů. Další informace najdete v tématu "[dodatek 3: použití Microsoft Message Analyzer pro zachycení síťového provozu](#appendix-3)".
* Pokud chcete provést test základní konektivity ke kontrole, že klientského počítače může připojit ke službě Azure storage v síti, nelze to provést pomocí standardní **ping** nástroj na straně klienta. Můžete však použít [ **použít příkaz tcping** nástroj](http://www.elifulkerson.com/projects/tcping.php) aby se ověřilo připojení.

V mnoha případech se data protokolu z úložiště, protokolování a klientskou knihovnu pro úložiště bude stačit k diagnostice problému, ale v některých případech může potřebovat podrobnější informace, které může poskytnout tyto nástroje protokolování sítě. Například použití aplikace Fiddler k zobrazení zpráv protokolu HTTP a HTTPS vám umožní zobrazit hlavičky a datové části data odesílaná do a ze služeb úložiště, které by vám umožní zkoumat, jak klientská aplikace opakování operace úložiště. Protokol analyzátorů Wireshark pracovat na úrovni paketů, můžete k zobrazení dat TCP, který vám umožní řešit ke ztrátě paketů a problémy s připojením. Message Analyzer může fungovat na HTTP a TCP vrstvy.

## <a name="end-to-end-tracing"></a>Trasování začátku do konce
Konec Konec trasování pomocí různých protokolů je užitečná technika k prošetření potenciálních problémů. Můžete použít informace data a času z vašich dat metrik jako údaj kde začít hledat v souborech protokolů podrobné informace, které vám pomohou při řešení potíží.

### <a name="correlating-log-data"></a>Korelaci dat protokolu
Při prohlížení protokolů z klientských aplikací, síťové trasování a protokolování úložiště na straně serveru, který je důležité mít možnost provést korelaci požadavky napříč různých protokolových souborech. Soubory protokolu zahrnout několik různých polí, které lze použít jako identifikátory korelace. ID požadavku klienta je nejužitečnější pole, které má použít ke korelaci položek v různých protokolech. Nicméně v některých případech může být užitečné používat ID serveru požadavku nebo časová razítka. Další informace o těchto možnostech naleznete v následujících částech.

### <a name="client-request-id"></a>ID žádosti klienta
Klientská knihovna pro úložiště se automaticky vygeneruje ID žádosti klienta jedinečný pro každý požadavek.

* V protokolu na straně klienta, který vytvoří klientskou knihovnu pro úložiště, ID žádosti klienta se zobrazí v **ID žádosti klienta** pole každá položka protokolu týkající se žádosti.
* Trasování sítě, jako jsou například nezachytává Fiddler, ID požadavku klienta je viditelný v zprávy s požadavkem jako **x-ms klienta request-id** hodnota hlavičky protokolu HTTP.
* V protokolu úložiště protokolování na straně serveru se zobrazí ID žádosti klienta ve sloupci ID požadavku klienta.

> [!NOTE]
> Je možné pro víc požadavků sdílet stejné ID žádosti klienta, protože klient můžete přiřadit tuto hodnotu (přestože klientskou knihovnu pro úložiště automaticky přiřadí novou hodnotu). Když klient pokus obnovuje, všechny pokusy sdílejí stejné ID požadavku klienta. V případě batch odeslaných z klienta dávka má ID. požadavek jednoho klienta
> 
> 

### <a name="server-request-id"></a>ID žádosti serveru
Služby úložiště automaticky vygeneruje ID serveru požadavku.

* Protokolování úložiště protokolu na straně serveru, zobrazí se ID serveru požadavku **záhlaví ID žádosti** sloupce.
* Trasování sítě, jako jsou například nezachytává Fiddler, zobrazí se ID serveru požadavku v odpovědích jako **x-ms-request-id** hodnota hlavičky protokolu HTTP.
* V protokolu na straně klienta, který vytvoří klientskou knihovnu pro úložiště, zobrazí se ID serveru požadavku v **operace Text** sloupec pro záznam protokolu s podrobnostmi o odpověď serveru.

> [!NOTE]
> Služba úložiště vždy přiřadí jedinečných serverů ID požadavku každého požadavku, které obdrží, takže každý opakovaný pokus od klienta a každé operace zahrnuté v dávce obsahuje ID žádosti o jedinečných serverů
> 
> 

Pokud vyvolá klientskou knihovnu pro úložiště **StorageException** v klientovi, **RequestInformation** obsahuje vlastnost **RequestResult** objekt, který zahrnuje  **ServiceRequestID** vlastnost. Se dá dostat taky **RequestResult** objektu z **OperationContext** instance.

Následující vzorový kód ukazuje, jak nastavit vlastní **ID žádosti klienta** hodnotu připojením **OperationContext** objektu požadavek na službu úložiště. Také ukazuje, jak načíst **ServerRequestId** hodnotu z zprávy s odpovědí.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Časová razítka
Můžete také použít časová razítka najít položky související protokolu, ale buďte opatrní z jakékoli časový posun mezi klientem a serverem, který může existovat. Hledat plus nebo mínus 15 minut, než odpovídající položky na straně serveru podle časového razítka na straně klienta. Mějte na paměti, že metadata objektu blob pro objekty BLOB, který obsahuje metriky označuje časový rozsah pro metriky, které jsou uloženy v objektu blob. Tento časový rozsah je užitečné, pokud máte velký počet objektů BLOB metriky pro stejný minuta nebo hodina.

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží
V této části vám pomůžou s diagnostiku a řešení potíží se některé běžné problémy s vaší aplikace může dojít při používání služby Azure storage. Pomocí níže uvedeného seznamu vyhledejte informace týkající se konkrétního problému.

**Řešení potíží s rozhodovací strom**

---
Souvisí váš problém s výkonem služby storage?

* [Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency]
* [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]
* [Metrika ukazuje vysokou hodnotu AverageServerLatency]
* [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]

---
Souvisí váš problém dostupnosti jedné služby úložiště?

* [Metrika ukazuje zvýšení u PercentThrottlingError]
* [Metrika ukazuje zvýšení u PercentTimeoutError]
* [Metrika ukazuje zvýšení u PercentNetworkError]

---
 Klientské aplikace přijímá odpovědi HTTP 4XX (například 404) ze služby úložiště?

* [Klient dostává zprávy HTTP 403 (zakázáno)]
* [Klient dostává zprávy HTTP 404 (Nenalezeno)]
* [Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]

---
[Metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]

---
[Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]

---
[Dochází k neočekávaným restartováním virtuálních počítačů, které mají velký počet virtuálních pevných disků připojených]

---
[Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]

---
[Narazíte na potíže s instalací sady Azure SDK pro .NET]

---
[Máte jiný problém se službou úložiště]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageE2ELatency i hodnotu AverageServerLatency
Na obrázku níže z [webu Azure portal](https://portal.azure.com) nástroj pro sledování ukazuje příklad kde **AverageE2ELatency** je výrazně vyšší než **hodnotu AverageServerLatency**.

![][4]

Služba úložiště spočítá jenom metriku **AverageE2ELatency** pro úspěšné požadavky a na rozdíl od **hodnotu AverageServerLatency**, zahrnuje čas klienta má odesílat data a přijímat potvrzení ze služby storage. Tedy rozdíl mezi **AverageE2ELatency** a **hodnotu AverageServerLatency** můžou být kvůli klientské aplikaci je pomalý nebo z důvodu podmínek v síti.

> [!NOTE]
> Můžete také zobrazit **E2ELatency** a **ServerLatency** storage jednotlivé operace v protokolování úložiště protokolovat data.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Prozkoumat problémy s výkonem klienta
Mezi možné důvody pro klienta reagovat pomalu patří omezený počet dostupných připojení nebo vláken, nebo je nedostatek prostředků, jako je například procesor, paměť nebo síť šířky pásma. Je možné, že problém vyřešit tak, že upravíte kód klienta efektivnější (například pomocí byla zahájena asynchronní volání do služby storage) nebo s použitím většího virtuálního počítače (s více jádry a větší množství paměti).

Pro tabulky a fronty služby algoritmus Nagle mohou být způsobeny také vysokou **AverageE2ELatency** ve srovnání s **hodnotu AverageServerLatency**: Další informace najdete v příspěvku [Nagle. Algoritmus není na malých požadavků není popisný](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Algoritmus Nagle v kódu můžete zakázat s použitím **Třída ServicePointManager** třídy v **System.Net** oboru názvů. Byste měli udělat předtím, než provedete všechna volání do tabulky nebo fronty služby ve vaší aplikaci, protože to nemá vliv na připojení, které jsou již otevřít. V následujícím příkladu se segmenty Convenience **Application_Start** metoda v roli pracovního procesu.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Měli byste zkontrolovat protokoly na straně klienta a zobrazit, kolik požadavky, že klientské aplikace posílá a vyhledat obecné .NET týkající se služby problémových míst výkonu v klientovi například CPU, uvolňování paměti .NET, využití sítě, paměť. Jako výchozí bod pro řešení potíží s .NET klientských aplikací, najdete v článku [ladění, trasování a profilace](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Prozkoumat problémy s latencí sítě
Obvykle vysokou latencí začátku do konce způsobena sítí je z důvodu přechodné podmínky. Oba problémy se sítí přechodným i trvalým například vynechaných paketů, můžete prozkoumat pomocí nástrojů, jako je Wireshark nebo Microsoft Message Analyzer.

Další informace o řešení potíží s problémy se sítí pomocí Wireshark najdete v tématu "[Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu]."

Další informace o řešení potíží s problémy se sítí pomocí nástroje Microsoft Message Analyzer, najdete v části "[dodatek 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale klienta je vysoká latence
V tomto scénáři je s největší pravděpodobností zpoždění při dosažení službu storage požadavků na úložiště. Byste měli prozkoumat, proč požadavky od klienta nejsou provedením ho pomocí služby blob service.

Jedním z možných důvodů pro klienta, přičemž dojde ke zpoždění odesílání požadavků je, že jsou omezený počet dostupných připojení nebo vláken.

Také zkontrolujte, zda klient provádí několik opakovaných pokusů a zjistěte důvod, pokud se jedná. Chcete-li určit, zda klient pracuje více opakování, můžete:

* Zkontrolujte protokoly Storage Analytics. Pokud se dějí více opakovaných pokusů, zobrazí se více operací se stejným ID žádosti klienta, ale s požadavkem na jiný server ID.
* Zkontrolujte protokoly klienta. Podrobné protokolování bude znamenat, že došlo k chybě zkuste to znovu.
* Ladění kódu a zkontrolujte vlastnosti **OperationContext** objekt přidružený k požadavku. Pokud se má opakovat operaci, **RequestResults** vlastnost bude obsahovat žádosti více jedinečných serverů ID. Můžete také zkontrolovat počáteční a koncový čas pro každý požadavek. Další informace najdete v ukázce kódu v části [ID žádosti serveru].

Pokud zde nejsou žádné problémy v klientovi, které byste měli prozkoumat potenciální problémy sítě, jako je ztráta paketů. Nástroje, jako je Wireshark nebo Microsoft Message Analyzer můžete použít k prozkoumání problémů se sítí.

Další informace o řešení potíží s problémy se sítí pomocí Wireshark najdete v tématu "[Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu]."

Další informace o řešení potíží s problémy se sítí pomocí nástroje Microsoft Message Analyzer, najdete v části "[dodatek 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageServerLatency.
V případě vysoce **hodnotu AverageServerLatency** pro požadavků na stažení objektů blob, byste měli použít protokoly protokolování úložiště zobrazíte, pokud jsou opakované požadavky na stejný objekt blob (nebo sadu objektů BLOB). Pro požadavky na nahrání objektu blob které byste měli prozkoumat jakém bloku je velikost klienta pomocí (například bloky, které chunks menší než 64 kB může způsobit režijní náklady, pokud čtení jsou také v méně než 64 kB), a pokud více klientů jsou nahrávání bloků do stejného objektu blob v para paralelní. Také byste měli zkontrolovat minutové metriky pro poraďte se špičkami počet požadavků, jejichž výsledkem je vyšší než každý druhý cíle škálovatelnosti: také naleznete v tématu "[metrika ukazuje zvýšení u PercentTimeoutError]."

Pokud dochází k vysoké **hodnotu AverageServerLatency** ke stažení objektů blob požadavky, když se opakují požadavky na stejný objekt blob nebo sadu objektů BLOB, měli byste uvažovat o ukládání do mezipaměti těchto objektů BLOB pomocí Azure Cache nebo doručování obsahu Azure Network (CDN). Pro požadavky na nahrání můžete zlepšit propustnost pomocí větší velikost bloku. Pro dotazy na tabulky je také možné implementovat na klientských počítačích, které provádí stejné operace dotazů a kde se data nemění příliš často ukládání do mezipaměti na straně klienta.

Vysoká **hodnotu AverageServerLatency** hodnoty mohou být také příznakem špatně navrženého tabulky nebo dotazy, výsledkem prohledávání nebo které mají tvar připojení/předřaďte proti. Další informace najdete v tématu "[metrika ukazuje zvýšení u PercentThrottlingError]".

> [!NOTE]
> Můžete najít zde kontrolní výkonu komplexní kontrolní seznam: [výkon úložiště Microsoft Azure a kontrolní seznam ke škálovatelnosti](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Dochází k neočekávaným zpožděním při doručování zpráv ve frontě
Pokud dochází ke zpoždění mezi časem aplikace přidá zprávu do fronty a čas, kdy bude k dispozici ke čtení z fronty, by měl proveďte následující kroky k diagnostice problému:

* Ověřte, že aplikace je úspěšně přidání zprávy do fronty. Zkontrolujte, zda aplikace není opakování **AddMessage** metody několikrát před úspěšné. Klientská knihovna pro úložiště protokolů se zobrazí všechny opakovaných pokusech operace úložiště.
* Ověřte, neexistuje žádné hodiny nerovnoměrné rozdělení mezi role pracovního procesu, který přidá zprávu do fronty a role pracovního procesu, která čte zprávy z fronty, která umožňuje objevit, jako kdyby dochází ke zpoždění při zpracování.
* Zaškrtněte, pokud se nedaří role pracovního procesu, která čte zprávy z fronty. Pokud klient fronty volá **GetMessage** metoda, ale nebude moci reagovat na potvrzení, zpráva zůstane neviditelné ve frontě, dokud **invisibilityTimeout** období vyprší. V tuto chvíli k dispozici pro zpracování zprávy.
* Zaškrtněte, pokud délka fronty časem roste. Tato situace může nastat, pokud nemáte dostatečnou pracovní procesy k dispozici pro zpracování všechny zprávy, které další pracovní procesy jsou uvedení ve frontě. Také zkontrolujte metriky, abyste viděli, pokud se odstranění, které dochází k selhání požadavků a odebrání z fronty Spolehněte se na zprávy, jež mohou indikovat opakovaných neúspěšných pokusech o odstranění zprávy.
* V protokolech protokolování úložiště pro všechny operace fronty, které mají vyšší, než se očekávalo **E2ELatency** a **ServerLatency** hodnoty po dobu delší dobu než obvykle.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metrika ukazuje zvýšení u PercentThrottlingError
Po překročení cíle škálovatelnosti služby úložiště, dojde k chybám omezování. Omezení služby úložiště a ujistěte se, že žádné jednoho klienta tenanta můžete použít službu za cenu ostatní. Další informace najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) podrobné informace o cíle škálovatelnosti pro účty úložiště a cíle výkonnosti pro oddíly v rámci účtů úložiště.

Pokud **PercentThrottlingError** metrika způsobit nárůst Procento požadavků, které se nedaří s chybou omezování, budete muset prozkoumat jeden z těchto dvou scénářů:

* [Přechodné zvýšení u PercentThrottlingError]
* [Trvalé zvýšení PercentThrottlingError chybu]

Zvýšení **PercentThrottlingError** často dochází ve stejnou dobu jako nárůst počtu požadavků na úložiště, nebo když jste původně zátěžové testování vaší aplikace. To může také projevit v klientovi jako "503 Server zaneprázdněn" nebo "časový limit 500 operace" HTTP stavové zprávy z operace úložiště.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Přechodné zvýšení u PercentThrottlingError
Pokud se vám zobrazují špičky v hodnotě **PercentThrottlingError** , který se shoduje s období vysoké aktivity pro aplikaci, implementujte exponenciální (nikoli lineární) regresní strategii opakování v klientovi. Regresní opakovaných pokusů snižovat zatížení okamžité oddílu a Nápověda aplikace vyhlazení špiček v provozu. Další informace o tom, jak implementovat zásady opakování s využitím klientské knihovny pro úložiště najdete v tématu [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Může se zobrazit také špičky v hodnotě **PercentThrottlingError** , který není časově shodovala se zastávkami období vysoké aktivity pro aplikaci: nejpravděpodobnější příčinou je služba úložiště, přesun oddílů, aby zlepšil Vyrovnávání zatížení.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Trvalé zvýšení PercentThrottlingError chybu
Pokud se vám zobrazují konzistentně vysoké hodnoty pro **PercentThrottlingError** následující trvalé zvýšení objemu transakcí, nebo při počátečním načtení provádění testů v aplikaci, je nutné vyhodnotit jak vaše aplikace používá oddílů pro úložiště a určuje, zda se přiblíží cíle škálovatelnosti účtu úložiště. Například pokud dochází k omezování chyb do fronty (které se počítá jako jeden oddíl), pak jste měli zvážit použití další fronty k šíření transakce napříč několika oddíly. Pokud dochází k chybám v tabulce, omezování, budete muset zvažte použití jiné schéma rozdělení oddílů pro vaše transakce rozdělit mezi několik oddílů s využitím používání nástroje většímu počtu hodnot klíče oddílu. Jednou z běžných příčin tohoto problému je prepend/připojovat proti vzor kde vybrat datum jako klíč oddílu a pak se všechna data v určitý den se zapisují do jednoho oddílu: pod zátěží, může dojít k kritický bod zápisu. Zvažte návrh, různých oddílů nebo vyhodnotit, jestli pomocí úložiště objektů blob může být lepší řešení. Také zkontrolujte, zda omezování dochází v důsledku špičkami v provozu a způsoby vyhlazení váš model požadavky prozkoumat.

Pokud distribuujete vaše transakce napříč několika oddíly, musí i nadále budete vědět limity škálovatelnosti mého nastavení pro účet úložiště. Například pokud jste použili deset fronty každé zpracování maximálně 2 000 1KB zpráv za sekundu, budete na celkový limit 20 000 zpráv za sekundu pro účet úložiště. Pokud potřebujete zpracovávat více než 20 000 entity za sekundu, měli byste zvážit, použití více účtů úložiště. Také byste měli mít na paměti, že velikost požadavků a entit má vliv na když službu storage omezuje vaši klienti: Pokud máte větší požadavky a entity, které může omezí dříve.

Návrh neefektivní dotazu může také způsobit dosažení limitů škálovatelnosti pro oddíly tabulky. Například dotaz s filtrem, ale jedno procento entit, který vybere jen v oddílu, který vyhledá všechny entity v oddílu potřebovat pro přístup k každé entity. Každá entita, přečtěte si budou započítávat do celkový počet transakcí v tomto oddílu. Proto můžete snadno dosáhnout cíle škálovatelnosti.

> [!NOTE]
> Testování výkonu byste měli odhalit, žádné návrhy neefektivní dotazu ve vaší aplikaci.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metrika ukazuje zvýšení u PercentTimeoutError
Vaše metrika ukazuje zvýšení **PercentTimeoutError** pro některé z vašich služeb úložiště. Ve stejnou dobu obdrží klient k velkému počtu stavové zprávy "časový limit 500 operace" HTTP z operace úložiště.

> [!NOTE]
> Může se zobrazit chyby časového limitu dočasně jako službu storage zatížení při zpracování žádostí zůstatky oddílu se přesunete na nový server.
> 
> 

**PercentTimeoutError** metrika je agregaci z těchto metrik: **ClientTimeoutError**, **AnonymousClientTimeoutError**,  **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, a **SASServerTimeoutError**.

Časový limit serveru jsou v důsledku chyby na serveru. Překročení časového limitu klienta dojít, protože operace na serveru překročilo časový limit zadaný klientem; Například můžete nastavit časový limit pro operace pomocí klienta pomocí klientskou knihovnu pro úložiště **ServerTimeout** vlastnost **QueueRequestOptions** třídy.

Vypršení časového limitu serveru znamenat problém se službou úložiště, který vyžaduje další šetření. Pokud chcete zobrazit, pokud dosahujete limitů škálovatelnosti služby a identifikovat všechny špičky v provozu, který může být příčinou tohoto problému, můžete použít metriky. Pokud se jedná o přerušovaný, může být kvůli Vyrovnávání zatížení aktivity ve službě. Pokud tento problém je trvalé a nezpůsobuje aplikace dosažení limitů škálovatelnosti služby, by měla vyvolat problém podpory. Pro překročení časového limitu klienta musíte rozhodnout, jestli časového limitu je nastaven na odpovídající hodnotu v klientovi a buď změnit, nastavte hodnotu časového limitu v klientovi nebo prozkoumat, jak může zlepšit výkon operace ve službě úložiště, například pomocí optimalizace dotazy na tabulku nebo nezmenšit velikost této zprávy.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metrika ukazuje zvýšení u PercentNetworkError
Vaše metrika ukazuje zvýšení **PercentNetworkError** pro některé z vašich služeb úložiště. **PercentNetworkError** metrika je agregaci z těchto metrik: **NetworkError**, **AnonymousNetworkError**, a **SASNetworkError** . Těm dochází, když služba úložiště zjistí chybu v síti, pokud klient odešle požadavek úložiště.

Nejčastější příčinou této chyby je klient odpojení před vypršení časového limitu vyprší za službu storage. Prošetření kódu v klientovi pochopit, proč a kdy se klient neodpojí ze služby storage. Také vám pomůže Wireshark, Microsoft Message Analyzer nebo použít příkaz Tcping prozkoumat problémy se síťovým připojením z klienta. Tyto nástroje jsou popsány v [Přílohy].

### <a name="the-client-is-receiving-403-messages"></a>Klient dostává zprávy HTTP 403 (zakázáno)
Pokud vaše klientská aplikace způsobující chyby HTTP 403 (zakázáno), pravděpodobnou příčinou je, že klient používá vypršenou platností sdíleného přístupového podpisu (SAS) při odesílání požadavku na úložiště (i když další možné příčiny zahrnují hodiny zkosení, neplatné klíče a prázdný záhlaví ). Pokud vypršela platnost klíče SAS příčinou je, neuvidíte žádné položky v datech log úložiště protokolování na straně serveru. V následující tabulce jsou uvedeny ukázky z protokolu na straně klienta generovaný klientskou knihovnu pro úložiště, který ukazuje tento problém vyskytující se:

| Zdroj | Podrobnosti | Podrobnosti | ID žádosti klienta | Operace text |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab-… |Spouští se operace s umístěním primární umístění režim PrimaryOnly podle. |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Spouští se požadavku na https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp; sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm 2FyhNYZEmJNQ % 3D&amp;verze api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Čekání na odpověď. |
| Microsoft.WindowsAzure.Storage |Upozornění |2 |85d077ab -… |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: zakázáno (403). |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Byla přijata odpověď. Stavový kód 403, ID žádosti = = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =. |
| Microsoft.WindowsAzure.Storage |Upozornění |2 |85d077ab -… |Při operaci došlo k výjimce: vzdálený server vrátil chybu: zakázáno (403)... |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Kontroluje se, pokud byste operaci opakovat. Počet opakování 0, stavový kód HTTP 403, výjimka = = vzdálený server vrátil chybu: zakázáno (403)... |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Následujícího umístění je nastavená na primární, na základě umístění režimu. |
| Microsoft.WindowsAzure.Storage |Chyba |1 |85d077ab -… |Zásady opakování pro opakování nepovolil. Došlo k selhání s vzdálený server vrátil chybu: zakázáno (403). |

V tomto scénáři které byste měli prozkoumat, proč je SAS token vyprší před klient odešle token do serveru:

* Obvykle by neměl nastavit čas spuštění při vytvoření SAS pro klienta k použití okamžitě. Pokud jsou hodiny malé rozdíly mezi hostiteli generování SAS pomocí aktuálního času a služba úložiště je možné pro službu storage pro příjem SAS, který ještě není platný.
* Nenastavujte dobu vypršení platnosti velmi krátké na SAS. Hodiny malé rozdíly mezi hostiteli generování SAS a služba úložiště, může vést k SAS zjevně vypršení platnosti dříve, než se očekává.
* Nemá parametr verze v klíči SAS (například **sv = 2015-04-05**) odpovídat verzi klientskou knihovnu pro úložiště používáte? Doporučujeme vždy používat nejnovější verzi [Klientská knihovna pro úložiště](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Pokud byste znovu generovali přístupových klíčů úložiště, může být zneplatněny všechny stávající tokeny SAS. Tento problém může nastat, pokud generovat tokeny SAS s časem vypršení platnosti dlouhé pro klientské aplikace do mezipaměti.

Pokud používáte klientskou knihovnu pro úložiště ke generování tokenů SAS, je snadné vytvořit platný token. Ale pokud se pomocí rozhraní REST API úložiště a ručně vytvářet tokeny SAS, přečtěte si téma [delegování přístupu pomocí sdíleného přístupového podpisu](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klient dostává zprávy HTTP 404 (Nenalezeno)
Pokud klientská aplikace obdrží zprávu HTTP 404 (Nenalezeno) ze serveru, to znamená, že objekt, který se klient pokouší použít (například entity, tabulky, objektů blob, kontejnerů nebo fronty), neexistuje ve službě storage. Existuje několik z možných důvodů, například:

* [Klient nebo jiným procesem dříve odstraněné objektu]
* [Chybu ověřování sdíleného přístupového podpisu (SAS)]
* [Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu]
* [Selhání sítě]

#### <a name="client-previously-deleted-the-object"></a>Klient nebo jiným procesem dříve odstraněné objektu
Ve scénářích, kde se klient pokouší číst, aktualizovat nebo odstranit data ve službě úložiště, je obvykle snadno rozpoznat v protokolech na straně serveru předchozí operace, které odstraněné ze služby storage u daného objektu. Často data protokolu ukazuje, že jiný uživatel nebo proces objektu. Protokolování úložiště protokolu na straně serveru zobrazit typ operace a požadovaný klíč objektu sloupce při odstranění objektu klienta.

V případě, kdy se klient pokouší o vložení objektu nemusí být hned zjevné důvod, proč to výsledků v odpovědi HTTP 404 (Nenalezeno) vzhledem k tomu, že klient je vytvoření nového objektu. Ale pokud klient je vytvoření objektu blob musí být schopen najít kontejner objektů blob, pokud klient je vytváření zprávy musí být schopna najít frontu, a pokud klienta se přidá řádek musí být schopen najít v tabulce.

Protokolu na straně klienta z klientskou knihovnu pro úložiště můžete získat podrobnější vysvětlení z když klient odešle konkrétní požadavky na službu storage.

Následující protokol na straně klienta generovaných knihovny klienta úložiště znázorňuje problém, když klient nemůže najít kontejner pro tento objekt blob, který se vytváří. Tento protokol obsahuje podrobnosti o následující operace úložiště:

| ID požadavku | Operace |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metoda odstranit kontejner objektů blob. Všimněte si, že tato operace zahrnuje **HEAD** požadavek na provedení kontroly existence kontejneru. |
| e2d06d78... |**CreateIfNotExists** metodu pro vytvoření kontejneru objektů blob. Všimněte si, že tato operace zahrnuje **HEAD** požadavek, který zkontroluje existenci kontejneru. **HEAD** vrátí zprávu 404, ale bude pokračovat. |
| de8b1c3c-... |**UploadFromStream** metodu pro vytvoření objektu blob. **UMÍSTIT** žádost selže se zprávou 404 |

Položky protokolu:

| ID požadavku | Operace Text |
| --- | --- |
| 07b26a5d-... |Spouští se požadavku na https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = 03 června 2014 HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Byla přijata odpověď. Stavový kód 200, ID žádosti = = eeead849-... Content-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Hlavičky odpovědi byly úspěšně zpracována, pokračuje se zbývající operace. |
| 07b26a5d-... |Stahuje se text odpovědi. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| 07b26a5d-... |Spouští se požadavku na https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = 03 června 2014 DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Byla přijata odpověď. Kód stavu = 202, ID žádosti = 6ab2a4cf-..., Content-MD5 = ETag =. |
| 07b26a5d-... |Hlavičky odpovědi byly úspěšně zpracována, pokračuje se zbývající operace. |
| 07b26a5d-... |Stahuje se text odpovědi. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Spouští se asynchronní požadavek na https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = 03 června 2014 HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Spouští se požadavku na https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Probíhá příprava k zápisu dat požadavku. |
| e2d06d78-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| e2d06d78-... |Byla přijata odpověď. Stavový kód 404, ID žádosti = = 353ae3bc-..., Content-MD5 = ETag =. |
| e2d06d78-... |Hlavičky odpovědi byly úspěšně zpracována, pokračuje se zbývající operace. |
| e2d06d78-... |Stahuje se text odpovědi. |
| e2d06d78-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Spouští se asynchronní požadavek na https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, 03 června 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Zápis dat požadavku. |
| de8b1c3c-... |Čekání na odpověď. |
| e2d06d78-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (409) konfliktů... |
| e2d06d78-... |Byla přijata odpověď. Stavový kód 409, ID žádosti = = c27da20e-..., Content-MD5 = ETag =. |
| e2d06d78-... |Text odpovědi v chybě stahování. |
| de8b1c3c-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| de8b1c3c-... |Byla přijata odpověď. Stavový kód 404, ID žádosti = = 0eaeab3e-..., Content-MD5 = ETag =. |
| de8b1c3c-... |Při operaci došlo k výjimce: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| de8b1c3c-... |Zásady opakování pro opakování nepovolil. Došlo k selhání s vzdálený server vrátil chybu: (404) nebyl nalezen... |
| e2d06d78-... |Zásady opakování pro opakování nepovolil. Došlo k selhání s vzdálený server vrátil chybu: (409) konfliktů... |

V tomto příkladu ukazuje, že klient je prokládání žádosti od protokolu **CreateIfNotExists** – metoda (žádost o ID e2d06d78...) s žádostmi od **UploadFromStream** – metoda (de8b1c3c-...). Tato prokládání se stane, protože klientské aplikace nyní volá tyto metody asynchronně. Upravte asynchronní kód v klientovi ověřte, že vytvoří kontejner před pokusem o nahrát data do objektu blob v tomto kontejneru. V ideálním případě byste měli předem vytvořit vaše kontejnery.

#### <a name="SAS-authorization-issue"></a>Problém ověření sdíleného přístupového podpisu (SAS)
Pokud klientská aplikace se pokusí použít klíč SAS, která nezahrnuje potřebná oprávnění pro tuto operaci, službu storage vrátí zprávu HTTP 404 (Nenalezeno) do klienta. Ve stejnou dobu, zobrazí se také nenulové hodnoty pro **SASAuthorizationError** v metriky.

V následující tabulce jsou uvedeny ukázkovou zprávu protokolu na straně serveru ze souboru protokolu protokolování úložiště:

| Název | Hodnota |
| --- | --- |
| Čas zahájení žádosti | 2014-05-30T06:17:48.4473697Z |
| Typ operace     | GetBlobProperties            |
| Stav žádosti     | SASAuthorizationError        |
| Stavový kód HTTP   | 404                          |
| Typ ověřování| SAS                          |
| Typ služby       | Objekt blob                         |
| Adresa URL požadavku        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Záhlaví ID žádosti  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID žádosti klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Zjistěte, proč se klientská aplikace pokouší o provedení operace, pro které mu nebylo uděleno oprávnění.

#### <a name="JavaScript-code-does-not-have-permission"></a>Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu
Pokud používáte javascriptový klient a služba úložiště je vracející HTTP 404, zkontrolujte následující chyby jazyka JavaScript v prohlížeči:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Vývojářské nástroje F12 v aplikaci Internet Explorer můžete použít k trasování zprávy vyměňují mezi prohlížečem a služba úložiště, pokud řešíte problémy JavaScriptu na straně klienta.
> 
> 

K těmto chybám, protože implementuje webový prohlížeč [zásada stejného zdroje](http://www.w3.org/Security/wiki/Same_Origin_Policy) omezení zabezpečení, které chrání webovou stránku z volání rozhraní API v jiné doméně z domény stránce pochází.

JavaScript problém obejít, můžete nakonfigurovat různé sdílení prostředků zdroji (CORS) pro službu storage, který klient přistupuje. Další informace najdete v tématu [podporu sdílení prostředků mezi zdroji (CORS) pro služby Azure Storage](http://msdn.microsoft.com/library/azure/dn535601.aspx).

Následující příklad kódu ukazuje postup při konfiguraci služby objektů blob umožňuje JavaScript spuštěný v doméně Contoso pro přístup k objektu blob ve službě blob storage:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Chyba sítě
V některých případech může vést ke ztrátě síťové pakety na službu storage zprávy HTTP 404 vrácením klientovi. Například když klientské aplikace je odstranění entity ze služby table service zobrazí klient vyvolat výjimky úložiště vytváření sestav "HTTP 404 (Nenalezeno)" stavovou zprávu ze služby table service. Při zkoumání tabulky ve službě table storage, uvidíte, že služba odstranit entitu podle požadavku.

Podrobnosti o výjimce v klientovi zahrnují ID žádosti (7e84f12d...), které přiřadil služby table service pro daný požadavek: tyto informace můžete najít podrobnosti o žádosti v protokolech úložiště na straně serveru tak, že v **záhlaví id žádosti**  sloupec v souboru protokolu. Metriky můžete také použít k identifikaci, pokud dojde k selhání takovou situaci a pak vyhledejte soubory protokolu na základě času metriky zaznamenané k této chybě. Této položky protokolu zobrazuje, že odstranění se nezdařilo se zpráva stav "Klienta HTTP (404) jiná chyba". Stejný záznam protokolu obsahuje také generované klientem v ID požadavku **client-request-id** sloupec (813ea74f...).

Protokolu na straně serveru také obsahuje jiný záznam se stejným **client-request-id** odstranění hodnoty (813ea74f...) pro úspěšného provozu pro stejnou entitu a ze stejného klienta. Tato operace odstranění úspěšná konal úplně velmi krátce předtím, než se odstranit požadavek.

Nejpravděpodobnější příčinou tohoto scénáře je, že klient odešle žádost o odstranění entity do služby table service, který byl úspěšný, avšak neobdržel potvrzení ze serveru (třeba kvůli problému se dočasná síť). Klient pak automaticky opakovat operaci (pomocí stejných **client-request-id**), a při tomto opakovaném pokusu se nezdařila, protože entita již byl odstraněn.

Pokud k tomuto problému dochází často, byste měli prozkoumat, proč klient nemůže na příjem potvrzení ze služby table service. Pokud se jedná o přerušovaný, by měly zachytávat chyby "HTTP (404) nebyl nalezen" a protokolu v klientovi ale povolit klienta, abyste mohli pokračovat.

### <a name="the-client-is-receiving-409-messages"></a>Klient dostává zprávy HTTP 409 (konflikt)
V následující tabulce jsou uvedeny extrakci z protokolu na straně serveru pro dva klientské operace: **DeleteIfExists** a potom hned za **CreateIfNotExists** pomocí stejného názvu kontejneru objektů blob. Výsledkem každého klienta operace dva požadavky odeslané na server, nejprve **GetContainerProperties** požadavek na zkontrolovat, zda kontejner existuje, za nímž následuje **DeleteContainer** nebo  **CreateContainer** požadavku.

| Časové razítko | Operace | Výsledek | Název kontejneru | ID žádosti klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Kód v klientské aplikaci odstraní a potom hned znovu vytvoří kontejner objektů blob se stejným názvem: **CreateIfNotExists** – metoda (požadavek klienta ID bc881924-...) nakonec selže s chybou HTTP 409 (konflikt). Když klient odstraní kontejnery objektů blob, tabulek nebo front je na krátkou dobu před názvem opět k dispozici.

Klientská aplikace by měl použít názvy kontejnerů jedinečné pokaždé, když se vytvoří nové kontejnery, pokud je běžný vzor odstranit a znovu vytvořte.

### <a name="metrics-show-low-percent-success"></a>Metrika ukazuje nízkou PercentSuccess nebo položky log analytics mají operací se stavem transakce ClientOtherErrors
**PercentSuccess** metrika zaznamená procento operace, které byly úspěšné podle jejich stavového kódu protokolu HTTP. Počet operací s stavové kódy 2XX jako úspěšně dokončený, že operace s stavové kódy oblastí 3XX, 4XX a 5XX se počítají jako neúspěšná a nižší **PercentSucess** hodnota metriky. V souborech protokolů úložiště na straně serveru, se zaznamenávají tyto operace se stavem transakce **ClientOtherErrors**.

Je důležité si uvědomit, že tyto operace byly úspěšně dokončeny a proto nemají vliv na jiné metriky, jako je dostupnost. Mezi operace, která proběhl úspěšně, ale, který může mít za následek neúspěšné stavové kódy HTTP patří:

* **ResourceNotFound** (nebyl nalezen 404), třeba z požadavek GET na objekt blob, který neexistuje.
* **ResouceAlreadyExists** (409 konflikt), např. ze **CreateIfNotExist** operace, kde už existuje prostředek.
* **ConditionNotMet** (ne upravit 304), třeba z podmíněné operace, například když klient odešle **ETag** hodnotu a HTTP **If-None-Match** záhlaví požádat o bitovou kopii pouze v případě, že má byly aktualizovány od poslední operaci.

Můžete najít seznam běžné kódy chyb rozhraní REST API, které vracejí služby úložiště na stránce [běžné kódy chyb rozhraní REST API](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště
Pokud se zobrazí i s náhlými, neočekávaným změnám v využití kapacity ve vašem účtu úložiště, můžete prozkoumat důvody nejprve si prohlédnout metriky dostupnosti; například nárůst počtu neúspěšných požadavků může vést ke zvýšení množství úložiště objektů blob, který používáte jako operace vyčištění specifické pro aplikaci, která se může mít měla být uvolňuje místo nemusí fungovat podle očekávání (například odstranění protože vypršela platnost tokenů SAS používá pro uvolnění místa).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Nastane problém pomocí emulátoru úložiště pro vývoj nebo testování
Obvykle použití emulátoru úložiště během vývoje a testování, aby požadavek pro účet úložiště Azure. Běžné problémy, které může dojít, pokud používáte emulátor úložiště patří:

* [Funkce "X" nepracuje v emulátoru úložiště]
* [Chyba "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu" při použití emulátoru úložiště]
* [Spuštění emulátor úložiště vyžaduje oprávnění správce]

#### <a name="feature-X-is-not-working"></a>Funkce "X" nefunguje v emulátoru úložiště
Emulátor úložiště nepodporuje všechny funkce služby Azure storage, jako je například služba souborů. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

Pro tyto funkce, které nepodporuje emulátor úložiště použijte službu Azure storage v cloudu.

#### <a name="error-HTTP-header-not-correct-format"></a>Chyba "hodnotu pro jeden z hlaviček HTTP není ve správném formátu" při použití emulátoru úložiště
Testování aplikace používá klientskou knihovnu pro úložiště s místním úložištěm emulátor a metoda volání jako **CreateIfNotExists** neúspěšné a zobrazí se chybová zpráva "hodnotu pro jeden z hlaviček HTTP není ve správné formát." To znamená, že verze emulátoru úložiště, který používáte nepodporuje verzi klientskou knihovnu pro úložiště, který používáte. Klientská knihovna pro úložiště přidá hlavičku **x-ms-version** na všechny požadavky, umožňuje. Pokud na emulátor úložiště nedokáže rozpoznat hodnotu v **x-ms-version** záhlaví, zamítne žádost.

Knihovna klienta úložiště protokolů můžete zobrazit hodnotu **hlavičky x-ms-version** je odesílání. Můžete také zobrazit hodnotu **hlavičky x-ms-version** Pokud použití aplikace Fiddler k trasování požadavků z klientské aplikace.

Tento scénář obvykle dochází, je-li nainstalovat a používat nejnovější verzi klientské knihovny úložiště bez aktualizace emulátoru úložiště. Instalace nejnovější verze emulátoru úložiště by měl nebo používat cloudové úložiště namísto emulátor pro vývoj a testování.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Spouštění v emulátoru úložiště vyžaduje oprávnění správce
Zobrazí se výzva k zadání přihlašovacích údajů správce při spuštění emulátoru úložiště. K tomu dochází pouze při prvním spuštění se inicializaci emulátoru úložiště. Po inicializaci emulátor úložiště není nutné oprávnění správce spustit znovu.

Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md). Můžete také inicializovat emulátor úložiště v sadě Visual Studio, které budou také vyžadují oprávnění správce.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Narazíte na potíže s instalací sady Azure SDK pro .NET
Při pokusu o instalaci sady SDK, selže, chcete nainstalovat emulátor úložiště na místním počítači. Protokol instalace obsahuje jeden z následujících zpráv:

* CAQuietExec: Chyba: Nelze získat přístup k instanci serveru SQL
* CAQuietExec: Chyba: Nelze vytvořit databázi

Příčinou je problém s existující instalace LocalDB. Emulátor úložiště ve výchozím nastavení používá LocalDB k uchování dat. Pokud simuluje služby Azure storage. Spuštěním následujících příkazů v okně příkazového řádku před pokusem o instalaci sady SDK můžete obnovit instanci LocalDB.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**Odstranit** příkaz odebere žádné staré soubory databáze z předchozí instalace emulátoru úložiště.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Máte jiný problém se službou storage
Pokud předchozí části řešení potíží není problém, který máte potíže se službou úložiště, by měl přijmout následující přístup k diagnostice a řešení vašeho problému.

* Zkontrolujte metriky a zjistěte, jestli se všechny změny z vaší očekávané chování base-line. Z metrik může být schopní určit, zda je problém přechodné nebo trvalé a operace úložiště, které tento problém ovlivňuje.
* Můžete použít metriky informace umožňují prohledávat svá data protokolu na straně serveru pro podrobnější informace o případných chybách, ke kterým dochází. Tyto informace možná zvládnete vyřešit problém.
* Pokud není dostatečná k odstranění tohoto problému úspěšně informace v protokolech na straně serveru, můžete použít protokoly na straně klienta Klientská knihovna pro úložiště pro zkoumání chování klientské aplikace a nástroje, jako je Fiddler, Wireshark a Microsoft Message Analyzer k prozkoumání vaší sítě.

Další informace o použití aplikace Fiddler, najdete v části "[Dodatek 1: Pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]."

Další informace o používání Wireshark najdete v tématu "[Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu]."

Další informace o použití Microsoft Message Analyzer, najdete v části "[dodatek 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

## <a name="appendices"></a>Přílohy
Dodatky popisují několik nástrojů, které může být pro vás užitečné při diagnostice a řešení potíží s Azure Storage (nebo jiné služby). Tyto nástroje, které nejsou součástí služby Azure Storage a některé produkty třetích stran. V důsledku toho nástroje popsané v tyto přílohy nejsou pokryty všechny dohodu o podpoře, které máte uzavřeny s Microsoft Azure nebo Azure Storage, a proto součástí vašeho zkušebního byste měli zkontrolovat dostupné z možnosti licencování a podpory Zprostředkovatelé těchto nástrojů.

### <a name="appendix-1"></a>Příloha 1: Pomocí Fiddleru pro zachycení provozu HTTP a HTTPS
[Fiddler](http://www.telerik.com/fiddler) je užitečný nástroj pro analýzu provozu HTTP a HTTPS mezi klientské aplikace a služby Azure storage, který používáte.

> [!NOTE]
> Fiddler dekódoval přenosy HTTPS; Přečtěte si dokumentaci Fiddleru pečlivě, abyste pochopili, jak to dělá a pochopili důsledky zabezpečení.
> 
> 

Tento dodatek obsahuje stručný návod, jak nakonfigurovat aplikaci Fiddler pro zachycení provozu na místním počítači, kam jste nainstalovali aplikaci Fiddler od služby Azure storage.

Po spuštění Fiddleru začne zachytávání HTTP a HTTPS provozy na místním počítači. Tady jsou některé užitečné příkazy pro řízení Fiddleru:

* Zastavit a spustit zachytávání provozu. V hlavní nabídce, přejít na **souboru** a potom klikněte na tlačítko **zachycování provozu** přepnete zachytávání zapnout a vypnout.
* Uložte data zachycená data. V hlavní nabídce, přejít na **souboru**, klikněte na tlačítko **Uložit**a potom klikněte na tlačítko **všechny relace**: umožňuje uložit provoz v souboru archivu relace. Můžete znovu načíst relaci archivu později pro analýzu, nebo odeslat žádost pro podporu Microsoftu.

Chcete-li omezit objem provozu, který zachycuje Fiddler, můžete použít filtry, které nakonfigurujete v **filtry** kartu. Následující snímek obrazovky ukazuje filtr, který zachycuje pouze provoz odeslaný na **contosoemaildist.table.core.windows.net** koncový bod úložiště:

![][5]

### <a name="appendix-2"></a>Příloha 2: Použití Wireshark pro zachycení síťového provozu
[Wireshark](http://www.wireshark.org/) je analyzátoru síťových protokolů, která umožňuje zobrazit informace o podrobné paketů pro širokou škálu síťových protokolů.

Následující postup ukazuje, jak zachycení paketu podrobné informace pro provoz z místního počítače nainstalovanou Wireshark do služby table service ve vašem účtu úložiště Azure.

1. Wireshark spusťte na svém místním počítači.
2. V **Start** vyberte místní síťové rozhraní nebo rozhraní, které jsou připojené k Internetu.
3. Klikněte na tlačítko **možnosti zachytávání**.
4. Přidat filtr **filtr pro sběr dat** textového pole. Například **hostovat contosoemaildist.table.core.windows.net** nakonfiguruje Wireshark zachytit pouze pakety odesílané do nebo z koncový bod služby table service v **contosoemaildist** účtu úložiště. Podívejte se [úplný seznam filtrů zachycení](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Klikněte na tlačítko **Start**. Wireshark nyní zachytit všechny pakety odesílání do nebo z koncový bod služby table service, jak používat klientskou aplikaci na místním počítači.
6. Po dokončení, v hlavní nabídce **zachycení** a potom **Zastavit**.
7. Uložte zaznamenaná data v souboru Wireshark zachycení, klikněte v hlavní nabídce **souboru** a potom **Uložit**.

WireShark zvýrazní všechny chyby, které existují v **packetlist** okna. Můžete také použít **informace poradce** okno (klikněte na tlačítko **analyzovat**, pak **Expert informace**) Chcete-li zobrazit souhrn chyby a upozornění.

![][7]

Můžete také zobrazit data protokolu TCP, jak aplikační vrstvu, uvidí ho tak, že kliknete pravým tlačítkem na data protokolu TCP a vyberete **postupujte podle TCP Stream**. To je užitečné v případě zaznamenat vaše s výpisem paměti bez filtru zachycení. Další informace najdete v tématu [následující datové proudy TCP](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Další informace o používání Wireshark, najdete v článku [Průvodce uživatele Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Dodatek 3: Použití Microsoft Message Analyzer pro zachycení síťového provozu
Můžete použít Microsoft Message Analyzer pro zachycení provozu HTTP a HTTPS podobným způsobem jako do Fiddleru a podobným způsobem jako do Wireshark zachycení síťového provozu.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurovat webovou relaci trasování pomocí nástroje Microsoft Message Analyzer
Konfigurace sledování relace webové přenosy HTTP i HTTPS pomocí Microsoft Message Analyzer, spusťte aplikaci Microsoft Message Analyzer a pak na **souboru** nabídky, klikněte na tlačítko **zachycení a trasování**. V seznamu dostupných trasování scénáře vyberte **webový proxy server**. Pak v **trasování scénáře konfigurace** panelu **HostnameFilter** textové pole, přidat jména koncových bodů úložiště (můžete vyhledat těmito názvy [webu Azure portal](https://portal.azure.com)). Například, pokud je název vašeho účtu úložiště Azure **contosodata**, měli byste přidat následující **HostnameFilter** textové pole:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Znak mezery odděluje názvy hostitelů.
> 
> 

Až budete připravení zahájit shromažďování dat trasování, klikněte na tlačítko **Start With** tlačítko.

Další informace o Microsoft Message Analyzer **webový proxy server** trasování, naleznete v tématu [zprostředkovatele Microsoft-PEF WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

Předdefinované **webový proxy server** trasování v Microsoft Message Analyzer je založen na Fiddleru; můžete zaznamenávat provoz HTTPS na straně klienta a zobrazit nešifrované zpráv protokolu HTTPS. **Webový proxy server** tím, že nakonfigurujete místní proxy server pro veškerý provoz protokolu HTTP a HTTPS, který poskytuje přístup k nezašifrované zprávy trasování funguje.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostika problémů se sítí pomocí nástroje Microsoft Message Analyzer
Kromě používání Microsoft Message Analyzer **webový proxy server** trasování zachytit podrobnosti přenosy HTTP/HTTPs mezi klientské aplikace a služby úložiště, můžete použít také integrovaného **místní vrstvy odkaz**  trasování k zaznamenání informací síťových paketů. To umožňuje, můžete k zaznamenání dat, které můžete zaznamenat s Wireshark a diagnostikovat podobný problémů se sítí, jako jsou vyřazené pakety.

Následující snímek obrazovky ukazuje příklad **místní vrstvy odkaz** trasování s některými **informační** zprávy v **DiagnosisTypes** sloupce. Kliknutím na ikonu **DiagnosisTypes** sloupci se zobrazuje podrobnosti o zprávě. V tomto příkladu serveru přenášena zpráva #305, protože neobdržela potvrzení z klienta:

![][9]

Když vytvoříte relaci trasování v Microsoft Message Analyzer, můžete určit filtry pro snížení šumu v trasování. Na **zachycení / Trace** stránku, kde definujete trasování, klikněte na **konfigurovat** odkaz **Microsoft-Windows-NDIS-PacketCapture**. Následující snímek obrazovky ukazuje konfiguraci, která filtruje provoz TCP pro IP adresy tři služby úložiště:

![][10]

Další informace o trasování Microsoft zpráva analyzátor místní propojení vrstvy, naleznete v tématu [zprostředkovatele Microsoft PEF NDIS PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Dodatek 4: Zobrazení metrik a protokolování dat pomocí aplikace Excel
Celou řadu nástrojů umožní stáhnout z Azure table storage ve formátu s oddělovači, který umožňuje snadno načíst data do aplikace Excel pro zobrazení a analýza dat metrik úložiště. Data protokolování úložiště z Azure blob storage je již ve formátu s oddělovači, který lze načíst do Excelu. Ale budete muset přidat záhlaví příslušných sloupců, podle informací uvedených v [formát Log Analytics úložiště](http://msdn.microsoft.com/library/azure/hh343259.aspx) a [tabulkovému schématu metrik Storage Analytics](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Pokud chcete importovat data protokolování úložiště do aplikace Excel po stažení z úložiště objektů blob:

* Na **Data** nabídky, klikněte na tlačítko **z textu**.
* Přejděte do souboru protokolu, kterou chcete zobrazit a klikněte na tlačítko **Import**.
* V kroku 1 tohoto **Průvodce importem textu**vyberte **s oddělovači**.

V kroku 1 tohoto **Průvodce importem textu**vyberte **středník** jako pouze oddělovač a zvolte dvojitých uvozovek jako **kvalifikátor Text**. Pak klikněte na tlačítko **Dokončit** a zvolit umístění, kam umístit data v sešitu.

### <a name="appendix-5"></a>Dodatek 5: Monitorování pomocí Application Insights pro Visual Studio Team Services
Můžete také použít funkci Application Insights pro Visual Studio Team Services jako součást vašeho sledování výkonu a dostupnosti. Tento nástroj můžete:

* Ujistěte se, že webová služba je k dispozici a reagují. Jestli je vaše aplikace na web nebo aplikace pro zařízení, která používá webovou službu, ho otestujte adresu URL každých několik minut, než z míst po celém světě a vám oznámíme, pokud je nějaký problém.
* Rychle Diagnostikujte jakékoli problémy s výkonem nebo výjimky ve webové službě. Zjistěte, pokud využití procesoru nebo jiné prostředky jsou roztažení, získat trasování zásobníku z výjimek a snadno prohledávat protokolu trasování. Pokud výkon vaší aplikace klesne pod přijatelné meze, Microsoft může odeslat e-mailu. Můžete monitorovat webové služby .NET a Javy.

Další informace najdete [co je Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Úvod]: #introduction
[Uspořádání příručky]: #how-this-guide-is-organized

[Monitorování vaší služby úložiště]: #monitoring-your-storage-service
[Monitorování stavu služby]: #monitoring-service-health
[Monitorování kapacity]: #monitoring-capacity
[Monitorování dostupnosti]: #monitoring-availability
[Sledování výkonu]: #monitoring-performance

[Diagnostika problémů s úložištěm]: #diagnosing-storage-issues
[Problémy se stavem služby]: #service-health-issues
[Problémy s výkonem]: #performance-issues
[Diagnostika chyb]: #diagnosing-errors
[Potíže s úložištěm emulátoru]: #storage-emulator-issues
[Nástroje protokolování úložiště]: #storage-logging-tools
[Pomocí nástroje protokolování sítě]: #using-network-logging-tools

[Trasování začátku do konce]: #end-to-end-tracing
[Korelaci dat protokolu]: #correlating-log-data
[ID žádosti klienta]: #client-request-id
[ID žádosti serveru]: #server-request-id
[Časová razítka]: #timestamps

[Pokyny při řešení potíží]: #troubleshooting-guidance
[Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje vysokou hodnotu AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrika ukazuje zvýšení u PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Přechodné zvýšení u PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Trvalé zvýšení PercentThrottlingError chybu]: #permanent-increase-in-PercentThrottlingError
[Metrika ukazuje zvýšení u PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrika ukazuje zvýšení u PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient dostává zprávy HTTP 403 (zakázáno)]: #the-client-is-receiving-403-messages
[Klient dostává zprávy HTTP 404 (Nenalezeno)]: #the-client-is-receiving-404-messages
[Klient nebo jiným procesem dříve odstraněné objektu]: #client-previously-deleted-the-object
[Chybu ověřování sdíleného přístupového podpisu (SAS)]: #SAS-authorization-issue
[Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu]: #JavaScript-code-does-not-have-permission
[Selhání sítě]: #network-failure
[Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[Metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]: #metrics-show-low-percent-success
[Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]: #capacity-metrics-show-an-unexpected-increase
[Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]: #your-issue-arises-from-using-the-storage-emulator
[Funkce "X" nepracuje v emulátoru úložiště]: #feature-X-is-not-working
[Chyba "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu" při použití emulátoru úložiště]: #error-HTTP-header-not-correct-format
[Spuštění emulátor úložiště vyžaduje oprávnění správce]: #storage-emulator-requires-administrative-privileges
[Narazíte na potíže s instalací sady Azure SDK pro .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Máte jiný problém se službou úložiště]: #you-have-a-different-issue-with-a-storage-service

[Přílohy]: #appendices
[Dodatek 1: Pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]: #appendix-1
[Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu]: #appendix-2
[Dodatek 3: Použití Microsoft Message Analyzer pro zachycení síťového provozu]: #appendix-3
[Dodatek 4: Zobrazení metriky a protokolovat data pomocí aplikace Excel]: #appendix-4
[Dodatek 5: Monitorování pomocí služby Application Insights pro Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
