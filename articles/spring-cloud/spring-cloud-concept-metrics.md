---
title: Principy metrik pro jarní cloud Azure
description: Naučte se kontrolovat metriky v Azure jaře cloudu.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 746366675501c80776f0a4308e22250f344a00c9
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343434"
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

Výchozí zobrazení zahrnuje všechny metriky application's ve službě Azure jarní Cloud Service společně. Metriky jedné aplikace nebo instance je možné filtrovat v zobrazení.  Klikněte na **Přidat filtr**, nastavte vlastnost na **aplikace**a v textovém poli **hodnoty** vyberte cílovou aplikaci, kterou chcete monitorovat. 

Můžete použít dva druhy filtrů (vlastnosti):
* Aplikace: filtrovat podle názvu aplikace
* Instance: filtrovat podle instance aplikace

![Filtry metrik](media/metrics/metrics-7.png)

Můžete také použít možnost **použít rozdělení** , která nakreslí více řádků pro jednu aplikaci:

![Rozdělení metriky](media/metrics/metrics-8.png)

>[!TIP]
> Na stránce metriky můžete vytvořit vlastní grafy a připnout je na **řídicí panel**. Začněte pojmenováním grafu.  Potom **v pravém horním rohu vyberte Připnout na řídicí panel**. Nyní se můžete podívat na svou aplikaci na **řídicím panelu**portálu.

## <a name="user-metrics-options"></a>Možnosti metrik uživatelů

V následujících tabulkách jsou uvedeny dostupné metriky a podrobnosti.

### <a name="error"></a>Chyba
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Globální chyba Tomcat (zastaralé)<br><br>(bude odebráno po 1. červenci 2020)  | Tomcat. Global. Error | Počet | Počet chyb, které se vyskytují u zpracovaných požadavků |
>| Tomcat. Global. Error | Tomcat. Global. Error | Počet | Počet chyb, které se vyskytují u zpracovaných požadavků |

### <a name="performance"></a>Výkon
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Procento využití procesoru v systému (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | System. CPU. Usage | Procento | Poslední využití procesoru pro celý systém. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že všechny procesory byly během neaktivního časového období nečinné, zatímco hodnota 1,0 znamená, že všechny procesory aktivně 100 běžely v nedávných obdobích během nedodržení% času.|
>| System. CPU. Usage | System. CPU. Usage | Procento | Poslední využití procesoru pro celý systém. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že všechny procesory byly během neaktivního časového období nečinné, zatímco hodnota 1,0 znamená, že všechny procesory aktivně 100 běžely v nedávných obdobích během nedodržení% času.|
>| Procento využití procesoru aplikací (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Procento využití procesoru aplikací | Procento | Poslední využití procesoru pro proces prostředí Java Virtual Machine. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že žádný z procesorů neběžel v nedávných časových obdobích vlákna z procesu JVM, zatímco hodnota 1,0 znamená, že všechny procesory aktivně spouštějí vlákna z JVM 100% času během nedávných období. Vlákna z JVM zahrnují vlákna aplikace a také interní vlákna JVM.|
>| Process. CPU. Usage | Procento využití procesoru aplikací | Procento | Poslední využití procesoru pro proces prostředí Java Virtual Machine. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že žádný z procesorů neběžel v nedávných časových obdobích vlákna z procesu JVM, zatímco hodnota 1,0 znamená, že všechny procesory aktivně spouštějí vlákna z JVM 100% času během nedávných období. Vlákna z JVM zahrnují vlákna aplikace a také interní vlákna JVM.|
>| Přiřazená paměť aplikace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. Memory. potvrzený | Bajty | Představuje velikost paměti, která je zaručena k dispozici pro použití v JVM. JVM může uvolnit paměť do systému a potvrzení může být menší než init. potvrzená bude vždycky větší nebo rovna hodnotě použité. |
>| JVM. Memory. potvrzený | JVM. Memory. potvrzený | Bajty | Představuje velikost paměti, která je zaručena k dispozici pro použití v JVM. JVM může uvolnit paměť do systému a potvrzení může být menší než init. potvrzená bude vždycky větší nebo rovna hodnotě použité. |
>| Využitá paměť aplikace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. Memory .Ed – použito | Bajty | Představuje velikost aktuálně využité paměti v bajtech. |
>| JVM. Memory .Ed – použito | JVM. Memory .Ed – použito | Bajty | Představuje velikost aktuálně využité paměti v bajtech. |
>| Maximální velikost paměti aplikace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. Memory. max | Bajty | Představuje maximální velikost paměti, kterou lze použít pro správu paměti. Velikost využité a potvrzené paměti bude vždy menší než nebo rovna hodnotě Max, pokud je definována hodnota max. Přidělení paměti může selhat, pokud se pokusí zvětšit využitou paměť tak, aby se použila > potvrzená i v případě, že použitá <= Max by byla pravdivá (například když je systém ve virtuální paměti nedostatek). |
>| JVM. Memory. max | JVM. Memory. max | Bajty | Představuje maximální velikost paměti, kterou lze použít pro správu paměti. Velikost využité a potvrzené paměti bude vždy menší než nebo rovna hodnotě Max, pokud je definována hodnota max. Přidělení paměti může selhat, pokud se pokusí zvětšit využitou paměť tak, aby se použila > potvrzená i v případě, že použitá <= Max by byla pravdivá (například když je systém ve virtuální paměti nedostatek). |
>| Maximální velikost dostupných starých dat generace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. max. data. Size | Bajty | Využití paměti ve špičce starého fondu paměti generace od spuštění virtuálního počítače Java. |
>| JVM. GC. max. data. Size | JVM. GC. max. data. Size | Bajty | Využití paměti ve špičce starého fondu paměti generace od spuštění virtuálního počítače Java. |
>| Stará velikost dat generace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. Live. data. Size | Bajty | Velikost staré generace fondu paměti po úplném GC. |
>| JVM. GC. Live. data. Size | JVM. GC. Live. data. Size | Bajty | Velikost staré generace fondu paměti po úplném GC. |
>| Stará velikost dat generace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. Memory. propagovaný | Bajty | Počet kladných zvýšení velikosti staré paměti generace před GC na po GC. |
>| JVM. GC. Memory. propagovaný | JVM. GC. Memory. propagovaný | Bajty | Počet kladných zvýšení velikosti staré paměti generace před GC na po GC. |
>| Zvýšení úrovně velikosti dat pro mladé generace (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. Memory. alokovaný | Bajty | Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC do dalšího. |
>| JVM. GC. Memory. alokovaný | JVM. GC. Memory. alokovaný | Bajty | Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC do dalšího. |
>| Počet pozastavení GC (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. Pause (celkový počet) | Počet | Celkový počet GC po zahájení této JMV, včetně mladého a starého GC. |
>| JVM. GC. Pause. Total. Count | JVM. GC. Pause (celkový počet) | Počet | Celkový počet GC po zahájení této JMV, včetně mladého a starého GC. |
>| Celkový čas pozastavení GC (zastaralé)<br><br>(bude odebráno po 1. červenci 2020) | JVM. GC. Pause (celkem-Time) | Milisekund | Celkový čas GC spotřebovaný po spuštění tohoto JMVu, včetně mladého a starého GC. |
>| JVM. GC. Pause. Total. time | JVM. GC. Pause (celkem-Time) | Milisekund | Celkový čas GC spotřebovaný po spuštění tohoto JMVu, včetně mladého a starého GC. |

### <a name="request"></a>Žádost
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Celkový počet odeslaných bajtů Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Global. odesláno | Bajty | Množství odeslaného webového serveru datového Tomcat |
>| Tomcat. Global. odesláno | Tomcat. Global. odesláno | Bajty | Množství odeslaného webového serveru datového Tomcat |
>| Celkový počet přijatých bajtů Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Global. Received | Bajty | Množství přijatého webového serveru datového Tomcat |
>| Tomcat. Global. Received | Tomcat. Global. Received | Bajty | Množství přijatého webového serveru datového Tomcat |
>| Celkový čas žádosti Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Global. Request (celkem-Time) | Milisekund | Celková doba, jakou Tomcat webový server zpracovává žádosti |
>| Celkový počet žádostí Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Global. Request (celkový počet) | Počet | Celkový počet zpracovaných požadavků Tomcat webového serveru |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (celkový počet) | Počet | Celkový počet zpracovaných požadavků Tomcat webového serveru |
>| Maximální čas požadavku Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Global. Request. max | Milisekund | Maximální doba, po kterou webový server Tomcat zpracuje požadavek |
>| Tomcat. Global. Request. max | Tomcat. Global. Request. max | Milisekund | Maximální doba, po kterou webový server Tomcat zpracuje požadavek |

### <a name="session"></a>Relace
>[!div class="mx-tdCol2BreakAll"]
>| Name | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Maximální počet aktivních relací Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Sessions. Active. max | Počet | Maximální počet relací, které byly aktivní ve stejnou dobu |
>| Tomcat. Sessions. Active. max | Tomcat. Sessions. Active. max | Počet | Maximální počet relací, které byly aktivní ve stejnou dobu |
>| Maximální doba připojení relace Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Sessions. Alive. max | Milisekund | Nejdelší čas (v sekundách), po který vypršela relace s vypršenou platností |
>| Tomcat. Sessions. Alive. max | Tomcat. Sessions. Alive. max | Milisekund | Nejdelší čas (v sekundách), po který vypršela relace s vypršenou platností |
>| Počet vytvořených relací Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Sessions. Created | Počet | Počet relací, které byly vytvořeny |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Počet | Počet relací, které byly vytvořeny |
>| Počet neplatných relací Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Sessions. vypršela platnost | Počet | Počet relací, jejichž platnost vypršela |
>| Tomcat. Sessions. vypršela platnost | Tomcat. Sessions. vypršela platnost | Počet | Počet relací, jejichž platnost vypršela |
>| Počet odmítnutých relací Tomcat (zastaralé)<br><br>(odebrané po 1. červenci 2020) | Tomcat. Sessions. odmítnuto | Počet | Počet relací, které nebyly vytvořeny, protože bylo dosaženo maximálního počtu aktivních relací. |
>| Tomcat. Sessions. odmítnuto | Tomcat. Sessions. odmítnuto | Počet | Počet relací, které nebyly vytvořeny, protože bylo dosaženo maximálního počtu aktivních relací. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Počet | Počet aktivních relací Tomcat |

## <a name="see-also"></a>Viz také
* [Začínáme s Průzkumníkem metrik Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Další kroky
* [Kurz: monitorování jarních cloudových prostředků pomocí výstrah a skupin akcí](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvóty a plány služeb pro jarní Cloud v Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

