---
title: 'Přehled: Co je náhled přehledů Azure Time Series Insights? - Přehledy azure time series | Dokumenty společnosti Microsoft'
description: Další informace o změnách, vylepšeních a funkcích v Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1e4f72300752c93659db4edd4610464dbebf2503
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77014415"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Co je Azure Time Series Insights ve verzi Preview?

Azure Time Series Insights Preview je komplexní nabídka platformy jako služby (PaaS). Můžete ji použít ke shromažďování, zpracování, ukládání, analýze a dotazování dat ve škále Internetu věcí (IoT) – dat, která jsou vysoce kontextualizovaná a optimalizovaná pro časové řady. 

Time Series Insights je určen pro průzkum ad hoc dat a provozní analýzu. Jedná se o rozšiřitelnou a přizpůsobenou nabídku služeb, která splňuje široké potřeby průmyslových nasazení IoT.

## <a name="video"></a>Video

Přečtěte si další informace o Azure Time Series Insights Preview.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definice dat IoT

Průmyslová data IoT v organizacích náročných na aktiva často postrádají strukturální konzistenci vzhledem k různorodé povaze zařízení a senzorů v průmyslovém prostředí. Data z těchto datových proudů jsou charakterizována významnými mezerami a někdy i poškozenými zprávami a falešnými údaji. Data IoT jsou často smysluplná v kontextu dalších datových vstupů, které pocházejí z prvotřídních nebo třetích zdrojů, jako je CRM nebo ERP, které přidávají kontext pro koncové pracovní postupy. Vstupy ze zdrojů dat třetích stran, jako jsou data o počasí, mohou pomoci rozšířit telemetrické proudy v dané instalaci. 

To vše znamená, že pouze zlomek dat se používá pro provozní a obchodní účely a analýza vyžaduje kontextovou situaci. Průmyslová data jsou často historizována pro hloubkovou analýzu v delším časovém rozpětí, aby bylo třeba pochopit a korelovat trendy. Přerozdělení shromážděných dat IoT na užitečné přehledy vyžaduje: 

* Zpracování dat pro čištění, filtrování, interpolaci, transformaci a přípravu dat pro analýzu.
* Struktura procházet a pochopit data, to znamená normalizovat a kontextualizovat data.
* Nákladově efektivní úložiště pro dlouhé nebo nekonečné uchovávání zpracovaných (nebo odvozených) dat a nezpracovaných dat.

Tyto údaje poskytují konzistentní, komplexní, aktuální a správné informace pro obchodní analýzu a vykazování.

Následující obrázek znázorňuje typický tok dat IoT.

[![Tok dat IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslové IoT

Krajina IoT je různorodá se zákazníky, kteří pokrývají různé průmyslové segmenty, včetně výroby, automobilového průmyslu, energetiky, veřejných služeb, inteligentních budov a poradenství. Na této široké škále průmyslových ioT trhu se stále vyvíjejí cloudová nativní řešení, která poskytují komplexní analýzy zaměřené na rozsáhlá data IoT. 

Azure Time Series Insights řeší tuto potřebu trhu tím, že poskytuje komplexní analytické řešení IoT na klíč s bohatým sémantickým modelováním pro kontextovou analýzu dat časových řad, přehledy založené na aktivech a nejlepší uživatelské prostředí ve své třídě pro zjišťování, trendy, detekce anomálií a provozní inteligenci. 

Bohatá platforma provozní analýzy v kombinaci s našimi interaktivními možnostmi zkoumání dat můžete pomocí Time Series Insights odvodit větší hodnotu z dat shromážděných z prostředků IoT. Nabídka náhledu podporuje: 

* Vícevrstvé úložné řešení s teplou a studenou analýzou podporuje poskytování zákazníkům možnost směrovat data mezi teplými a studenými pro interaktivní analýzu přes teplá data a provozní inteligenci po celá desetiletí historických dat. 

    *   Vysoce interaktivní teplé analytické řešení pro časté a velké množství dotazů v kratším časovém rozpětí 
    *   Škálovatelné, výkonné a nákladově optimalizované datové jezero časových řad založené na Azure Storage umožňuje zákazníkům trend ovat data časových řad za několik let. 

* Podpora sémantického modelu, která popisuje doménu a metadata spojená s odvozenými a nezpracovanými signály z prostředků a zařízení.

* Flexibilní analytická platforma pro ukládání historických dat časových řad v účtu Azure Storage vlastněném zákazníkem, což zákazníkům umožňuje vlastnictví jejich dat IoT. Data jsou uložena ve formátu Apache Parquet s otevřeným zdrojovým kódem, který umožňuje připojení a propojení mezi různými datovými scénáři, včetně prediktivní analýzy, strojového učení a dalších vlastních výpočtů provedených pomocí známých technologií včetně Spark, Databricks a Jupyter.

* Bohatá analýza s rozšířenými rozhraními API dotazů a uživatelským prostředím, která kombinují přehledy dat založené na aktivech s bohatými analýzami dat ad hoc s podporou interpolačních, skalárních a agregačních funkcí, kategorických proměnných, bodových grafů a času posunu času sériové signály pro hloubkovou analýzu.

*   Platforma podnikové úrovně pro podporu potřeb v oblasti škálování, výkonu, zabezpečení a spolehlivosti našich podnikových zákazníků IoT.

* Rozšiřitelnost a podpora integrace pro komplexní analýzy. Time Series Insights poskytuje rozšiřitelnou analytickou platformu pro různé scénáře dat. Konektor Time Series Insights Power BI umožňuje zákazníkům pořštit dotazy, které dělají v Time Series Insights, přímo do Power BI a získat tak jednotný přehled o svých BI a analýzách časových řad v jediném skleněném podokně.

Následující diagram znázorňuje tok dat na vysoké úrovni.

  [![Klíčové funkce](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights poskytuje škálovatelný model průběžných plateb pro zpracování dat, úložiště (data a metadata) a dotaz, který zákazníkům umožňuje optimalizovat jejich využití tak, aby vyhovovalo jejich obchodním požadavkům. 
 
Se zavedením těchto klíčových průmyslových funkcí IoT, Time Series Insights také poskytuje následující klíčové výhody.  

| | |
| ---| ---|
| Vícevrstvé úložiště pro data časových řad v měřítku IoT | Pomocí kanálu zpracování sdílených dat pro příjem dat můžete ingestovat data do teplých i studených úložišť. Pro ukládání velkých objemů dat použijte teplé úložiště pro interaktivní dotazy a chladírenské úložiště. Další informace o tom, jak využít výhod vysoce výkonných dotazů založených na datových zdrojích, naleznete [v tématu dotazy](./time-series-insights-update-tsq.md). |
| Model časových řad pro kontextualizaci nezpracovaných telemetrických dat a odvození přehledů založených na aktivech | Model časových řad můžete použít k vytvoření instancí, hierarchií, typů a proměnných pro data časových řad. Další informace o modelu časových řad naleznete v [tématu Model časových řad](./time-series-insights-update-tsm.md).  |
| Hladká a nepřetržitá integrace s dalšími datovými řešeními | Data v cold store Time Series Insights jsou [uložena](./time-series-insights-update-storage-ingress.md) v open source souborech Apache Parquet. To umožňuje integraci dat s jinými datovými řešeními první nebo třetí strany pro scénáře, které zahrnují business intelligence, pokročilé strojové učení a prediktivní analýzy. |
| Průzkum dat v reálném čase | Uživatelské prostředí [průzkumníka Azure Time Series Insights Preview](./time-series-insights-update-explorer.md) poskytuje vizualizaci pro všechna data streamovaná prostřednictvím kanálu ingestování. Po připojení zdroje událostí můžete zobrazit, prozkoumat a dotazovat data událostí. Tímto způsobem můžete ověřit, zda zařízení vydává data podle očekávání. Můžete také sledovat prostředek IoT pro zdraví, produktivitu a celkovou efektivitu. | 
| Rozšiřitelnost a integrace | Integrace konektoru Power BI Azure Time Series Insights je dostupná přímo v uživatelském prostředí Aplikace Time Series Explorer prostřednictvím možnosti **Export,** která zákazníkům umožňuje exportovat dotazy na časové řady, které vytvářejí v našem uživatelském prostředí, přímo do plochy Power BI a zobrazit grafy časových řad spolu s dalšíanalýzou BI. To otevírá dveře nové třídě scénářů pro průmyslové podniky IoT, které investovaly do Power BI tím, že poskytují jednu skleněnou tabuli nad analytikou z různých zdrojů dat, včetně časových řad IoT. | 
| Vlastní aplikace postavené na platformě Time Series Insights | Time Series Insights podporuje [javascriptovou sadu SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Sada SDK poskytuje bohaté ovládací prvky a zjednodušený přístup k dotazům. Pomocí sady SDK můžete vytvářet vlastní aplikace IoT nad časem Přehledy, které budou vyhovovat vašim obchodním potřebám. Můžete také použít časové řady Insights [dotazu API](./time-series-insights-update-tsq.md) přímo k diskuzi dat do vlastních aplikací IoT. |

## <a name="next-steps"></a>Další kroky

Začínáme s Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Příručka Rychlý start](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Náhled případy použití](./time-series-insights-update-use-cases.md)