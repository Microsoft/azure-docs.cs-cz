---
title: Monitorování, diagnostika a řešení potíží s Azure Storage | Dokumenty společnosti Microsoft
description: K identifikaci, diagnostice a řešení problémů souvisejících s úložištěm Azure používejte funkce, jako je analýza úložiště, protokolování na straně klienta a další nástroje třetích stran.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 0bbffacc0a8c47950b8637e826d1d5db9fbdb234
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605068"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Přehled
Diagnostika a řešení problémů v distribuované aplikaci hostované v cloudovém prostředí může být složitější než v tradičních prostředích. Aplikace lze nasadit v infrastruktuře PaaS nebo IaaS, místně, na mobilním zařízení nebo v nějaké kombinaci těchto prostředí. Síťový provoz vaší aplikace může obvykle procházet veřejnými a soukromými sítěmi a vaše aplikace může kromě jiných úložišť, jako jsou relační a dokumentové databáze, používat více technologií úložiště Microsoft Azure, objektů Blob, Frontnebo Souborů.

Chcete-li tyto aplikace úspěšně spravovat, měli byste je proaktivně sledovat a pochopit, jak diagnostikovat a řešit všechny aspekty těchto aplikací a jejich závislé technologie. Jako uživatel služeb Azure Storage byste měli průběžně sledovat služby úložiště, které vaše aplikace používá pro jakékoli neočekávané změny v chování (například pomalejší než obvykle doby odezvy) a protokolování použít ke shromažďování podrobnějších dat a k analýze problému do hloubky. Diagnostické informace, které získáte z monitorování i protokolování vám pomůže určit hlavní příčinu problému, se kterým se vaše aplikace setkala. Potom můžete problém vyřešit a určit příslušné kroky, které můžete provést k jeho nápravě. Azure Storage je základní služba Azure a tvoří důležitou součást většiny řešení, která zákazníci nasazují do infrastruktury Azure. Azure Storage obsahuje funkce pro zjednodušení monitorování, diagnostiky a řešení problémů s úložištěm ve vašich cloudových aplikacích.

> [!NOTE]
> Soubory Azure v tuto chvíli nepodporuje protokolování.
>

Praktického průvodce pro komplexní řešení potíží v aplikacích Azure Storage najdete v [tématu End-to-End Troubleshooting using Azure Storage Metrics and Logging, AzCopy a Message Analyzer](../storage-e2e-troubleshooting.md).

* [Úvod]
  * [Jak je tato příručka organizována]
* [Sledování služby úložiště]
  * [Stav monitorovací služby]
  * [Monitorovací kapacita]
  * [Dostupnost monitorování]
  * [Sledování výkonu]
* [Diagnostika problémů s úložištěm]
  * [Problémy se stavem služby]
  * [Problémy s výkonem]
  * [Diagnostika chyb]
  * [Problémy s emulátorem úložiště]
  * [Nástroje pro protokolování úložiště]
  * [Použití nástrojů pro protokolování sítě]
* [Komplexní trasování]
  * [Korelační data protokolu]
  * [ID požadavku klienta]
  * [ID požadavku serveru]
  * [Časová razítka]
* [Pokyny pro řešení potíží]
  * [Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.]
  * [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.]
  * [Metrika ukazuje vysokou hodnotu AverageServerLatency.]
  * [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]
  * [Metrika ukazuje zvýšení u PercentThrottlingError.]
  * [Metrika ukazuje zvýšení u PercentTimeoutError.]
  * [Metrika ukazuje zvýšení u PercentNetworkError.]
  * [Klient dostává stavové kódy HTTP 403 (Zakázáno)]
  * [Klient dostává stavové kódy HTTP 404 (Nenalezeno)]
  * [Klient dostává stavové kód HTTP 409 (Konflikt)]
  * [Metriky ukazují nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors]
  * [Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště]
  * [Váš problém vzniká použitím emulátoru úložiště pro vývoj nebo testování]
  * [Dochází k problémům s instalací sady Azure SDK pro rozhraní .NET]
  * [Se službou úložiště máte jiný problém.]
  * [Poradce při potížích s virtuálními servery na virtuálních počítačích s Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Řešení potíží s virtuálními počítači na virtuálních počítačích s Linuxem](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Řešení potíží se soubory Azure s Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Řešení problémů se soubory Azure s Linuxem](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Dodatky]
  * [Dodatek 1: Použití Šumař zachytit HTTP a HTTPS provozu]
  * [Dodatek 2: Použití Wireshark zachytit síťový provoz]
  * [Dodatek 3: Použití analyzátoru zpráv společnosti Microsoft k zachycení síťového provozu]
  * [Dodatek 4: Použití Excelu k zobrazení metrik a dat protokolu]
  * [Dodatek 5: Monitorování pomocí přehledů aplikací pro Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Úvod
Tato příručka ukazuje, jak používat funkce, jako je Azure Storage Analytics, protokolování na straně klienta v klientské knihovně úložiště Azure a další nástroje třetích stran k identifikaci, diagnostice a řešení problémů souvisejících s úložištěm Azure.

![][1]

Tato příručka je určena především vývojáři online služeb, které používají Služby Azure Storage Services a IT profesionály odpovědné za správu těchto online služeb. Cílem této příručky jsou:

* Pomáhávám udržovat stav a výkon vašich účtů Azure Storage.
* Abychom vám poskytli potřebné procesy a nástroje, které vám pomohou rozhodnout, jestli se problém nebo problém v aplikaci týká Azure Storage.
* Pokud vám poskytneme užitečné pokyny pro řešení problémů souvisejících s Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Jak je tato příručka organizována
Část[Sledování služby úložiště]popisuje, jak sledovat stav a výkon služeb Azure Storage pomocí metrik Azure Storage Analytics (Storage Metrics).

Část[Diagnostika problémů s úložištěm]popisuje, jak diagnostikovat problémy pomocí protokolování Azure Storage Analytics (Protokolování úložiště). Také popisuje, jak povolit protokolování na straně klienta pomocí zařízení v jedné z klientských knihoven, jako je například klientská knihovna úložiště pro .NET nebo Azure SDK pro Javu.

Část["End-to-end trasování]" popisuje, jak můžete korelovat informace obsažené v různých souborech protokolu a data metriky.

Část Pokyny[pro řešení potíží]obsahuje pokyny pro řešení potíží pro některé běžné problémy související s úložištěm, se kterými se můžete setkat.

["Dodatky]" obsahují informace o použití jiných nástrojů, jako je Wireshark a Netmon pro analýzu dat síťového paketu, Fiddler pro analýzu zpráv HTTP/HTTPS a Microsoft Message Analyzer pro korelaci dat protokolu.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Sledování služby úložiště
Pokud jste obeznámeni s monitorování výkonu systému Windows, můžete si myslet metriky úložiště jako ekvivalent Azure Storage čítačů Sledování výkonu Systému Windows. V metrikách úložiště najdete komplexní sadu metrik (čítačů v terminologii sledování výkonu systému Windows), jako je dostupnost služeb, celkový počet požadavků na službu nebo procento úspěšných požadavků na službu. Úplný seznam dostupných metrik najdete v [tématu Schéma tabulky metrik analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx). Můžete určit, zda chcete, aby služba úložiště shromažďovala a agregovala metriky každou hodinu nebo každou minutu. Další informace o povolení metrik a monitorování účtů úložiště najdete v [tématu Povolení metrik úložiště a zobrazení dat metrik](https://go.microsoft.com/fwlink/?LinkId=510865).

Můžete si vybrat, které hodinové metriky chcete zobrazit na [webu Azure Portal,](https://portal.azure.com) a nakonfigurovat pravidla, která upozorní správce e-mailem vždy, když hodinová metrika překročí určitou prahovou hodnotu. Další informace naleznete v [tématu Příjem oznámení o výstražných upozorněních](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

Doporučujeme zkontrolovat [Azure Monitor pro úložiště](../../azure-monitor/insights/storage-insights-overview.md) (preview). Jedná se o funkci Azure Monitoru, která nabízí komplexní monitorování vašich účtů Azure Storage tím, že poskytuje jednotné zobrazení výkonu, kapacity a dostupnosti služeb Azure Storage. Nevyžaduje, abyste nic povolovali nebo nakonfigurovali, a tyto metriky můžete okamžitě zobrazit z předdefinovaných interaktivních grafů a dalších vizualizací.

Služba úložiště shromažďuje metriky s využitím maximálního úsilí, ale nemusí zaznamenávat všechny operace úložiště.

Na webu Azure Portal můžete zobrazit metriky, jako je dostupnost, celkový počet požadavků a čísla průměrné latence pro účet úložiště. Bylo také nastaveno pravidlo oznámení, které upozorňuje správce, pokud dostupnost klesne pod určitou úroveň. Ze zobrazení těchto dat, jeden možný oblast pro šetření je procento úspěšnosti služby tabulky je nižší než 100 % (další informace naleznete v části[" Metriky zobrazit nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors]").

Měli byste průběžně sledovat vaše aplikace Azure, abyste zajistili, že jsou v pořádku a fungují podle očekávání:

* Vytvoření některé základní metriky pro aplikaci, která vám umožní porovnat aktuální data a identifikovat všechny významné změny v chování úložiště Azure a vaší aplikace. Hodnoty směrných metrik budou v mnoha případech specifické pro aplikaci a měli byste je vytvořit při testování výkonu aplikace.
* Zaznamenávání minutových metrik a jejich použití k aktivnímu sledování neočekávaných chyb a anomálií, jako jsou špičky v počtu chyb nebo sazby požadavků.
* Zaznamenávání hodinových metrik a jejich použití ke sledování průměrných hodnot, jako je průměrný počet chyb a míry požadavků.
* Zkoumání potenciálních problémů pomocí diagnostických nástrojů, jak je popsáno dále v části[Diagnostika problémů s úložištěm].

Grafy na následujícím obrázku ilustrují, jak průměrování, ke kterému dochází pro hodinové metriky, může skrýt špičky aktivity. Zdá se, že hodinové metriky zobrazují stabilní rychlost požadavků, zatímco minutové metriky odhalují výkyvy, ke kterým skutečně dochází.

![][3]

Zbývající část této části popisuje, jaké metriky byste měli sledovat a proč.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Stav monitorovací služby
[Na portálu Azure](https://portal.azure.com) můžete zobrazit stav služby Storage (a dalších služeb Azure) ve všech oblastech Azure po celém světě. Monitorování umožňuje okamžitě zobrazit, pokud problém mimo váš ovládací prvek ovlivňuje službu úložiště v oblasti, kterou používáte pro vaši aplikaci.

[Portál Azure](https://portal.azure.com) může také poskytovat oznámení o incidentech, které ovlivňují různé služby Azure.
Poznámka: Tyto informace byly dříve k dispozici spolu s historickými daty na [řídicím panelu služby Azure](https://status.azure.com).

Zatímco [portál Azure](https://portal.azure.com) shromažďuje informace o stavu z datových center Azure (monitorování zevnitř ven), můžete také zvážit přijetí přístupu mimo vstup ke generování syntetických transakcí, které pravidelně přistupují k vaší webové aplikaci hostované v Azure z více míst. Služby nabízené [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) a Application Insights pro Azure DevOps jsou příklady tohoto přístupu. Další informace o přehledech aplikací pro Azure DevOps najdete v dodatku "[Dodatek 5: Monitorování s application insights pro Azure DevOps](#appendix-5)."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Monitorovací kapacita
Metriky úložiště pouze ukládá metriky kapacity pro službu objektů blob, protože objekty BLOB obvykle představují největší podíl uložených dat (v době psaní není možné použít metriky úložiště ke sledování kapacity tabulek a front). Tato data můžete najít v **tabulce $MetricsCapacityBlob,** pokud jste povolili monitorování pro službu objektů Blob. Metriky úložiště zaznamenává tato data jednou denně a můžete použít hodnotu **RowKey** k určení, zda řádek obsahuje entitu, která se vztahuje k uživatelským datům **(data**o hodnotě) nebo analytickým **datům (analýza**hodnot). Každá uložená entita obsahuje informace o množství použitého úložiště **(Kapacita** měřená v bajtech) a aktuální počet kontejnerů (**ContainerCount**) a objektů BLOB (**ObjectCount**) používaných v účtu úložiště. Další informace o metrikách kapacity uložených v tabulce **$MetricsCapacityBlob** naleznete v [tématu Schéma tabulky metrik analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Tyto hodnoty byste měli sledovat pro včasné upozornění, že se blížíte omezení kapacity vašeho účtu úložiště. Na webu Azure Portal můžete přidat pravidla výstrah, která vás upozorní, pokud využití agregovaného úložiště překročí nebo klesne pod prahové hodnoty, které zadáte.
>
>

Nápovědu k odhadu velikosti různých objektů úložiště, jako jsou objekty BLOB, najdete v příspěvku blogu [Principy fakturace úložiště Azure – šířky pásma, transakcí a kapacity](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Dostupnost monitorování
Dostupnost služeb úložiště ve vašem účtu úložiště byste měli sledovat sledováním hodnoty ve **sloupci Dostupnost** v tabulkách metrik hodinové nebo minutové – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Sloupec **Dostupnost** obsahuje procentuální hodnotu, která označuje dostupnost služby nebo operace rozhraní API reprezentované řádek **(RowKey** ukazuje, pokud řádek obsahuje metriky pro službu jako celek nebo pro konkrétní operaci rozhraní API).

Jakákoli hodnota menší než 100 % označuje, že některé požadavky na úložiště selhávají. Můžete zjistit, proč se jejich selhání kontrolou další sloupce v datech metriky, které ukazují počet požadavků s různými typy chyb, jako je **například ServerTimeoutError**. Měli byste očekávat, že **dostupnost** dočasně klesne pod 100 % z důvodů, jako jsou přechodné časové oposy serveru, zatímco služba přesune oddíly na lepší požadavek na vyrovnávání zatížení; logika opakování v klientské aplikaci by měla zpracovávat takové občasné podmínky. V článku [Protokolované operace a zprávy o stavu služby Storage Analytics](https://msdn.microsoft.com/library/azure/hh343260.aspx) uvádí typy transakcí, které metriky úložiště zahrnují do výpočtu **dostupnosti.**

Na [webu Azure Portal](https://portal.azure.com)můžete přidat pravidla výstrah, která vás upozorní, pokud **dostupnost** služby klesne pod prahovou hodnotu, kterou zadáte.

Část Pokyny[pro řešení potíží]v této příručce popisuje některé běžné problémy se službou úložiště související s dostupností.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Sledování výkonu
Chcete-li sledovat výkon služeb úložiště, můžete použít následující metriky z tabulek hodinových a minutových metrik.

* Hodnoty ve sloupcích **AverageE2ELatency** a **AverageServerLatency** zobrazují průměrnou dobu, po kterou služba úložiště nebo typ operace rozhraní API provádí zpracování požadavků. **AverageE2ELatency** je míra latence od konce do konce, která zahrnuje čas, který trval na přečtení požadavku a odeslat odpověď kromě doby, která byla přijata ke zpracování požadavku (proto zahrnuje latenci sítě, jakmile požadavek dosáhne služby úložiště); **AverageServerLatency** je míra pouze čas zpracování a proto vylučuje všechny latence sítě související s komunikací s klientem. Naleznete v části[" Metriky zobrazit vysoké AverageE2ELatency a nízké AverageServerLatency]dále v této příručce pro diskusi o tom, proč může být významný rozdíl mezi těmito dvěma hodnotami.
* Hodnoty ve sloupcích **TotalIngress** a **TotalEgress** zobrazují celkové množství dat v bajtů, přicházejících do služby úložiště nebo prostřednictvím konkrétního typu operace rozhraní API.
* Hodnoty ve sloupci **TotalRequests** zobrazují celkový počet požadavků, které přijímá služba úložiště operace rozhraní API. **TotalRequests** je celkový počet požadavků, které služba úložiště obdrží.

Obvykle budete sledovat neočekávané změny v některé z těchto hodnot jako indikátor, že máte problém, který vyžaduje šetření.

Na [webu Azure Portal](https://portal.azure.com)můžete přidat pravidla výstrah, která vás upozorní, pokud některá metrika výkonu pro tuto službu klesne pod nebo překročí prahovou hodnotu, kterou zadáte.

Část "[Pokyny pro řešení potíží]" v této příručce popisuje některé běžné problémy se službou úložiště související s výkonem.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnostika problémů s úložištěm
Existuje několik způsobů, jak se můžete seznámit s problémem nebo problémem v aplikaci, včetně:

* Hlavní selhání, které způsobí selhání aplikace nebo ukončení práce.
* Významné změny oproti hodnotám směrného plánu v metrikách, které sledujete, jak je popsáno v předchozí části[Sledování služby úložiště.]
* Zprávy od uživatelů aplikace, že některé konkrétní operace nebyla dokončena podle očekávání nebo že některé funkce nefunguje.
* Chyby generované v rámci aplikace, které se zobrazí v souborech protokolu nebo prostřednictvím jiné metody oznámení.

Problémy související se službami úložiště Azure obvykle spadají do jedné ze čtyř širokých kategorií:

* Vaše aplikace má problém s výkonem, a to buď hlášeny uživateli, nebo odhalilzměny v metriky výkonu.
* Došlo k potížím s infrastrukturou Úložiště Azure v jedné nebo více oblastech.
* Aplikace se setkává s chybou, kterou hlásí uživatelé nebo je odhalila zvýšení jedné z metrik počtu chyb, které monitorujete.
* Během vývoje a testování můžete používat emulátor místního úložiště; může dojít k některé problémy, které se týkají konkrétně použití emulátoru úložiště.

Následující části popisují kroky, které byste měli postupovat při diagnostice a řešení problémů v každé z těchto čtyř kategorií. Část "[Pokyny pro řešení potíží]" dále v této příručce obsahuje další podrobnosti o některých běžných problémech, se kterými se můžete setkat.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problémy se stavem služby
Problémy se stavem služby jsou obvykle mimo vaši kontrolu. [Portál Azure](https://portal.azure.com) poskytuje informace o všech probíhajících problémech se službami Azure, včetně služeb úložiště. Pokud jste se při vytváření účtu úložiště rozhodli pro geograficky redundantní úložiště pro přístup ke čtení, pak pokud vaše data nebudou dostupná v primárním umístění, může vaše aplikace dočasně přepnout na kopii jen pro čtení v sekundárním umístění. Chcete-li číst ze sekundární, aplikace musí být schopen přepínat mezi pomocí primární a sekundární umístění úložiště a musí být schopen pracovat v režimu se sníženou funkčností s daty jen pro čtení. Knihovny klienta úložiště Azure umožňují definovat zásady opakování, které lze číst ze sekundárního úložiště v případě, že se nezdaří čtení z primárního úložiště. Vaše aplikace také musí být vědomi, že data v sekundárním umístění je nakonec konzistentní. Další informace najdete v příspěvku blogu [Azure Storage Redundancy Options a Geo Redundantní úložiště pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problémy s výkonem
Výkon aplikace může být subjektivní, zejména z pohledu uživatele. Proto je důležité mít k dispozici základní metriku, která vám pomůže identifikovat možné problémy s výkonem. Výkon služby úložiště Azure může z hlediska klientské aplikace ovlivnit mnoho faktorů. Tyto faktory mohou fungovat ve službě úložiště, v klientovi nebo v síťové infrastruktuře; proto je důležité mít strategii pro identifikaci původu problému s výkonností.

Poté, co jste identifikovali pravděpodobné umístění příčiny problému s výkonem z metriky, potom můžete pomocí souborů protokolu najít podrobné informace k diagnostice a řešení problému dále.

Další informace o některých běžných problémech[souvisejících]s výkonem, se kterými se můžete setkat, naleznete v části Pokyny pro řešení potíží dále v této příručce.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnostika chyb
Uživatelé aplikace vás mohou upozornit na chyby hlášené klientskou aplikací. Metriky úložiště také zaznamenávají počty různých typů chyb ze služeb úložiště, jako je **NetworkError**, **ClientTimeoutError**nebo **AuthorizationError**. Zatímco metriky úložiště zaznamenávají pouze počty různých typů chyb, můžete získat další podrobnosti o jednotlivých požadavcích kontrolou protokolů na straně serveru, na straně klienta a sítě. Stavový kód HTTP vrácený službou úložiště obvykle uvede, proč se požadavek nezdařil.

> [!NOTE]
> Nezapomeňte, že byste měli očekávat, že se zobrazí některé občasné chyby: například chyby způsobené přechodnými podmínkami sítě nebo chyby aplikace.
>
>

Následující zdroje informací jsou užitečné pro pochopení stavů a chybových kódů souvisejících s úložištěm:

* [Běžné chybové kódy rozhraní REST API](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Chybové kódy služby objektů Blob](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Chybové kódy služby fronty](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kódy chyb služby Table Service](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kódy chyb služby souborů](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problémy s emulátorem úložiště
Sada Azure SDK obsahuje emulátor úložiště, který můžete spustit na vývojové pracovní stanici. Tento emulátor simuluje většinu chování služeb úložiště Azure a je užitečný při vývoji a testování, což vám umožní spouštět aplikace, které používají služby úložiště Azure bez nutnosti předplatného Azure a účtu úložiště Azure.

Část Pokyny[pro řešení potíží]v této příručce popisuje některé běžné problémy, u kterých došlo při emulátoru úložiště.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Nástroje pro protokolování úložiště
Protokolování úložiště poskytuje protokolování požadavků na úložiště na straně serveru ve vašem účtu úložiště Azure. Další informace o povolení protokolování na straně serveru a přístupu k datům protokolu naleznete v [tématu Povolení protokolování úložiště a přístupu k datům protokolu](https://go.microsoft.com/fwlink/?LinkId=510867).

Klientská knihovna úložiště pro rozhraní .NET umožňuje shromažďovat data protokolu na straně klienta, která se vztahují k operacím úložiště prováděným vaší aplikací. Další informace najdete v článku [Protokolování na straně klienta s klientskou knihovnou pro úložiště .NET](https://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> V některých případech (například selhání autorizace SAS) může uživatel nahlásit chybu, pro kterou můžete najít žádná data požadavku v protokolech úložiště na straně serveru. Pomocí možností protokolování klientské knihovny úložiště můžete zjistit, zda je příčina problému v klientovi, nebo pomocí nástrojů pro sledování sítě k prozkoumání sítě.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Použití nástrojů pro protokolování sítě
Můžete zachytit provoz mezi klientem a serverem poskytnout podrobné informace o data klienta a serveru jsou výměnu a základní podmínky sítě. Mezi užitečné nástroje pro protokolování v síti patří:

* [Fiddler](https://www.telerik.com/fiddler) je bezplatný webový ladicí proxy server, který umožňuje prozkoumat záhlaví a data datové části zpráv požadavků a odpovědí HTTP a HTTPS. Další informace naleznete [v dodatku 1: Použití šumavy k zachycení přenosu HTTP a HTTPS](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) a [Wireshark](https://www.wireshark.org/) jsou bezplatné analyzátory síťových protokolů, které umožňují zobrazit podrobné informace o paketech pro širokou škálu síťových protokolů. Další informace o wiresharku naleznete v tématu "[Dodatek 2: Použití wiresharku k zachycení síťového provozu](#appendix-2)".
* Microsoft Message Analyzer je nástroj od společnosti Microsoft, který nahrazuje Netmon a že kromě zachycení dat síťového paketu, vám pomůže zobrazit a analyzovat data protokolu zachycená z jiných nástrojů. Další informace naleznete v[tématu " Dodatek 3: Použití nástroje Microsoft Message Analyzer k zachycení síťového provozu](#appendix-3)".
* Pokud chcete provést základní test připojení ke kontrole, že váš klientský počítač můžete připojit ke službě úložiště Azure přes síť, nelze to provést pomocí standardního nástroje **ping** na straně klienta. Ke kontrole připojení však můžete použít nástroj [ **tcping.** ](https://www.elifulkerson.com/projects/tcping.php)

V mnoha případech budou k diagnostice problému dostatečná data protokolu z protokolování úložiště a klientské knihovny úložiště, ale v některých případech můžete potřebovat podrobnější informace, které mohou tyto nástroje pro protokolování sítě poskytnout. Například pomocí Fiddler zobrazit zprávy HTTP a HTTPS umožňuje zobrazit záhlaví a datovou část dat odeslaných do a ze služeb úložiště, které by vám umožní prozkoumat, jak klientská aplikace opakuje operace úložiště. Analyzátory protokolů, jako je Wireshark, pracují na úrovni paketů, což umožňuje zobrazit data TCP, což by vám umožnilo řešit problémy se ztracenými pakety a připojením. Analyzátor zpráv může pracovat na vrstvách HTTP i TCP.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Komplexní trasování
End-to-end trasování pomocí různých souborů protokolu je užitečná technika pro zkoumání potenciálních problémů. Informace o datu a čase z dat metrik můžete použít jako údaj o tom, kde začít hledat v souborech protokolu podrobné informace, které vám pomohou problém vyřešit.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Korelační data protokolu
Při prohlížení protokolů z klientských aplikací, trasování sítě a protokolování úložiště na straně serveru je důležité, aby bylo možné korelovat požadavky mezi různými soubory protokolu. Soubory protokolu obsahují řadu různých polí, které jsou užitečné jako identifikátory korelace. ID požadavku klienta je nejužitečnější pole, které se používá ke korelaci položek v různých protokolech. Někdy však může být užitečné použít ID požadavku serveru nebo časová razítka. V následujících částech jsou uvedeny další podrobnosti o těchto možnostech.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID požadavku klienta
Klientská knihovna úložiště automaticky generuje jedinečné ID požadavku klienta pro každý požadavek.

* V protokolu na straně klienta klienta klienta klienta vytvoří ID požadavku klienta se zobrazí v poli **ID požadavku klienta** každé položky protokolu vztahující se k požadavku.
* V trasování sítě, jako je například ten zachycený Fiddler, ID požadavku klienta je viditelný ve zprávách požadavku jako hodnota hlavičky http **x-ms-client-request-id.**
* V protokolu protokolování úložiště na straně serveru se ID požadavku klienta zobrazí ve sloupci ID požadavku klienta.

> [!NOTE]
> Je možné pro více požadavků na sdílení stejného ID požadavku klienta, protože klient může přiřadit tuto hodnotu (i když klientská knihovna úložiště přiřadí novou hodnotu automaticky). Když se klient pokusí, všechny pokusy sdílejí stejné ID požadavku klienta. V případě dávky odeslané z klienta má dávka jedno ID požadavku klienta.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID požadavku serveru
Služba úložiště automaticky generuje ID požadavků serveru.

* V protokolu protokolu protokolování úložiště na straně serveru se id požadavku serveru zobrazí ve sloupci **záhlaví ID požadavku.**
* Při trasování v síti, například v síti zachycené fiddlerem, se ID požadavku serveru zobrazí ve zprávách odpovědi jako hodnota hlavičky http **x-ms-request-id.**
* V protokolu na straně klienta klienta klienta klienta klienta klienta klienta vytvoří ID požadavku serveru se zobrazí ve **sloupci Text operace** pro položku protokolu zobrazující podrobnosti o odpovědi serveru.

> [!NOTE]
> Služba úložiště vždy přiřadí jedinečné ID požadavku serveru každému požadavku, který obdrží, takže každý pokus o opakování od klienta a každá operace zahrnutá v dávce má jedinečné ID požadavku serveru.
>
>

Pokud knihovna klienta úložiště vyvolá **storageException** v klientovi, **vlastnost RequestInformation** obsahuje **RequestResult** objekt, který obsahuje **Vlastnost ServiceRequestID.** Můžete také přistupovat **k requestresult** objektu z **OperationContext** instance.

Ukázka kódu níže ukazuje, jak nastavit vlastní **ClientRequestId** hodnotu připojením **OperationContext** objekt požadavek na službu úložiště. Také ukazuje, jak načíst hodnotu **ServerRequestId** ze zprávy odpovědi.

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

### <a name="timestamps"></a><a name="timestamps"></a>Časová razítka
Časová razítka můžete také použít k vyhledání souvisejících položek protokolu, ale buďte opatrní při zkosení hodin mezi klientem a serverem, které mohou existovat. Hledání plus nebo mínus 15 minut pro odpovídající položky na straně serveru na základě časového razítka na straně klienta. Nezapomeňte, že metadata objektu blob pro objekty BLOB obsahující metriky označuje časový rozsah pro metriky uložené v objektu blob. Tento časový rozsah je užitečný, pokud máte mnoho objektů BLOB metrik pro stejnou minutu nebo hodinu.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Pokyny pro řešení potíží
Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se vaše aplikace může setkat při používání služeb úložiště Azure. Pomocí následujícího seznamu vyhledejte informace týkající se konkrétního problému.

**Rozhodovací strom pro řešení potíží**

---
Souvisí váš problém s výkonem jedné ze služeb úložiště?

* [Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.]
* [Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.]
* [Metrika ukazuje vysokou hodnotu AverageServerLatency.]
* [Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]

---
Souvisí váš problém s dostupností jedné ze služeb úložiště?

* [Metrika ukazuje zvýšení u PercentThrottlingError.]
* [Metrika ukazuje zvýšení u PercentTimeoutError.]
* [Metrika ukazuje zvýšení u PercentNetworkError.]

---
 Přijímá klientská aplikace odpověď HTTP 4XX (například 404) ze služby úložiště?

* [Klient dostává stavové kódy HTTP 403 (Zakázáno)]
* [Klient dostává stavové kódy HTTP 404 (Nenalezeno)]
* [Klient dostává stavové kód HTTP 409 (Konflikt)]

---
[Metriky ukazují nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors]

---
[Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště]

---
[Dochází k neočekávaným restartováním virtuálních počítačů, které mají velký počet připojených virtuálních discích]

---
[Váš problém vzniká použitím emulátoru úložiště pro vývoj nebo testování]

---
[Dochází k problémům s instalací sady Azure SDK pro rozhraní .NET]

---
[Se službou úložiště máte jiný problém.]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.
Následující obrázek z nástroje monitorování [portálu Azure](https://portal.azure.com) ukazuje příklad, kde **AverageE2ELatency** je výrazně vyšší než **AverageServerLatency**.

![][4]

Služba úložiště vypočítá pouze metriku **AverageE2ELatency** pro úspěšné požadavky a na rozdíl od **AverageServerLatency**zahrnuje čas, který klient potřebuje k odeslání dat a přijetí potvrzení ze služby úložiště. Proto rozdíl mezi **AverageE2ELatency** a **AverageServerLatency** může být buď z důvodu klientské aplikace je pomalé reagovat, nebo z důvodu podmínek v síti.

> [!NOTE]
> Můžete také zobrazit **E2ELatency** a **ServerLatency** pro jednotlivé operace úložiště v datech protokolu protokolování úložiště.
>
>

#### <a name="investigating-client-performance-issues"></a>Zkoumání problémů s výkonem klienta
Možné důvody pro klienta reagovat pomalu patří s omezený počet dostupných připojení nebo podprocesů, nebo je nedostatek prostředků, jako je procesor, paměť nebo šířku pásma sítě. Problém můžete vyřešit úpravou klientského kódu tak, aby byl efektivnější (například pomocí asynchronních volání služby úložiště) nebo pomocí většího virtuálního počítače (s větším jádry a více paměti).

Pro tabulky a fronty služby Nagle algoritmus může také způsobit vysoké **AverageE2ELatency** ve srovnání s **AverageServerLatency**: další informace naleznete v části post [Nagle algoritmus není přátelský k malé požadavky](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Algoritmus Nagle můžete zakázat v kódu pomocí třídy **ServicePointManager** v **System.Net** oboru názvů. Měli byste to provést před provedením volání do tabulky nebo fronty služby ve vaší aplikaci, protože to nemá vliv na připojení, které jsou již otevřeny. Následující příklad pochází z **metody Application_Start** v roli pracovníka.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Měli byste zkontrolovat protokoly na straně klienta, abyste zjistili, kolik požadavků klientská aplikace odesílá, a zkontrolujte obecné problémy s výkonem související s rozhraním .NET ve vašem klientovi, jako je procesor, uvolňování paměti .NET, využití sítě nebo paměť. Jako výchozí bod pro řešení potíží s klientskými aplikacemi .NET naleznete [v tématu Ladění, trasování a profilování](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Zkoumání problémů s latencí sítě
Obvykle je špičková latence end-to-end způsobená sítí způsobená přechodovými podmínkami. Můžete prozkoumat přechodné i trvalé problémy se sítí, jako jsou vynecháné pakety pomocí nástrojů, jako je Wireshark nebo Microsoft Message Analyzer.

Další informace o použití technologie Wireshark k řešení problémů se sítí naleznete v[tématu " Dodatek 2: Použití technologie Wireshark k zachycení síťového provozu]".

Další informace o použití nástroje Microsoft Message Analyzer k řešení problémů se sítí naleznete v[tématu " Dodatek 3: Použití nástroje Microsoft Message Analyzer k zachycení síťového provozu]" .

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.
V tomto scénáři nejpravděpodobnější příčinou je zpoždění v požadavcích na úložiště dosažení služby úložiště. Měli byste prozkoumat, proč požadavky od klienta nejsou dělat to až do služby objektů blob.

Jedním z možných důvodů pro klienta zpoždění odesílání požadavků je, že existuje omezený počet dostupných připojení nebo podprocesů.

Zkontrolujte také, zda klient provádí více opakování a prozkoumejte důvod, pokud je. Chcete-li zjistit, zda klient provádí více opakování, můžete:

* Zkontrolujte protokoly Analýzy úložiště. Pokud dochází k více opakovaným pokusům, zobrazí se více operací se stejným ID požadavku klienta, ale s různými ID požadavků serveru.
* Zkontrolujte protokoly klienta. Podrobné protokolování bude znamenat, že došlo k opakování.
* Ladit kód a zkontrolujte vlastnosti **OperationContext** objekt u svého přidruženého k požadavku. Pokud byla operace opakována, vlastnost **RequestResults** bude obsahovat více jedinečných ID požadavků serveru. Můžete také zkontrolovat počáteční a koncový čas pro každý požadavek. Další informace naleznete v ukázce kódu v části [ID požadavku serveru].

Pokud nejsou žádné problémy v klientovi, měli byste prozkoumat potenciální problémy sítě, jako je například ztráta paketů. K prozkoumání problémů se sítí můžete použít nástroje jako Wireshark nebo Microsoft Message Analyzer.

Další informace o použití technologie Wireshark k řešení problémů se sítí naleznete v[tématu " Dodatek 2: Použití technologie Wireshark k zachycení síťového provozu]".

Další informace o použití nástroje Microsoft Message Analyzer k řešení problémů se sítí naleznete v[tématu " Dodatek 3: Použití nástroje Microsoft Message Analyzer k zachycení síťového provozu]" .

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageServerLatency.
V případě vysoké **AverageServerLatency** pro požadavky na stahování objektů blob, měli byste použít protokoly protokoly protokolů protokolování úložiště zobrazíte, pokud existují opakované požadavky na stejný objekt blob (nebo sadu objektů BLOB). Pro požadavky na nahrávání objektů blob byste měli zjistit, jakou velikost bloku klient používá (například bloky menší než 64 k Velikostmůže mít za následek režii, pokud čtení jsou také v méně než 64 kB bloky) a pokud více klientů jsou nahrávání bloků do stejného objektu blob paralelně. Měli byste také zkontrolovat metriky za minutu pro špičky v počtu požadavků, které mají za následek překročení cílů škálovatelnosti za sekundu: viz také "[Metriky ukazují zvýšení PercentTimeoutError]."

Pokud vidíte vysoké **AverageServerLatency** pro požadavky na stahování objektů blob při opakovaných požadavcích na stejný objekt blob nebo sadu objektů BLOB, měli byste zvážit ukládání těchto objektů BLOB pomocí Azure Cache nebo Sítě pro doručování obsahu Azure (CDN). Pro odesílání požadavků můžete zlepšit propustnost pomocí větší velikostbloku. Pro dotazy na tabulky je také možné implementovat ukládání do mezipaměti na straně klienta u klientů, kteří provádějí stejné operace dotazu a kde se data často nemění.

Vysoké **hodnoty AverageServerLatency** mohou být také příznakem špatně navržených tabulek nebo dotazů, které vedou k operacím skenování nebo které následují anti-vzor typu připojit/předřadit. Další informace naleznete v tématu "[Metriky ukazují zvýšení PercentThrottlingError]".

> [!NOTE]
> Úplný kontrolní seznam s kontrolním seznamem najdete zde: [Kontrolní seznam výkonu úložiště Microsoft Azure a škálovatelnosti](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Dochází k neočekávaným zpožděním při doručování zpráv ve frontě
Pokud dochází ke zpoždění mezi časem, kdy aplikace přidá zprávu do fronty, a časem, který bude k dispozici pro čtení z fronty, měli byste provést následující kroky k diagnostice problému:

* Ověřte, zda aplikace úspěšně přidává zprávy do fronty. Zkontrolujte, zda aplikace není opakování **AddMessage** metoda několikrát před úspěšné. Protokoly knihovny klienta úložiště zobrazí všechny opakované opakování operací úložiště.
* Ověřte, že mezi rolí pracovního procesu, která přidá zprávu do fronty, a rolí pracovního procesu, která čte zprávu z fronty, není žádné zkosení hodin, které by vypadalo, jako by došlo ke zpoždění zpracování.
* Zkontrolujte, zda role pracovního procesu, který čte zprávy z fronty selhává. Pokud klient fronty volá **GetMessage** metoda, ale neodpoví s potvrzením, zpráva zůstane neviditelné ve frontě, dokud nevyprší **neviditelnostTimeout** období. V tomto okamžiku zpráva bude k dispozici pro zpracování znovu.
* Zkontrolujte, zda délka fronty v průběhu času roste. Tato situace může nastat, pokud nemáte dostatek pracovníků k dispozici pro zpracování všech zpráv, které ostatní pracovníci jsou umístění do fronty. Zkontrolujte také metriky, zda se nezdaří požadavky na odstranění a počet vyřazení zpráv, což může znamenat opakované neúspěšné pokusy o odstranění zprávy.
* Zkontrolujte protokoly protokolů protokolů protokolu úložiště pro všechny operace fronty, které mají vyšší než očekávané hodnoty **E2ELatency** a **ServerLatency** po delší časové období než obvykle.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metrika ukazuje zvýšení u PercentThrottlingError.
Omezení chybdochází při překročení cíle škálovatelnosti služby úložiště. Služba úložiště omezení zajistit, že žádný jeden klient nebo klient můžete používat službu na úkor ostatních. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro účty standardníúložiště](scalability-targets-standard-account.md) podrobnosti o škálovatelnost cíle pro účty úložiště a cíle výkonu pro oddíly v rámci účtů úložiště.

Pokud **percentthrottlingerror** metrika zobrazit zvýšení procenta požadavků, které selhávají s chybou omezení, je třeba prozkoumat jeden ze dvou scénářů:

* [Přechodné zvýšení chyby PercentThrottlingError]
* [Trvalé zvýšení chyby PercentThrottlingError]

Zvýšení **PercentThrottlingError** často dochází ve stejnou dobu jako zvýšení počtu požadavků na úložiště, nebo při načítání testování aplikace. To se může také projevit v klientovi jako "503 Server Busy" nebo "500 Operace Timeout" HTTP stavové zprávy z operací úložiště.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Přechodné zvýšení chyby PercentThrottlingError
Pokud vidíte špičky v hodnotě **PercentThrottlingError,** které se shodují s období vysoké aktivity pro aplikaci, implementovat exponenciální (není lineární) back-off strategie pro opakování v klientovi. Back-off opakování snížit okamžité zatížení oddílu a pomoci vaší aplikaci vyhladit špičky v provozu. Další informace o implementaci zásad opakování pomocí knihovny klienta úložiště naleznete v [oboru názvů Microsoft.Azure.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Můžete také zobrazit špičky v hodnotě **PercentThrottlingError,** které se neshodují s obdobími vysoké aktivity pro aplikaci: nejpravděpodobnější příčinou je zde přesouvání oddílů služby úložiště pro zlepšení vyrovnávání zatížení.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Trvalé zvýšení chyby PercentThrottlingError
Pokud vidíte trvale vysokou hodnotu pro **PercentThrottlingError** po trvalé zvýšení objemu transakcí nebo při provádění počáteční zátěžové testy na vaší aplikaci, pak je třeba vyhodnotit, jak vaše aplikace používá oddíly úložiště a zda se blíží cíle škálovatelnosti pro účet úložiště. Například pokud vidíte omezení chyby ve frontě (což se počítá jako jeden oddíl), pak byste měli zvážit použití dalších front k rozložení transakcí mezi více oddílů. Pokud se v tabulce zobrazují chyby omezení, je třeba zvážit použití jiného schématu dělení k rozložení transakcí mezi více oddílů pomocí širšího rozsahu hodnot klíče oddílu. Jednou z běžných příčin tohoto problému je předkladatelé/připojit anti-vzor, kde vyberete datum jako klíč oddílu a pak všechna data v určitý den je zapsán do jednoho oddílu: při zatížení, to může mít za následek zápis kritický bod. Zvažte jiný návrh dělení nebo vyhodnoťte, zda může být lepším řešením použití úložiště objektů blob. Zkontrolujte také, zda dochází k omezení v důsledku špičky v provozu a prozkoumejte způsoby vyhlazení vzor požadavků.

Pokud distribuujete transakce mezi více oddílů, musíte si stále být vědomi omezení škálovatelnosti nastavených pro účet úložiště. Například pokud jste použili deset front každý zpracování maximálně 2 000 zpráv 1 KB za sekundu, budete na celkový limit 20 000 zpráv za sekundu pro účet úložiště. Pokud potřebujete zpracovat více než 20 000 entit za sekundu, měli byste zvážit použití více účtů úložiště. Měli byste také mít na paměti, že velikost vašich požadavků a entit má vliv na to, kdy služba úložiště omezuje vaše klienty: pokud máte větší požadavky a entity, můžete být omezeni dříve.

Neefektivní návrh dotazu může také způsobit, že dosáhnete omezení škálovatelnosti pro oddíly tabulky. Například dotaz s filtrem, který vybere pouze jedno procento entit v oddílu, ale který prohledá všechny entity v oddílu bude muset přistupovat ke každé entitě. Každá čtená entita se započítává do celkového počtu transakcí v tomto oddílu. proto můžete snadno dosáhnout cíle škálovatelnosti.

> [!NOTE]
> Testování výkonu by měl odhalit všechny návrhy dotazů neefektivní ve vaší aplikaci.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metrika ukazuje zvýšení u PercentTimeoutError.
Metriky ukazují zvýšení **PercentTimeoutError** pro jednu ze služeb úložiště. Současně klient obdrží velký objem "500 Operace Timeout" HTTP stavové zprávy z operací úložiště.

> [!NOTE]
> Chyby časového období se mohou dočasně zobrazit jako požadavky na vyrovnávání zatížení služby úložiště přesunutím oddílu na nový server.
>
>

Metrika **PercentTimeoutError** je agregace následujících metrik: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**a **SASServerTimeoutError**.

Časové výčasové opony serveru jsou způsobeny chybou na serveru. K časovým limitům klienta dochází, protože operace na serveru překročila časový limit určený klientem. například klient pomocí knihovny klienta úložiště můžete nastavit časový čas pro operaci pomocí **ServerTimeout** vlastnost **QueueRequestOptions třídy.**

Časové výtky serveru označují problém se službou úložiště, který vyžaduje další šetření. Pomocí metrik můžete zjistit, zda jsou dosažení omezení škálovatelnosti pro službu a identifikovat všechny špičky v provozu, které mohou být příčinou tohoto problému. Pokud je problém přerušovaný, může to být způsobeno aktivitou vyrovnávání zatížení ve službě. Pokud je problém trvalý a není způsoben vaší aplikace bít omezení škálovatelnosti služby, měli byste vyvolat problém podpory. Pro časové opony klienta, musíte rozhodnout, zda časový čas je nastavena na příslušnou hodnotu v klientovi a změnit hodnotu časového času nastavenou v klientovi nebo prozkoumat, jak můžete zlepšit výkon operací ve službě úložiště, například optimalizací dotazů na tabulka nebo zmenšením velikosti zpráv.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metrika ukazuje zvýšení u PercentNetworkError.
Metriky ukazují zvýšení **PercentNetworkError** pro jednu ze služeb úložiště. Metrika **PercentNetworkError** je agregace následujících metrik: **NetworkError**, **AnonymousNetworkError**a **SASNetworkError**. K nim dochází, když služba úložiště zjistí chybu sítě, když klient provede požadavek na úložiště.

Nejčastější příčinou této chyby je odpojení klienta před vypršením časového limitu ve službě úložiště. Prozkoumejte kód ve vašem klientovi, abyste pochopili, proč a kdy se klient odpojí od služby úložiště. Můžete také použít Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumat problémy s připojením k síti z klienta. Tyto nástroje jsou popsány v [přílohách].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klient dostává stavové kódy HTTP 403 (Zakázáno)
Pokud vaše klientská aplikace způsobuje chyby HTTP 403 (Zakázáno), pravděpodobnou příčinou je, že klient používá při odesílání žádosti na úložiště prošlý sdílený přístupový podpis (SAS) (i když další možné příčiny zahrnují nesoulad hodin, neplatné klíče a prázdné záhlaví). Pokud je příčinou vypršení platnosti klíče SAS, neuvidíte žádné záznamy v protokolu úložiště na straně serveru. V následující tabulce je uvedena ukázka z protokolu na straně klienta generovaná klientskou knihovnou úložiště, která ilustruje tento problém:

| Zdroj | Podrobnosti | Podrobnosti | ID požadavku klienta | Text operace |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Informace |3 |85d077ab-... |Spuštění operace s umístěním Primární podle režimu umístění PrimárníPouze. |
| Microsoft.Azure.Storage |Informace |3 |85d077ab -... |Spuštění synchronního požadavku na<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Informace |3 |85d077ab -... |Čekání na odpověď. |
| Microsoft.Azure.Storage |Upozornění |2 |85d077ab -... |Výjimka vyvolána při čekání na odpověď: Vzdálený server vrátil chybu: (403) Zakázáno. |
| Microsoft.Azure.Storage |Informace |3 |85d077ab -... |Byla přijata odpověď. Stavový kód = 403, ID požadavku = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Upozornění |2 |85d077ab -... |Výjimka vyvolána během operace: Vzdálený server vrátil chybu: (403) Zakázáno.. |
| Microsoft.Azure.Storage |Informace |3 |85d077ab -... |Kontrola, zda má být operace opakována. Počet opakování = 0, stavový kód HTTP = 403, Výjimka = Vzdálený server vrátil chybu: (403) Zakázáno.. |
| Microsoft.Azure.Storage |Informace |3 |85d077ab -... |Další umístění bylo nastaveno na Primární, na základě režimu umístění. |
| Microsoft.Azure.Storage |Chyba |1 |85d077ab -... |Zásady opakování nepovolily opakování. Selhání se vzdáleným serverem vrátil chybu: (403) Zakázáno. |

V tomto scénáři byste měli zjistit, proč vyprší platnost tokenu SAS před klient odešle token na server:

* Obvykle byste neměli nastavit čas zahájení, když vytváříte SAS pro okamžité použití. Pokud existují malé časové rozdíly mezi hostitelem generujícím SAS s použitím aktuálního času a mezi službou úložiště, je možné, že služba úložiště přijme SAS, který ještě není platný.
* Nenastavujte ani velmi krátkou dobu vypršení platnosti SAS. Opět, malé časové rozdíly mezi hostitelem generujícím SAS a službou úložiště můžou vést k předčasnému zdánlivému vypršení platnosti SAS.
* Odpovídá parametr verze v klíči SAS (například **sv=2015-04-05)** verzi klientské knihovny úložiště, kterou používáte? Doporučujeme vždy používat nejnovější verzi [klientské knihovny úložiště](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Když znovu vygenerujete přístupové klíče k úložišti, může to zneplatnit stávající tokeny SAS. Tento problém může nastat, když vygenerujete tokeny SAS s dlouhou dobou vypršení platnosti pro uložení v mezipaměti u klientů.

Pokud k vygenerování tokenů SAS používáte klientskou knihovnu úložiště, je vytvoření platného tokenu snadné. Pokud však používáte rozhraní REST API úložiště a vytváříte tokeny SAS ručně, přečtěte si část [Delegování přístupu pomocí sdíleného přístupového podpisu](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klient dostává stavové kódy HTTP 404 (Nenalezeno)
Pokud klientská aplikace obdrží od server stavový kód HTTP 404 (Nenalezeno), znamená to, že objekt, který se klient pokouší použít (například entita, tabulka, objekt blob, kontejner nebo fronta), ve službě úložiště neexistuje. Pro to může existovat několik důvodů, například:

* [Klient nebo jiný proces už objekt odstranil]
* [Problém s ověřením sdíleného přístupového podpisu (SAS)]
* [JavaScriptový kód na straně klienta nemá oprávnění pro přístup k objektu]
* [Chyba sítě]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klient nebo jiný proces už objekt odstranil
Ve scénářích, kde se klient pokouší číst, aktualizovat nebo odstranit data ve službě úložiště, je obvykle snadné identifikovat v protokolech na straně serveru předchozí operaci, která odstranila daný objekt ze služby úložiště. Data protokolu často ukazují, že jiný uživatel nebo proces odstranil objekt. V protokolu protokolu protokolování úložiště na straně serveru se sloupce typu operace a požadovaný objekt-klíč zobrazí, když klient odstranil objekt.

Ve scénáři, kde se klient pokouší vložit objekt, nemusí být okamžitě zřejmé, proč to má za následek odpověď HTTP 404 (Nebyl nalezen) vzhledem k tomu, že klient vytváří nový objekt. Pokud však klient vytváří objekt blob, musí být schopen najít kontejner objektů blob, pokud klient vytváří zprávu, musí být schopen najít frontu a pokud klient přidává řádek, musí být schopen najít tabulku.

Protokol na straně klienta můžete použít z klientské knihovny úložiště získat podrobnější informace o tom, kdy klient odešle konkrétní požadavky na službu úložiště.

Následující protokol na straně klienta generovaný knihovnou klienta úložiště ilustruje problém, když klient nemůže najít kontejner pro objekt blob, který vytváří. Tento protokol obsahuje podrobnosti o následujících operacích úložiště:

| ID požadavku | Operace |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metoda odstranit kontejner objektů blob. Všimněte si, že tato operace obsahuje **požadavek HEAD** ke kontrole existence kontejneru. |
| e2d06d78... |**CreateIfNotExists** metoda k vytvoření kontejneru objektů blob. Všimněte si, že tato operace zahrnuje **požadavek HEAD,** který kontroluje existenci kontejneru. **HEAD** vrátí zprávu 404, ale pokračuje. |
| de8b1c3c-... |**UploadFromStream** metoda k vytvoření objektu blob. Požadavek **PUT** se nezdaří se zprávou 404 |

Záznamy protokolu:

| ID požadavku | Text operace |
| --- | --- |
| 07b26a5d-... |Spuštění synchronního `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`požadavku na soubor . |
| 07b26a5d-... |StringToSign = HLAVA............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Út, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Byla přijata odpověď. Stavový kód = 200, ID požadavku = eeead849-... Content-MD5 = , &quot;ETag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Hlavičky odpovědí byly úspěšně zpracovány a pokračovaly ve zbytku operace. |
| 07b26a5d-... |Stahování těla odezvy. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| 07b26a5d-... |Spuštění synchronního `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`požadavku na soubor . |
| 07b26a5d-... |StringToSign = ODSTRANIT............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Út, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Čekání na odpověď. |
| 07b26a5d-... |Byla přijata odpověď. Stavový kód = 202, ID požadavku = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Hlavičky odpovědí byly úspěšně zpracovány a pokračovaly ve zbytku operace. |
| 07b26a5d-... |Stahování těla odezvy. |
| 07b26a5d-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Spuštění asynchronního `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`požadavku na soubor .</td> |
| e2d06d78-... |StringToSign = HLAVA............ x-ms-client-request-id:e2d06d78-.... x-ms-date:Út, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Spuštění synchronního `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`požadavku na soubor . |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ=........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-date:Út, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Příprava na zápis dat požadavku. |
| e2d06d78-... |Výjimka vyvolána při čekání na odpověď: Vzdálený server vrátil chybu: (404) Nebyl nalezen.. |
| e2d06d78-... |Byla přijata odpověď. Stavový kód = 404, ID požadavku = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Hlavičky odpovědí byly úspěšně zpracovány a pokračovaly ve zbytku operace. |
| e2d06d78-... |Stahování těla odezvy. |
| e2d06d78-... |Operace byla úspěšně dokončena. |
| e2d06d78-... |Spuštění asynchronního `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`požadavku na soubor . |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-client-request-id:e2d06d78-.... x-ms-date:Út, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Čekání na odpověď. |
| de8b1c3c-... |Zápis dat požadavku. |
| de8b1c3c-... |Čekání na odpověď. |
| e2d06d78-... |Výjimka vyvolána při čekání na odpověď: Vzdálený server vrátil chybu: (409) Conflict.. |
| e2d06d78-... |Byla přijata odpověď. Stavový kód = 409, ID požadavku = c27da20e-..., Obsah-MD5 = , ETag = . |
| e2d06d78-... |Stahování těla odpovědi na chybu. |
| de8b1c3c-... |Výjimka vyvolána při čekání na odpověď: Vzdálený server vrátil chybu: (404) Nebyl nalezen.. |
| de8b1c3c-... |Byla přijata odpověď. Stavový kód = 404, ID požadavku = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Výjimka vyvolána během operace: Vzdálený server vrátil chybu: (404) Nebyl nalezen.. |
| de8b1c3c-... |Zásady opakování nepovolily opakování. Selhání se vzdáleným serverem vrátil chybu: (404) Nebyl nalezen.. |
| e2d06d78-... |Zásady opakování nepovolily opakování. Selhání se vzdáleným serverem vrátil chybu: (409) Conflict.. |

V tomto příkladu protokol ukazuje, že klient je prokládání požadavky z **CreateIfNotExists** metoda (ID požadavku e2d06d78...) s požadavky z **UploadFromStream** metoda (de8b1c3c3c-...). K tomuto prokládání dochází, protože klientská aplikace se odvolává na tyto metody asynchronně. Upravte asynchronní kód v klientovi a ujistěte se, že vytvoří kontejner před pokusem o odeslání dat do objektu blob v tomto kontejneru. V ideálním případě byste měli vytvořit všechny kontejnery předem.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Problém s ověřením sdíleného přístupového podpisu (SAS)
Pokud se klientská aplikace pokusí použít klíč SAS, který neobsahuje potřebná oprávnění pro operaci, vrátí služba úložiště klientovi zprávu HTTP 404 (Nebylnalezen). Současně se také zobrazí nenulová hodnota pro **SASAuthorizationError** v metrikách.

V následující tabulce je uvedena ukázková zpráva protokolu na straně serveru ze souboru protokolu protokolování úložiště:

| Název | Hodnota |
| --- | --- |
| Požadavek na počáteční čas | 2014-05-30T06:17:48.4473697Z |
| Typ operace     | GetBlobProperties            |
| Stav požadavku     | Chyba autorizace SAS        |
| Stavový kód HTTP   | 404                            |
| Typ ověřování| Sas                          |
| Typ služby       | Objekt blob                         |
| Adresa URL požadavku         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&&;sig=XXXXX api-version=2014-02-14 |
| Hlavička ID požadavku  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID požadavku klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Zjistěte, proč se klientská aplikace pokouší provést operaci, pro kterou nebyla udělena oprávnění.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>JavaScriptový kód na straně klienta nemá oprávnění pro přístup k objektu
Pokud používáte klienta JavaScript a služba úložiště vrací zprávy HTTP 404, zkontrolujte následující chyby JavaScriptu v prohlížeči:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Pomocí vývojářských nástrojů F12 v aplikaci Internet Explorer můžete sledovat zprávy vyměňované mezi prohlížečem a službou úložiště při řešení problémů javascriptu na straně klienta.
>
>

K těmto chybám dochází, protože webový prohlížeč implementuje stejné omezení zabezpečení [zásad původu,](https://www.w3.org/Security/wiki/Same_Origin_Policy) které brání webové stránce volat rozhraní API v jiné doméně z domény, ze které stránka pochází.

Chcete-li vyřešit problém javascriptu, můžete nakonfigurovat sdílení prostředků mezi zdroji (CORS) pro službu úložiště, ke které klient přistupuje. Další informace najdete [v tématu podpora sdílení prostředků napříč zdroji (CORS) pro služby Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx).

Následující ukázka kódu ukazuje, jak nakonfigurovat službu blob tak, aby javascript uvolňoval v doméně Contoso pro přístup k objektu blob ve službě úložiště objektů blob:

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

#### <a name="network-failure"></a><a name="network-failure"></a>Selhání sítě
V některých případech může ztráta síťových paketů vést ke službě úložiště, která vrátí klientovi zprávy HTTP 404. Například když vaše klientská aplikace je odstranění entity ze služby tabulky se zobrazí klient vyvolat výjimku úložiště hlášení "HTTP 404 (Nenalezeno)" stavovou zprávu ze služby tabulky. Při zkoumání tabulky ve službě úložiště tabulek uvidíte, že služba odstranila entitu, jak bylo požadováno.

Podrobnosti o výjimce v klientovi zahrnují ID požadavku (7e84f12d...) přiřazené službou table service pro požadavek: tyto informace můžete použít k vyhledání podrobností požadavku v protokolech úložiště na straně serveru vyhledáním ve sloupci **hlavičky požadavku id** v souboru protokolu. Metriky můžete také použít k identifikaci, kdy dojde k chybám, jako je tato, a potom prohledávat soubory protokolu na základě času, kdy metriky zaznamenaly tuto chybu. Tato položka protokolu ukazuje, že odstranění se nezdařilo se stavovou zprávou "HTTP (404) Client Other Error". Stejná položka protokolu také obsahuje ID požadavku generované klientem ve sloupci **client-request-id** (813ea74f...).

Protokol na straně serveru také obsahuje jinou položku se stejnou hodnotou **id požadavku klienta** (813ea74f...) pro úspěšnou operaci odstranění pro stejnou entitu a od stejného klienta. Tato úspěšná operace odstranění proběhla velmi krátce před neúspěšným požadavkem na odstranění.

Nejpravděpodobnější příčinou tohoto scénáře je, že klient odeslal požadavek na odstranění entity službě table service, která byla úspěšná, ale neobdržela potvrzení ze serveru (pravděpodobně z důvodu dočasného problému se sítí). Klient pak automaticky zopakoval operaci (pomocí stejného **id požadavku klienta**) a toto opakování se nezdařilo, protože entita již byla odstraněna.

Pokud k tomuto problému dochází často, měli byste zjistit, proč klient nepřijímá potvrzení ze služby table service. Pokud je problém přerušovaný, měli byste vytvořit soutisk chyby HTTP (404) Not Found" a přihlásit ji do klienta, ale povolit klientovi pokračovat.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klient dostává stavové kód HTTP 409 (Konflikt)
V následující tabulce je uveden výpis z protokolu na straně serveru pro dvě operace **klienta: DeleteIfExists** bezprostředně následuje **CreateIfNotExists** pomocí stejného názvu kontejneru objektu blob. Každá operace klienta má za následek dva požadavky odeslané na server, nejprve požadavek **GetContainerProperties** ke kontrole, zda kontejner existuje, následovaný **deletecontainer** nebo **CreateContainer** požadavek.

| Časové razítko | Operace | Výsledek | Název kontejneru | ID požadavku klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |Vlastnosti GetContainer |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |Odstranit kontejner |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |Vlastnosti GetContainer |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |Vytvořitkontejner |409 |mmcont |bc881924-... |

Kód v klientské aplikaci odstraní a okamžitě znovu vytvoří kontejner objektů blob se stejným názvem: **CreateIfNotExists** metoda (ID požadavku klienta bc881924-...) nakonec selže s http 409 (konflikt) chyba. Když klient odstraní kontejner, objekt blob, tabulku nebo frontu, chvilku potrvá, než bude daný název opět volný.

Klientská aplikace by měla pokaždé, když vytvoří nový kontejner, použít jedinečný název, pokud je tento postup odstraňování a vytváření běžný.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Metriky ukazují nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors
**PercentSuccess** metrika zachycuje procento operací, které byly úspěšné na základě jejich kód stavu HTTP. Operace se stavovými kódy 2XX se počítají jako úspěšné, zatímco operace se stavovými kódy v rozsahu 3XX, 4XX a 5XX se počítají jako neúspěšné a snižují hodnotu **metriky PercentSuccess.** V souborech protokolu úložiště na straně serveru jsou tyto operace zaznamenány se stavem transakce **ClientOtherErrors**.

Je důležité si uvědomit, že tyto operace byly úspěšně dokončeny, a proto nemají vliv na jiné metriky, jako je například dostupnost. Některé příklady operací, které se úspěšně svedou, ale které mohou vést k neúspěšným stavovým kódům HTTP, zahrnují:

* **ResourceNotFound** (Nebyl nalezen 404), například z požadavku GET na objekt blob, který neexistuje.
* **ResourceAlreadyExists** (Conflict 409), například z operace **CreateIfNotExist,** kde prostředek již existuje.
* **ConditionNotMet** (Nezměněno 304), například z podmíněné operace, například když klient odešle hodnotu **ETag** a hlavičku HTTP **If-None-Match,** aby požadoval bitovou kopii pouze v případě, že byla aktualizována od poslední operace.

Seznam běžných kódů chyb rozhraní REST API, které služby úložiště vracejí, najdete na stránce [Běžné kódy chyb rozhraní REST API](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště
Pokud se v účtu úložiště zobrazí náhlé a neočekávané změny využití kapacity, můžete důvody prozkoumat tak, že se nejprve podíváte na metriky dostupnosti. Například zvýšení počtu neúspěšných požadavků na odstranění může vést ke zvýšení množství úložiště objektů blob, které používáte jako operace čištění specifické pro aplikaci, které jste očekávali, že uvolníte místo, nemusí fungovat podle očekávání (například proto, že tokeny SAS používané k uvolnění místa vypršely).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Váš problém vzniká použitím emulátoru úložiště pro vývoj nebo testování
Obvykle používáte emulátor úložiště během vývoje a testování, abyste se vyhnuli požadavku na účet úložiště Azure. Běžné problémy, které mohou nastat při použití emulátoru úložiště jsou:

* [Funkce "X" nefunguje v emulátoru úložiště]
* [Chyba "Hodnota pro jednu ze záhlaví PROTOKOLU HTTP není ve správném formátu" při použití emulátoru úložiště]
* [Spuštění emulátoru úložiště vyžaduje oprávnění správce]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funkce "X" nefunguje v emulátoru úložiště
Emulátor úložiště nepodporuje všechny funkce služeb úložiště Azure, jako je například souborová služba. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

Pro ty funkce, které emulátor úložiště nepodporuje, použijte službu úložiště Azure v cloudu.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Chyba "Hodnota pro jednu ze záhlaví PROTOKOLU HTTP není ve správném formátu" při použití emulátoru úložiště
Testujete aplikaci, která používá klientskou knihovnu úložiště proti emulátoru místního úložiště a volání metod, jako je **například CreateIfNotExists** selhání s chybovou zprávou "Hodnota pro jednu ze záhlaví PROTOKOLU HTTP není ve správném formátu." To znamená, že verze emulátoru úložiště, který používáte, nepodporuje verzi klientské knihovny úložiště, kterou používáte. Klientská knihovna úložiště přidá hlavičku **x-ms-version** ke všem požadavkům, které provede. Pokud emulátor úložiště nerozpozná hodnotu v hlavičce **verze x-ms,** požadavek odmítne.

Pomocí protokolů klienta knihovny úložiště můžete zobrazit hodnotu **hlavičky x-ms-version,** kterou odesílá. Můžete také zobrazit hodnotu **hlavičky x-ms-version,** pokud používáte Fiddler ke sledování požadavků z klientské aplikace.

K tomuto scénáři obvykle dochází, pokud nainstalujete a použijete nejnovější verzi klientské knihovny úložiště bez aktualizace emulátoru úložiště. Měli byste buď nainstalovat nejnovější verzi emulátoru úložiště, nebo použít cloudové úložiště namísto emulátoru pro vývoj a testování.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Spuštění emulátoru úložiště vyžaduje oprávnění správce
Při spuštění emulátoru úložiště budete vyzváni k zadání pověření správce. K tomu dochází pouze při inicializaci emulátoru úložiště poprvé. Po inicializaci emulátoru úložiště nepotřebujete oprávnění správce, abyste jej mohli znovu spustit.

Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md). Můžete také inicializovat emulátor úložiště v sadě Visual Studio, která bude také vyžadovat oprávnění správce.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Dochází k problémům s instalací sady Azure SDK pro rozhraní .NET
Při pokusu o instalaci sady SDK se nezdaří pokus o instalaci emulátoru úložiště v místním počítači. Protokol instalace obsahuje jednu z následujících zpráv:

* CAQuietExec: Chyba: Nelze získat přístup k instanci SQL
* CAQuietExec: Chyba: Nelze vytvořit databázi

Příčinou je problém s existující instalací LocalDB. Ve výchozím nastavení emulátor úložiště používá LocalDB k uchování dat při simulaci služeb úložiště Azure. Před pokusem o instalaci sady SDK můžete obnovit instanci LocalDB spuštěním následujících příkazů v okně příkazového řádku.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Příkaz **delete** odebere všechny staré databázové soubory z předchozích instalací emulátoru úložiště.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Se službou úložiště máte jiný problém.
Pokud předchozí části řešení potíží neobsahují problém, který máte se službou úložiště, měli byste přijmout následující přístup k diagnostice a řešení problému.

* Zkontrolujte metriky a zjistěte, zda došlo k nějaké změně z očekávaného chování základní linie. Z metriky můžete zjistit, zda je problém přechodné nebo trvalé a které operace úložiště problém ovlivňuje.
* Informace o metrikách můžete použít k vyhledávání v datech protokolu na straně serveru, kde naleznete podrobnější informace o všech chybách, ke kterým dochází. Tyto informace vám mohou pomoci s řešením potíží a vyřešením problému.
* Pokud informace v protokolech na straně serveru není dostatečná k řešení problému úspěšně, můžete použít protokoly klientské knihovny úložiště klienta prozkoumat chování klientské aplikace a nástroje, jako je Šumař, Wireshark a Microsoft Message Analyzer prozkoumat vaši síť.

Další informace o používání šumavy naleznete v[tématu " Dodatek 1: Použití šumavy k zachycení přenosu HTTP a HTTPS]" .

Další informace o používání technologie Wireshark naleznete v tématu "[Dodatek 2: Použití wiresharku k zachycení síťového provozu]".

Další informace o použití nástroje Microsoft Message Analyzer naleznete v[tématu " Dodatek 3: Použití nástroje Microsoft Message Analyzer k zachycení síťového provozu]" .

## <a name="appendices"></a><a name="appendices"></a>Dodatky
Dodatky popisují několik nástrojů, které můžete považovat za užitečné při diagnostice a řešení problémů s Azure Storage (a dalšími službami). Tyto nástroje nejsou součástí Služby Azure Storage a některé jsou produkty třetích stran. Jako takové nástroje popsané v těchto přílohách nejsou zahrnuty do žádné smlouvy o podpoře, kterou můžete mít s Microsoft Azure nebo Azure Storage, a proto jako součást procesu hodnocení byste měli prozkoumat možnosti licencování a podpory, které jsou k dispozici od poskytovatelů těchto nástrojů.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Dodatek 1: Použití Šumař zachytit HTTP a HTTPS provozu
[Fiddler](https://www.telerik.com/fiddler) je užitečný nástroj pro analýzu přenosu HTTP a HTTPS mezi klientskou aplikací a službou úložiště Azure, kterou používáte.

> [!NOTE]
> Fiddler může dekódovat provoz HTTPS; Měli byste si pečlivě přečíst dokumentaci Fiddler, abyste pochopili, jak to dělá, a pochopili důsledky zabezpečení.
>
>

Tento dodatek poskytuje stručný návod, jak nakonfigurovat Šumař zachytit provoz mezi místním počítačem, kde jste nainstalovali Šumař a služby úložiště Azure.

Po spuštění Šumař začne zachycovat provoz HTTP a HTTPS v místním počítači. Níže jsou uvedeny některé užitečné příkazy pro ovládání Šumař:

* Zastavte a začněte zachycovat provoz. V hlavní nabídce přejděte na **Soubor** a pak klikněte na **Zachytit provoz** a přepněte zapínání a vypínání zapínání a vypínání.
* Uložte zachycená dopravní data. V hlavní nabídce přejděte na **Soubor**, klepněte na tlačítko **Uložit**a potom klepněte na **tlačítko Všechny relace**: to vám umožní uložit provoz do souboru archivu relací. Archiv relací můžete později znovu načíst pro analýzu nebo ji odeslat, pokud o to požádáte podporu společnosti Microsoft.

Chcete-li omezit objem provozu, který Šumař zachycuje, můžete použít filtry, které nakonfigurujete na kartě **Filtry.** Následující snímek obrazovky ukazuje filtr, který zachycuje pouze provoz odeslaný do koncového bodu **úložiště contosoemaildist.table.core.windows.net:**

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Dodatek 2: Použití Wireshark zachytit síťový provoz
[Wireshark](https://www.wireshark.org/) je analyzátor síťových protokolů, který umožňuje zobrazit podrobné informace o paketech pro širokou škálu síťových protokolů.

Následující postup ukazuje, jak zachytit podrobné informace o paketech pro provoz z místního počítače, kde jste nainstalovali Wireshark do služby table service ve vašem účtu úložiště Azure.

1. Spusťte Wireshark na místním počítači.
2. V části **Start** vyberte rozhraní místní sítě nebo rozhraní, která jsou připojena k Internetu.
3. Klepněte na **položku Možnosti zachycení**.
4. Přidejte filtr do textového pole **Filtr pro digitalizaci.** **Například contosoemaildist.table.core.windows.net hostitele** nakonfiguruje Wireshark tak, aby zaznamenával pouze pakety odeslané do koncového bodu služby table service nebo z ní v účtu úložiště **contosoemaildist.** Prohlédněte si [úplný seznam filtrů pro digitalizaci](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Klepněte na tlačítko **Start**. Wireshark nyní zachytí všechny pakety odeslané do nebo z koncového bodu služby table service při použití klientské aplikace v místním počítači.
6. Po dokončení klikněte v hlavní nabídce na **capture** a potom na **Zastavit**.
7. Chcete-li uložit zachycená data do souboru zachycení wiresharku, klepněte v hlavní nabídce na **položku Soubor** a potom na **tlačítko Uložit**.

WireShark zvýrazní všechny chyby, které existují v okně **packetlist.** Můžete také použít okno **Informace o expertovi** (klikněte na **Analyzovat**, potom na informace **o expertovi)** a zobrazit souhrn chyb a upozornění.

![][7]

Můžete také zvolit zobrazení dat TCP tak, jak je vidí aplikační vrstva, kliknutím pravým tlačítkem myši na data TCP a výběrem **možnosti Sledovat tcp stream**. To je užitečné, pokud jste zachytili výpis bez filtru sběru. Další informace naleznete [v tématu Following TCP Streams](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Další informace o používání technologie Wireshark naleznete v [příručce Wireshark Users Guide](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Dodatek 3: Použití analyzátoru zpráv společnosti Microsoft k zachycení síťového provozu
Analyzátor zpráv společnosti Microsoft můžete použít k zachycení přenosu HTTP a HTTPS podobným způsobem jako Fiddler a k zachycení síťového provozu podobným způsobem jako Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurace relace trasování webu pomocí nástroje Microsoft Message Analyzer
Chcete-li nakonfigurovat relaci trasování webu pro přenosy protokolu HTTP a HTTPS pomocí nástroje Microsoft Message Analyzer, spusťte aplikaci Microsoft Message Analyzer a v nabídce **Soubor** klepněte na tlačítko **Zachytit nebo trasovat**. V seznamu dostupných scénářů trasování vyberte **webový proxy server**. Potom v panelu **Konfigurace scénáře trasování** v textovém poli **HostnameFilter** přidejte názvy koncových bodů úložiště (tyto názvy můžete vyhledat na [webu Azure Portal).](https://portal.azure.com) Pokud je například název vašeho účtu úložiště Azure **contosodata**, měli byste do textového pole **HostnameFilter** přidat následující:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Znak mezery odděluje názvy hostitelů.
>
>

Až budete připraveni začít shromažďovat data trasování, klikněte na tlačítko **Začít s.**

Další informace o trasování **webového serveru Proxy** analyzátoru zpráv společnosti Microsoft naleznete v [tématu Microsoft-PEF-WebProxy Provider](https://technet.microsoft.com/library/jj674814.aspx).

Integrované trasování **webového serveru proxy** v nástroji Microsoft Message Analyzer je založeno na nástroji Šumař; může zachytit přenos HTTPS na straně klienta a zobrazit nešifrované zprávy HTTPS. Trasování **webového serveru proxy** funguje tak, že konfiguruje místní proxy server pro veškerý přenos protokolu HTTP a HTTPS, který mu poskytuje přístup k nešifrovaným zprávám.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostika problémů se sítí pomocí nástroje Microsoft Message Analyzer
Kromě použití trasování **webového proxy** programu Microsoft Message Analyzer k zachycení podrobností o přenosech http/https mezi klientskou aplikací a službou úložiště můžete také použít integrované trasování **vrstvy místní vazby** k zachycení informací o síťových paketech. To umožňuje zachytit data podobná tomu, které můžete zachytit pomocí Wireshark, a diagnostikovat problémy se sítí, jako jsou vynecháné pakety.

Následující snímek obrazovky ukazuje příklad trasování **vrstvy místní vazby** s **některými informačními** zprávami ve sloupci **DiagnosisTypes.** Kliknutím na ikonu ve sloupci **DiagnosisTypes** se zobrazí podrobnosti zprávy. V tomto příkladu server opakovaně vysílané zprávy #305 protože neobdržel potvrzení od klienta:

![][9]

Při vytváření relace trasování v nástroji Microsoft Message Analyzer můžete určit filtry, které sníží množství šumu v trasování. Na stránce **Capture / Trace,** kde definujete trasování, klikněte na odkaz **Konfigurovat** vedle **microsoft-Windows-NDIS-PacketCapture**. Následující snímek obrazovky ukazuje konfiguraci, která filtruje přenos protokolu TCP pro adresy IP tří služeb úložiště:

![][10]

Další informace o trasování vrstvy místního propojení analyzátoru zpráv společnosti Microsoft naleznete v [tématu Microsoft-PEF-NDIS-PacketCapture Provider](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Dodatek 4: Použití Excelu k zobrazení metrik a dat protokolu
Mnoho nástrojů vám umožní stahovat data metrik úložiště z úložiště tabulek Azure v odděleném formátu, který usnadňuje načítání dat do Excelu pro zobrazení a analýzu. Data protokolování úložiště z úložiště objektů blob Azure už je ve formátu s omezeným přístupem, který můžete načíst do Excelu. Budete však muset přidat příslušná záhlaví sloupců založená na informacích ve schématu tabulky [storage Analytics Log Format](https://msdn.microsoft.com/library/azure/hh343259.aspx) a Storage [Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Import dat protokolování úložiště do Excelu po stažení z úložiště objektů blob:

* V nabídce **Data** klikněte na **From Text**.
* Přejděte k souboru protokolu, který chcete zobrazit, a klepněte na tlačítko **Importovat**.
* V kroku 1 **Průvodce importem textu**vyberte **oddělovat .**

V kroku 1 **Průvodce importem textu**vyberte jako jediný oddělovač **středník** a jako **kvalifikátor textu**zvolte dvojité uvozovky . Pak klikněte na **Dokončit** a zvolte, kam chcete data do sešitu umístit.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Dodatek 5: Monitorování pomocí přehledů aplikací pro Azure DevOps
Funkci Přehledy aplikací pro Azure DevOps můžete také použít jako součást monitorování výkonu a dostupnosti. Tento nástroj může:

* Ujistěte se, že vaše webová služba je k dispozici a reaguje. Ať už je vaše aplikace webová stránka nebo aplikace pro zařízení, která používá webovou službu, může otestovat adresu URL každých několik minut z míst po celém světě a dát vám vědět, pokud se nějaký problém nejedná.
* Rychle diagnostikujte všechny problémy s výkonem nebo výjimky ve webové službě. Zjistěte, zda se procesor nebo jiné prostředky roztahují, získejte trasování zásobníku z výjimek a snadno prohledejte trasování protokolů. Pokud výkon aplikace klesne pod přijatelné limity, může vám společnost Microsoft poslat e-mail. Můžete sledovat webové služby .NET i Java.

Další informace naleznete na what [is application insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Další kroky

Další informace o analýzách ve Službě Azure Storage najdete v těchto zdrojích informací:

* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Analýzy úložiště](storage-analytics.md)
* [Metriky analýzy úložiště](storage-analytics-metrics.md)
* [Schéma tabulky metrik analýzy úložiště](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Protokoly analýzy úložiště](storage-analytics-logging.md)
* [Formát protokolu analýzy úložiště](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Úvod]: #introduction
[Jak je tato příručka organizována]: #how-this-guide-is-organized

[Sledování služby úložiště]: #monitoring-your-storage-service
[Stav monitorovací služby]: #monitoring-service-health
[Monitorovací kapacita]: #monitoring-capacity
[Dostupnost monitorování]: #monitoring-availability
[Sledování výkonu]: #monitoring-performance

[Diagnostika problémů s úložištěm]: #diagnosing-storage-issues
[Problémy se stavem služby]: #service-health-issues
[Problémy s výkonem]: #performance-issues
[Diagnostika chyb]: #diagnosing-errors
[Problémy s emulátorem úložiště]: #storage-emulator-issues
[Nástroje pro protokolování úložiště]: #storage-logging-tools
[Použití nástrojů pro protokolování sítě]: #using-network-logging-tools

[Komplexní trasování]: #end-to-end-tracing
[Korelační data protokolu]: #correlating-log-data
[ID požadavku klienta]: #client-request-id
[ID požadavku serveru]: #server-request-id
[Časová razítka]: #timestamps

[Pokyny pro řešení potíží]: #troubleshooting-guidance
[Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje vysokou hodnotu AverageServerLatency.]: #metrics-show-high-AverageServerLatency
[Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrika ukazuje zvýšení u PercentThrottlingError.]: #metrics-show-an-increase-in-PercentThrottlingError
[Přechodné zvýšení chyby PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Trvalé zvýšení chyby PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Metrika ukazuje zvýšení u PercentTimeoutError.]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrika ukazuje zvýšení u PercentNetworkError.]: #metrics-show-an-increase-in-PercentNetworkError

[Klient dostává stavové kódy HTTP 403 (Zakázáno)]: #the-client-is-receiving-403-messages
[Klient dostává stavové kódy HTTP 404 (Nenalezeno)]: #the-client-is-receiving-404-messages
[Klient nebo jiný proces už objekt odstranil]: #client-previously-deleted-the-object
[Problém s ověřením sdíleného přístupového podpisu (SAS)]: #SAS-authorization-issue
[JavaScriptový kód na straně klienta nemá oprávnění pro přístup k objektu]: #JavaScript-code-does-not-have-permission
[Chyba sítě]: #network-failure
[Klient dostává stavové kód HTTP 409 (Konflikt)]: #the-client-is-receiving-409-messages

[Metriky ukazují nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors]: #metrics-show-low-percent-success
[Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště]: #capacity-metrics-show-an-unexpected-increase
[Váš problém vzniká použitím emulátoru úložiště pro vývoj nebo testování]: #your-issue-arises-from-using-the-storage-emulator
[Funkce "X" nefunguje v emulátoru úložiště]: #feature-X-is-not-working
[Chyba "Hodnota pro jednu ze záhlaví PROTOKOLU HTTP není ve správném formátu" při použití emulátoru úložiště]: #error-HTTP-header-not-correct-format
[Spuštění emulátoru úložiště vyžaduje oprávnění správce]: #storage-emulator-requires-administrative-privileges
[Dochází k problémům s instalací sady Azure SDK pro rozhraní .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Se službou úložiště máte jiný problém.]: #you-have-a-different-issue-with-a-storage-service

[Dodatky]: #appendices
[Dodatek 1: Použití Šumař zachytit HTTP a HTTPS provozu]: #appendix-1
[Dodatek 2: Použití Wireshark zachytit síťový provoz]: #appendix-2
[Dodatek 3: Použití analyzátoru zpráv společnosti Microsoft k zachycení síťového provozu]: #appendix-3
[Dodatek 4: Použití Excelu k zobrazení metrik a dat protokolu]: #appendix-4
[Dodatek 5: Monitorování pomocí přehledů aplikací pro Azure DevOps]: #appendix-5

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
