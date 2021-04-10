---
title: Jak nakonfigurovat metriky JMX – Azure Monitor Application Insights pro Java
description: Konfigurace další kolekce metrik JMX pro Azure Monitor agenta Application Insights Java
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609104"
---
# <a name="configuring-jmx-metrics"></a>Konfigurace metrik JMX

Agent Application Insights Java 3,0 ve výchozím nastavení shromažďuje některé metriky JMX, ale v mnoha případech to není dostačující. Tento dokument popisuje možnost konfigurace JMX v podrobnostech.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Návody shromažďovat další metriky JMX?

Kolekci metrik JMX lze nakonfigurovat přidáním ```"jmxMetrics"``` oddílu do applicationinsights.jsv souboru. Můžete zadat název metriky tak, jak se má zobrazit v Azure Portal v prostředku Application Insights. Je nutné definovat název objektu a atribut pro každou metriku, kterou chcete shromáždit.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Návody víte, jaké metriky jsou k dispozici pro konfiguraci?

Jsi je, že je nutné znát názvy objektů a atributů, tyto vlastnosti jsou různé pro různé knihovny, architektury a aplikační servery a často nejsou dokumentovány. Chcete-li získat názvy a atributy objektů, je třeba zobrazit strom MBean. MBean je spravovaný objekt Java, který může představovat zařízení, aplikaci nebo prostředek a má sadu atributů. 

Pokud chcete zobrazit dostupné metriky a procházet dostupné metriky, doporučujeme použít [ovládací prvek Java Mission](https://www.oracle.com/java/technologies/jdk-mission-control.html).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Jak přejít na kontrolní ovládací prvek Java, aby se získaly správné metriky?

Když spustíte nástroj Java Mission Control, budete mít k dispozici výběr JVMs na levé straně, klikněte na příslušný proces pod kartou JVM Browser. Počkejte, až JMC načte řídicí panel pro daný proces, v dolní části vyberte kartu MBean Browser (viz níže). JMC se musí nacházet ve stejné složce jako JVM a proces nebo aplikace musí být spuštěné.

![Snímek obrazovky s JMC a prohlížečem MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Jak se dostat k požadovaným metrikám a potřebné atributy?

Prohlížeč MBean otevře strom MBean se seznamem kategorií, které lze rozšířit. Výběrem kategorie na levé straně se otevře seznam atributů na pravé straně. Níže je příklad metriky, jejího názvu objektu a atributů. Atributy mohou být vnořené, jako v následujícím příkladu.

![Snímek obrazovky JMC stromu MBean](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Příklad konfigurace

Z výběru, jak je znázorněno na obrázku výše, umožňuje nakonfigurovat několik metrik. První z nich je příklad vnořené metriky `LastGcInfo` , která má několik vlastností a chceme zachytit `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Typy shromážděných metrik a dostupných možností konfigurace?

Podporujeme číselné a logické JMX metriky, zatímco jiné typy nejsou podporované a budou se ignorovat. 

V současné době se zástupné znaky a agregované atributy nepodporují. proto je nutné, aby každý atribut ' dvojici atributu ' Object '/' atribut ' byl nakonfigurován samostatně. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Kde najdu metriky JMX v Application Insights?

Když je vaše aplikace spuštěná a shromažďují se metriky JMX, můžete je zobrazit tak, že přejdete na Azure Portal a přejdete do svého prostředku Application Insights. Na kartě metriky vyberte rozevírací seznam, jak vidíte níže, a zobrazte tak metriky.

![Snímek obrazovky s metrikami na portálu](media/java-ipa/jmx/jmx-portal.png)
