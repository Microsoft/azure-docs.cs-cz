---
title: Principy metrik pro jarní cloud Azure
description: Naučte se kontrolovat metriky v Azure jaře cloudu.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 682cc8383db5704b28b9f4d6b8c22c4bbfc3f1ab
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528194"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Principy metrik pro jarní cloud Azure

Průzkumník metrik Azure je součást portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají v metrikách. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků. 

V Azure jaře cloudu existují dva body zobrazení pro metriky.
* Grafy na jednotlivých stránkách s přehledem aplikace
* Stránka běžné metriky

 ![Grafy metrik](media/metrics/metrics-1.png)

Grafy v **přehledu** aplikace poskytují rychlé kontroly stavu pro každou aplikaci. Stránka společná **metrika** obsahuje všechny metriky, které jsou k dispozici pro referenci. Na stránce běžné metriky můžete vytvořit vlastní grafy a připnout je na řídicí panel.

## <a name="application-overview-page"></a>Stránka s přehledem aplikace
V části **Správa aplikací** vyberte aplikaci pro hledání grafů na stránce Přehled.  

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

V dalším kroku vyberte typ agregace pro každou metriku:

![Agregace metrik](media/metrics/metrics-5.png)

Typ agregace označuje, jak agregovat čas. Každou minutu je jeden bod metriky.
* Total: součet všech metrik jako cílový výstup.
* Average: jako cílový výstup použijte průměrnou hodnotu v periodě.
* Max/min: jako cílový výstup použijte hodnotu Max/min v periodě.

Lze také upravit časový rozsah, který se má zobrazit.  Časový rozsah můžete vybrat z posledních 30 minut do posledních 30 dní nebo z vlastního časového rozsahu.

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

## <a name="user-portal-metrics-options"></a>Možnosti metrik portálu User Portal

V následující tabulce jsou uvedeny dostupné metriky a podrobnosti.
>[!div class="mx-tdBreakAll"]
>| Name (Název) | Zobrazovaný název | Název metriky pružinového válce | Jednotka | Podrobnosti |
>|----|----|----|----|------------|
>| SystemCpuUsagePercentage | Procento využití procesoru v systému | System. CPU. Usage | Procento | Poslední využití procesoru pro celý systém. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že všechny procesory byly během neaktivního časového období nečinné, zatímco hodnota 1,0 znamená, že všechny procesory aktivně 100 běžely v nedávných obdobích během nedodržení% času. Všechny hodnoty mezi 0,0 a 1,0 jsou možné v závislosti na aktivitách, které v systému procházejí. Pokud není dostupné systémové využití procesoru, vrátí metoda zápornou hodnotu. |
>| AppCpuUsagePercentage | Procento využití procesoru aplikací | Procento využití procesoru aplikací | Procento | Poslední využití procesoru pro proces prostředí Java Virtual Machine. Tato hodnota je v intervalu [0,0, 1.0] dvojitá. Hodnota 0,0 znamená, že žádný z procesorů neběžel v nedávných časových obdobích vlákna z procesu JVM, zatímco hodnota 1,0 znamená, že všechny procesory aktivně spouštějí vlákna z JVM 100% času během nedávných období. Vlákna z JVM zahrnují vlákna aplikace a také interní vlákna JVM. Všechny hodnoty mezi 0,0 a 1,0 jsou možné v závislosti na činnostech v procesu JVM a v celém systému. Pokud prostředí Java Virtual Machine nedávné využití procesoru není k dispozici, metoda vrátí zápornou hodnotu. |
>| AppMemoryCommitted | Přiřazená paměť aplikace | JVM. Memory. potvrzený | B | Představuje množství paměti (v bajtech), které je zaručeno k dispozici pro použití ve virtuálním počítači Java. Velikost potvrzené paměti se může v průběhu času (zvýšení nebo snížení) změnit. Virtuální počítač Java může uvolnit paměť do systému a potvrzení může být menší než init. potvrzená bude vždycky větší nebo rovna hodnotě použité. |
>| AppMemoryUsed | Využitá paměť aplikace | JVM. Memory .Ed – použito | B | Představuje velikost aktuálně využité paměti v bajtech. |
>| AppMemoryMax | Maximální velikost paměti aplikace | JVM. Memory. max | B | Představuje maximální velikost paměti (v bajtech), která se dá použít pro správu paměti. Její hodnota může být nedefinovaná. Maximální velikost paměti se může v průběhu času měnit, je-li tato hodnota definována. Velikost využité a potvrzené paměti bude vždy menší než nebo rovna hodnotě Max, pokud je definována hodnota max. Přidělení paměti může selhat, pokud se pokusí zvětšit využitou paměť tak, aby se použila > potvrzená i v případě, že použitá < = Max by byla pravdivá (například když je systém ve virtuální paměti nedostatek). |
>| MaxOldGenMemoryPoolBytes | Maximální velikost dostupných starých dat generace | JVM. GC. max. data. Size | B | Využití paměti ve špičce starého fondu paměti generace od spuštění virtuálního počítače Java. |
>| OldGenMemoryPoolBytes | Stará velikost dat generace | JVM. GC. Live. data. Size | B | Velikost staré generace fondu paměti po úplném GC. |
>| OldGenPromotedBytes | Zvýšit úroveň na starou velikost dat generace | JVM. GC. Memory. propagovaný | B | Počet kladných zvýšení velikosti staré paměti generace před GC na po GC. |
>| YoungGenPromotedBytes | Zvýšení úrovně na velikost dat malé generace | JVM. GC. Memory. alokovaný | B | Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC do dalšího. |
>| GCPauseTotalCount | Počet pozastavení GC | JVM. GC. Pause (celkový počet) | Počet | Celkový počet GC po zahájení této JMV, včetně mladého a starého GC. |
>| GCPauseTotalTime | Celkový čas pozastavení GC | JVM. GC. Pause (celkem-Time) | Milisekundy | Celkový čas GC spotřebovaný po spuštění tohoto JMVu, včetně mladého a starého GC. |
>| TomcatSentBytes | Celkový počet odeslaných bajtů Tomcat | Tomcat. Global. odesláno | B | Množství odeslaného webového serveru s daty Tomcat v bajtech |
>| TomcatReceivedBytes | Celkový počet přijatých bajtů Tomcat | Tomcat. Global. Received | B | Množství přijatého webového serveru datového Tomcat, v bajtech |
>| TomcatRequestTotalTime | Celkový čas žádosti Tomcat | Tomcat. Global. Request (celkem-Time) | Milisekundy | Celková doba, jakou Tomcat webový server zpracovává požadavky. |
>| TomcatRequestTotalCount | Celkový počet žádostí Tomcat | Tomcat. Global. Request (celkový počet) | Počet | Celkový počet zpracovaných požadavků Tomcat webového serveru |
>| TomcatRequestMaxTime | Maximální čas požadavku Tomcat | Tomcat. Global. Request. max | Milisekundy | Maximální doba, po kterou webový server Tomcat zpracuje požadavek |
>| TomcatErrorCount | Globální chyba Tomcat | Tomcat. Global. Error | Počet | Počet chyb, které se vyskytují u zpracovaných požadavků |
>| TomcatSessionActiveMaxCount | Maximální počet aktivních relací Tomcat | Tomcat. Sessions. Active. max | Počet | Maximální počet relací, které byly aktivní ve stejnou dobu |
>| TomcatSessionAliveMaxTime | Maximální doba běhu relace Tomcat | Tomcat. Sessions. Alive. max | Milisekundy | Nejdelší čas (v sekundách), po který vypršela relace s vypršenou platností |
>| TomcatSessionCreatedCount | Počet vytvořených relací Tomcat | Tomcat. Sessions. Created | Počet | Počet relací, které byly vytvořeny |
>| TomcatSessionExpiredCount | Počet vypršení relace Tomcat | Tomcat. Sessions. vypršela platnost | Počet | Počet relací, jejichž platnost vypršela |
>| TomcatSessionRejectedCount | Počet odmítnutých relací Tomcat | Tomcat. Sessions. odmítnuto | Počet | Počet relací, které nebyly vytvořeny, protože bylo dosaženo maximálního počtu aktivních relací. |

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme s Průzkumníkem metrik Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Další kroky
* [Kurz: monitorování jarních cloudových prostředků pomocí výstrah a skupin akcí](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvóty a plány služeb pro jarní Cloud v Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

