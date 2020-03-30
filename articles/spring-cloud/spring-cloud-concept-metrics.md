---
title: Principy metrik pro Azure Spring Cloud
description: Přečtěte si, jak zkontrolovat metriky v Azure Spring Cloudu
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256754"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Principy metrik pro Azure Spring Cloud

Průzkumník Metrik Azure je součástí portálu Microsoft Azure, který umožňuje vykreslovat grafy, vizuálně korelovat trendy a zkoumat špičky a poklesy metrik. Pomocí průzkumníka metrik prozkoumejte stav a využití vašich prostředků. 

V Azure Spring Cloud existují dva body zobrazení pro metriky.
* Grafy na každé stránce s přehledem aplikací
* Stránka běžných metrik

 ![Grafy metrik](media/metrics/metrics-1.png)

Grafy v aplikaci **Přehled** poskytují rychlé kontroly stavu pro každou aplikaci. Stránka **Společné metriky** obsahuje všechny metriky, které jsou k dispozici pro referenci. Na stránce běžných metrik si můžete vytvořit vlastní grafy a připnout je na řídicí panel.

## <a name="application-overview-page"></a>Stránka přehledu aplikace
Vyberte aplikaci ve **Správě aplikací** a najděte grafy na stránce s přehledem.  

 ![Správa metrik aplikací](media/metrics/metrics-2.png)

Každá aplikace **přehled aplikací** stránka představuje metriky graf, který umožňuje provádět rychlou kontrolu stavu vaší aplikace.  

 ![Přehled metrik aplikací](media/metrics/metrics-3.png)

Azure Spring Cloud poskytuje těchto pět grafů s metrikami, které se aktualizují každou minutu:

* **Chyby serveru Http**: Počet chyb u požadavků HTTP do vaší aplikace
* **Data v:** Bajty přijaté vaší aplikací
* **Data Out**: Bajty odeslané vaší aplikací
* **Žádosti**: Žádosti přijaté vaší aplikací
* **Průměrná doba odezvy**: Průměrná doba odezvy z aplikace

V grafu můžete vybrat časový rozsah od jedné hodiny do sedmi dnů.

## <a name="common-metrics-page"></a>Stránka běžných metrik

**Metriky** v levém navigačním podokně odkazují na stránku běžných metrik.

Nejprve vyberte metriky, které chcete zobrazit:

![Vybrat zobrazení metriky](media/metrics/metrics-4.png)

Podrobnosti o všech metrikách naleznete v [části](#user-metrics-options) níže.

Dále vyberte typ agregace pro každou metriku:

![Agregace metrik](media/metrics/metrics-5.png)

Typ agregace označuje, jak agregovat metrické body v grafu podle času. Existuje jeden hrubý bod metriky každou minutu a typ předběžné agregace během jedné minuty je předem definován typem metriky.
* Součet: Součet všech hodnot jako cílový výstup.
* Průměr: Jako cílový výstup použijte průměrnou hodnotu v období.
* Max/Min: Použijte hodnotu Max/Min v období jako cílový výstup.

Časový rozsah, který se má zobrazit, lze také upravit. Časový rozsah lze zvolit od posledních 30 minut do posledních 30 dnů nebo vlastní časový rozsah.

![Modifikace metriky](media/metrics/metrics-6.png)

Výchozí zobrazení zahrnuje všechny metriky aplikace služby Azure Spring Cloud společně. Metriky jedné aplikace nebo instance lze filtrovat na displeji.  Klikněte na **Přidat filtr**, nastavte vlastnost na **Aplikaci**a v textovém poli **Hodnoty** vyberte cílovou aplikaci, kterou chcete sledovat. 

Můžete použít dva druhy filtrů (vlastnosti):
* Aplikace: filtrování podle názvu aplikace
* Instance: filtrování podle instance aplikace

![Filtry metrik](media/metrics/metrics-7.png)

Můžete také použít možnost **Použít rozdělení,** která nakreslí více čar pro jednu aplikaci:

![Rozdělení metrik](media/metrics/metrics-8.png)

>[!TIP]
> Můžete vytvořit vlastní grafy na stránce metrika a připnout je na **řídicí panel**. Začněte pojmenováním grafu.  Dále vyberte **Připnout na řídicí panel v pravém horním rohu**. Nyní můžete zkontrolovat svou aplikaci na **řídicím panelu portálu**.

## <a name="user-metrics-options"></a>Možnosti uživatelských metrik

V následujících tabulkách jsou uvedeny dostupné metriky a podrobnosti.

### <a name="error"></a>Chyba
>[!div class="mx-tdCol2BreakAll"]
>| Name (Název) | Metrický název pružiny pohonu | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Globální chyba Společnosti Tomcat | tomcat.global.error | Počet | Počet chyb dojde u zpracovaných požadavků |

### <a name="performance"></a>Výkon
>[!div class="mx-tdCol2BreakAll"]
>| Name (Název) | Metrický název pružiny pohonu | Jednotka | Podrobnosti |
>|----|----|----|------------|
>|Procento využití procesoru systému | system.cpu.usage | Procento | Nedávné využití procesoru pro celý systém. Tato hodnota je double v intervalu [0.0,1.0]. Hodnota 0,0 znamená, že všechny procesory byly nečinné během nedávného období pozorovány, zatímco hodnota 1,0 znamená, že všechny procesory byly aktivně spuštěny 100 % času během nedávného období bylo pozorováno.|
>| Procento využití procesoru aplikace | Procento využití procesoru aplikace | Procento | Nedávné využití procesoru pro proces virtuálního počítače Java. Tato hodnota je double v intervalu [0.0,1.0]. Hodnota 0,0 znamená, že žádný z procesorů byly spuštěny vlákna z procesu JVM během nedávné hoobdobí pozorováno, zatímco hodnota 1,0 znamená, že všechny procesory byly aktivně spuštěny podprocesy z JVM 100 % času během nedávné období bylo pozorováno. Vlákna z JVM zahrnují vlákna aplikace, stejně jako interní vlákna JVM.|
>| Přiřazená paměť aplikace | soubor jvm.memory.committed | Bajty | Představuje množství paměti, která je zaručena k dispozici pro použití JVM. JVM může uvolnit paměť do systému a potvrzena může být menší než init. potvrzena bude vždy větší nebo rovna použité. |
>| Použitá paměť aplikace | jvm.memory.used | Bajty | Představuje velikost paměti aktuálně používané v bajtů. |
>| Paměť aplikace Max | jvm.memory.max | Bajty | Představuje maximální množství paměti, které lze použít pro správu paměti. Pokud je definována hodnota max, bude vždy menší nebo rovna maximální velikosti využité a potvrzené paměti. Přidělení paměti může selhat, pokud se pokusí zvýšit využité paměti tak, že používá > potvrzena i v případě, že používá <= max by stále true (například při nedostatku systému virtuální paměti). |
>| Maximální dostupná velikost dat staré generace | jvm.gc.max.data.size | Bajty | Využití paměti ve špičce fondu paměti staré generace od spuštění virtuálního počítače Java. |
>| Velikost dat staré generace | jvm.gc.live.data.size | Bajty | Velikost fondu paměti staré generace po úplné gc. |
>| Zvýšit velikost dat staré generace | jvm.gc.memory.promoted | Bajty | Počet kladných zvýšení velikosti fondu paměti staré generace před GC po GC. |
>| Povýšení na velikost dat mladé generace | jvm.gc.memory.allocated | Bajty | Zvýšení velikosti fondu paměti mladé generace po jednom GC na před další. |
>| Počet pozastavení GC | jvm.gc.pause (celkový počet) | Počet | Celkový počet GC po zahájení tohoto JMV, včetně young a old gc. |
>| Celkový čas pozastavení GC | jvm.gc.pause (celkový čas) | Milisekund | Celkový čas GC spotřebovaný po spuštění tohoto JMV, včetně young a old gc. |

### <a name="request"></a>Žádost
>[!div class="mx-tdCol2BreakAll"]
>| Name (Název) | Metrický název pružiny pohonu | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Celkový počet odeslaných bajtů kotomak | tomcat.global.odeslán | Bajty | Množství dat, které byl webový server Tomcat odeslán |
>| Tomcat Celkem přijatých bajtů | tomcat.global.přijato | Bajty | Množství přijatých dat, které byl server Tomcat přijat |
>| Celkový čas požadavku Tomcat | tomcat.global.request (celkový čas) | Milisekund | Celkový čas webového serveru Tomcat pro zpracování požadavků |
>| Celkový počet požadavků na kotomcat | tomcat.global.request (celkový počet) | Počet | Celkový počet zpracovaných požadavků na webový server Tomcat |
>| Maximální čas požadavku Tomcat | tomcat.global.request.max | Milisekund | Maximální doba zpracování požadavku webovým serverem Tomcat |

### <a name="session"></a>Relace
>[!div class="mx-tdCol2BreakAll"]
>| Name (Název) | Metrický název pružiny pohonu | Jednotka | Podrobnosti |
>|----|----|----|------------|
>| Počet aktivních aktivit relace Kotomcat | tomcat.sessions.active.max | Počet | Maximální počet relací, které byly aktivní současně |
>| Tomcat Session Max Alive Čas | tomcat.sessions.alive.max | Milisekund | Nejdelší doba (v sekundách), po kterou byla relace s ukončenou platností aktivní |
>| Počet vytvořených relací Tomcat | tomcat.sessions.vytvořeno | Počet | Počet vytvořených relací |
>| Počet prošlých platností relace Tomcat | tomcat.sessions.expired | Počet | Počet relací, jejichž platnost vypršela |
>| Počet odmítnutých relace Tomcat | tomcat.sessions.odmítnuto | Počet | Počet relací, které nebyly vytvořeny, protože byl dosažen maximální počet aktivních relací. |

## <a name="see-also"></a>Viz také
* [Začínáme s Průzkumníkem metrik Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Další kroky
* [Kurz: Sledování prostředků spring cloudu pomocí výstrah a skupin akcí](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvóty a plány služeb pro Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

