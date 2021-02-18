---
title: Principy metrik pro jarní cloud Azure
description: Naučte se kontrolovat metriky v Azure jaře cloudu.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5506d2da0ccf09de7db7aff744984ae93c59fd32
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571714"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Principy metrik pro jarní cloud Azure

Průzkumník metrik Azure je součást portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají v metrikách. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků. 

V Azure jaře cloudu existují dvě hlediska metriky.
* Grafy na jednotlivých stránkách s přehledem aplikace
* Stránka běžné metriky

 ![Grafy metrik](media/metrics/metrics-1.png)

Grafy v **přehledu** aplikace poskytují rychlé kontroly stavu pro každou aplikaci. Stránka společná **metrika** obsahuje všechny metriky, které jsou k dispozici pro referenci. Na stránce běžné metriky můžete vytvořit vlastní grafy a připnout je na řídicí panel.

## <a name="application-overview-page"></a>Stránka s přehledem aplikace
Vyberte aplikaci v **aplikacích** pro hledání grafů na stránce Přehled.  

 ![Správa metrik aplikace](media/metrics/metrics-2.png)

Na stránce **Přehled aplikací** každé aplikace se zobrazuje graf metrik, který vám umožní provést rychlou kontrolu stavu aplikace.  

 ![Přehled metrik aplikace](media/metrics/metrics-3.png)

Azure jaře Cloud nabízí tyto pět grafů s metrikami, které se aktualizují každou minutu:

* **Chyby serveru http**: počet chyb pro požadavky HTTP na aplikaci
* **Data v**: bajtů přijatých vaší aplikací
* **Výstupní data**: počet bajtů odeslaných aplikací
* **Požadavky**: žádosti přijaté vaší aplikací
* **Průměrná doba odezvy**: Průměrná doba odezvy z vaší aplikace

U grafu můžete vybrat časový rozsah od jedné hodiny do sedmi dnů.

## <a name="common-metrics-page"></a>Stránka běžné metriky

**Metriky** v levém navigačním podokně odkazují na stránku běžné metriky.

Nejdřív vyberte metriky, které chcete zobrazit:

![Vybrat zobrazení metriky](media/metrics/metrics-4.png)

Podrobnosti o všech možnostech metrik najdete v níže uvedené [části](#user-metrics-options) .

V dalším kroku vyberte typ agregace pro každou metriku:

![Agregace metrik](media/metrics/metrics-5.png)

Typ agregace označuje, jak se mají v grafu agregovat body metriky podle času. Každá minuta obsahuje jeden nezpracovaný metrikový bod a typ předagregačních za minutu je předem definovaný typem metriky.
* Sum: sečte všechny hodnoty jako cílový výstup.
* Average: jako cílový výstup použijte průměrnou hodnotu v periodě.
* Max/min: jako cílový výstup použijte hodnotu Max/min v periodě.

Časový rozsah lze také upravit z posledních 30 minut na posledních 30 dní nebo do vlastního časového rozsahu.

![Úprava metriky](media/metrics/metrics-6.png)

Výchozí zobrazení zahrnuje všechny metriky application's ve službě Azure jarní Cloud Service společně. Metriky jedné aplikace nebo instance je možné filtrovat v zobrazení.  Klikněte na **Přidat filtr**, nastavte vlastnost na **aplikace** a v textovém poli **hodnoty** vyberte cílovou aplikaci, kterou chcete monitorovat. 

Můžete použít dva druhy filtrů (vlastnosti):
* Aplikace: filtrovat podle názvu aplikace
* Instance: filtrovat podle instance aplikace

![Filtry metrik](media/metrics/metrics-7.png)

Můžete také použít možnost **použít rozdělení** , která nakreslí více řádků pro jednu aplikaci:

![Rozdělení metriky](media/metrics/metrics-8.png)

>[!TIP]
> Na stránce metriky můžete vytvořit vlastní grafy a připnout je na **řídicí panel**. Začněte pojmenováním grafu.  Potom **v pravém horním rohu vyberte Připnout na řídicí panel**. Nyní se můžete podívat na svou aplikaci na **řídicím panelu** portálu.

## <a name="user-metrics-options"></a>Možnosti metrik uživatelů

V následujících tabulkách jsou uvedeny dostupné metriky a podrobnosti.

### <a name="error"></a>Chyba
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Tomcat. Global. Error | Tomcat. Global. Error | Počet | Počet chyb, ke kterým došlo ve zpracovaných žádostech |

### <a name="performance"></a>Výkon
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| System. CPU. Usage | System. CPU. Usage | Procento | Poslední využití procesoru pro celý systém (zastaralé a nedoporučuje se ho používat). Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že všechny procesory byly během neaktivního časového období nečinné, zatímco hodnota 1,0 znamená, že všechny procesory aktivně 100 běžely v nedávných obdobích během nedodržení% času.|
>| Process. CPU. Usage | Procento využití procesoru aplikací | Procento | Poslední využití CPU pro proces prostředí Java Virtual Machine (zastaralé a nedoporučuje se ho používat). Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že žádný z procesorů neběžel v nedávných časových obdobích vlákna z procesu JVM, zatímco hodnota 1,0 znamená, že všechny procesory aktivně spouštějí vlákna z JVM 100% času během nedávných období. Vlákna z JVM zahrnují vlákna aplikace a také interní vlákna JVM.|
>| AppCpuUsage | Využití CPU aplikací (Preview) | Procento | Poslední využití CPU procesu JVM proti procesoru přidělenému této aplikaci, hodnota typu Double v rozsahu [0,0, 1,0]. Hodnota 0,0 znamená, že žádný z procesorů neběžel v nedávných časových obdobích vlákna z procesu JVM, zatímco hodnota 1,0 znamená, že všechny procesory aktivně spouštějí vlákna z JVM 100% času během nedávných období. Vlákna z JVM zahrnují vlákna aplikace a také interní vlákna JVM.|
>| JVM. Memory. potvrzený | JVM. Memory. potvrzený | Bajty | Představuje velikost paměti, která je zaručena k dispozici pro použití v JVM. JVM může uvolnit paměť do systému a potvrzení může být menší než init. potvrzená bude vždycky větší nebo rovna hodnotě použité. |
>| JVM. Memory .Ed – použito | JVM. Memory .Ed – použito | Bajty | Představuje velikost aktuálně využité paměti v bajtech. |
>| JVM. Memory. max | JVM. Memory. max | Bajty | Představuje maximální velikost paměti, kterou lze použít pro správu paměti. Velikost využité a potvrzené paměti bude vždy menší než nebo rovna hodnotě Max, pokud je definována hodnota max. Přidělení paměti může selhat, pokud se pokusí zvětšit využitou paměť tak, aby se použila > potvrzená i v případě, že použitá <= Max by byla pravdivá (například když je systém ve virtuální paměti nedostatek). |
>| JVM. GC. max. data. Size | JVM. GC. max. data. Size | Bajty | Využití paměti ve špičce starého fondu paměti generace od spuštění virtuálního počítače Java. |
>| JVM. GC. Live. data. Size | JVM. GC. Live. data. Size | Bajty | Velikost staré generace fondu paměti po úplném GC. |
>| JVM. GC. Memory. propagovaný | JVM. GC. Memory. propagovaný | Bajty | Počet kladných zvýšení velikosti staré paměti generace před GC na po GC. |
>| JVM. GC. Memory. alokovaný | JVM. GC. Memory. alokovaný | Bajty | Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC do dalšího. |
>| JVM. GC. Pause. Total. Count | JVM. GC. Pause (celkový počet) | Počet | Celkový počet GC po zahájení této JMV, včetně mladého a starého GC. |
>| JVM. GC. Pause. Total. time | JVM. GC. Pause (celkem-Time) | Milisekund | Celkový čas GC spotřebovaný po spuštění tohoto JMVu, včetně mladého a starého GC. |

### <a name="performance-net"></a>Výkon (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|------|-----------------------------|------|---------|
>| Využití procesoru       | využití procesoru      | Procento      | Procento využití procesoru procesu vzhledem ke všem prostředkům systémového procesoru [0-100]. |
>| Pracovní sada     | pracovní sada    | Megabajty    | Množství pracovní sady používané procesem. |
>| Velikost haldy GC    | GC-velikost haldy   | Megabajty    | Celková velikost haldy hlášené systémem uvolňování paměti. |
>| Počet GC 0. generace  | gen-0-GC-Count | Počet        | Počet kolekcí uvolnění paměti generace 0 za sekundu |
>| Počet GC 1. generace  | Obecná-1-GC-Count | Počet        | Počet kolekcí uvolnění paměti 1. generace za sekundu |
>| Počet GC 2. generace  | Gen-2 – GC-Count | Počet        | Počet kolekcí uvolnění paměti 2. generace za sekundu |
>| Čas v GC      | TIMEin – GC      | Procento      | Procento času v uvolňování paměti od posledního uvolňování paměti. |
>| Velikost haldy 0. generace | gen-0-velikost     | Bajty        | Velikost haldy generace 0 |
>| Velikost haldy 1. generace | Obecná-1-velikost     | Bajty        | Velikost haldy 1. generace |
>| Velikost haldy 2. generace | Obecná 2 – velikost     | Bajty        | Velikost haldy 2. generace |
>| Velikost haldy LOH   | LOH-Size       | Bajty        | Velikost haldy haldy Large Object. |
>| Míra přidělení | přidělení – sazba     | Bajty        | Počet přidělených bajtů za sekundu |
>| Počet sestavení  | počet sestavení | Počet        | Počet načtených sestavení |
>| Počet výjimek | počet výjimek | Počet       | Počet výjimek za sekundu |
>| Počet vláken ve fondu vláken      | počet podprocesů              | Počet | Počet vláken fondu vláken. |
>| Sledovat počet kolizí zámků | Monitor-Lock-Content – Count        | Počet | Počet výskytů kolizí za sekundu při pokusu o provedení zámku monitoru. |
>| Délka fronty fondu vláken      | nevlákenná doba zařazení do fronty              | Počet | Délka fronty pracovních položek fondu vláken |
>| Počet dokončených položek ve fondu vláken | nedokončené dokončování – položky-počet | Počet | Počet dokončených pracovních položek fondu vláken |
>| Počet aktivních časovačů               | aktivní – časovač-počet               | Počet | Počet časovačů, které jsou aktuálně aktivní. Aktivní časovač je ten, který se zaregistruje v nějakém okamžiku v budoucnu a ještě není zrušený. |

Další informace najdete v tématu [čítače dotnet](/dotnet/core/diagnostics/dotnet-counters).

### <a name="request"></a>Žádost
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Tomcat. Global. odesláno | Tomcat. Global. odesláno | Bajty | Množství odeslaného webového serveru datového Tomcat |
>| Tomcat. Global. Received | Tomcat. Global. Received | Bajty | Množství přijatého webového serveru datového Tomcat |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (celkový počet) | Počet | Celkový počet zpracovaných požadavků Tomcat webového serveru |
>| Tomcat. Global. Request. max | Tomcat. Global. Request. max | Milisekund | Maximální doba, po kterou webový server Tomcat zpracuje požadavek |

### <a name="request-net"></a>Požadavek (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|------|-----------------------------|------|---------|
>| Počet žádostí za sekundu | požadavky – za sekundu | Počet | Frekvence požadavků. |
>| Celkový počet požadavků | celkem – požadavky | Počet | Celkový počet požadavků |
>| Aktuální požadavky | aktuální požadavky | Počet | Počet aktuálních požadavků. |
>| Neúspěšné požadavky | neúspěšné – požadavky | Počet | Počet neúspěšných žádostí |

Další informace najdete v tématu [čítače dotnet](/dotnet/core/diagnostics/dotnet-counters).

### <a name="session"></a>Relace
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Tomcat. Sessions. Active. max | Tomcat. Sessions. Active. max | Počet | Maximální počet relací, které byly aktivní ve stejnou dobu |
>| Tomcat. Sessions. Alive. max | Tomcat. Sessions. Alive. max | Milisekund | Nejdelší čas (v sekundách), po který vypršela relace s ukončenou platností |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Počet | Počet relací, které byly vytvořeny |
>| Tomcat. Sessions. vypršela platnost | Tomcat. Sessions. vypršela platnost | Počet | Počet relací, jejichž platnost vypršela |
>| Tomcat. Sessions. odmítnuto | Tomcat. Sessions. odmítnuto | Počet | Počet relací, které nebyly vytvořeny, protože bylo dosaženo maximálního počtu aktivních relací. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Počet | Počet aktivních relací Tomcat |

## <a name="see-also"></a>Viz také

* [Rychlý Start: monitorování jarních cloudových aplikací Azure díky protokolům, metrikám a trasování](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Začínáme s Průzkumníkem metrik Azure](../azure-monitor/essentials/metrics-getting-started.md)

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](./diagnostic-services.md)

## <a name="next-steps"></a>Další kroky

* [Kurz: monitorování jarních cloudových prostředků pomocí výstrah a skupin akcí](./spring-cloud-tutorial-alerts-action-groups.md)

* [Kvóty a plány služeb pro jarní Cloud v Azure](./spring-cloud-quotas.md)
