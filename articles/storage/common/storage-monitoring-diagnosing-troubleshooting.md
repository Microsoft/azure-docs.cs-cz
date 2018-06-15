---
title: Monitorování, Diagnostika a řešení potíží s Azure Storage | Microsoft Docs
description: Použijte funkce, jako jsou analytika úložiště, protokolování klienta a další nástroje třetích stran k identifikaci, diagnostikovat a vyřešit problémy související s Azure Storage.
services: storage
documentationcenter: ''
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: b89071048594e1e11efb321da3d0b48005824b46
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "29740659"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Přehled
Diagnostika a řešení potíží v distribuované aplikace hostované v cloudovém prostředí může být složitější než v tradiční prostředích. V infrastruktuře PaaS nebo IaaS místně na mobilních zařízeních nebo v některých kombinací těchto prostředích, mohou být aplikace nasazeny. Obvykle síťový provoz vaší aplikace můžou procházet veřejné a privátní sítě a vaše aplikace může používat více technologií úložiště, jako je například Microsoft Azure úložiště tabulek, objekty BLOB, fronty nebo ukládá soubory kromě další data, například jako relační a databáze v dokumentu.

Ke správě těchto aplikací úspěšně by je aktivně sledovat a pochopit, jak diagnostikovat a vyřešit všechny aspekty je a jejich závislé technologie. Jako uživatel služby Azure Storage doporučujeme neustále monitorovat služby úložiště, které vaše aplikace používá pro všechny neočekávané změny v chování (například nižší než obvyklé odezvy) a ke shromažďování dat podrobnější a k analýze problému podrobněji, používat protokolování. Diagnostické informace, které můžete získat z monitorování i protokolování vám pomůže určit hlavní příčinu potíže vaší aplikace. Potom můžete při řešení potíží a určit příslušné kroky, které můžete provést při jeho řešení. Úložiště Azure je základní služby Azure a je důležitou součástí většina řešení, které zákazníci nasadit na infrastrukturu Azure. Úložiště Azure poskytuje funkce pro zjednodušení monitorování, Diagnostika a řešení potíží problémů s úložištěm ve vaší cloudové aplikace.

> [!NOTE]
> Soubory Azure protokolování v tuto chvíli nepodporuje.
> 

Praktické informace na začátku do konce odstraňování potíží v aplikacích Azure Storage najdete v tématu [na kompletní řešení problémů pomocí Azure Storage Metrics a protokolování, AzCopy a Message Analyzer](../storage-e2e-troubleshooting.md).

* [Úvod]
  * [Uspořádání Tato příručka]
* [monitorování vaší služby úložiště]
  * [Monitorování stavu služby]
  * [Monitorování kapacity]
  * [Monitorování dostupnosti]
  * [Sledování výkonu]
* [diagnostice problémů s úložištěm]
  * [Problémy v oblasti služby stavu]
  * [problémy s výkonem]
  * [Diagnostikování chyb]
  * [Emulátor problémů s úložištěm]
  * [Nástroje protokolování úložiště]
  * [Pomocí nástroje protokolování]
* [začátku do konce trasování]
  * [Korelace data protokolu]
  * [ID žádosti klienta]
  * [ID žádosti serveru]
  * [Časová razítka]
* [pokyny při řešení potíží]
  * [metriky ukazují AverageE2ELatency vysoké a nízké AverageServerLatency]
  * [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]
  * [Metrika ukazuje vysokou hodnotu AverageServerLatency]
  * [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]
  * [metriky způsobit nárůst PercentThrottlingError]
  * [metriky způsobit nárůst PercentTimeoutError]
  * [Metrika ukazuje zvýšení u PercentNetworkError]
  * [Klient je přijímání zpráv protokolu HTTP 403 (zakázáno)]
  * [Klient je přijímání zpráv HTTP 404 (není nalezena)]
  * [Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]
  * [metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]
  * [Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]
  * [Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]
  * [Narazíte na potíže s instalací sady Azure SDK pro .NET]
  * [Máte jiný problém se službou úložiště]
  * [Řešení potíží virtuální pevné disky ve virtuálních počítačích s Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Řešení potíží virtuální pevné disky na virtuální počítače s Linuxem](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Řešení potíží s Azure soubory s Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Řešení potíží s Azure soubory operačního systému Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [přílohy]
  * [dodatku 1: pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]
  * [příloze 2: zachycení síťového provozu pomocí Wireshark]
  * [příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]
  * [Dodatek 4: Zobrazení metriky a protokolovat data pomocí aplikace Excel]
  * [Dodatek 5: Monitorování pomocí služby Application Insights pro Visual Studio Team Services]

## <a name="introduction"></a>Úvod
Tento průvodce vám ukáže, jak používat funkce, jako jsou například analýzy úložiště Azure, problémům klienta protokolování v Klientská knihovna pro úložiště Azure a další nástroje třetích stran k identifikaci, Diagnostika a řešení potíží s Azure Storage.

![][1]

Tato příručka je určena především číst vývojáři online služeb, které používají služby úložiště Azure a IT Profesionálové zodpovědní za správu těchto online služeb. Cílem této příručky je:

* Chcete-li udržovat stav a výkon svých účtů Azure Storage.
* Abyste si udělali potřebné procesy a nástroje, které vám pomohou rozhodnout, zda problém nebo potíže v aplikaci má vztah k Azure Storage.
* Abyste měli řešitelné pokyny pro řešení problémů souvisejících s úložištěm Azure.

### <a name="how-this-guide-is-organized"></a>Uspořádání Tato příručka
V části "[monitorování vaší služby úložiště]" popisuje, jak sledovat stav a výkon vašich služeb Azure Storage pomocí Azure Storage Analytics Metrics (Storage Metrics).

V části "[diagnostice problémů s úložištěm]" popisuje, jak diagnostikovat problémy s Azure Storage Analytics protokolování (úložiště protokolování). Také popisuje, jak povolit protokolování na straně klienta pomocí možností v jednom z knihovny klienta například Klientská knihovna pro úložiště pro .NET a sady Azure SDK pro jazyk Java.

V části "[začátku do konce trasování]" popisuje, jak můžete porovnat informace obsažené v různých metriky, dat a souborů protokolu.

V části "[pokyny při řešení potíží]" poskytuje pokyny při řešení potíží pro některé běžné související s úložištěm problémů setkat.

"[přílohy]" obsahují informace o použití jiných nástrojů, například Wireshark nebo Netmon pro analýzu síťových paketů data, Fiddler pro analýzu zprávy HTTP/HTTPS, a Microsoft Message Analyzer pro korelace protokolovat data.

## <a name="monitoring-your-storage-service"></a>Monitorování služby úložiště
Pokud jste obeznámeni s sledování výkonu systému Windows, si můžete představit metriky úložiště jako ekvivalentní čítačů sledování výkonu systému Windows Azure Storage. V metriky úložiště zjistíte komplexní sadu metriky (čítače v terminologii sledování výkonu systému Windows), například dostupnost služby, celkový počet žádostí o služby nebo procento úspěšné žádosti o služby. Úplný seznam dostupné metriky, najdete v části [schématu tabulky metriky Analytics úložiště](http://msdn.microsoft.com/library/azure/hh343264.aspx). Můžete zadat, zda chcete službu úložiště k shromažďování a agregace metriky každou hodinu nebo každou minutu. Další informace o tom, jak povolit metriky a monitorování účtů úložiště najdete v tématu [zapnutí metrik úložiště a prohlížení dat metrik](http://go.microsoft.com/fwlink/?LinkId=510865).

Můžete zvolit, které hodinové metriky, které chcete zobrazit v [portál Azure](https://portal.azure.com) a nakonfigurovat pravidla, která upozorní správce e-mailem, kdykoli po hodinách metrika překračuje prahovou hodnotu konkrétní. Další informace najdete v tématu [dostávat oznámení o výstrahách](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). 

Služba úložiště shromažďuje metriky pomocí nejlepší úsilí, ale nemusí záznam každé operace úložiště.

Metriky, například dostupnost, celkový počet požadavků a čísla Průměrná latence pro účet úložiště lze zobrazit na portálu Azure. Pravidla oznámení je rovněž nastaven upozorní správce, pokud dostupnosti klesne pod určitou úroveň. Zobrazit tato data, je jednou z možných oblastí pro zkoumání procento úspěšnosti služby tabulky je nižší, než 100 % (Další informace najdete v části "[metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]").

Měli byste sledovat nepřetržitě aplikací Azure k zajištění, že jsou v pořádku a provádění podle očekávání tím:

* Zřízení některé základní metriky pro aplikace, která vám umožní porovnat aktuální data a určete veškeré významné změny v chování úložiště Azure a aplikace. V mnoha případech se tyto hodnoty vaše základní metriky konkrétní aplikace a je měli vytvořit, když jsou výkonu testování vaší aplikace.
* Záznam minutu metriky a jejich používání za účelem sledování aktivně neočekávaným chybám a anomálií, jako je například špičky v chybě počty nebo požadavku sazby.
* Záznam hodinové metriky a jejich používání za účelem monitorování průměrné hodnoty jako například průměrný počet chyb a požadovat sazby.
* Na odstranění příčin možných problémů pomocí diagnostiky nástrojů, jak je popsáno dále v části "[diagnostice problémů s úložištěm]."

Grafy na následujícím obrázku znázorňují, jak průměrování, ke kterému dochází po hodinách metrik, které můžete skrýt špičky v aktivitě. Hodinové metrika se zobrazí zobrazíte konstantní počet požadavků, při minutu metriky odhalit fluktuace, které skutečně dala.

![][3]

Zbytek této části popisuje, jaké metriky, měli byste sledovat a proč.

### <a name="monitoring-service-health"></a>Monitorování stavu služby
Můžete použít [portál Azure](https://portal.azure.com) pro zobrazení stavu služby Storage (a jinými službami Azure) ve všech oblastech Azure po celém světě. Monitorování umožňuje můžete okamžitě zjistěte, zda problém mimo váš dosah ovlivňuje služby úložiště v oblasti, které používáte pro vaši aplikaci.

[Portál Azure](https://portal.azure.com) můžete zadat taky oznámení incidentů, které ovlivňují různé služby Azure.
Poznámka: Tyto informace byl dříve k dispozici, spolu s historických dat, na [řídicího panelu služby Azure](http://status.azure.com).

Když [portál Azure](https://portal.azure.com) shromažďuje informace o stavu z v datových centrech Azure (zevnitř monitorování), zvažte také přijetí přístup mimo in k vygenerování syntetické transakce, které pravidelně Azure hostovaná webovou aplikaci přístup z více umístění. Služeb, které [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) a Application Insights pro Visual Studio Team Services jsou příklady tohoto přístupu. Další informace o Application Insights pro Visual Studio Team Services, najdete v příloze "[příloha 5: monitorování pomocí Application Insights pro Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Monitorování kapacity
Metriky úložiště ukládá metriky kapacity pro službu blob jenom, protože objekty BLOB obvykle účet pro největší podíl uložená data (v době psaní, není možné používat úložiště metriky k monitorování kapacity tabulek a front). Tato data v můžete najít **$MetricsCapacityBlob** tabulky, pokud jste povolili monitorování pro služby objektů Blob. Metriky úložiště zaznamenává tato data jednou za den, a můžete použít hodnotu **RowKey** k určení, zda řádek obsahuje entity, která má vztah k uživatelským datům (hodnota **data**) nebo analytická data (hodnota **analytics**). Každá entita uložené obsahuje informace o velikosti použitého úložiště (**kapacity** měřená v bajtech) a aktuální počet kontejnerů (**ContainerCount**) a objekty BLOB (**ObjectCount**) používá v účtu úložiště. Další informace o metriky kapacity, které jsou uložené v **$MetricsCapacityBlob** tabulky najdete v tématu [schématu tabulky metriky Analytics úložiště](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Měli byste sledovat tyto hodnoty včasného varování, že se blíží limity kapacity účtu úložiště. Na portálu Azure můžete přidat pravidla výstrah pro upozornění, pokud využití agregační úložiště překračuje nebo nedosahuje prahové hodnoty, které zadáte.
> 
> 

Nápovědu k odhad velikosti různých objektů úložiště, jako je například objekty BLOB, naleznete v příspěvku blogu [Principy Azure úložiště fakturace – šířku pásma, transakce a kapacity](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorování dostupnosti
Je třeba sledovat dostupnost služby úložiště ve vašem účtu úložiště podle hodnota v monitorování **dostupnosti** sloupce v tabulkách hodinových nebo minutu metriky – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. **Dostupnosti** sloupec obsahuje procentuální hodnotu, která označuje dostupnost služby nebo se operace rozhraní API reprezentována řádek ( **RowKey** zobrazí, pokud řádek obsahuje metriky pro služby jako celek, nebo pro určité operace rozhraní API).

Všechny hodnoty a menší než 100 % označuje, že dochází k selhání některých požadavků úložiště. Zobrazí se, proč se nedaří prověřením ostatních sloupců v datech metriky, které zobrazují počet požadavků s typy jiné chyby, jako **ServerTimeoutError**. Měli byste měli vidět **dostupnosti** patří dočasně nižší než 100 % z důvodů, jako je například časové limity přechodný serveru při službu přesune oddíly na žádost o lepší vyrovnávání zatížení; logika opakovaných pokusů klientské aplikace by měla řídit tyto přerušované podmínky. Článek [stavové zprávy a Storage Analytics protokolované](http://msdn.microsoft.com/library/azure/hh343260.aspx) uvádí typy transakcí, které obsahuje úložiště metriky v jeho **dostupnosti** výpočtu.

V [portál Azure](https://portal.azure.com), můžete přidat pravidla výstrah pro upozornění, pokud **dostupnosti** pro službu klesne pod prahovou hodnotou, který určíte.

"[pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy služby úložiště souvisejících s dostupností.

### <a name="monitoring-performance"></a>Sledování výkonu
Sledování výkonu služby úložiště, můžete použít následující metriky z tabulky hodinové a minutu metriky.

* Hodnoty **AverageE2ELatency** a **AverageServerLatency** sloupcích se zobrazují průměrnou dobu služby úložiště nebo typ operace rozhraní API trvá pro zpracování požadavků. **AverageE2ELatency** se rozumí míra začátku do konce latence, která zahrnuje čas potřebný k čtení požadavku a odpovědi kromě čas potřebný pro zpracování požadavku pro posílání (proto zahrnuje latence sítě Jakmile požadavek dosáhne úložiště Služba); **AverageServerLatency** se rozumí míra právě doba zpracování a proto nezahrnuje žádné latence sítě související s komunikaci s klientem. Najdete v části "[metriky ukazují AverageE2ELatency vysoké a nízké AverageServerLatency]" dál v této příručce diskuzi o důvod, proč může být velký rozdíl mezi tyto dvě hodnoty.
* Hodnoty **TotalIngress** a **TotalEgress** sloupcích se zobrazují celkové množství dat, v bajtech přicházejících na a přejdete mimo vaší služby úložiště nebo konkrétní typ operace rozhraní API.
* Hodnoty **TotalRequests** sloupec zobrazit celkový počet požadavků, které přijímá službu úložiště operace rozhraní API. **TotalRequests** je celkový počet požadavků, které obdrží služby úložiště.

Budete obvykle monitorovat neočekávané změny v některém z těchto hodnot jako indikátoru, abyste měli problém, který vyžaduje šetření.

V [portál Azure](https://portal.azure.com), můžete přidat pravidla výstrah pro upozornění, pokud žádné z metriku výkonu pro tuto službu klesnou pod nebo překročit prahovou hodnotu, která zadáte.

"[pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy služby úložiště souvisejících s výkonem.

## <a name="diagnosing-storage-issues"></a>Diagnostika problémů s úložištěm
Existuje několik způsobů, že jste může se dozvěděli o problém nebo problém v aplikaci, včetně:

* Hlavní selhání, které způsobí, že aplikace došlo k chybě, nebo přestane fungovat.
* Významné změny z hodnoty standardních hodnot v metriky, které monitorujete, jak je popsáno v předchozí části "[monitorování vaší služby úložiště]."
* Sestavy z uživatelů vaší aplikace, která nějaké konkrétní operace nebyla dokončena, podle očekávání nebo že některé funkce nefunguje.
* Chyby generované v rámci vaší aplikace, který zobrazí v souborech protokolů nebo prostřednictvím jiné metody oznámení.

Problémy související s služby Azure storage obvykle spadají do jedné ze čtyř kategorií:

* Aplikace má potíže s výkonu, buď hlášené vaši uživatelé nebo zjištěná změny v metrik výkonu.
* Došlo k potížím s infrastrukturou Azure Storage v jedné nebo více oblastech.
* Aplikace je zjištění stavu chyby, a to buď hlášené vaši uživatelé nebo zjištěná o zvýšení v jednom z metriky počet chyb, které sledujete.
* Během vývoj a testování může pomocí emulátoru místního úložiště; Některé problémy, které se týkají speciálně použití emulátoru úložiště se může vyskytnout.

Následující oddíly popisují postupujte podle kroků pro diagnostiku a řešení problémů v každé z těchto čtyř kategorií. V části "[pokyny při řešení potíží]" dál v této příručce poskytující víc podrobností pro některé běžné problémy, se můžete setkat.

### <a name="service-health-issues"></a>Problémy v oblasti služby stavu
Problémům se stavem služby jsou obvykle mimo vlastního ovládacího prvku. [Portál Azure](https://portal.azure.com) poskytuje informace o probíhající problémy se službami Azure včetně služby úložiště. Pokud jste se rozhodli pro geograficky redundantní úložiště s přístupem pro čtení při vytvoření účtu úložiště, pak data přestane být dostupný v primárním umístění, vaše aplikace můžete přepnout dočasně kopie jen pro čtení v sekundárním umístění. Číst ze sekundární, musí aplikace přepínat mezi použitím umístění primárního a sekundárního úložiště a mohli pracovat v režimu omezené funkčnosti daty jen pro čtení. Knihovny klienta úložiště Azure umožňují definovat zásady opakovaných pokusů, který může číst ze sekundární úložiště v případě, že pro čtení z primárního úložiště se nezdaří. Aplikace také musí být data v sekundárním umístění je, že nakonec byl konzistentní. Další informace naleznete v příspěvku blogu [možnosti redundance úložiště Azure a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>problémy s výkonem
Výkon aplikace může být subjektivní, zejména z pohledu uživatele. Proto je důležité mít k dispozici základní metriku, která vám pomůže identifikovat možné problémy s výkonem. Mnoha faktorech může ovlivnit výkon služby Azure storage z pohledu klienta aplikace. Tyto faktory mohou fungovat ve službě úložiště, v klientovi nebo v síťové infrastruktuře; Proto je důležité mít strategie pro identifikaci původ problémy s výkonem.

Jakmile zjistíte příčinu problémy s výkonem z metriky pravděpodobně umístění, pak můžete soubory protokolu naleznete podrobné informace pro diagnostiku a řešení problému další.

V části "[pokyny při řešení potíží]" dál v této příručce poskytuje další informace o některé běžné související s výkonem problémy se můžete setkat.

### <a name="diagnosing-errors"></a>Diagnostikování chyb
Uživatelům vaší aplikace může upozorňovat na chyby oznámené službou klientské aplikace. Úložiště metriky taky zaznamenává počty typů různé chyby z vaší služby úložiště, jako **NetworkError**, **ClientTimeoutError**, nebo **AuthorizationError**. Zatímco úložiště metriky pouze zaznamenává počty typů různých chyb, můžete získat více podrobností o jednotlivých požadavků prověřením na straně serveru, klienta a protokoly sítě. Stavový kód protokolu HTTP, který je vrácena službou úložiště obvykle získáte údaj o tom, proč se nezdařil požadavek.

> [!NOTE]
> Mějte na paměti, že byste měli očekávat zobrazit některé občasné chyby: například chyb z důvodu přechodného síťové podmínky, nebo chyby aplikace.
> 
> 

V následujících zdrojích informací jsou užitečné pro pochopení související s úložištěm stavu a chybové kódy:

* [Běžné kódy chyb rozhraní API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Kódy chyb služby objektů BLOB](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Kódy chyb služby fronty](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kódy chyb služby Table](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kódy chyb služby souboru](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Emulátor problémů s úložištěm
Azure SDK zahrnuje emulátor úložiště, můžete spouštět na pracovní stanici. Tato emulátoru simuluje většinu chování služby Azure storage a je užitečné při vývoj a testování, umožňuje spouštět aplikace, které používají služby Azure storage bez nutnosti předplatné Azure a účet úložiště Azure.

"[pokyny při řešení potíží]" část tato příručka popisuje některé běžné problémy došlo pomocí emulátoru úložiště.

### <a name="storage-logging-tools"></a>Nástroje protokolování úložiště
Protokolování úložiště poskytuje serverové protokolování požadavků na úložiště v účtu úložiště Azure. Další informace o tom, jak povolit protokolování na straně serveru a získat přístup k datům protokolu najdete v tématu [povolení protokolování úložiště a přístup k datům protokolu](http://go.microsoft.com/fwlink/?LinkId=510867).

Klientská knihovna pro úložiště pro .NET umožňuje shromažďovat data protokolu na straně klienta, která má vztah k operace úložiště, provádí aplikace. Další informace najdete v článku [Protokolování na straně klienta s klientskou knihovnou pro úložiště .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> V některých případech (například selháním autorizace SAS) může uživatel sestavy k chybě, pro které můžete najít data žádosti nejsou v protokolech úložiště na straně serveru. Můžete použít možnosti protokolování Klientská knihovna pro úložiště zjistit, zda je příčinou problém na straně klienta nebo používat nástroje monitorování sítě k prošetření sítě.
> 
> 

### <a name="using-network-logging-tools"></a>Pomocí nástroje protokolování
Můžete zaznamenávat provoz mezi klientem a serverem, který obsahuje podrobné informace o datech, které jsou výměna klient a server a základní síťové podmínky. Nástroje protokolování užitečné sítě, patří:

* [Fiddler](http://www.telerik.com/fiddler) je bezplatných webových ladění proxy server, který umožňuje zkontrolovat hlavičky a datové části zpráv žádostí a odpovědí HTTP a HTTPS. Další informace najdete v tématu [dodatku 1: použití Fiddler k zachycení přenosů dat HTTP a HTTPS](#appendix-1).
* [Sledování sítě (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) a [Wireshark](http://www.wireshark.org/) jsou analyzátorů protokolu, které vám umožní zobrazit informace o podrobné paketů pro širokou škálu protokolů síťové volné sítí. Další informace o Wireshark najdete v tématu "[příloze 2: pomocí Wireshark pro zachycení síťového provozu](#appendix-2)".
* Microsoft Message Analyzer je nástroj od společnosti Microsoft, který nahrazuje sledování sítě a že kromě zachytávání paketů dat v síti, pomůže vám umožní zobrazit a analyzovat data protokolu zaznamenat z dalších nástrojů. Další informace najdete v tématu "[příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu](#appendix-3)".
* Pokud chcete provést test základní připojení zkontrolujte, že klientský počítač může připojit ke službě úložiště Azure přes síť, nelze to provést pomocí standardní **ping** nástroj na straně klienta. Můžete však použít [ **tcping** nástroj](http://www.elifulkerson.com/projects/tcping.php) zkontrolujte připojení.

V mnoha případech bude dostatečná k diagnostice problému dat protokolu z úložiště protokolování a Klientská knihovna pro úložiště, ale v některých případech může potřebovat podrobnější informace, které může poskytnout tyto nástroje protokolování. Například pomocí Fiddleru zobrazení zpráv protokolu HTTP a HTTPS umožňuje zobrazit záhlaví a datovou část data odesílaná do a ze služby storage, které by vám umožní zkontrolovat, jak klientská aplikace opakování operace úložiště. Protokol analyzátorů například Wireshark fungovat na úrovni paketů umožňuje zobrazit TCP data, která vám umožní řešení ke ztrátě paketů a problémy s připojením. Message Analyzer může fungovat na protokolu HTTP a TCP vrstvy.

## <a name="end-to-end-tracing"></a>Konec Konec trasování
Trasování začátku do konce pomocí různých souborů protokolu je užitečné pro na odstranění příčin možných problémů. Datum a čas informace z vašich dat metriky slouží jako ukazatel kde začít hledat v souborech protokolu podrobné informace, které vám pomohou vyřešit problém.

### <a name="correlating-log-data"></a>Korelace data protokolu
Při prohlížení protokolů z klientských aplikací, trasování sítě a úložiště serverové protokolování, které je velmi důležité, abyste mohli ke korelaci požadavků v různých protokolových souborech. Soubory protokolu obsahují celou řadu různých polí, které lze použít jako identifikátory korelace. ID žádosti klienta je velmi užitečné pole, které chcete použít ke korelaci položek v jiné protokoly. Může se stát může být užitečné používat ID žádosti serveru nebo časová razítka. Další informace o těchto možnostech naleznete v následujících částech.

### <a name="client-request-id"></a>ID žádosti klienta
Klientská knihovna pro úložiště automaticky vygeneruje ID žádosti klienta jedinečný pro každý požadavek.

* V protokol klienta, který vytvoří Klientská knihovna pro úložiště, se zobrazí ID žádosti klienta v **ID žádosti klienta** pole každé položky protokolu týkající se žádosti.
* Trasování sítě, například ten, který zachycenou aplikaci Fiddler, se zobrazí na zprávy žádosti jako ID žádosti klienta **x-ms-client-request-id** hodnotu hlavičky protokolu HTTP.
* ID žádosti klienta se v protokolu protokolování úložiště serverové zobrazí ve sloupci ID žádosti klienta.

> [!NOTE]
> Je možné, protože klient se může přiřadit tuto hodnotu (i když klientská knihovna pro úložiště automaticky přiřadí novou hodnotu) sdílejí stejné ID žádosti klienta více žádostí. Pokud klient pokus obnovuje, všechny pokusy sdílejí stejné ID žádosti klienta. V případě dávky odeslaných z klienta má dávky ID. požadavek jednoho klienta
> 
> 

### <a name="server-request-id"></a>ID žádosti serveru
Služba úložiště automaticky vygeneruje ID žádosti serveru.

* V protokolu protokolování úložiště serverové ID žádosti serveru se zobrazí **záhlaví ID žádosti** sloupce.
* Trasování sítě, například ten, který zachycenou aplikaci Fiddler, ID žádosti serveru se zobrazí v odpovědi na zprávy jako **x-ms-request-id** hodnotu hlavičky protokolu HTTP.
* V protokol klienta, který vytvoří Klientská knihovna pro úložiště, ID žádosti serveru se zobrazí v **operaci Text** sloupec pro záznam protokolu s podrobnostmi o odpověď serveru.

> [!NOTE]
> Služby úložiště vždy přiřadí jedinečné serveru ID žádosti každého požadavku, které obdrží, takže každý pokus opakovat z klienta a každé operace zahrnuté v dávce má jedinečný server žádost ID.
> 
> 

Pokud vyvolá Klientská knihovna pro úložiště **StorageException** v klientovi **RequestInformation** vlastnost obsahuje **RequestResult** objekt, který zahrnuje **ServiceRequestID** vlastnost. Můžete taky Přejít **RequestResult** objektu z **informacím OperationContext** instance.

Následující příklad ukazuje, jak nastavit vlastní **ClientRequestId** hodnotu připojením **informacím OperationContext** objektu žádost o služby úložiště. Také ukazuje, jak načíst **ServerRequestId** hodnotu ze zprávy odpovědi.

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
Časová razítka můžete použít také k vyhledání položky související protokolu, ale buďte opatrní z jakékoli zkosení hodin mezi klientem a serverem, který může existovat. Prohledávání plus nebo minus 15 minut pro párování serverové položky podle časového razítka na straně klienta. Mějte na paměti, že metadata objektu blob pro objekty BLOB obsahující metriky Určuje časové rozmezí metrik, které jsou uložené v objektu blob. Tento časový rozsah je užitečné, pokud máte mnoho objektů BLOB metriky pro stejné minutu nebo hodinu.

## <a name="troubleshooting-guidance"></a>Pokyny k odstraňování problémů
V této části vám pomůžou s diagnostiku a řešení potíží s některé běžné problémy, vaše aplikace může dojít při používání služby Azure storage. Pomocí níže uvedeného seznamu vyhledejte informace, které jsou relevantní pro konkrétní problém.

**Řešení potíží s rozhodovací strom.**

---
Souvisí problém výkonu jednoho ze služby storage?

* [metriky ukazují AverageE2ELatency vysoké a nízké AverageServerLatency]
* [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]
* [Metrika ukazuje vysokou hodnotu AverageServerLatency]
* [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]

---
Souvisí problém dostupnost jednoho ze služby storage?

* [metriky způsobit nárůst PercentThrottlingError]
* [metriky způsobit nárůst PercentTimeoutError]
* [Metrika ukazuje zvýšení u PercentNetworkError]

---
 Klientské aplikace přijímá odpovědi HTTP 4XX (například 404) ze služby úložiště?

* [Klient je přijímání zpráv protokolu HTTP 403 (zakázáno)]
* [Klient je přijímání zpráv HTTP 404 (není nalezena)]
* [Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]

---
[metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]

---
[Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]

---
[Dochází neočekávané restartování virtuálních počítačů, které mají velký počet virtuálních pevných disků připojených]

---
[Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]

---
[Narazíte na potíže s instalací sady Azure SDK pro .NET]

---
[Máte jiný problém se službou úložiště]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriky ukazují AverageE2ELatency vysoké a nízké AverageServerLatency
Na obrázku níže z [portál Azure](https://portal.azure.com) monitorování nástroj ukazuje příklad kde **AverageE2ELatency** výrazně vyšší, než **AverageServerLatency**.

![][4]

Služba úložiště pouze vypočítá metriku **AverageE2ELatency** pro úspěšné požadavky a na rozdíl od **AverageServerLatency**, zahrnuje čas klienta trvá odesílat data a přijímat potvrzení ze služby storage. Proto rozdíl mezi **AverageE2ELatency** a **AverageServerLatency** může být buď z důvodu klientská aplikace je pomalá reagovat, nebo kvůli podmínek v síti.

> [!NOTE]
> Můžete také zobrazit **E2ELatency** a **ServerLatency** pro jednotlivé úložiště operace v protokolování úložiště protokolovat data.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Prozkoumat problémy s výkonem klienta
Možné důvody pro klienta reagovat pomalu zahrnují mají omezený počet připojení k dispozici nebo vláken, nebo se nedostatek prostředků, jako je například CPU, paměť či síťové šířky pásma. Bude pravděpodobně možné vyřešit problém úpravou kódu klienta efektivnější (například pomocí asynchronní volání služby storage) nebo pomocí větší virtuální počítač (s více jader a další paměť).

Pro služby tabulky a fronty algoritmus Nagle taky může způsobit vysokou **AverageE2ELatency** ve srovnání s **AverageServerLatency**: Další informace najdete v příspěvku [na Nagle Algoritmus není popisný směrem malé požadavky](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Algoritmus Nagle v kódu můžete zakázat pomocí **ServicePointManager –** třídy v **System.Net** oboru názvů. Bude třeba provést před všechny volání do tabulky nebo fronty služby ve vaší aplikaci, protože to nemá vliv na připojení, které jsou již otevřít. Následující příklad pochází z **Application_Start** metoda v roli pracovního procesu.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Klientské protokoly chcete zobrazit, kolik požadavky, že je odesílání klientskou aplikaci a zkontrolujte Obecné .NET související kritické body v vašeho klienta, například CPU, uvolňování paměti .NET, využití sítě, paměť, byste měli zkontrolovat. Jako výchozí bod pro řešení potíží s .NET klientské aplikace, najdete v části [ladění, trasování a profilování](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Prozkoumat problémy s latencí sítě
Obvykle vysokou latencí začátku do konce způsobena sítí je z důvodu přechodné podmínky. Obě problémy se síťovým přechodný a trvalé například vynechaných paketů můžete prozkoumat pomocí nástrojů, jako je například Wireshark nebo Microsoft Message Analyzer.

Další informace o používání Wireshark k řešení problémů se sítí, najdete v části "[příloze 2: zachycení síťového provozu pomocí Wireshark]."

Další informace o používání Microsoft Message Analyzer k řešení problémů se sítí, najdete v části "[příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriky ukazují nízkou AverageE2ELatency a nízkou AverageServerLatency ale dochází k vysoké latenci klienta
V tomto scénáři nejpravděpodobnější příčinou je zpoždění v žádostech úložiště dosažení služby úložiště. Proč požadavky od klienta nejsou prováděním přes služby objektů blob, které byste měli prozkoumat.

Jedním z možných důvodů pro klienta, čímž dochází ke zpoždění odesílání požadavků je, že jsou omezený počet připojení k dispozici nebo vláken.

Také zkontrolujte, zda je klient provádění více pokusů a zjistěte příčinu, pokud se jedná. Chcete-li určit, zda klient pracuje více opakování, můžete:

* Zkontrolujte protokoly analytika úložiště. Pokud první pokus se děje, zobrazí se více operací se stejným ID žádosti klienta, ale s požadavkem na jiný server ID.
* Zkontrolujte protokoly klienta. Podrobné protokolování bude znamenat, že došlo k chybě a k opakování.
* Ladění kódu a zkontrolujte vlastnosti **informacím OperationContext** objekt přidružený k požadavku. Pokud operace se pokusila akci opakovat, **RequestResults** vlastnost bude obsahovat více požadavek serveru jedinečné identifikátory. Můžete také zkontrolovat počáteční a koncový čas pro každý požadavek. Další informace najdete v tématu ukázka kódu v části [ID žádosti serveru].

Pokud zde nejsou žádné problémy v klientovi, které byste měli prozkoumat potenciální problémy se síťovým například ztráta paketů. Nástroje, například Wireshark nebo Microsoft Message Analyzer můžete použít k prozkoumání problémů se sítí.

Další informace o používání Wireshark k řešení problémů se sítí, najdete v části "[příloze 2: zachycení síťového provozu pomocí Wireshark]."

Další informace o používání Microsoft Message Analyzer k řešení problémů se sítí, najdete v části "[příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metriky ukazují vysoké AverageServerLatency
V případě vysoce **AverageServerLatency** žádostí o stažení objektů blob, měli byste použít protokol úložiště protokolování Pokud jsou k dispozici opakované žádosti pro stejný objekt blob (nebo sadu objektů BLOB). Pro žádostí o nahrání objektu blob které byste měli prozkoumat co bloku je velikost klienta pomocí (například bloků, které bloky dat je menší než 64 tisíc velikost může vést režie pouze v případě čtení jsou i v méně než 64 kB), a pokud více klientů nahráváte bloky na stejný objekt blob v odstavec paralelní. Také byste měli zkontrolovat za minutu metriky špiček v počet požadavků, jejichž výsledkem je vyšší než na druhý cíle škálovatelnosti: také najdete v části "[metriky způsobit nárůst PercentTimeoutError]."

Pokud vidíte základní **AverageServerLatency** ke stažení objektů blob požadavky, když jsou opakována požádá o stejný objekt blob nebo sadu objektů BLOB, pak byste měli zvážit ukládání do mezipaměti tyto objekty BLOB pomocí Azure Cache nebo Azure Content Delivery Network (CDN). Pro nahrávání požadavky můžete zlepšit propustnost pomocí větší velikost bloku. Pro dotazy na tabulky je také možné implementovat ukládání do mezipaměti na straně klienta na klientech, provádět stejné operace dotazů a kde data nemění příliš často.

Vysoká **AverageServerLatency** hodnoty mohou být také příznakem nesprávně navržen tabulky nebo dotazy, výsledek v operacích kontroly nebo který postupujte připojení nebo předřazení proti vzor. Další informace najdete v tématu "[metriky způsobit nárůst PercentThrottlingError]".

> [!NOTE]
> Můžete najít komplexní kontrolní seznam výkonu kontrolní seznam zde: [Microsoft Azure Storage výkon a škálovatelnost kontrolní seznam](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Dochází k neočekávané zpoždění při doručování zpráv ve frontě
Pokud dochází ke zpoždění mezi aplikace přidá zprávu do fronty čas a čas, kdy je k dispozici ke čtení z fronty, potom můžete provést následující kroky k diagnostice problému:

* Ověřte, že aplikace je úspěšně přidání zprávy do fronty. Zkontrolujte, zda není aplikace opakování **AddMessage** metody několikrát před úspěšné. Protokoly Klientská knihovna pro úložiště se zobrazí všechny opakovaných pokusech operace úložiště.
* Ověřte, neexistuje žádný hodiny zkosení mezi role pracovního procesu, který přidá zprávu do fronty a role pracovního procesu, který čte zprávy z fronty, který umožňuje zobrazit, jako když dochází ke zpoždění při zpracování.
* Zkontrolujte, pokud se nedaří role pracovního procesu, který čte zprávy z fronty. Fronty klienta volá-li **GetMessage** metoda, ale přestane reagovat s potvrzení, zpráva zůstane neviditelná ve frontě, dokud **invisibilityTimeout** období vyprší platnost. V tomto okamžiku zpráva bude k dispozici pro zpracování znovu.
* Zkontrolujte, pokud se časem zvětšuje délka fronty. Tato situace může nastat, pokud nemáte dostatečnou pracovníci k dispozici pro zpracování všechny zprávy, které ostatní zaměstnanci jsou umístění na fronty. Také zkontrolujte podle metrik, které chcete zobrazit, pokud odstranění dochází k selhání požadavků a dequeue počet u zpráv, jež mohou indikovat opakovaných neúspěšných pokusech o odstranění zprávy.
* Zkontrolujte protokoly protokolování úložiště pro všechny operace fronty, které mají vyšší, než se očekávalo **E2ELatency** a **ServerLatency** hodnoty po dobu delší dobu než obvykle.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metriky způsobit nárůst PercentThrottlingError
Dochází k omezení chybám, když překročíte cíle škálovatelnosti služby úložiště. Omezení služby úložiště Ujistěte se, že žádná jednoho klienta nebo klienta pomocí služby za cenu ostatní. Další informace najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md) podrobné informace o cíle škálovatelnosti pro účty úložiště a cíle výkonnosti pro oddíly v rámci účty úložiště.

Pokud **PercentThrottlingError** metrika způsobit nárůst procento žádostí, které se selháním kvůli chybě omezení, budete muset prozkoumat jeden z těchto dvou scénářů:

* [Přechodný zvýšení PercentThrottlingError]
* [Trvalé zvýšení PercentThrottlingError chyba]

Zvýšení **PercentThrottlingError** často dochází ve stejnou dobu jako zvýšení počtu požadavků na úložiště nebo pokud jste původně zátěžové testování vaší aplikace. To může také projevit v klientovi jako "503 Server zaneprázdněn" nebo "500 časový limit operace" HTTP stavové zprávy z operace úložiště.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Přechodný zvýšení PercentThrottlingError
Pokud vidíte špičky v hodnotě **PercentThrottlingError** , shoduje s období vysoké aktivity pro aplikaci, můžete implementovat exponenciální (ne lineární) back vypnout strategie pro opakování vašeho klienta. Back vypnout opakování snížení okamžitou zatížení oddílu a pomoci aplikace vyhlazení špičky v provozu. Další informace o tom, jak implementovat zásady opakování pomocí klientské knihovny pro úložiště najdete v tématu [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Může se také zobrazit špičky v hodnotě **PercentThrottlingError** který nekolidují s období vysoké aktivity pro aplikaci: nejpravděpodobnější příčinou je služba úložiště přesunutí oddíly zvýšíte Vyrovnávání zatížení.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Trvalé zvýšení PercentThrottlingError chyba
Pokud vidíte konzistentně vysoké hodnoty pro **PercentThrottlingError** následující trvalé nárůstem svazků transakce, nebo při provádění počáteční zatížení testů na vaší aplikace a potřebujete vyhodnotit, jak vaše aplikace používá oddílů pro úložiště a zda se přiblíží cíle škálovatelnosti účtu úložiště. Například pokud vidíte omezení chyby ve frontě (který se počítá jako jeden oddíl), pak měli byste zvážit použití další fronty rozkládá transakce mezi více oddílů. Pokud vidíte omezení chyby v tabulce, je potřeba zvážit použití různých schéma rozdělení oddílů rozkládá vaší transakce mezi více oddílů s využitím širší rozsah hodnot klíče oddílu. Jednou z běžných příčin tohoto problému je prepend/připojovat proti vzor kde vyberte datum jako klíč oddílu a pak se všechna data v určitý den jsou zapsána do jednoho oddílu: zátěži, může být výsledkem kritický bod zápisu. Buď zvažte jiného rozdělení návrhu nebo vyhodnocení, zda pomocí úložiště objektů blob může být lepším řešením. Také zkontrolujte, zda omezení dochází v důsledku špičky v provozu a prozkoumat způsoby vyhlazení vaší vzor požadavků.

Pokud distribuujete vaší transakce napříč více oddílů, musíte stále být informace o limitech škálovatelnosti, nastavte pro účet úložiště. Například pokud jste použili deset fronty každé zpracování maximálně 2 000 1KB zpráv za sekundu, nebudete na celkový limit 20 000 zpráv za sekundu pro účet úložiště. Pokud potřebujete ke zpracování více než 20 000 entity za sekundu, měli byste zvážit použití více účtů úložiště. Vezměte také na paměti, že velikost žádosti a entity má dopad na při službu úložiště omezí generovaný vaši klienti: Pokud máte větší požadavky a entity, které může omezeny dříve.

Návrh neefektivní dotazu může také způsobit dosáhl omezení škálovatelnosti pro tabulku oddílů. Dotaz s filtrem jedno procento entit který vybere jen v oddílu, ale který prohledá všechny entity v oddílu například potřebovat přístup k každé entity. Každou entitu číst se bude započítávat celkový počet transakcí v tomto oddílu. Proto můžete snadno dosáhnout cíle škálovatelnosti.

> [!NOTE]
> Testování výkonu by měl odhalit jakékoli návrhy neefektivní dotazu v aplikaci.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metriky způsobit nárůst PercentTimeoutError
Vaše metriky způsobit nárůst v **PercentTimeoutError** pro jednu z vaší služby úložiště. Ve stejnou dobu obdrží klient k velkému počtu stavové zprávy "500 časový limit operace" HTTP z operace úložiště.

> [!NOTE]
> Může se zobrazit chyby vypršení časového limitu dočasně jako službu úložiště zatížení u žádostí o zůstatky přesunutím oddílu na nový server.
> 
> 

**PercentTimeoutError** metrika je agregaci následující metriky: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, a **SASServerTimeoutError**.

Časový limit serveru jsou způsobeny chybu na serveru. Překročení časového limitu klienta dojít, protože operace na serveru překročil časový limit zadaný klientem; Například můžete nastavit časový limit operace pomocí klienta pomocí klientské knihovny pro úložiště **ServerTimeout** vlastnost **QueueRequestOptions** třídy.

Časový limit serveru znamenat problém související s službu úložiště, který vyžaduje další šetření. Metriky můžete použít, pokud chcete zobrazit, pokud jste nedosáhli limitech škálovatelnosti pro službu a identifikovat všechny špičky v provozu, který může být příčinou tohoto problému. Pokud je problém přerušované, může být kvůli Vyrovnávání zatížení aktivity v rámci služby. Pokud tento problém je trvalé a není způsobené aplikace nedosáhli omezení škálovatelnosti služby, by měla vyvolat podporu problém. Pro překročení časového limitu klienta musíte rozhodnout, pokud je časový limit je nastaven na odpovídající hodnotu v klientovi a buď změňte nastavení hodnota časového limitu v klientovi nebo zjistěte, jak může zvýšit výkon operací v úložišti služby, například optimalizace své dotazy tabulky nebo zmenšení velikosti zprávy.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metriky způsobit nárůst PercentNetworkError
Vaše metriky způsobit nárůst v **PercentNetworkError** pro jednu z vaší služby úložiště. **PercentNetworkError** metrika je agregaci následující metriky: **NetworkError**, **AnonymousNetworkError**, a **SASNetworkError**. Tyto nastane, když služba úložiště zjistí chybu v síti, když klient odešle žádost úložiště.

Nejčastější příčinou této chyby je klient odpojení vyprší časový limit v rámci služby úložiště. Prozkoumejte kód z vašeho klienta pochopit, proč a kdy se klient neodpojí ze služby storage. Můžete také použít Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumat problémy se síťovým připojením z klienta. Tyto nástroje jsou popsány v [přílohy].

### <a name="the-client-is-receiving-403-messages"></a>Klient je přijímání zpráv protokolu HTTP 403 (zakázáno)
Pokud klientské aplikace způsobující chyby protokolu HTTP 403 (zakázáno), pravděpodobnou příčinou je, že klient při odesílání požadavku na úložiště (i když další možné příčiny patří hodiny zkosení, neplatné klíče a prázdný hlavičky) používá vypršenou platností sdíleného přístupového podpisu (SAS). Pokud vypršela platnost SAS klíč je příčinou, neuvidíte žádné položky v protokolu datům na serveru protokolování úložiště. Následující tabulka uvádí ukázku z klienta protokolu vygenerovaných Klientská knihovna pro úložiště, který znázorňuje výskytu tohoto problému:

| Zdroj | Podrobnosti | Podrobnosti | ID žádosti klienta | Operace textu |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab-… |Spuštění operace s umístěním primární umístění režim PrimaryOnly podle. |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Synchronní požadavek na spuštění https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14 &amp;sr = c&amp;ma = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;rozhraní api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Čekání na odpověď. |
| Microsoft.WindowsAzure.Storage |Upozornění |2 |85d077ab -… |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (403) zakázán. |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Odpověď. Stavový kód = 403, ID žádosti = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, obsah MD5 =, značka ETag =. |
| Microsoft.WindowsAzure.Storage |Upozornění |2 |85d077ab -… |Při operaci došlo k výjimce: vzdálený server vrátil chybu: (403) zakázán... |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Kontrola, pokud je třeba opakovat operaci. Počet opakování = 0, stavový kód HTTP = 403, výjimka = vzdálený server vrátil chybu: (403) zakázán... |
| Microsoft.WindowsAzure.Storage |Informace |3 |85d077ab -… |Další umístění již byla nastavena na primární, na základě umístění režimu. |
| Microsoft.WindowsAzure.Storage |Chyba |1 |85d077ab -… |Zásady opakování neumožňuje pro opakovaný pokus. Došlo k selhání s vzdálený server vrátil chybu: (403) zakázán. |

V tomto scénáři které byste měli prozkoumat, proč je předtím, než klient odešle token do serveru vypršení platnosti tokenu SAS:

* Obvykle neměli nastavit čas spuštění, když vytvoříte SAS pro klienta k použití okamžitě. Pokud existují malé hodiny rozdíly mezi hostiteli generování SAS pomocí aktuálního času a službu úložiště, je možné pro službu úložiště pro příjem SAS, který dosud není platný.
* Nenastavujte SAS čas vypršení platnosti velmi krátké. Znovu hodiny malé rozdíly mezi hostiteli generování SAS a službu úložiště může vést k SAS zjevně vypršení platnosti dříve, než se očekává.
* Parametr verze v SAS klíč, který nemá (například **sv = 2015-04-05**), odpovídat verzi knihovny klienta úložiště používáte? Doporučujeme vždy použít nejnovější verzi [Klientská knihovna pro úložiště](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Pokud jste znovu vygenerovat přístupové klíče k úložišti, všechny existující tokeny SAS může zrušena. Tento problém může nastat, pokud generovat tokeny SAS s dobou vypršení platnosti dlouhé pro klientské aplikace do mezipaměti.

Pokud používáte Klientská knihovna pro úložiště ke generování tokenů SAS, je snadné sestavení platný token. Ale pokud používáte rozhraní API REST úložiště a vytváření Tokeny SAS ručně, najdete v části [delegování přístupu k pomocí sdíleného přístupového podpisu](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klient je přijímání zpráv HTTP 404 (není nalezena)
Pokud klientská aplikace obdrží zprávu HTTP 404 (není nalezena) ze serveru, znamená to, že objekt, který klient se pokouší použít (například entity, tabulka, objektů blob, kontejneru nebo fronty) neexistuje v úložišti služby. Existuje několik z možných důvodů, jako například:

* [Klient nebo jiným procesem dřív odstranit objekt]
* [Chybu ověřování sdíleného přístupového podpisu (SAS)]
* [Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu]
* [Selhání sítě]

#### <a name="client-previously-deleted-the-object"></a>Klient nebo jiným procesem dřív odstranit objekt
Ve scénářích, kde se klient pokouší číst, aktualizovat nebo odstranit data v úložišti služby je obvykle snadné identifikaci v protokolech serverové předchozí operace, které u daného objektu odstraněné ze služby storage. Data protokolu často, ukazuje, že jiný uživatel nebo proces neodstranil tento objekt. V protokolování úložiště protokolu na straně serveru typ operace a požadovaný klíč objektu sloupce ukazují, když klient odstraněn objekt.

Ve scénáři, kde se klient pokouší o vložení objektu nemusí být hned zjevné Proč to vede odpovědi HTTP 404 (není nalezena), vzhledem k tomu, že klient je vytvoření nového objektu. Ale pokud klient je vytvoření objektu blob musí být schopna nalézt kontejner objektů blob, pokud klient je vytvoření zprávy, musí být schopna nalézt fronty, a pokud klient je přidání řádku musí být schopna nalézt v tabulce.

Můžete použít protokol klienta z Klientská knihovna pro úložiště na podrobnější pochopit když klient odešle konkrétní požadavky na služby úložiště.

Následující klienta protokolu vygenerovaných knihovny klienta úložiště znázorňuje problém, když klient nemůže najít kontejner pro tento objekt blob, které se vytváří. Tento protokol obsahuje podrobnosti o následující operace úložiště:

| ID požadavku | Operace |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metoda odstranit kontejner objektů blob. Všimněte si, že tato operace zahrnuje **HEAD** žádost o vyhledání existenci kontejneru. |
| e2d06d78... |**CreateIfNotExists** metodu pro vytvoření kontejneru objektů blob. Všimněte si, že tato operace zahrnuje **HEAD** požadavek, který zkontroluje existenci kontejneru. **HEAD** vrací zprávu 404, ale bude pokračovat. |
| de8b1c3c-... |**UploadFromStream** metodu pro vytvoření objektu blob. **PUT** požadavek selže s zprávu 404 |

Položky protokolu:

| ID požadavku | Operace textu |
| --- | --- |
| 07b26a5d-... |Synchronní požadavek na spuštění https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 června 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Odpověď. Stavový kód = 200, ID žádosti = eeead849-... Obsah MD5 =, značka ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Hlavičky odpovědi počet úspěšně zpracovaných, budete pokračovat s ostatními operaci. |
| 07b26a5d-... |Při stahování text odpovědi. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| 07b26a5d-... |Synchronní požadavek na spuštění https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 června 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Odpověď. Stavový kód = 202, ID žádosti = 6ab2a4cf-..., obsah MD5 =, značka ETag =. |
| 07b26a5d-... |Hlavičky odpovědi počet úspěšně zpracovaných, budete pokračovat s ostatními operaci. |
| 07b26a5d-... |Při stahování text odpovědi. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Asynchronní požadavek na spuštění https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 června 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Synchronní požadavek na spuštění https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Příprava k zápisu dat požadavku. |
| e2d06d78-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| e2d06d78-... |Odpověď. Stavový kód 404, ID žádosti = = 353ae3bc-..., obsah MD5 =, značka ETag =. |
| e2d06d78-... |Hlavičky odpovědi počet úspěšně zpracovaných, budete pokračovat s ostatními operaci. |
| e2d06d78-... |Při stahování text odpovědi. |
| e2d06d78-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Asynchronní požadavek na spuštění https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, 03 června 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Data žádosti o zápis. |
| de8b1c3c-... |Čekání na odpověď. |
| e2d06d78-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (409) – konflikt... |
| e2d06d78-... |Odpověď. Stavový kód = 409, ID žádosti = c27da20e-..., obsah MD5 =, značka ETag =. |
| e2d06d78-... |Probíhá stahování textu odpovědi na chyby. |
| de8b1c3c-... |Došlo k výjimce při čekání na odpověď: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| de8b1c3c-... |Odpověď. Stavový kód 404, ID žádosti = = 0eaeab3e-..., obsah MD5 =, značka ETag =. |
| de8b1c3c-... |Při operaci došlo k výjimce: vzdálený server vrátil chybu: (404) nebyl nalezen... |
| de8b1c3c-... |Zásady opakování neumožňuje pro opakovaný pokus. Došlo k selhání s vzdálený server vrátil chybu: (404) nebyl nalezen... |
| e2d06d78-... |Zásady opakování neumožňuje pro opakovaný pokus. Došlo k selhání s vzdálený server vrátil chybu: (409) – konflikt... |

V tomto příkladu protokol ukazuje, že klient je prokládání požadavky od **CreateIfNotExists** – metoda (žádost o ID e2d06d78...) s požadavky z **UploadFromStream** metody (de8b1c3c...). Tato prokládání dojde proto, že klientská aplikace je asynchronně volání těchto metod. Upravte asynchronní kód klienta a ověřte, že vytvoří kontejner před pokusem o odeslání žádná data do objektu blob v tomto kontejneru. V ideálním případě byste měli předem vytvořit všechny kontejnery.

#### <a name="SAS-authorization-issue"></a>Chybu ověřování sdíleného přístupového podpisu (SAS)
Pokud aplikace klienta se pokusí použít SAS klíč, který nezahrnuje potřebná oprávnění pro operaci, službu úložiště vrátí klientovi zprávu HTTP 404 (není nalezena). Ve stejnou dobu, zobrazí se také nenulové hodnoty pro **SASAuthorizationError** v metriky.

V následující tabulce jsou uvedeny ukázkovou zprávu protokolu na straně serveru ze souboru protokolu protokolování úložiště:

| Název | Hodnota |
| --- | --- |
| Žádost o spuštění | 2014-05-30T06:17:48.4473697Z |
| Typ operace     | GetBlobProperties            |
| Stav žádosti     | SASAuthorizationError        |
| Stavový kód HTTP   | 404                          |
| Typ ověřování| SAS                          |
| Typ služby       | Objekt blob                         |
| Adresa URL požadavku        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Hlavičky ID žádosti  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID žádosti klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Zjistěte, proč se pokouší klientskou aplikaci k provedení určité operace, pro které jí nebylo uděleno oprávnění.

#### <a name="JavaScript-code-does-not-have-permission"></a>Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu
Pokud používáte JavaScript klienta a služby úložiště vrací zprávy HTTP 404, můžete zkontrolovat následující chyby jazyka JavaScript v prohlížeči:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Můžete F12 Developer Tools v aplikaci Internet Explorer pro trasování zprávy vyměňují mezi prohlížečem a službu úložiště při odstraňování problémů, JavaScript na straně klienta.
> 
> 

K těmto chybám, protože webový prohlížeč implementuje [stejné zásady počátek](http://www.w3.org/Security/wiki/Same_Origin_Policy) omezení zabezpečení, které brání volání rozhraní API v jiné doméně z domény stránky na webové stránce pochází z.

JavaScript problém obejít, můžete nakonfigurovat různé sdílení prostředků zdroji (CORS) pro službu úložiště, které klient přistupuje. Další informace najdete v tématu [Podpora sdílení prostředků různých původů (CORS) pro úložiště služby Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

Následující příklad kódu ukazuje, jak nakonfigurovat služby objektů blob povolit jazyk JavaScript spuštěna v doméně Contoso přístup k objektu blob ve službě úložiště objektů blob:

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

#### <a name="network-failure"></a>Selhání sítě
V některých případech může vést ke ztrátě síťové pakety k službu úložiště zpráv HTTP 404 vrácením klientovi. Například když klientské aplikace je odstranění entity ze služby table uvidíte klienta throw výjimka úložiště vytváření sestav "HTTP 404 (není nalezena)" stavovou zprávu ze služby table. Můžete prozkoumat v tabulce ve službě table storage, uvidíte, že služba odstranit entitu podle požadavku.

Podrobnosti o výjimce v klientovi zahrnují ID požadavku (7e84f12d...) přiřazené službou tabulky pro požadavek: tyto informace můžete najít podrobnosti požadavku v protokolech úložiště serverové ve vyhledávání v **hlavička požadavku id**  sloupec v souboru protokolu. Metriky můžete také použít k identifikaci při selhání, jako je tato dojít a pak hledání souborů protokolu podle času metriky zaznamenávají této chybě. Tato položka protokolu ukazuje odstranění se nezdařilo se zpráva stav "Klient jiné chyby protokolu HTTP (404)". Stejné záznam protokolu také zahrnuje generované klientem v ID požadavku **client-request-id** sloupce (813ea74f...).

Na straně serveru protokolu obsahuje také jiný záznam se stejným **client-request-id** hodnotu (813ea74f...) úspěšně odstranit operaci pro stejnou entitu a ze stejného klienta. Tato operace úspěšné odstranění došlo velmi krátce před neúspěšný odstranit požadavek.

Nejpravděpodobnější příčinou tohoto scénáře je, že klient odeslal žádost o odstranění pro entitu do služby table, který byl úspěšný, avšak neobdržel potvrzení ze serveru (třeba kvůli problému v dočasné síti). Klient pak automaticky opakovat operaci (používající stejný **client-request-id**), a tento opakování se nezdařilo, protože entita byl již odstraněn.

Pokud tento problém opakuje často, které byste měli prozkoumat, proč klient nemůže na příjem potvrzení ze služby table. Pokud problém přerušované, by měl depeše chyby "HTTP (404) nebyl nalezen" a protokolu v klientovi ale povolit klientovi pokračovat.

### <a name="the-client-is-receiving-409-messages"></a>Klient je přijímání zpráv protokolu HTTP 409 (konflikt)
V následující tabulce jsou uvedeny výpis z protokolu na straně serveru pro dva klientské operace: **DeleteIfExists** a okamžitě nástrojem **CreateIfNotExists** použití stejného názvu kontejneru objektů blob. Výsledkem dva požadavky odeslané na server, nejdřív každé operace klienta **GetContainerProperties** požadavek na zkontrolujte, zda kontejner existuje, za nímž následuje **DeleteContainer** nebo  **CreateContainer** požadavku.

| Časové razítko | Operace | Výsledek | Název kontejneru | ID žádosti klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Kód v aplikaci klienta odstraní a okamžitě vytvoří kontejner objektů blob pomocí stejného názvu: **CreateIfNotExists** – metoda (klient v požadavku ID bc881924-...) nakonec selže s chybou HTTP 409 (konflikt). Když klient odstraní kontejnery objektů blob, tabulky a fronty je po krátkou dobu před název opět k dispozici.

Klientská aplikace měli používat názvy jedinečný kontejnerů, vždy, když se vytvoří nové kontejnery, pokud je běžné vzoru odstranit nebo znovu vytvoří.

### <a name="metrics-show-low-percent-success"></a>Metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors
**PercentSuccess** metrika zaznamená procento operací, které byly úspěšné podle jejich stavový kód HTTP. Počet operací s kódy stavu 2XX jako úspěšné, zatímco operací s kódy stavu v 3XX, 4XX a 5XX rozsahy, se počítají jako neúspěšná a nižší **PercentSucess** hodnota metriky. V souborech protokolu úložiště na straně serveru, se zaznamenávají tyto operace se stavem transakce **ClientOtherErrors**.

Je důležité si uvědomit, že tyto operace byly úspěšně dokončeny a proto nemají vliv na jiné metriky, jako je dostupnost. Některé příklady operací, které úspěšně provést, ale můžete výsledkem neúspěšných stavové kódy HTTP patří:

* **ResourceNotFound** (není nalezen 404), například ze požadavek GET na objekt blob, který neexistuje.
* **ResouceAlreadyExists** (409 konflikt), například ze **CreateIfNotExist** operace, kde již existuje prostředek.
* **ConditionNotMet** (ne upravit 304), například z podmíněného operace, například když klient odešle **značka ETag** hodnota a HTTP **If-None-Match** hlavičky k žádosti o bitovou kopii, pouze pokud má byly aktualizovány od poslední operace.

Můžete najít seznam běžné kódy chyb rozhraní REST API, které vracejí služby úložiště na stránce [běžné kódy chyb rozhraní API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště
Pokud se zobrazí nečekané, neočekávané změny využití kapacity ve vašem účtu úložiště můžete prozkoumat důvodů, proč nejprve kontrolou vaše dostupnosti metriky; například zvýšit počet neúspěšných požadavků může vést ke zvýšení množství úložiště objektů blob, který používáte jako operace čištění specifické pro aplikace, které může mít měla být uvolnění místa na nemusí fungovat podle očekávání (například odstranění protože vypršela platnost tokeny SAS používá pro uvolnění místa).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování
Obvykle pomocí emulátoru úložiště během vývoje a testování, aby se zabránilo požadavek pro účet úložiště Azure. Běžné problémy, které může dojít, když používáte emulátor úložiště jsou:

* [Funkce "X" nepracuje v emulátoru úložiště]
* [Chyba "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu" při použití emulátoru úložiště]
* [Spuštění emulátor úložiště vyžaduje oprávnění správce]

#### <a name="feature-X-is-not-working"></a>Funkce "X" nepracuje v emulátoru úložiště
Emulátor úložiště nepodporuje všechny funkce služby Azure storage, jako je například služba souborů. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

Pro tyto funkce, které nepodporuje emulátor úložiště použijte službu Azure storage v cloudu.

#### <a name="error-HTTP-header-not-correct-format"></a>Chyba "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu" při použití emulátoru úložiště
Testování vaší aplikace, která používá Klientská knihovna pro úložiště pro místní úložiště emulátoru a metoda volání, jako **CreateIfNotExists** nezdaří s chybovou zprávou "hodnota pro jednu z hlaviček protokolu HTTP není ve správné formát." To znamená, že verze, kterou používáte emulátor úložiště nepodporuje verzi klientské knihovny pro úložiště, který používáte. Klientská knihovna pro úložiště přidá hlavičku **x-ms-version** pro všechny požadavky, které umožňuje. Pokud emulátor úložiště nebyla rozpoznána hodnota v **x-ms-version** záhlaví, jeho žádost odmítne.

Protokoly úložiště knihovny klienta můžete zobrazit hodnotu **hlavičky x-ms-version** je odesílání. Můžete také zjistit hodnotu **hlavičky x-ms-version** Pokud použít aplikaci Fiddler Pokud chcete trasovat žádosti z klientské aplikace.

Tento scénář obvykle dochází, je-li nainstalovat a použít nejnovější verzi klientské knihovny pro úložiště bez aktualizace emulátor úložiště. Měli buď nainstalovat nejnovější verze emulátoru úložiště nebo použijte cloudového úložiště místo emulátor pro vývoj a testování.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Spuštění emulátor úložiště vyžaduje oprávnění správce
Zobrazí se výzva k zadání pověření správce při spuštění emulátor úložiště. K tomu dochází pouze při emulátor úložiště jsou inicializaci poprvé. Poté, co jste inicializovali emulátor úložiště, není nutné oprávnění správce se spustit znovu.

Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md). Můžete také provést inicializaci emulátor úložiště v sadě Visual Studio, který bude také vyžadují oprávnění správce.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Narazíte na potíže s instalací sady Azure SDK pro .NET
Když se pokusíte nainstalovat sadu SDK, dojde k chybě při pokusu o instalaci emulátor úložiště na místním počítači. V protokolu instalace obsahuje jednu z následujících zpráv:

* CAQuietExec: Chyba: Nelze získat přístup k instanci SQL
* CAQuietExec: Chyba: Nelze vytvořit databázi

Příčinou je problém se stávající instalací LocalDB. Ve výchozím nastavení používá emulátor úložiště LocalDB uchovávat data, když ho simuluje služby Azure storage. Spuštěním následujících příkazů v okně příkazového řádku, než se pokusíte nainstalovat sadu SDK můžete resetovat vaší instanci LocalDB.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**Odstranit** příkaz odebere žádné staré soubory databáze z předchozí instalace emulátoru úložiště.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Máte jiný problém se službou úložiště
Pokud předchozí části nezahrnují problém, který máte službou úložiště, by měl použít následující postup k diagnostikování a řešení potíží problém.

* Zkontrolujte vaše metriky a zjistěte, zda dojde ke změně z vaší očekávané chování základní line. Z metriky může být schopní určit, zda je problém přechodný nebo trvalé a operací, které úložiště problém ovlivňuje.
* Můžete použít informace metriky můžete hledat data protokolu na straně serveru pro podrobnější informace o chyby, ke kterým dochází. Tyto informace mohou pomoci při řešení potíží a vyřešte problém.
* Pokud nejsou dostatečná k odstranění problému úspěšně informace v protokolech na straně serveru, můžete prozkoumat chování vaší aplikace klienta a nástrojů, například aplikaci Fiddler, Wireshark a Microsoft Message Analyzer prozkoumat síti klientské protokoly Klientská knihovna pro úložiště.

Další informace o používání aplikaci Fiddler, najdete v části "[dodatku 1: pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]."

Další informace o používání Wireshark najdete v tématu "[příloze 2: zachycení síťového provozu pomocí Wireshark]."

Další informace o používání Microsoft Message Analyzer najdete v tématu "[příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]."

## <a name="appendices"></a>Přílohy
Dodatky popisují několik nástrojů, které mohou být užitečné při diagnostikování a řešení potíží s Azure Storage (a další služby). Tyto nástroje nejsou součástí Azure Storage a některé produkty třetích stran. Jako takový nástrojů popsaných v těchto přílohy nejsou zahrnuty jakékoli smlouvy podpory, kterou můžete mít s Microsoft Azure nebo Azure Storage, a proto jako součást procesu vyhodnocení byste měli zkontrolovat licencování a podporu možnosti dostupné na poskytovateli těchto nástrojů.

### <a name="appendix-1"></a>Dodatek 1: Pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS
[Fiddler](http://www.telerik.com/fiddler) je užitečným nástrojem pro analýzu přenosů dat HTTP a HTTPS mezi klientské aplikace a služba úložiště Azure, kterou používáte.

> [!NOTE]
> Fiddler může dekódovat provoz HTTPS; Přečtěte si dokumentaci k aplikaci Fiddler, pečlivě, abyste pochopili, jak to dělá a vysvětlení důsledků pro zabezpečení.
> 
> 

Tento dodatek obsahuje stručný návod, jak nakonfigurovat aplikaci Fiddler pro zachycení přenosu dat mezi místním počítači nainstalovanou aplikaci Fiddler a služby úložiště Azure.

Po spuštění Fiddler zahájíte zaznamenávání HTTP a HTTPS provozy na místním počítači. Tady jsou některé užitečné příkazy pro řízení Fiddler:

* Zastavení a spuštění zachycení provozu. Na hlavní nabídky, přejděte na **soubor** a pak klikněte na **zachycování provozu** k přepnutí zaznamenávání zapnout a vypnout.
* Zachycená data data uložte. Na hlavní nabídky, přejděte na **soubor**, klikněte na tlačítko **Uložit**a potom klikněte na **všechny relace**: Díky tomu můžete uložit do souboru archivu relace provoz. Můžete znovu načíst relace archivu později pro analýzu, nebo odeslat, pokud požadavek na podporu společnosti Microsoft.

Chcete-li omezit objem provozu, který zachycuje aplikaci Fiddler, můžete použít filtry, které nakonfigurujete v **filtry** kartě. Následující snímek obrazovky ukazuje filtr, který zaznamená pouze data odesílaná do **contosoemaildist.table.core.windows.net** koncový bod úložiště:

![][5]

### <a name="appendix-2"></a>Dodatek 2: Pomocí Wireshark pro zachycení síťového provozu
[Wireshark](http://www.wireshark.org/) je analyzátoru protokolu sítě, která umožňuje zobrazit informace o podrobné paketů pro širokou škálu protokolů síťové.

Následující postup ukazuje, jak k zaznamenání informací podrobné paketů pro provoz z místního počítače kam jste nainstalovali Wireshark do služby table v účtu úložiště Azure.

1. Spusťte Wireshark na místním počítači.
2. V **spustit** vyberte rozhraní místní sítě nebo rozhraní, které jsou připojené k Internetu.
3. Klikněte na tlačítko **zaznamenat možnosti**.
4. Přidat filtr na **filtr pro sběr dat** textové pole. Například **hostitele contosoemaildist.table.core.windows.net** nakonfiguruje Wireshark k zachycení pouze pakety odesílané do nebo z koncový bod služby tabulek v **contosoemaildist** účet úložiště. Podívejte se [úplný seznam filtrů zaznamenat](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Klikněte na tlačítko **spustit**. Wireshark teď zaznamená všechny pakety odesílat z koncový bod služby tabulek nebo při používání klientské aplikace na místním počítači.
6. Po dokončení, v hlavní nabídce **zaznamenat** a potom **Zastavit**.
7. Pokud chcete uložit zaznamenaná data do souboru Wireshark zachycení, v hlavní nabídce klikněte na tlačítko **soubor** a potom **Uložit**.

WireShark bude zvýrazněte všechny chyby, které existují v **packetlist** okno. Můžete také **Expert informace** okno (klikněte na tlačítko **analyzovat**, pak **Expert informace**) k zobrazení souhrnu chyb a varování.

![][7]

Můžete také zobrazit TCP data aplikační vrstvu, uvidí ho kliknutím pravým tlačítkem myši na data protokolu TCP a výběrem **podle datového proudu TCP**. To je užitečné v případě zaznamenat vaše výpisu bez zachycení filtru. Další informace najdete v tématu [následující datové proudy TCP](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Další informace o používání Wireshark najdete v tématu [Wireshark uživatelé průvodce](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Dodatek 3: Použití Microsoft Message Analyzer pro zachycení síťového provozu
Můžete použít Microsoft Message Analyzer pro zachycení přenosu dat HTTP a HTTPS podobným způsobem jako pro aplikaci Fiddler a zachycení síťového provozu na Wireshark podobným způsobem.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurovat webovou relaci trasování pomocí Microsoft Message Analyzer
Konfigurace relace trasování webu pro HTTP a HTTPS provozy pomocí Microsoft Message Analyzer, spusťte aplikaci Microsoft Message Analyzer a potom na **soubor** nabídky, klikněte na tlačítko **zachycení/trasování**. V seznamu dostupných trasování scénáře vyberte **webový proxy server**. Potom v **trasování scénáře konfigurace** v panelu **HostnameFilter** textovému poli, přidat jména koncových bodů úložiště (můžete vyhledat tyto názvy v [portál Azure](https://portal.azure.com)). Například, pokud je název účtu úložiště Azure **contosodata**, měli byste přidat následující **HostnameFilter** textové pole:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Znak mezery odděluje názvy hostitelů.
> 
> 

Když budete chtít spustit shromažďování dat trasování, klikněte na **Start With** tlačítko.

Další informace o Microsoft Message Analyzer **webový proxy server** trasování najdete v tématu [zprostředkovatele Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

Integrované **webový proxy server** trasování v Microsoft Message Analyzer je založena na aplikaci Fiddler, můžete zaznamenávat provoz HTTPS na straně klienta a zobrazit nezašifrované zpráv protokolu HTTPS. **Webový proxy server** trasování funguje tak, že nakonfigurujete místní proxy server pro všechny přenosy HTTP a HTTPS, která poskytuje přístup k nezašifrované zprávy.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostika sítě problémy při použití Microsoft Message Analyzer
Kromě používání Microsoft Message Analyzer **webový proxy server** trasování pro zachycení podrobnosti přenosy HTTP/HTTPs mezi klientská aplikace a služby úložiště, můžete také použít integrované **místní vrstvy odkaz** trasování k zaznamenání informací síťových paketů. To umožňuje, můžete k zaznamenání dat, podobně jako na informace, které můžete zaznamenat s Wireshark a diagnostikovat problémy sítě, jako jsou vyřazené pakety.

Následující snímek obrazovky ukazuje příklad **místní vrstvy odkaz** trasování s některými **informační** zprávy v **DiagnosisTypes** sloupce. Kliknutím na ikonu v **DiagnosisTypes** sloupci se zobrazuje podrobnosti o zprávě. V tomto příkladu server opakovaně odeslané zprávy #305 protože nepřijala potvrzení z klienta:

![][9]

Když vytvoříte relace trasování v Microsoft Message Analyzer, můžete zadat filtry pro snížení šumu v trasování. Na **zachycení / trasování** stránky, kde můžete definovat trasování, klikněte na **konfigurace** vedle **Microsoft-Windows-NDIS-PacketCapture**. Následující snímek obrazovky ukazuje konfigurace, který filtruje přenosy TCP pro IP adresy tři služby úložiště:

![][10]

Další informace o trasování Microsoft zpráva analyzátor místní odkaz vrstvy najdete v tématu [zprostředkovatele Microsoft PEF NDIS PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Dodatek 4: Zobrazení metriky a protokolovat data pomocí aplikace Excel
Celou řadu nástrojů umožňují stahování metriky úložiště dat z úložiště tabulek Azure ve formátu s oddělovači, který umožňuje snadno načíst data do Excelu pro zobrazení a analýzu. Úložiště protokolování dat z Azure blob storage je již ve formátu s oddělovači, který můžete načíst do aplikace Excel. Ale budete muset přidat záhlaví příslušných sloupců, které jsou založené na informace na [úložiště analýzy protokolů formátu](http://msdn.microsoft.com/library/azure/hh343259.aspx) a [schématu tabulky metriky Analytics úložiště](http://msdn.microsoft.com/library/azure/hh343264.aspx).

K importu dat protokolování úložiště do aplikace Excel po jeho stažení z úložiště objektů blob:

* Na **Data** nabídky, klikněte na tlačítko **z textu**.
* Vyhledejte soubor protokolu, které chcete zobrazit a klikněte na tlačítko **Import**.
* V kroku 1 tohoto **Průvodce importem textu**, vyberte **odděleného**.

V kroku 1 tohoto **Průvodce importem textu**, vyberte **středník** jako oddělovač, který pouze a zvolte dvojité uvozovky jako **kvalifikátor Text**. Pak klikněte na tlačítko **Dokončit** a zvolit umístění, kam umístit data v sešitu.

### <a name="appendix-5"></a>Dodatek 5: Monitorování pomocí služby Application Insights pro Visual Studio Team Services
Můžete také použít funkci Application Insights pro Visual Studio Team Services jako součást výkonu a dostupnosti monitorování. Tento nástroj můžete:

* Ujistěte se, že webová služba je k dispozici a dobře reagovaly. Zda je vaše aplikace na web nebo aplikaci ze zařízení, která používá webovou službu, je otestovat adresu URL z umístění po celém světě každých několik minut a umožňují vědět, pokud dojde k problému.
* Rychle diagnostikuje všechny problémy s výkonem nebo výjimky v webové služby. Zjistěte, pokud jsou jejich protažení procesoru nebo jiným prostředkům, sám trasování zásobníku výjimky a snadno prohledávat protokolu trasování. Pokud aplikace výkonu klesne pod přijatelnou omezení, Microsoft můžete odesílat e-mailu. Můžete monitorovat webové služby .NET a Java.

Další informace najdete [co je Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Úvod]: #introduction
[Uspořádání Tato příručka]: #how-this-guide-is-organized

[monitorování vaší služby úložiště]: #monitoring-your-storage-service
[Monitorování stavu služby]: #monitoring-service-health
[Monitorování kapacity]: #monitoring-capacity
[Monitorování dostupnosti]: #monitoring-availability
[Sledování výkonu]: #monitoring-performance

[diagnostice problémů s úložištěm]: #diagnosing-storage-issues
[Problémy v oblasti služby stavu]: #service-health-issues
[problémy s výkonem]: #performance-issues
[Diagnostikování chyb]: #diagnosing-errors
[Emulátor problémů s úložištěm]: #storage-emulator-issues
[Nástroje protokolování úložiště]: #storage-logging-tools
[Pomocí nástroje protokolování]: #using-network-logging-tools

[začátku do konce trasování]: #end-to-end-tracing
[Korelace data protokolu]: #correlating-log-data
[ID žádosti klienta]: #client-request-id
[ID žádosti serveru]: #server-request-id
[Časová razítka]: #timestamps

[pokyny při řešení potíží]: #troubleshooting-guidance
[metriky ukazují AverageE2ELatency vysoké a nízké AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje vysokou hodnotu AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]: #you-are-experiencing-unexpected-delays-in-message-delivery

[metriky způsobit nárůst PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Přechodný zvýšení PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Trvalé zvýšení PercentThrottlingError chyba]: #permanent-increase-in-PercentThrottlingError
[metriky způsobit nárůst PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrika ukazuje zvýšení u PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient je přijímání zpráv protokolu HTTP 403 (zakázáno)]: #the-client-is-receiving-403-messages
[Klient je přijímání zpráv HTTP 404 (není nalezena)]: #the-client-is-receiving-404-messages
[Klient nebo jiným procesem dřív odstranit objekt]: #client-previously-deleted-the-object
[Chybu ověřování sdíleného přístupového podpisu (SAS)]: #SAS-authorization-issue
[Kód jazyka JavaScript na straně klienta nemá oprávnění pro přístup k objektu]: #JavaScript-code-does-not-have-permission
[Selhání sítě]: #network-failure
[Klient je přijímání zpráv protokolu HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors]: #metrics-show-low-percent-success
[Metriky kapacity způsobit neočekávané nárůst využití kapacity úložiště]: #capacity-metrics-show-an-unexpected-increase
[Problém vyplývá z pomocí emulátoru úložiště pro vývoj nebo testování]: #your-issue-arises-from-using-the-storage-emulator
[Funkce "X" nepracuje v emulátoru úložiště]: #feature-X-is-not-working
[Chyba "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu" při použití emulátoru úložiště]: #error-HTTP-header-not-correct-format
[Spuštění emulátor úložiště vyžaduje oprávnění správce]: #storage-emulator-requires-administrative-privileges
[Narazíte na potíže s instalací sady Azure SDK pro .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Máte jiný problém se službou úložiště]: #you-have-a-different-issue-with-a-storage-service

[přílohy]: #appendices
[dodatku 1: pomocí Fiddleru pro zachycení přenosu dat HTTP a HTTPS]: #appendix-1
[příloze 2: zachycení síťového provozu pomocí Wireshark]: #appendix-2
[příloha 3: použití Microsoft Message Analyzer pro zachycení síťového provozu]: #appendix-3
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
