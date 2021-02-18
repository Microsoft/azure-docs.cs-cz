---
title: Monitorování, diagnostika a řešení potíží s Azure Storage | Microsoft Docs
description: K identifikaci, diagnostice a řešení potíží souvisejících s Azure Storage můžete využít funkce jako Analytics úložiště, protokolování na straně klienta a další nástroje třetích stran.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f68452bed69e4e174f126d259e8df6d2283e6cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585215"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Přehled
Diagnostiku a řešení problémů v distribuované aplikaci hostované v cloudovém prostředí může být složitější než v tradičních prostředích. Aplikace se dají nasadit v infrastruktuře PaaS nebo IaaS, místně, na mobilním zařízení nebo v některé kombinaci těchto prostředí. Síťový provoz vaší aplikace obvykle může procházet veřejné a privátní sítě a vaše aplikace může kromě jiných úložišť dat, jako jsou relační databáze a dokumenty dokumentů, používat víc technologií úložiště, jako jsou Microsoft Azure Storage tabulky, objekty blob, fronty nebo soubory.

Aby bylo možné tyto aplikace spravovat úspěšně, měli byste je monitorovat aktivně a pochopit, jak diagnostikovat a řešit potíže se všemi aspekty a jejich závislými technologiemi. Jako uživatel služby Azure Storage Services byste měli průběžně monitorovat služby úložiště, které vaše aplikace používá k jakýmkoli neočekávaným změnám v chování (například pomalejší než obvyklá doba odezvy), a používat protokolování ke shromažďování podrobnějších dat a k analýze problému podrobněji. Diagnostické informace, které získáte z monitorování a protokolování, vám pomůžou určit hlavní příčinu problému, ke kterému vaše aplikace došlo. Pak můžete problém vyřešit a určit vhodné kroky, které můžete provést, abyste ho mohli opravit. Azure Storage je základní služba Azure a tvoří důležitou část většiny řešení, která zákazníci nasazují do infrastruktury Azure. Azure Storage obsahuje možnosti pro zjednodušení monitorování, diagnostikování a odstraňování potíží s úložištěm ve vašich cloudových aplikacích.

* [Úvod]
  * [Jak je tato příručka organizována]
* [Monitorování služby úložiště]
  * [Stav služby monitorování]
  * [Kapacita monitorování]
  * [Monitorování dostupnosti]
  * [Monitorování výkonu]
* [Diagnostikování problémů s úložištěm]
  * [Problémy se stavem služby]
  * [Problémy s výkonem]
  * [Diagnostikování chyb]
  * [Problémy emulátoru úložiště]
  * [Nástroje protokolování úložiště]
  * [Používání nástrojů pro protokolování sítě]
* [Komplexní trasování]
  * [Korelace dat protokolu]
  * [ID požadavku klienta]
  * [ID žádosti serveru]
  * [Časová razítka]
* [Pokyny k odstraňování potíží]
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
  * [Metriky zobrazují nízké PercentSuccess nebo položky protokolu analýzy mají operace se stavem transakce ClientOtherErrors.]
  * [Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště.]
  * [K vašemu problému dochází při použití emulátoru úložiště pro vývoj nebo testování.]
  * [Narazíte na problémy s instalací sady Azure SDK pro .NET.]
  * [Máte jiný problém se službou úložiště.]
  * [Řešení potíží s virtuálními počítači na virtuálních počítačích s Windows](../../virtual-machines/troubleshooting/index.yml)   
  * [Řešení potíží s VHD na virtuálních počítačích se systémem Linux](../../virtual-machines/troubleshooting/index.yml)
  * [Řešení potíží se soubory Azure v systému Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Řešení potíží se soubory Azure se systémem Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Dodatky]
  * [Příloha 1: použití Fiddler k zachycení přenosů HTTP a HTTPS]
  * [Příloha 2: použití programu Wireshark k zachycení síťového provozu]
  * [Příloha 4: použití Excelu k zobrazení metrik a dat protokolu]
  * [Příloha 5: monitorování pomocí Application Insights pro Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Úvod
V této příručce se dozvíte, jak používat funkce, jako je Analýza úložiště Azure, protokolování na straně klienta v klientské knihovně Azure Storage a další nástroje třetích stran k identifikaci, diagnostice a řešení potíží souvisejících s Azure Storage.

![Diagram znázorňující tok informací mezi klientskými aplikacemi a službami Azure Storage.][1]

Tento průvodce je určený hlavně pro vývojáře online služby, kteří používají služby Azure Storage a odborníky na IT, kteří zodpovídají za správu takových online služby. Cílem této příručky jsou:

* Vám pomůžou udržovat stav a výkon vašich Azure Storagech účtů.
* Pro poskytování nezbytných procesů a nástrojů, které vám pomůžou určit, jestli problém nebo problém v aplikaci souvisí Azure Storage.
* Poskytne vám užitečné pokyny k vyřešení problémů souvisejících se Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Jak je tato příručka organizována
Část[monitorování služby úložiště]popisuje, jak monitorovat stav a výkon vašich Azure Storagech služeb pomocí analýza úložiště Azure metriky (metriky úložiště).

Oddíl "[diagnostikování problémů s úložištěm]" popisuje, jak diagnostikovat problémy pomocí protokolování analýza úložiště Azure (protokolování úložiště). Také popisuje, jak povolit protokolování na straně klienta pomocí zařízení v jedné z klientských knihoven, jako je například Klientská knihovna pro úložiště pro .NET nebo sada Azure SDK pro jazyk Java.

Část "[komplexní trasování]" popisuje, jak můžete korelovat informace obsažené v různých protokolových souborech a datech metrik.

Část "[pokyny k odstraňování]potíží" poskytuje pokyny k odstraňování některých běžných problémů souvisejících s úložištěm, se kterými se můžete setkat.

"[Dodatky]" obsahují informace o použití jiných nástrojů, jako je třeba Wireshark a Netmon pro analýzu dat síťových paketů, a Fiddler pro analýzu zpráv HTTP/HTTPS.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Monitorování služby úložiště
Pokud jste obeznámeni s monitorováním výkonu systému Windows, můžete si představit metriky úložiště jako Azure Storage ekvivalent čítačů sledování výkonu systému Windows. V části metriky úložiště najdete komplexní sadu metrik (čítače v terminologii sledování výkonu systému Windows), jako je například dostupnost služby, celkový počet požadavků na službu nebo procento úspěšných požadavků na službu. Úplný seznam dostupných metrik najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Můžete určit, jestli chcete, aby služba úložiště shromáždila a agregační metriky každou hodinu nebo každou minutu. Další informace o tom, jak povolit metriky a monitorovat účty úložiště, najdete v tématu [Povolení metrik úložiště a zobrazení dat metrik](../blobs/monitor-blob-storage.md).

Můžete zvolit, které hodinové metriky chcete zobrazit v [Azure Portal](https://portal.azure.com) a nakonfigurovat pravidla, která upozorní správce e-mailem vždy, když hodinová metrika překročí určitou prahovou hodnotu. Další informace najdete v tématu [přijímání oznámení o výstrahách](../../azure-monitor/alerts/alerts-overview.md).

Doporučujeme, abyste zkontrolovali [Azure monitor pro úložiště](../../azure-monitor/insights/storage-insights-overview.md) (Preview). Jedná se o funkci Azure Monitor, která nabízí ucelený přehled o vašich Azure Storagech účtech tím, že poskytuje jednotný přehled o výkonu, kapacitě a dostupnosti služby Azure Storage Services. Nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tyto metriky můžete okamžitě zobrazit z předem definovaných interaktivních grafů a dalších vizualizací, které jsou v ní obsažené.

Služba úložiště shromažďuje metriky s využitím optimálního úsilí, ale nemusí zaznamenávat každou operaci úložiště.

V Azure Portal můžete zobrazit metriky, jako je dostupnost, celkový počet požadavků a průměrná latence pro účet úložiště. Bylo také nastaveno upozornění na správce, pokud dostupnost klesne pod určitou úroveň. Po zobrazení těchto dat je jednou z možných oblastí pro šetření procento úspěšnosti služby Table v procentech pod 100% (Další informace najdete v části[metriky zobrazit nízké PercentSuccess nebo položky protokolu analýzy, které mají operace se stavem transakce ClientOtherErrors]").

Měli byste průběžně monitorovat aplikace Azure, abyste měli jistotu, že jsou v pořádku a co očekáváte:

* Stanovení některých základních metrik pro aplikaci, které vám umožní porovnat aktuální data a identifikovat významné změny v chování služby Azure Storage a vaší aplikace. Hodnoty vašich metrik směrného plánu budou v mnoha případech specifické pro aplikace a při testování výkonu aplikace byste je měli vytvořit.
* Zaznamenáváme minutové metriky a pomocí nich monitorujte aktivně, aby nedošlo k neočekávaným chybám a anomáliím, jako jsou špičky v počtech chyb nebo sazby požadavků.
* Zaznamenávání hodinových metrik a jejich použití k monitorování průměrných hodnot, jako jsou průměrné počty chyb a sazby požadavků.
* Zkoumání potenciálních problémů pomocí diagnostických nástrojů, jak je popsáno dále v části "[diagnostikování problémů s úložištěm]".

Grafy na následujícím obrázku znázorňují, jak průměrné průměry, ke kterým dochází pro hodinové metriky, mohou skrýt špičky v aktivitě. Po hodinových metrikách se zobrazí konstantní míra požadavků, zatímco minutová metrika odhalí výkyvy, které skutečně probíhají.

![Grafy, které ukazují, jak průměrné průměry, ke kterým dochází pro hodinové metriky, mohou skrýt špičky v aktivitě.][3]

Zbývající část této části popisuje metriky, které byste měli monitorovat, a proč.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Stav služby monitorování
Pomocí [Azure Portal](https://portal.azure.com) můžete zobrazit stav služby úložiště (a dalších služeb Azure) ve všech oblastech Azure po celém světě. Monitorování umožňuje okamžitě zobrazit v případě, že problém mimo váš ovládací prvek ovlivňuje službu úložiště v oblasti, kterou používáte pro svou aplikaci.

[Azure Portal](https://portal.azure.com) může také poskytovat oznámení o incidentech, které mají vliv na různé služby Azure.
Poznámka: tyto informace byly na [řídicím panelu služby Azure](https://status.azure.com)dříve k dispozici společně s historickými daty.
Další informace o Application Insights pro Azure DevOps najdete v dodatku[5: monitoring with Application Insights for Azure DevOps](#appendix-5).

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Kapacita monitorování
Metriky úložiště pouze ukládají metriky kapacity pro službu BLOB Service, protože objekty blob obvykle využívají největší podíl uložených dat (v době psaní není možné použít metriky úložiště k monitorování kapacity tabulek a front). Tato data můžete najít v tabulce **$MetricsCapacityBlob** , pokud jste povolili monitorování pro BLOB Service. Metrika úložiště zaznamenává tato data jednou za den a můžete použít hodnotu **RowKey** k určení, zda řádek obsahuje entitu, která souvisí s uživatelskými daty (hodnotami **dat**) nebo analytickými daty ( **Analýza** hodnot). Každá uložená entita obsahuje informace o velikosti využitého úložiště (měřený **objem** v bajtech) a o aktuálním počtu kontejnerů (**ContainerCount**) a objektů BLOB (**objectCount**) používaných v účtu úložiště. Další informace o metrikách kapacity uložených v tabulce **$MetricsCapacityBlob** najdete v tématu [Analýza úložiště schématu tabulky metrik](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Tyto hodnoty byste měli monitorovat pro počáteční upozornění, ke kterému se přiblížíte omezení kapacity účtu úložiště. V Azure Portal můžete přidat pravidla výstrah, která vás upozorní, pokud použití agregačního úložiště překročí nebo klesne pod prahovou hodnotu, kterou určíte.
>
>

Nápovědu k odhadu velikosti různých objektů úložiště, jako jsou objekty blob, najdete v příspěvku na blogu [principy Azure Storage fakturace – šířka pásma, transakce a kapacita](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Monitorování dostupnosti
Dostupnost služeb úložiště ve vašem účtu úložiště byste měli sledovat monitorováním hodnoty ve sloupci **dostupnost** v tabulkách hodinové nebo minutové metriky – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Sloupec **dostupnosti** obsahuje procentuální hodnotu, která indikuje dostupnost služby nebo operace rozhraní API reprezentované řádkem ( **RowKey** ukazuje, jestli řádek obsahuje metriky pro službu jako celek nebo pro konkrétní operaci rozhraní API).

Jakákoli hodnota menší než 100% znamená, že některé požadavky na úložiště selžou. Můžete zjistit, proč selžou, prozkoumáním dalších sloupců v datech metrik, které zobrazují počet požadavků s různými typy chyb, jako je **ServerTimeoutError**. Měli byste očekávat, že **dostupnost dostupnosti** je dočasně nižší než 100% z důvodů, jako je například přechodný časový limit serveru, zatímco služba přesouvá oddíly pro lepší požadavek na Vyrovnávání zatížení. logika opakování v klientské aplikaci by měla zpracovat takové přerušované podmínky. Článek [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) obsahuje seznam typů transakcí, které metriky úložiště zahrnují při výpočtu jejich **dostupnosti** .

V [Azure Portal](https://portal.azure.com)můžete přidat pravidla upozornění, která vás upozorní, pokud **dostupnost** služby klesne pod prahovou hodnotu, kterou určíte.

Část[pokyny k řešení potíží]v této příručce popisuje některé běžné problémy se službou úložiště související s dostupností.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Monitorování výkonu
Pokud chcete monitorovat výkon služeb úložiště, můžete použít následující metriky z tabulek hodinových a minutových metrik.

* Hodnoty ve sloupcích **hodnotu averagee2elatency** a **hodnotu averageserverlatency** ukazují průměrnou dobu, kterou služba úložiště nebo typ operace rozhraní API přebírá zpracování požadavků. **Hodnotu averagee2elatency** je míra celkové latence, která zahrnuje dobu nutnou ke čtení žádosti a odeslání odpovědi společně s časem potřebným ke zpracování žádosti (proto zahrnuje latenci sítě, jakmile požadavek dosáhne služby úložiště); **Hodnotu averageserverlatency** je míra pouze doby zpracování, a proto vylučuje jakoukoli latenci sítě související s komunikací s klientem. V části "[metriky ukazují vysoké hodnotu averagee2elatency a nízké hodnotu averageserverlatency]" dále v této příručce najdete informace o tom, proč mezi těmito dvěma hodnotami může být značný rozdíl.
* Hodnoty ve sloupcích **totalbillablerequests** a **TotalEgress** zobrazují celkové množství dat v bajtech přicházejících do služby úložiště nebo z konkrétního typu operace rozhraní API (v bajtech).
* Hodnoty ve sloupci **TotalRequests** zobrazují celkový počet požadavků, které služba úložiště pro operaci rozhraní API přijímá. **TotalRequests** je celkový počet požadavků, které služba Storage přijme.

Obvykle budete monitorovat neočekávané změny v některé z těchto hodnot jako ukazatel, u kterého máte problém, který vyžaduje prošetření.

V [Azure Portal](https://portal.azure.com)můžete přidat pravidla výstrah, která vás upozorní, pokud kterákoli z metrik výkonu pro tuto službu klesne pod nebo překročí zadanou prahovou hodnotu.

Část[pokyny k řešení potíží]v této příručce popisuje některé běžné problémy se službou úložiště, které souvisejí s výkonem.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnostikování problémů s úložištěm
Existuje několik způsobů, jak se můžete setkat s problémem nebo problémem ve vaší aplikaci, včetně těchto:

* Došlo k závažné chybě, která způsobí selhání aplikace nebo zastavení práce.
* Významné změny z hodnot směrného plánu v metrikách, které sledujete, jak je popsáno v předchozí části[monitoring služby úložiště].
* Sestavy uživatelů vaší aplikace, že některé konkrétní operace nebyly dokončeny podle očekávání nebo že některé funkce nefungují.
* Chyby generované v rámci aplikace, které se zobrazují v souborech protokolu nebo prostřednictvím některé jiné metody oznámení.

Problémy související se službou Azure Storage se typicky nacházejí v jedné ze čtyř hlavních kategorií:

* Vaše aplikace má problém s výkonem, který si nahlásili vaši uživatelé nebo které odhalily změny v metrikách výkonu.
* Došlo k potížím s infrastrukturou Azure Storage v jedné nebo několika oblastech.
* Ve vaší aplikaci dochází k chybě, kterou nahlásili vaši uživatelé nebo které se zvýšily v jedné z metrik počtu chyb, které sledujete.
* Během vývoje a testování můžete použít emulátor místního úložiště; Můžete se setkat s některými problémy, které se týkají konkrétně použití emulátoru úložiště.

Následující části popisují kroky, které byste měli provést při diagnostice a řešení problémů v každé z těchto čtyř kategorií. Část "[pokyny k odstraňování potíží]dále v tomto průvodci poskytuje další podrobnosti k běžným problémům, se kterými se můžete setkat.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problémy se stavem služby
Problémy se stavem služby jsou obvykle mimo váš ovládací prvek. [Azure Portal](https://portal.azure.com) poskytuje informace o všech probíhajících problémech se službami Azure, včetně služeb úložiště. Pokud jste se rozhodli, že Read-Access Geo-Redundant úložiště při vytváření účtu úložiště, pak se vaše data budou v primárním umístění dočasně přepnout na kopii, která je jen pro čtení. Aby bylo možné číst ze sekundárního nástroje, musí být aplikace schopná přepínat mezi primárním a sekundárním úložištěm a být schopná pracovat v režimu omezené funkčnosti s daty jen pro čtení. Klientské knihovny Azure Storage umožňují definovat zásady opakování, které se můžou číst ze sekundárního úložiště v případě, že se operace čtení z primárního úložiště nezdařila. Vaše aplikace také potřebuje vědět, že data v sekundárním umístění jsou nakonec konzistentní. Další informace najdete v blogovém příspěvku [Azure Storage možnosti redundance a geograficky redundantního úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problémy s výkonem
Výkon aplikace může být subjektivní, zejména z pohledu uživatele. Proto je důležité mít k dispozici základní metriku, která vám pomůže identifikovat možné problémy s výkonem. Mnoho faktorů může ovlivnit výkon služby Azure Storage z pohledu klientské aplikace. Tyto faktory mohou fungovat ve službě úložiště, v klientovi nebo v síťové infrastruktuře; Proto je důležité mít strategii pro identifikaci původu problému s výkonem.

Po zjištění problematického umístění příčiny potíží s výkonem z metriky pak můžete pomocí souborů protokolu vyhledat podrobné informace k dalšímu diagnostikování a řešení potíží.

Další informace o některých běžných problémech souvisejících s výkonem, ke kterým může dojít, najdete v části[pokyny k odstraňování potíží]dále v tomto průvodci.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnostikování chyb
Uživatelé vaší aplikace mohou upozorňovat na chyby hlášené klientskou aplikací. Metriky úložiště také zaznamenává počty různých typů chyb z vašich služeb úložiště, například **NetworkError**, **ClientTimeoutError** nebo **AuthorizationError**. I když metrika úložiště zaznamenává jenom počty různých typů chyb, můžete získat další podrobnosti o jednotlivých požadavcích tím, že prozkoumáte protokoly na straně serveru, klienta a sítě. Stavový kód HTTP vrácený službou úložiště obvykle poskytne informace o tom, proč se žádost nezdařila.

> [!NOTE]
> Nezapomeňte, že byste měli očekávat, že se zobrazí některé občasné chyby: například chyby z důvodu přechodných síťových podmínek nebo chyby aplikace.
>
>

Následující zdroje informací jsou užitečné pro pochopení stavů a chybových kódů souvisejících s úložištěm:

* [Běžné kódy chyb REST API](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Kódy chyb služby BLOB Service](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Queue – kódy chyb služby](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Chybové kódy služby Table Service](/rest/api/storageservices/Table-Service-Error-Codes)
* [Chybové kódy souborové služby](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problémy emulátoru úložiště
Sada Azure SDK obsahuje emulátor úložiště, který můžete spustit na vývojové pracovní stanici. Tento emulátor simuluje většinu chování služeb Azure Storage a je užitečný při vývoji a testování, takže můžete spouštět aplikace, které používají službu Azure Storage, bez nutnosti použít předplatné Azure a účet úložiště Azure.

Část[pokyny k řešení potíží]v této příručce popisuje některé běžné problémy zjištěné při použití emulátoru úložiště.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Nástroje protokolování úložiště
Protokolování úložiště poskytuje protokolování požadavků na úložiště na straně serveru v účtu úložiště Azure. Další informace o tom, jak povolit protokolování na straně serveru a přistupovat k datům protokolu, najdete v tématu [Povolení protokolování úložiště a přístup k datům protokolu](./storage-analytics-logging.md).

Klientská knihovna pro úložiště pro .NET umožňuje shromažďovat data protokolu na straně klienta, která se vztahují k operacím úložiště provedeným vaší aplikací. Další informace najdete v článku [Protokolování na straně klienta s klientskou knihovnou pro úložiště .NET](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).

> [!NOTE]
> V některých případech (například selhání autorizace SAS) může uživatel ohlásit chybu, pro kterou nemůžete najít žádná data žádosti v protokolech úložiště na straně serveru. Pomocí možností protokolování klientské knihovny pro úložiště můžete prozkoumat, zda je příčina problému na klientovi, nebo použít nástroje pro monitorování sítě k prozkoumání sítě.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Používání nástrojů pro protokolování sítě
Můžete zachytit provoz mezi klientem a serverem a poskytnout podrobné informace o datech, které klient a server vyměňuje, a o základních síťových podmínkách. K užitečným nástrojům pro protokolování sítě patří:

* [Fiddler](https://www.telerik.com/fiddler) je bezplatný webový proxy server pro ladění, který umožňuje kontrolovat hlavičky a data datové části požadavků HTTP a HTTPS a zprávy s odpovědí. Další informace najdete v [dodatku 1: použití Fiddler k zachycení přenosů http a HTTPS](#appendix-1).
* [Microsoft sledování sítě (Netmon)](https://download.cnet.com/s/network-monitor/) a [Wireshark](https://www.wireshark.org/) jsou bezplatné nástroje pro analyzátor síťových protokolů, které umožňují zobrazit podrobné informace o paketech pro nejrůznější síťové protokoly. Další informace o nástroji Wireshark najdete v[dodatku 2: použití nástroje Wireshark k zachycení síťového provozu](#appendix-2).
* Pokud chcete provést základní test připojení, abyste zkontrolovali, jestli se Váš klientský počítač může připojit ke službě Azure Storage přes síť, nemůžete to udělat pomocí standardního nástroje **příkazového** testu na klientovi. K zkontrolování připojení ale můžete použít [Nástroj **tcping**](https://www.elifulkerson.com/projects/tcping.php) .

V mnoha případech budou data protokolu z protokolování úložiště a klientské knihovny pro úložiště dostatečné k diagnostice problému, ale v některých scénářích budete možná potřebovat podrobnější informace, které tyto nástroje pro protokolování sítě můžou poskytnout. Například použití Fiddler k zobrazení zpráv HTTP a HTTPS vám umožní zobrazit hlavičku a data datové části odesílané do a ze služeb úložiště, což vám umožní zjistit, jak klientská aplikace opakuje operace úložiště. Analyzátory protokolů, jako je třeba Wireshark, pracují na úrovni paketů, což vám umožní zobrazit data protokolu TCP, což vám umožní řešit ztracené pakety a problémy s připojením. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Komplexní trasování
Komplexní trasování pomocí nejrůznějších souborů protokolu je užitečnou technikou pro zkoumání potenciálních problémů. Informace o datu a čase z vašich dat metriky můžete použít jako indikaci, kde začít hledat v souborech protokolu, kde najdete podrobné informace, které vám pomůžou problém vyřešit.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Korelace dat protokolu
Při prohlížení protokolů z klientských aplikací, trasování sítě a protokolování úložiště na straně serveru je důležité, aby bylo možné korelovat požadavky napříč různými soubory protokolů. Soubory protokolu obsahují řadu různých polí, která jsou užitečná jako identifikátory korelace. ID žádosti klienta je nejužitečnější pole, které se používá ke korelaci záznamů v různých protokolech. V některých případech ale může být užitečné použít buď ID žádosti serveru, nebo časová razítka. Následující části obsahují další podrobnosti o těchto možnostech.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID požadavku klienta
Klientská knihovna pro úložiště automaticky vygeneruje jedinečné ID žádosti klienta pro každý požadavek.

* V protokolu na straně klienta, který Klientská knihovna pro úložiště vytvoří, se ID žádosti klienta zobrazuje v poli **ID žádosti klienta** každé položky protokolu týkající se žádosti.
* V trasování sítě, jako je například jedna zachycena serverem Fiddler, se ID žádosti klienta zobrazuje ve zprávách požadavku jako hodnota hlavičky protokolu HTTP **x-MS-Client-Request-ID** .
* V protokolu protokolování úložiště na straně serveru se ID žádosti klienta zobrazí ve sloupci ID žádosti klienta.

> [!NOTE]
> Je možné, že více požadavků bude sdílet stejné ID žádosti klienta, protože klient může tuto hodnotu přiřadit (i když klientská knihovna pro úložiště přiřadí novou hodnotu automaticky). Po opakování klienta všechny pokusy sdílejí stejné ID žádosti klienta. V případě dávky odeslané z klienta má dávka jedno ID žádosti klienta.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID žádosti serveru
Služba úložiště automaticky generuje ID žádosti serveru.

* V protokolu protokolování úložiště na straně serveru se zobrazí ID žádosti serveru, ve kterém se nachází sloupec **Hlavička ID žádosti** .
* V trasování sítě, jako je například jedna zachycena serverem Fiddler, se ID žádosti serveru zobrazí v odpovědi jako hodnota hlavičky protokolu HTTP **x-MS-Request-ID** .
* V protokolu na straně klienta, který Klientská knihovna pro úložiště vytvoří, se ID žádosti serveru zobrazí ve sloupci **text operace** pro položku protokolu ukazující podrobnosti odpovědi serveru.

> [!NOTE]
> Služba úložiště vždy přiřadí jedinečné ID žádosti serveru pro každý požadavek, který obdrží, takže každý Opakovaný pokus od klienta a každá operace zahrnutá v dávce má jedinečné ID žádosti serveru.
>
>

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Následující ukázka kódu ukazuje, jak použít vlastní ID žádosti klienta. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud klientská knihovna pro úložiště vyvolá v klientovi **StorageException** , vlastnost **RequestInformation** obsahuje objekt **RequestResult** , který obsahuje vlastnost **ServiceRequestID** . K objektu **RequestResult** můžete získat přístup také z instance **OperationContext** .

Následující ukázka kódu ukazuje, jak nastavit vlastní hodnotu **ID žádosti klienta** připojením objektu **OperationContext** k žádosti službě úložiště. Také ukazuje, jak načíst hodnotu **ServerRequestId** ze zprávy s odpovědí.

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

---

### <a name="timestamps"></a><a name="timestamps"></a>Časová razítka
Můžete také použít časová razítka k vyhledání souvisejících položek protokolu, ale buďte opatrní při jakémkoli časovém intervalu mezi klientem a serverem, který může existovat. Pro porovnání položek na straně serveru na základě časového razítka v klientovi hledejte plus nebo mínus 15 minut. Mějte na paměti, že metadata objektů BLOB pro objekty BLOB obsahující metriky označují časový rozsah pro metriky uložené v objektu BLOB. Tento časový rozsah je užitečný, když máte spoustu objektů BLOB metrik za stejnou minutu nebo hodinu.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Pokyny k odstraňování potíží
Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se může vaše aplikace setkat při používání služeb Azure Storage. K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

**Řešení potíží se stromem pro rozhodování**

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
 Přijímá klientská aplikace odpověď HTTP 4XX (například 404) od služby úložiště?

* [Klient dostává stavové kódy HTTP 403 (Zakázáno)]
* [Klient dostává stavové kódy HTTP 404 (Nenalezeno)]
* [Klient dostává stavové kód HTTP 409 (Konflikt)]

---
[Metriky zobrazují nízké PercentSuccess nebo položky protokolu analýzy mají operace se stavem transakce ClientOtherErrors.]

---
[Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště.]

---
[Došlo k neočekávanému restartování Virtual Machines, které mají velký počet připojených VHD.]

---
[K vašemu problému dochází při použití emulátoru úložiště pro vývoj nebo testování.]

---
[Narazíte na problémy s instalací sady Azure SDK pro .NET.]

---
[Máte jiný problém se službou úložiště.]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.
Na ilustraci níže v nástroji pro monitorování [Azure Portal](https://portal.azure.com) se zobrazuje příklad, kdy je **hodnotu averagee2elatency** výrazně vyšší než **hodnotu averageserverlatency**.

![Obrázek z Azure Portal, který ukazuje příklad, kde je hodnotu averagee2elatency výrazně vyšší než hodnotu averageserverlatency.][4]

Služba Storage počítá jenom **hodnotu averagee2elatency** metriky pro úspěšné požadavky a na rozdíl od **hodnotu averageserverlatency** zahrnuje dobu, kterou klient potřebuje k odeslání dat a přijetí potvrzení ze služby úložiště. Proto rozdíl mezi **hodnotu averagee2elatency** a **hodnotu averageserverlatency** může být buď z důvodu pomalé odezvy klientské aplikace, nebo v důsledku podmínek v síti.

> [!NOTE]
> Můžete také zobrazit **E2ELatency** a **ServerLatency** pro jednotlivé operace úložiště v datech protokolu protokolování úložiště.
>
>

#### <a name="investigating-client-performance-issues"></a>Zkoumání problémů s výkonem klienta
Možné příčiny, proč klient reaguje pomalu, zahrnuje omezený počet dostupných připojení nebo vláken nebo má nízkou velikost prostředků, jako jsou například CPU, paměť nebo šířka pásma sítě. Problém může být možné vyřešit úpravou kódu klienta tak, aby byl efektivnější (například pomocí asynchronního volání služby úložiště), nebo pomocí většího virtuálního počítače (s více jádry a více paměti).

Pro služby Table a Queue může Nagle algoritmus také způsobit vysoké **hodnotu averagee2elatency** ve srovnání s **hodnotu averageserverlatency**: Další informace najdete v tématu o [algoritmu post Nagle není uživatelsky přívětivý vůči malým požadavkům](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests). Nagle algoritmus můžete v kódu zakázat pomocí třídy **Třída ServicePointManager** v oboru názvů **System.NET** . Tento postup byste měli provést předtím, než v aplikaci provedete jakékoli volání služby Table nebo Queue, protože to nemá vliv na připojení, která jsou již otevřena. Následující příklad pochází z metody **Application_Start** v roli pracovního procesu.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Měli byste kontrolovat protokoly na straně klienta, abyste viděli, kolik požadavků vaše klientská aplikace odesílá, a měli byste vyhledat obecná slabá místa výkonu související s .NET v klientovi, jako je například CPU, uvolňování paměti .NET, využití sítě nebo paměť. Jako výchozí bod pro řešení potíží s klientskými aplikacemi .NET si přečtěte téma [ladění, trasování a profilace](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Zkoumání potíží s latencí sítě
Většinou je vysoká koncová latence způsobená sítí způsobená přechodnými podmínkami. Dočasné a trvalé problémy se sítí, jako jsou například vyřazené pakety, můžete prozkoumat pomocí nástrojů, jako je třeba Wireshark.

Další informace o použití nástroje Wireshark k řešení problémů se sítí najdete v[dodatku 2: použití nástroje Wireshark k zachycení síťového provozu].

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.
V tomto scénáři je nejpravděpodobnější příčinou zpoždění požadavků úložiště, které se blíží službě úložiště. Měli byste prozkoumat, proč se požadavky klienta neprovádějí do služby BLOB Service.

Jedním z možných důvodů, proč klient zpozdit odeslání požadavků, je, že existuje omezený počet dostupných připojení nebo vláken.

Také zkontrolujte, zda klient provádí více opakovaných pokusů, a prozkoumat důvod, pokud je to. Chcete-li zjistit, zda klient provádí více opakovaných pokusů, můžete:

* Projděte si protokoly Analýza úložiště. Pokud dojde k více opakovaným pokusům, zobrazí se více operací se stejným ID žádosti klienta, ale s různými ID žádosti serveru.
* Projděte si protokoly klienta. Podrobné protokolování znamená, že došlo k opakování.
* Proveďte ladění kódu a ověřte vlastnosti objektu **OperationContext** přidruženého k žádosti. Pokud se operace zopakuje, bude vlastnost **RequestResults** zahrnovat několik jedinečných ID žádosti serveru. Můžete také kontrolovat počáteční a koncové časy pro každý požadavek. Další informace naleznete v ukázce kódu v části [ID žádosti serveru].

Pokud v klientovi nejsou žádné problémy, měli byste prozkoumat potenciální problémy se sítí, jako je například ztráta paketů. K prozkoumání problémů se sítí můžete použít nástroje, jako je třeba Wireshark.

Další informace o použití nástroje Wireshark k řešení problémů se sítí najdete v[dodatku 2: použití nástroje Wireshark k zachycení síťového provozu].

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Metrika ukazuje vysokou hodnotu AverageServerLatency.
V případě vysokého **hodnotu averageserverlatency** požadavků na stažení objektů BLOB byste měli použít protokoly protokolování úložiště a zjistit, jestli existují opakované žádosti pro stejný objekt BLOB (nebo sadu objektů BLOB). V případě požadavků na nahrání objektů BLOB byste měli prozkoumat, jakou velikost bloku klient používá (například bloky menší než 64 KB můžou způsobit, že se v případě, že jsou v nich taky méně než 64 MB bloků dat, nahrávají i v případě, že jsou bloky do stejného objektu BLOB souběžně nahrávají. Měli byste taky kontrolovat metriky pro špičky v počtu požadavků, které vedou k překročení cílů škálovatelnosti za sekundu: taky se v části[metriky zobrazuje zvýšení PercentTimeoutError].

Pokud se vám zobrazují vysoké **hodnotu averageserverlatency** požadavků na stažení objektů BLOB v případě opakovaných žádostí o stejný objekt BLOB nebo sadu objektů blob, měli byste zvážit ukládání těchto objektů blob do mezipaměti pomocí mezipaměti Azure nebo Azure Content DELIVERY Network (CDN). V případě požadavků na nahrání můžete zvýšit propustnost pomocí větší velikosti bloku. Pro dotazy na tabulky je také možné implementovat ukládání do mezipaměti na straně klienta v klientech, kteří provádějí stejné operace s dotazem a kde se data často nemění.

Vysoká hodnota **hodnotu averageserverlatency** může být také příznakem nesprávně navržených tabulek nebo dotazů, které vedou k operacím prohledávání nebo které následují po anti-vzoru připojit/k předplatným. Další informace najdete v části "[metriky ukazují zvýšení PercentThrottlingError]".

> [!NOTE]
> Podrobný kontrolní seznam výkonu kontrolního seznamu můžete najít tady: [Microsoft Azure Storage kontrolní seznam pro výkon a škálovatelnost](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Dochází k neočekávaným zpožděním při doručování zpráv ve frontě
Pokud dojde ke zpoždění mezi tím, kdy aplikace přidává do fronty zprávu a čas, kdy je k dispozici pro čtení z fronty, měli byste při diagnostice tohoto problému provést následující kroky:

* Ověřte, že aplikace úspěšně přidala zprávy do fronty. Ověřte, že aplikace neopakuje opakování metody **AddMessage** několikrát předtím, než bude úspěšná. V protokolech klientské knihovny pro úložiště se zobrazí všechny opakované pokusy operací úložiště.
* Ověřte, že mezi rolí pracovního procesu, která přidává zprávu do fronty, a role pracovního procesu, která čte zprávu z fronty, není žádné časové zkosení, které se zobrazí, jako kdyby došlo ke zpoždění zpracování.
* Ověřte, zda role pracovního procesu, která načítá zprávy z fronty, selhává. Pokud klient fronty zavolá metodu **GetMessage** , ale neodpoví na potvrzení, zpráva zůstane ve frontě neviditelná, dokud nevyprší doba **invisibilityTimeout** . V tomto okamžiku bude zpráva k dispozici pro opětovné zpracování.
* Zkontroluje, jestli se délka fronty v průběhu času zvětšuje. Tato situace může nastat, pokud nemáte k dispozici dostatečné pracovní procesy pro zpracování všech zpráv, které ve frontě umísťují jiní zaměstnanci. Podívejte se také na metriky, abyste viděli, jestli nechybí žádosti o odstranění a počet vyřazení zpráv ve zprávách, což může označovat opakované neúspěšné pokusy o odstranění zprávy.
* Projděte si protokoly protokolování úložiště pro všechny operace front, které mají více než očekávanou hodnotu **E2ELatency** a **ServerLatency** hodnoty za delší dobu, než je obvyklé.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metrika ukazuje zvýšení u PercentThrottlingError.
K chybám omezování dochází, když překročíte cíle škálovatelnosti služby úložiště. Služba Storage omezuje omezení, aby nemohlo používat službu na náklady ostatních klientů ani klientů. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](scalability-targets-standard-account.md) , kde najdete podrobnosti o cílech škálovatelnosti pro účty úložiště a cíle výkonu pro oddíly v rámci účtů úložiště.

Pokud metrika **PercentThrottlingError** ukazuje zvýšení procentuální hodnoty požadavků, které selžou s chybou omezení, je nutné prozkoumat jeden ze dvou scénářů:

* [Přechodné zvýšení v PercentThrottlingError]
* [Trvalé zvýšení chyby PercentThrottlingError]

K nárůstu **PercentThrottlingError** často dochází ve stejnou dobu jako u zvýšení počtu požadavků na úložiště nebo při počátečním zátěži testování vaší aplikace. Může to také v klientovi manifestovat jako "503 server zaneprázdněn" nebo "500" časový limit operace HTTP "z operací úložiště.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Přechodné zvýšení v PercentThrottlingError
Pokud vidíte špičky v hodnotě **PercentThrottlingError** , která se shoduje s obdobími vysoké aktivity aplikace, implementujete exponenciální (nelineární) strategii pro opakované pokusy v klientovi. Záložní opakované pokusy omezují okamžité zatížení oddílu a umožňují vaší aplikaci hladké špičky v provozu. Další informace o implementaci zásad opakování pomocí klientské knihovny pro úložiště najdete v [oboru názvů Microsoft. Azure. Storage. RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Může se zobrazit také špička v hodnotě **PercentThrottlingError** , která se neshoduje s obdobími vysoké aktivity pro aplikaci: nejpravděpodobnější příčinou je, že služba úložiště přesouvá oddíly pro zlepšení vyrovnávání zatížení.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Trvalé zvýšení chyby PercentThrottlingError
Pokud se zobrazuje konzistentně vysoká hodnota pro **PercentThrottlingError** po trvalém nárůstu objemu transakcí nebo při provádění počátečních zátěžových testů ve vaší aplikaci, je nutné vyhodnotit, jak vaše aplikace používá oddíly úložiště a zda se blíží cílům škálovatelnosti pro účet úložiště. Pokud například vidíte chyby omezování ve frontě (což se počítá jako jeden oddíl), měli byste zvážit použití dalších front pro rozprostření transakcí napříč více oddíly. Pokud vidíte chyby omezování v tabulce, musíte zvážit použití odlišného schématu dělení k rozložení transakcí napříč více oddíly pomocí širší škály hodnot klíče oddílu. Jednou z běžných příčin tohoto problému je, že při výběru data jako klíče oddílu předplatíte nebo připojím a pak všechna data v konkrétním dni se zapisují do jednoho oddílu: při zatížení to může způsobit kritické místo pro zápis. Vezměte v úvahu jiný návrh dělení nebo Vyhodnoťte, jestli je možné používat úložiště objektů BLOB jako lepší řešení. Zkontrolujte taky, jestli se k omezování dochází v důsledku špičky v provozu, a prozkoumejte způsoby, jak vyhladit vzor požadavků.

Pokud vaše transakce distribuujete mezi více oddílů, musíte si pořád uvědomit omezení škálovatelnosti nastavené pro účet úložiště. Pokud jste například použili deset front pro každé zpracování maximálně 2 000 zpráv 1 KB za sekundu, bude pro účet úložiště k dispozici celkový limit 20 000 zpráv za sekundu. Pokud potřebujete zpracovat více než 20 000 entit za sekundu, měli byste zvážit použití více účtů úložiště. Měli byste také mít na paměti, že velikost vašich požadavků a entit má vliv na to, kdy služba úložiště omezuje vaše klienty: Pokud máte větší požadavky a entity, můžete se omezit dřív.

Neefektivní návrh dotazů může také způsobit omezení škálovatelnosti pro oddíly tabulky. Například dotaz s filtrem, který v oddílu vybere jenom jednu procentuální část entit, ale prověřování všech entit v oddílu bude potřebovat přístup ke každé entitě. Každá přečtená entita se bude počítat s celkovým počtem transakcí v tomto oddílu. Proto můžete snadno dosáhnout cílů škálovatelnosti.

> [!NOTE]
> Vaše testování výkonu by mělo odhalit neefektivní návrhy dotazů ve vaší aplikaci.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metrika ukazuje zvýšení u PercentTimeoutError.
Vaše metriky ukazují zvýšení **PercentTimeoutError** pro jednu z vašich služeb úložiště. Klient zároveň obdrží velký objem "500" časového limitu operace "" zpráv o stavu protokolu HTTP z operací úložiště.

> [!NOTE]
> Chyby vypršení časového limitu se můžou zobrazovat dočasně, protože služba Storage vyrovnává požadavky přesunutím oddílu na nový server.
>
>

Metrika **PercentTimeoutError** je agregace následujících metrik: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** a **SASServerTimeoutError**.

Vypršení časového limitu serveru je způsobeno chybou na serveru. K vypršení časového limitu klienta dochází, protože operace na serveru překročila časový limit zadaný klientem. například klient využívající knihovnu klienta úložiště může nastavit časový limit pro operaci pomocí vlastnosti **ServerTimeout** třídy **QueueRequestOptions** .

Vypršení časových limitů serveru indikují problém se službou úložiště, která vyžaduje další šetření. Metriky můžete použít, pokud chcete zjistit, jestli jste u služby nedosáhli limitu škálovatelnosti, a identifikovat případné špičky v provozu, které by mohly způsobovat tento problém. Pokud je problém přerušovaný, může to být způsobeno aktivitou vyrovnávání zatížení ve službě. Pokud je problém trvalý a není způsobený tím, že vaše aplikace zasáhne omezení škálovatelnosti služby, měli byste vyvolat problém podpory. V případě časových limitů klienta se musíte rozhodnout, zda je časový limit nastaven na odpovídající hodnotu v klientovi, a buď změnit hodnotu časového limitu nastavenou v klientovi, nebo prozkoumat, jak můžete zlepšit výkon operací ve službě úložiště, například optimalizací dotazů tabulky nebo zmenšením velikosti zpráv.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metrika ukazuje zvýšení u PercentNetworkError.
Vaše metriky ukazují zvýšení **PercentNetworkError** pro jednu z vašich služeb úložiště. Metrika **PercentNetworkError** je agregace následujících metrik: **NetworkError**, **AnonymousNetworkError** a **SASNetworkError**. K tomu dojde, když služba úložiště detekuje chybu sítě, když klient vytvoří požadavek na úložiště.

Nejběžnější příčinou této chyby je odpojení klienta před vypršením časového limitu ve službě úložiště. Prozkoumejte kód ve vašem klientovi, abyste zjistili, proč a kdy se klient odpojí od služby úložiště. Nástroj Wireshark nebo Tcping můžete použít také k prozkoumání problémů s připojením k síti z klienta. Tyto nástroje jsou popsány v [dodatcích].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klient dostává stavové kódy HTTP 403 (Zakázáno)
Pokud vaše klientská aplikace způsobuje chyby HTTP 403 (Zakázáno), pravděpodobnou příčinou je, že klient používá při odesílání žádosti na úložiště prošlý sdílený přístupový podpis (SAS) (i když další možné příčiny zahrnují nesoulad hodin, neplatné klíče a prázdné záhlaví). Pokud je příčinou vypršení platnosti klíče SAS, neuvidíte žádné záznamy v protokolu úložiště na straně serveru. Následující tabulka ukazuje ukázku z protokolu na straně klienta generovaného klientskou knihovnou úložiště, která ukazuje tento problém:

| Zdroj | Podrobnosti | Podrobnosti | ID požadavku klienta | Text operace |
| --- | --- | --- | --- | --- |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Spouští se operace s primárním umístěním na režim umístění PrimaryOnly. |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Spouští se synchronní požadavek na <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Čeká se na odpověď. |
| Microsoft. Azure. Storage |Upozornění |2 |85d077ab-... |Při čekání na odpověď se vyvolala výjimka: vzdálený server vrátil chybu: (403) zakázáno. |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Přijata odpověď. Stavový kód = 403, ID žádosti = 9d67c64a-64ed-4B0D-9515-3b14bbcdc63d, Content-MD5 =, ETag =. |
| Microsoft. Azure. Storage |Upozornění |2 |85d077ab-... |Během operace došlo k výjimce: vzdálený server vrátil chybu: (403) zakázáno... |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Kontroluje se, jestli se má operace opakovat. Počet opakování = 0, stavový kód HTTP = 403, výjimka = vzdálený server vrátil chybu: (403) zakázáno... |
| Microsoft. Azure. Storage |Informace |3 |85d077ab-... |Další umístění bylo nastaveno na primární v závislosti na režimu umístění. |
| Microsoft. Azure. Storage |Chyba |1 |85d077ab-... |Zásada opakování nepovolovala pokus o opakování. Selhání se vzdáleným serverem vrátilo chybu: (403) zakázáno. |

V tomto scénáři byste měli prozkoumat, proč platnost tokenu SAS vyprší předtím, než klient pošle token na server:

* Obvykle byste neměli nastavit čas zahájení, když vytváříte SAS pro okamžité použití. Pokud existují malý časový rozdíl mezi hostitelem generujícím SAS s použitím aktuálního času a mezi službou úložiště, je možné, že služba úložiště přijme SAS, který ještě není platný.
* Nenastavujte ani velmi krátkou dobu vypršení platnosti SAS. Opět, malý časový rozdíl mezi hostitelem generujícím SAS a službou úložiště může vést k předčasnému zdánlivému vypršení platnosti SAS.
* Odpovídá parametr verze v klíči SAS (například **sv = 2015-04-05**) verzi klientské knihovny pro úložiště, kterou používáte? Doporučujeme vždy používat nejnovější verzi [klientské knihovny pro úložiště](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Když znovu vygenerujete přístupové klíče k úložišti, může to zneplatnit stávající tokeny SAS. Tento problém může nastat, když vygenerujete tokeny SAS s dlouhou dobou vypršení platnosti pro uložení v mezipaměti u klientů.

Pokud k vygenerování tokenů SAS používáte klientskou knihovnu úložiště, je vytvoření platného tokenu snadné. Pokud ale používáte úložiště REST API a vytváříte tokeny SAS ručně, přečtěte si téma [delegování přístupu pomocí sdíleného přístupového podpisu](/rest/api/storageservices/delegate-access-with-shared-access-signature).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klient dostává stavové kódy HTTP 404 (Nenalezeno)
Pokud klientská aplikace obdrží od server stavový kód HTTP 404 (Nenalezeno), znamená to, že objekt, který se klient pokouší použít (například entita, tabulka, objekt blob, kontejner nebo fronta), ve službě úložiště neexistuje. Pro to může existovat několik důvodů, například:

* [Klient nebo jiný proces už objekt odstranil]
* [Problém s ověřením sdíleného přístupového podpisu (SAS)]
* [JavaScriptový kód na straně klienta nemá oprávnění pro přístup k objektu]
* [Chyba sítě]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klient nebo jiný proces dřív odstranil objekt
Ve scénářích, kdy se klient pokouší přečíst, aktualizovat nebo odstranit data v rámci služby úložiště, je obvykle snadné ho identifikovat v protokolu na straně serveru předchozí operace, která odstranila příslušný objekt ze služby úložiště. Často se v datech protokolu zobrazuje, že objekt odstranil jiný uživatel nebo proces. V protokolu protokolování úložiště na straně serveru se sloupce operace typu a požadováno-Object-Key zobrazují, když klient odstranil objekt.

V případě, kdy se klient pokouší vložit objekt, nemusí být ihned zřejmé, proč to má za následek odpověď HTTP 404 (Nenalezeno), protože klient vytváří nový objekt. Pokud však klient vytváří objekt blob, musí být schopný najít kontejner objektů blob, pokud klient vytváří zprávu, musí být schopný najít frontu a pokud klient přidává řádek, musí být schopný najít tabulku.

Pomocí protokolu na straně klienta z klientské knihovny úložiště můžete získat podrobnější informace o tom, kdy klient odesílá konkrétní požadavky do služby úložiště.

Následující protokol založený na klientovi generovaný knihovnou klienta úložiště ilustruje problém, když klient nemůže najít kontejner pro objekt blob, který vytváří. Tento protokol obsahuje podrobnosti o následujících operacích úložiště:

| ID požadavku | Operace |
| --- | --- |
| 07b26a5d-... |Metoda **DeleteIfExists** pro odstranění kontejneru objektů BLOB. Všimněte si, že tato operace obsahuje požadavek **head** ke kontrole existence kontejneru. |
| e2d06d78... |Metoda **CreateIfNotExists** pro vytvoření kontejneru objektů BLOB. Všimněte si, že tato operace obsahuje požadavek **head** , který kontroluje existenci kontejneru. **Head** vrátí zprávu 404, ale pokračuje. |
| de8b1c3c-... |Metoda **UploadFromStream** pro vytvoření objektu BLOB Požadavek **Put** se nezdařil. zpráva je 404. |

Položky protokolu:

| ID požadavku | Text operace |
| --- | --- |
| 07b26a5d-... |Spouští se synchronní požadavek na `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = HEAD.................. x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: Út, 03. června 2014 10:33:11 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| 07b26a5d-... |Čeká se na odpověď. |
| 07b26a5d-... |Přijata odpověď. Stavový kód = 200, ID žádosti = eeead849-... Content-MD5 =, ETag =    &quot; 0x8D14D2DC63D059B &quot; . |
| 07b26a5d-... |Hlavičky odpovědi byly úspěšně zpracovány, pokračuje se zbývající částí operace. |
| 07b26a5d-... |Stahuje se text odpovědi. |
| 07b26a5d-... |Operace se úspěšně dokončila. |
| 07b26a5d-... |Spouští se synchronní požadavek na `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = odstranit................... x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: Út, 03. června 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| 07b26a5d-... |Čeká se na odpověď. |
| 07b26a5d-... |Přijata odpověď. Stavový kód = 202, ID žádosti = 6ab2a4cf-..., obsahu-MD5 =, ETag =. |
| 07b26a5d-... |Hlavičky odpovědi byly úspěšně zpracovány, pokračuje se zbývající částí operace. |
| 07b26a5d-... |Stahuje se text odpovědi. |
| 07b26a5d-... |Operace se úspěšně dokončila. |
| e2d06d78-... |Spouští se asynchronní požadavek na `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = HEAD.................. x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: Út, 03. června 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| e2d06d78-... |Čeká se na odpověď. |
| de8b1c3c-... |Spouští se synchronní požadavek na `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = PUT... 64. qCmF + TQLPhq/YYK50mP9ZQ = =........... x-MS-BLOB-typ: BlockBlob. x-MS-Client-Request-ID: de8b1c3c-.... x-MS-Date: Út, 03 června 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Probíhá příprava na zápis dat žádosti. |
| e2d06d78-... |Při čekání na odpověď se vyvolala výjimka: vzdálený server vrátil chybu: (404) Nenalezeno. |
| e2d06d78-... |Přijata odpověď. Stavový kód = 404, ID žádosti = 353ae3bc-..., obsahu-MD5 =, ETag =. |
| e2d06d78-... |Hlavičky odpovědi byly úspěšně zpracovány, pokračuje se zbývající částí operace. |
| e2d06d78-... |Stahuje se text odpovědi. |
| e2d06d78-... |Operace se úspěšně dokončila. |
| e2d06d78-... |Spouští se asynchronní požadavek na `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = PUT... 0................... x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: Út, 03. června 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| e2d06d78-... |Čeká se na odpověď. |
| de8b1c3c-... |Zápis dat požadavku. |
| de8b1c3c-... |Čeká se na odpověď. |
| e2d06d78-... |Při čekání na odpověď se vyvolala výjimka: vzdálený server vrátil chybu: (409) konflikt.. |
| e2d06d78-... |Přijata odpověď. Stavový kód = 409, ID žádosti = c27da20e-..., obsahu-MD5 =, ETag =. |
| e2d06d78-... |Stahuje se text chybové odpovědi. |
| de8b1c3c-... |Při čekání na odpověď se vyvolala výjimka: vzdálený server vrátil chybu: (404) Nenalezeno. |
| de8b1c3c-... |Přijata odpověď. Stavový kód = 404, ID žádosti = 0eaeab3e-..., obsahu-MD5 =, ETag =. |
| de8b1c3c-... |Během operace došlo k výjimce: vzdálený server vrátil chybu: (404) Nenalezeno. |
| de8b1c3c-... |Zásada opakování nepovolovala pokus o opakování. Selhání se vzdáleným serverem vrátilo chybu: (404) Nenalezeno... |
| e2d06d78-... |Zásada opakování nepovolovala pokus o opakování. Selhání se vzdáleným serverem vrátilo chybu: (409) konflikt.. |

V tomto příkladu protokol ukazuje, že klient zanechává požadavky z metody **CreateIfNotExists** (ID žádosti e2d06d78...) s požadavky z metody **UploadFromStream** (de8b1c3c-...). K tomuto proplutí dochází, protože klientská aplikace volá tyto metody asynchronně. Upravte asynchronní kód v klientovi, abyste měli jistotu, že vytvoří kontejner před tím, než se pokusí nahrát data do objektu BLOB v tomto kontejneru. V ideálním případě byste měli vytvořit všechny kontejnery předem.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Autorizační problém s autorizací sdíleného přístupového podpisu (SAS)
Pokud se klientská aplikace pokusí použít klíč SAS, který neobsahuje potřebná oprávnění pro operaci, služba Storage vrátí klientovi zprávu HTTP 404 (Nenalezeno). Zároveň se v metrikách zobrazí nenulová hodnota pro **SASAuthorizationError** .

Následující tabulka ukazuje ukázkovou zprávu protokolu na straně serveru ze souboru protokolu protokolování úložiště:

| Name | Hodnota |
| --- | --- |
| Čas zahájení požadavku | 2014-05-30T06:17:48.4473697 Z |
| Typ operace     | GetBlobProperties            |
| Stav žádosti     | SASAuthorizationError        |
| Stavový kód HTTP   | 404                            |
| Typ ověřování| Vede                          |
| Typ služby       | Objekt blob                         |
| Adresa URL požadavku         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ? sv = 2014-02-14&SR = c&si = mypolicy&SIG = XXXXX &; API-Version = 2014-02-14 |
| Hlavička ID žádosti  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID požadavku klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Zjistěte, proč se klientská aplikace pokouší provést operaci, ke které se neudělila oprávnění.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Kód JavaScriptu na straně klienta nemá oprávnění pro přístup k objektu.
Pokud používáte klienta JavaScriptu a služba úložiště vrací zprávy HTTP 404, zkontrolujete následující chyby JavaScriptu v prohlížeči:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> V aplikaci Internet Explorer můžete pomocí Vývojářské nástroje F12 sledovat zprávy vyměňované mezi prohlížečem a službou úložiště při řešení potíží s JavaScript na straně klienta.
>
>

K těmto chybám dochází, protože webový prohlížeč implementuje stejné omezení zabezpečení jako [zásady původu](https://www.w3.org/Security/wiki/Same_Origin_Policy) , které brání webové stránce v volání rozhraní API v jiné doméně než z domény, ze které stránka pochází.

Pokud chcete vyřešit problém s JavaScriptem, můžete nakonfigurovat sdílení prostředků mezi zdroji (CORS) pro službu úložiště, ke které má klient přístup. Další informace najdete v tématu [Podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage služby](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Následující ukázka kódu ukazuje, jak nakonfigurovat službu BLOB Service tak, aby povolovala spuštění JavaScriptu v doméně contoso, aby měl přístup k objektu BLOB ve službě BLOB Storage:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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

---

#### <a name="network-failure"></a><a name="network-failure"></a>Selhání sítě
V některých případech mohou ztracené síťové pakety vést ke službě úložiště, která vrací zprávy HTTP 404 klientovi. Například Pokud klientská aplikace odstraňuje entitu ze služby Table Service, zobrazí se u klienta zpráva o stavu protokolu HTTP 404 (Nenalezeno) ze služby Table Service. Když prozkoumáte tabulku ve službě Table Storage, zjistíte, že služba odstranila entitu podle požadavku.

Podrobnosti výjimky v klientovi zahrnují ID žádosti (7e84f12d...) přiřazené službou Table Service pro požadavek: tyto informace můžete použít k vyhledání podrobností žádosti v protokolech úložiště na straně serveru tak, že v souboru protokolu vyhledáte sloupec **Request-ID-Header** . Můžete také použít metriky k identifikaci, kdy k tomu dojde, a pak vyhledat soubory protokolu na základě času, kdy metrika tuto chybu zaznamenal. Tato položka protokolu ukazuje, že odstranění selhalo se stavovou zprávou "HTTP (404) Client other Error". Stejná položka protokolu obsahuje také ID žádosti generované klientem ve sloupci **Client-Request-ID** (813ea74f...).

Protokol na straně serveru také obsahuje další položku se stejnou hodnotou **ID klienta-požadavku** (813ea74f...) pro úspěšnou operaci odstranění pro stejnou entitu a ze stejného klienta. Tato úspěšná operace odstranění trvala krátce před neúspěšným požadavkem na odstranění.

Nejpravděpodobnější příčinou tohoto scénáře je, že klient odeslal žádost o odstranění pro entitu službě Table Service, která byla úspěšná, ale neobdržela potvrzení ze serveru (možná kvůli dočasnému problému v síti). Klient pak automaticky znovu vyzkoušel operaci (pomocí stejného **ID klienta-požadavku**) a tento pokus se nezdařil, protože entita již byla odstraněna.

Pokud k těmto potížím dochází často, měli byste prozkoumat, proč se klientovi nedaří přijímat potvrzení ze služby Table Service. Pokud je problém přerušovaný, měli byste zachytit chybu "HTTP (404) Nenalezeno" a zaznamenat ji do klienta, ale umožněte klientovi, aby pokračoval.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klient přijímá zprávy HTTP 409 (konflikt).
Následující tabulka ukazuje extrakci z protokolu na straně serveru pro dvě operace klienta: **DeleteIfExists** , za kterým následuje **CreateIfNotExists** , pomocí stejného názvu kontejneru objektů BLOB. Každá operace klienta má za následek odeslání dvou požadavků na server, nejprve **GetContainerProperties** požadavek na kontrolu existence kontejneru a za ním požadavek **DeleteContainer** nebo **CreateContainer** .

| Timestamp | Operace | Výsledek | Název kontejneru | ID požadavku klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Kód v klientské aplikaci odstraní a pak okamžitě znovu vytvoří kontejner objektů BLOB se stejným názvem: metoda **CreateIfNotExists** (ID žádosti klienta bc881924-...) nakonec selhává s chybou HTTP 409 (konflikt). Když klient odstraní kontejner, objekt blob, tabulku nebo frontu, chvilku potrvá, než bude daný název opět volný.

Klientská aplikace by měla pokaždé, když vytvoří nový kontejner, použít jedinečný název, pokud je tento postup odstraňování a vytváření běžný.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Metriky zobrazují nízké PercentSuccess nebo položky protokolu analýzy mají operace se stavem transakce ClientOtherErrors.
Metrika **PercentSuccess** zachycuje procento operací, které byly úspěšné na základě jejich stavového kódu http. Operace se stavovým kódem 2XX se počítají jako úspěšné, zatímco operace se stavovým kódem v 3XX, 4XX a 5XX rozsahy se počítají jako neúspěšné a snižují hodnotu metriky **PercentSuccess** . V souborech protokolu úložiště na straně serveru se tyto operace zaznamenávají se stavem transakce **ClientOtherErrors**.

Je důležité si uvědomit, že tyto operace byly úspěšně dokončeny, a proto nemají vliv na jiné metriky, jako je dostupnost. Některé příklady operací, které jsou úspěšně spouštěny, ale mohou mít za následek neúspěšné kódy stavu HTTP:

* **ResourceNotFound** (nenalezeno 404), například z požadavku Get do objektu blob, který neexistuje.
* **ResourceAlreadyExists** (konflikt 409), například z operace **CreateIfNotExist** , kde prostředek již existuje.
* **ConditionNotMet** (neupraveno 304), například z podmíněné operace, například když klient odešle hodnotu **ETag** a hlavičku HTTP **If-None-Match** pro vyžádání obrázku pouze v případě, že byla od poslední operace aktualizována.

Seznam běžných chybových kódů REST API, které služby úložiště vrací na stránce [common REST API Error code](/rest/api/storageservices/Common-REST-API-Error-Codes).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště.
Pokud vidíte náhlé, neočekávané změny využití kapacity v účtu úložiště, můžete tyto důvody prozkoumat tak, že nejprve prohlížíte metriky dostupnosti. například zvýšení počtu nezdařených žádostí o odstranění může vést ke zvýšení velikosti úložiště objektů blob, které používáte jako operace čištění specifické pro aplikaci, které jste pravděpodobně očekávali, nefungují podle očekávání (například kvůli tomu, že tokeny SAS použité pro uvolnění místa vypršely).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>K vašemu problému dochází při použití emulátoru úložiště pro vývoj nebo testování.
Emulátor úložiště se obvykle používá během vývoje a testování, aby nedocházelo k požadavku na účet služby Azure Storage. K běžným problémům, ke kterým může dojít při použití emulátoru úložiště, patří:

* [Funkce X nefunguje v emulátoru úložiště.]
* [Chyba: hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu při použití emulátoru úložiště.]
* [Spuštění emulátoru úložiště vyžaduje oprávnění správce.]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funkce X nefunguje v emulátoru úložiště.
Emulátor úložiště nepodporuje všechny funkce služeb Azure Storage, jako je třeba služba File Service. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

U funkcí, které emulátor úložiště nepodporuje, použijte službu Azure Storage v cloudu.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Chyba: hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu při použití emulátoru úložiště.
Testujete aplikaci, která používá knihovnu klienta úložiště pro emulátor místního úložiště, a volání metod, jako je **CreateIfNotExists** , selže s chybovou zprávou "hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu." To znamená, že verze emulátoru úložiště, kterou používáte, nepodporuje verzi klientské knihovny pro úložiště, kterou používáte. Klientská knihovna pro úložiště přidá hlavičku **x-MS-Version** do všech požadavků, které dělá. Pokud emulátor úložiště nerozpozná hodnotu v hlavičce **x-MS-Version** , požadavek odmítne.

Pomocí protokolů klienta knihovny úložiště můžete zobrazit hodnotu **záhlaví x-MS-Version** , kterou posílá. Můžete také zobrazit hodnotu **hlavičky x-MS-Version** , pokud použijete Fiddler k trasování požadavků z klientské aplikace.

K tomuto scénáři obvykle dochází v případě, že nainstalujete a použijete nejnovější verzi klientské knihovny pro úložiště bez aktualizace emulátoru úložiště. Měli byste buď nainstalovat nejnovější verzi emulátoru úložiště, nebo místo emulátoru použít cloudové úložiště pro vývoj a testování.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Spuštění emulátoru úložiště vyžaduje oprávnění správce.
Po spuštění emulátoru úložiště se zobrazí výzva k zadání přihlašovacích údajů správce. K tomu dochází pouze v případě, že inicializujete emulátor úložiště poprvé. Po inicializaci emulátoru úložiště nepotřebujete pro jeho opětovné spuštění oprávnění správce.

Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md). Můžete také inicializovat emulátor úložiště v aplikaci Visual Studio, který bude také vyžadovat oprávnění správce.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Narazíte na problémy s instalací sady Azure SDK pro .NET.
Při pokusu o instalaci sady SDK dojde k chybě při pokusu o instalaci emulátoru úložiště na místním počítači. Protokol instalace obsahuje jednu z následujících zpráv:

* CAQuietExec: Chyba: nepovedlo se získat přístup k instanci SQL.
* CAQuietExec: Chyba: nepovedlo se vytvořit databázi.

Příčinou je problém s existující instalací LocalDB. Ve výchozím nastavení emulátor úložiště používá LocalDB k uchování dat při simulaci služeb Azure Storage. Instanci LocalDB můžete resetovat spuštěním následujících příkazů v okně příkazového řádku a teprve potom se pokusíte nainstalovat sadu SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Příkaz **Delete** odebere všechny staré databázové soubory z předchozích instalací emulátoru úložiště.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Máte jiný problém se službou úložiště.
Pokud předchozí oddíly řešení potíží neobsahují problém, který používáte se službou úložiště, měli byste při diagnostice a řešení potíží přijmout následující postup.

* Podívejte se na metriky a zjistěte, jestli nedošlo ke změně očekávaného chování na základním řádku. Z metriky můžete určit, jestli je problém přechodný nebo trvalý, a u kterých operací s úložištěm dochází k ovlivnění problému.
* Informace o metrikách můžete použít k vyhledání podrobnějších informací o všech chybách, ke kterým dochází, v datech protokolu na straně serveru. Tyto informace vám můžou pomoct při odstraňování problému a jeho řešení.
* Pokud informace v protokolech na straně serveru nestačí k úspěšnému vyřešení tohoto problému, můžete použít protokoly klientské knihovny pro úložiště k prozkoumání chování klientské aplikace a nástroje, jako je Fiddler, Wireshark k prozkoumání vaší sítě.

Další informace o použití Fiddler najdete v části "[Příloha 1: použití Fiddler pro zachycení přenosů http a HTTPS]".

Další informace o použití nástroje Wireshark najdete v[dodatku 2: použití nástroje Wireshark k zachycení síťového provozu].

## <a name="appendices"></a><a name="appendices"></a>Dodatky
Přílohy popisují několik nástrojů, které mohou být užitečné při diagnostice a řešení potíží s Azure Storage (a dalšími službami). Tyto nástroje nejsou součástí Azure Storage a některé jsou produkty třetích stran. V takovém případě se nástroje popsané v těchto dodatcích nevztahují na žádnou smlouvu o podpoře, kterou máte Microsoft Azure nebo Azure Storage, a proto jako součást procesu hodnocení byste měli prostudovat možnosti licencování a podpory, které jsou dostupné od poskytovatelů těchto nástrojů.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Příloha 1: použití Fiddler k zachycení přenosů HTTP a HTTPS
[Fiddler](https://www.telerik.com/fiddler) je užitečný nástroj pro analýzu přenosů http a HTTPS mezi klientskou aplikací a službou Azure Storage, kterou používáte.

> [!NOTE]
> Fiddler umí dekódovat přenosy HTTPS; měli byste si pečlivě přečíst dokumentaci k Fiddler a porozumět tomu, jak to dělá, a porozumět vlivům na zabezpečení.
>
>

V tomto dodatku najdete Stručný návod, jak nakonfigurovat Fiddler pro zachycení provozu mezi místním počítačem, na který jste nainstalovali Fiddler a službu Azure Storage.

Po spuštění Fiddler zahájí zachycení provozu HTTP a HTTPS na místním počítači. Níže jsou uvedeny některé užitečné příkazy pro řízení Fiddler:

* Zastavte a zahajte zachytávání provozu. V hlavní nabídce přejděte na **soubor** a potom klikněte na **zachytit provoz** . tím přepnete zachytávání.
* Uložit zachycená data o provozu. V hlavní nabídce přejděte na **soubor**, klikněte na **Uložit** a pak klikněte na **všechny relace**: to vám umožní uložit provoz do souboru archivu relace. Archiv relace můžete znovu znovu načíst pro účely analýzy nebo ho odeslat, pokud se požaduje podpora Microsoftu.

Pokud chcete omezit objem přenosů, které Fiddler zachycuje, můžete použít filtry, které nakonfigurujete na kartě **filtry** . Následující snímek obrazovky ukazuje filtr, který zachycuje jenom provoz odeslaný do koncového bodu **contosoemaildist.Table.Core.Windows.NET** úložiště:

![Snímek obrazovky, který zobrazuje filtr, který zachycuje jenom provoz odeslaný do koncového bodu contosoemaildist.table.core.windows.net úložiště.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Příloha 2: použití programu Wireshark k zachycení síťového provozu
[Wireshark](https://www.wireshark.org/) je analyzátor síťových protokolů, který umožňuje zobrazit podrobné informace o paketech pro široké spektrum síťových protokolů.

Následující postup ukazuje, jak zachytit podrobné informace o paketech pro provoz z místního počítače, do které jste nainstalovali Nástroj Wireshark do služby Table Service ve vašem účtu úložiště Azure.

1. Spusťte na svém místním počítači Nástroj Wireshark.
2. V části **Start** vyberte místní síťové rozhraní nebo rozhraní, která jsou připojená k Internetu.
3. Klikněte na možnost **zachytit možnosti**.
4. Přidejte filtr do textového pole **filtru zachycení** . Například **hostitel contosoemaildist.Table.Core.Windows.NET** nakonfiguruje Nástroj Wireshark, aby zachytával pouze pakety odeslané do nebo z koncového bodu služby Table Service v účtu úložiště **contosoemaildist** . Podívejte se na [úplný seznam zachycených filtrů](https://wiki.wireshark.org/CaptureFilters).

   ![Snímek obrazovky, který ukazuje, jak přidat filtr do textového pole filtru zachycení.][6]
5. Klikněte na tlačítko **Start**. Nástroj Wireshark nyní bude zachytit všechny pakety odeslané do nebo z koncového bodu služby Table Service při použití klientské aplikace na místním počítači.
6. Po dokončení klikněte v hlavní nabídce na **zachytit** a pak na **zastavit**.
7. Pokud chcete zachycená data uložit do digitalizačního souboru Wireshark, klikněte v hlavní nabídce na **soubor** a pak na **Uložit**.

Nástroj WireShark vyzvýrazní všechny chyby, které se nachází v okně **packetlist** . K zobrazení souhrnu chyb a upozornění můžete použít také okno **informace o poradci** (kliknutím na tlačítko **analyzovat**, **informace o odborném** programu).

![Snímek obrazovky zobrazující okno informace o poradci, kde můžete zobrazit souhrn chyb a upozornění.][7]

Můžete se také rozhodnout zobrazit data TCP jako aplikační vrstvu, a to tak, že pravým tlačítkem myši kliknete na data TCP a vyberete **sledovat datový proud TCP**. To je užitečné, pokud jste si vychytili výpis bez filtru zachycení. Další informace najdete v [následujících datových proudech TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Snímek obrazovky, který ukazuje, jak zobrazit data protokolu TCP jako aplikační vrstvu.][8]

> [!NOTE]
> Další informace o použití nástroje Wireshark najdete v [příručce uživatelů nástroje Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Příloha 4: použití Excelu k zobrazení metrik a dat protokolu
Řada nástrojů vám umožňuje stahovat data metrik úložiště z Azure Table Storage ve formátu s oddělovači, který usnadňuje načítání dat do Excelu pro zobrazení a analýzu. Data protokolování úložiště z Azure Blob Storage jsou už ve formátu s oddělovači, který můžete načíst do Excelu. Budete ale muset přidat vhodné záhlaví sloupců na základě informací ve [formátu protokolu analýza úložiště](/rest/api/storageservices/Storage-Analytics-Log-Format) a [Analýza úložiště schématu tabulky metrik](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

Import dat protokolování úložiště do Excelu po jeho stažení z úložiště objektů BLOB:

* V nabídce **data** klikněte na **text**.
* Přejděte k souboru protokolu, který chcete zobrazit, a klikněte na **importovat**.
* V kroku 1 **Průvodce importem textu** vyberte **odděleno**.

V kroku 1 **Průvodce importem textu** vyberte jako jediný oddělovač **středník** a jako **kvalifikátor textu** zvolte dvojité uvozovky. Pak klikněte na **Dokončit** a zvolte, kam umístit data do sešitu.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Příloha 5: monitorování pomocí Application Insights pro Azure DevOps
Jako součást monitorování výkonu a dostupnosti můžete použít také funkci Application Insights pro Azure DevOps. Tento nástroj může:

* Ujistěte se, že je webová služba k dispozici a reaguje. Bez ohledu na to, jestli je vaše aplikace Web nebo aplikace zařízení, která používá webovou službu, může otestovat vaši adresu URL každých několik minut z umístění po celém světě a dát vám jistotu, že dojde k nějakému problému.
* Rychle Diagnostikujte všechny problémy s výkonem nebo výjimky ve webové službě. Zjistěte, jestli se roztáhnou procesor nebo jiné prostředky, Získejte trasování zásobníku z výjimek a můžete je snadno prohledávat prostřednictvím trasování protokolů. Pokud výkon aplikace klesne pod přijatelná omezení, Microsoft vám pošle e-mail. Můžete monitorovat webové služby .NET i Java.

Další informace najdete v [Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Další kroky

Další informace o analýzách v Azure Storage najdete v těchto materiálech:

* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Analýza úložiště](storage-analytics.md)
* [Metriky služby Storage Analytics](storage-analytics-metrics.md)
* [Schéma tabulky metrik analýzy úložiště](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Protokoly analýzy úložiště](storage-analytics-logging.md)
* [Formát protokolu Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Úvod]: #introduction
[Jak je tato příručka organizována]: #how-this-guide-is-organized

[Monitorování služby úložiště]: #monitoring-your-storage-service
[Stav služby monitorování]: #monitoring-service-health
[Kapacita monitorování]: #monitoring-capacity
[Monitorování dostupnosti]: #monitoring-availability
[Monitorování výkonu]: #monitoring-performance

[Diagnostikování problémů s úložištěm]: #diagnosing-storage-issues
[Problémy se stavem služby]: #service-health-issues
[Problémy s výkonem]: #performance-issues
[Diagnostikování chyb]: #diagnosing-errors
[Problémy emulátoru úložiště]: #storage-emulator-issues
[Nástroje protokolování úložiště]: #storage-logging-tools
[Používání nástrojů pro protokolování sítě]: #using-network-logging-tools

[Komplexní trasování]: #end-to-end-tracing
[Korelace dat protokolu]: #correlating-log-data
[ID požadavku klienta]: #client-request-id
[ID žádosti serveru]: #server-request-id
[Časová razítka]: #timestamps

[Pokyny k odstraňování potíží]: #troubleshooting-guidance
[Metrika ukazuje vysokou hodnotu AverageE2ELatency a nízkou hodnotu AverageServerLatency.]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje nízkou hodnotu AverageE2ELatency i hodnotu AverageServerLatency, ale latence klienta je vysoká.]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrika ukazuje vysokou hodnotu AverageServerLatency.]: #metrics-show-high-AverageServerLatency
[Dochází k neočekávaným zpožděním při doručování zpráv ve frontě]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrika ukazuje zvýšení u PercentThrottlingError.]: #metrics-show-an-increase-in-PercentThrottlingError
[Přechodné zvýšení v PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
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

[Metriky zobrazují nízké PercentSuccess nebo položky protokolu analýzy mají operace se stavem transakce ClientOtherErrors.]: #metrics-show-low-percent-success
[Metriky kapacity ukazují neočekávané zvýšení využití kapacity úložiště.]: #capacity-metrics-show-an-unexpected-increase
[K vašemu problému dochází při použití emulátoru úložiště pro vývoj nebo testování.]: #your-issue-arises-from-using-the-storage-emulator
[Funkce X nefunguje v emulátoru úložiště.]: #feature-X-is-not-working
[Chyba: hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu při použití emulátoru úložiště.]: #error-HTTP-header-not-correct-format
[Spuštění emulátoru úložiště vyžaduje oprávnění správce.]: #storage-emulator-requires-administrative-privileges
[Narazíte na problémy s instalací sady Azure SDK pro .NET.]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Máte jiný problém se službou úložiště.]: #you-have-a-different-issue-with-a-storage-service

[Dodatky]: #appendices
[Příloha 1: použití Fiddler k zachycení přenosů HTTP a HTTPS]: #appendix-1
[Příloha 2: použití programu Wireshark k zachycení síťového provozu]: #appendix-2
[Příloha 4: použití Excelu k zobrazení metrik a dat protokolu]: #appendix-4
[Příloha 5: monitorování pomocí Application Insights pro Azure DevOps]: #appendix-5

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
