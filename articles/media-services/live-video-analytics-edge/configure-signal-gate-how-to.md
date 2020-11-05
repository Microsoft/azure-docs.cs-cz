---
title: Konfigurace brány signálu pro záznam videa založeného na událostech – Azure
description: Tento článek poskytuje pokyny ke konfiguraci brány signálu v mediálním grafu.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380139"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurace brány signálu pro nahrávání videa založeného na událostech

V rámci mediálního grafu umožňuje [uzel procesoru signálu signálu](media-graph-concept.md#signal-gate-processor) předávané médium z jednoho uzlu do druhého, když je brána aktivována událostí. Když se aktivuje, otevře se brána a v průběhu zadané doby umožní tok médií. V případě nepřítomnosti událostí pro aktivaci brány se tato brána ukončí a zastaví se tok médií. Procesor brány signálu je vhodný pro nahrávání videa založeného na událostech.
V tomto článku se seznámíte s podrobnostmi o tom, jak nakonfigurovat procesor pro bránu signálu.

## <a name="suggested-pre-reading"></a>Navrhované před čtením
-   [Graf médií](media-graph-concept.md)
-   [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)


## <a name="problem"></a>Problém
Uživatel může chtít spustit záznam konkrétního času před nebo po spuštění této brány událostí. Uživatel ví přijatelnou latenci v rámci svého systému, takže uživatel chce zadat latenci procesoru brány signálu. Uživatel chce zadat nejkratší a nejdelší, že doba jejich záznamu může být bez ohledu na to, kolik nových událostí se dostalo.
 
### <a name="use-case-scenario"></a>Scénář použití
Předpokládejme, že chcete nahrávat video pokaždé, když se otevře přední dvířka vaší budovy. Potřebujete **X** sekund před otevřenými dvířky zahrnutými do záznamu. Chcete, aby nahrávání bylo poslední alespoň **Y** sekund, pokud se dvířka znovu neotevřou. Chcete, aby nahrávání bylo poslední v řádu **Z** sekund, pokud se dveře opakovaně otevírají. Víte, že senzor dvířek má latenci **v sekundách a** chcete snížit pravděpodobnost, že se události nepovažují ("zpožděné doručení"), takže chcete, aby se události dostaly aspoň **K** sekundách.


## <a name="solution"></a>Řešení

**_Úprava parametrů procesoru brány signálu_* _

Procesor brány signálu je nakonfigurovaný pomocí 4 parametrů:
- _ *okno vyhodnocení aktivace**
- **posun aktivačního signálu**
- **minimální interval aktivace**
- **maximální interval aktivace** 

Když se aktivuje procesor brány signálu, zůstane otevřený pro minimální dobu aktivace. Aktivační událost začíná na časovém razítku pro nejstarší událost a posun aktivačního signálu. Pokud se procesor brány signálu znovu aktivuje, zatímco je otevřený, časovač se resetuje a brána zůstane otevřená aspoň na minimální dobu aktivace. Procesor brány signálu nikdy nezůstane otevřený déle než maximální doba aktivace. Událost **(Event 1)** , která se vyskytuje před jinou **událostí (událost 2)** na základě časových razítek média, podléhá nepozornostu, pokud systém prodlevy a **událost 1** dorazí na procesor brány signálu po **události 2**. Pokud **událost 1** nepřijde mezi doručením **události 2** a **oknem hodnocení aktivace** , bude **událost 1** ignorována a nebude předána procesorem brány signálu. ID korelace jsou nastavena pro každou událost. Tato ID se nastavují od počáteční události a jsou sekvenční pro každou následující událost.

> [!IMPORTANT]
> Doba média je založena na časovém razítku média při výskytu události v médiu. Sekvence událostí přicházejících do brány signálu nesmí odrážet sekvenci událostí přicházejících v čase média.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parametry: (na základě toho, kdy události dorazí do brány signálu v fyzickém čase)

* **minimumActivationTime (nejkratší možná doba trvání záznamu)** = minimální počet sekund, po které bude procesor brány signálu otevřený po aktivaci pro příjem nových událostí, pokud se **maximumActivationTime** nepřerušil.
* **maximumActivationTime (nejdelší možná doba trvání záznamu)** = maximální počet sekund od počáteční události, po jejímž uplynutí bude procesor brány signálu otevřený pro příjem nových událostí bez ohledu na to, jaké události se přijímají
* **activationSignalOffset** = počet sekund mezi aktivací procesoru brány signálu a okamžikem nahrávání videa, obvykle je tato hodnota záporná, aby bylo možné spustit záznam před triggerem události.
* **activationEvaluationWindow** = počet sekund od počáteční události aktivace, kdy se událost, ke které došlo před počáteční událostí, v čase média, musí dorazit na procesor brány signálu, než se bude považovat za "pozdní doručení".

> [!NOTE]
> Pozdní doručení je jakákoli událost, která se dokončí po úspěšném dokončení okna pro vyhodnocení aktivace, ale tato událost byla doručena před počáteční událostí v čase média.

### <a name="limits-of-parameters"></a>Limity parametrů

* **activationEvaluationWindow: 0 sekund až 10 sekund**

* **activationSignalOffset:-1 minuta až 1 minuta**

* **minimumActivationTime: 1 sekunda až 1 hodina**

* **maximumActivationTime: 1 sekunda až 1 hodina**


Na základě případu použití se parametry nastaví takto:

* **activationEvaluationWindow = K s**
* **activationSignalOffset =-X s**
* **minimumActivationWindow = Y s**
* **maximumActivationWindow = Z s**


Tady je příklad toho, co v části uzlu procesoru pro bránu signálu má být v topologii mediálního grafu pro následující hodnoty parametrů:
* **activationEvaluationWindow = 1 sekunda**
* **activationSignalOffset =-5 sekund**
* **minimumActivationTime = 20 sekund**
* **maximumActivationTime = 40 sekund**

> [!IMPORTANT]
> Pro každou hodnotu parametru se očekává [Formát trvání ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . 
**Např.: PT1S = 1 sekunda**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Podívejme se, jak se tato konfigurace procesoru signálu signálu bude chovat v různých scénářích záznamu.


**1 událost z 1 zdroje ( *normální aktivace* )**

Procesor brány signálu, který přijímá jednu událost, by způsobil záznam, který spustí "posun aktivačního signálu" (5) sekund před tím, než se událost dorazila do brány. Zbývající část záznamu je "minimální doba aktivace" (20) sekund, protože žádné jiné události nepřicházejí do doby, než se minimální doba aktivace dokončí pro opětovné spuštění brány.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Normální aktivace":::

* Trvání záznamu =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**2 události z 1 zdroje ( *aktivovaná aktivace* )**

Procesor brány signálu, který přijímá dvě události, by způsobil záznam, který spustí "posun aktivačního signálu" (pět) sekund před tím, než se první událost dorazila do brány. Druhá událost dorazí pět sekund po první události, což je před dokončením "minimální doba aktivace" (20) sekund od první události, a proto se brána znovu aktivuje, aby zůstala otevřená. Zbývající část záznamu je "minimální doba aktivace" (20) sekund, protože žádné jiné události nepřicházejí do doby, než se minimální doba aktivace od druhé události dokončí, aby se brána znovu znovu aktivovala.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Opětovně aktivované aktivace":::

* Doba nahrávání = – posun + (přijetí druhé události – přijetí 1. události) + minimumActivationTime


**N událostí z 1 zdroje ( *maximální aktivace* )**

Procesor brány signálu, který přijímá N událostí, má za následek záznam, který spustí "posun aktivačního signálu" (5) sekund před tím, než se první událost dorazila do brány. Vzhledem k tomu, že každá událost dorazí před dokončením minimální doby aktivace (20) sekund od předchozí události, brána se průběžně znovu aktivuje a zůstane otevřená, dokud "maximální doba aktivace" (40) sekund po první události, kdy by brána zavřela a už nepřijímala žádné nové události.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Maximální aktivace":::
 
* Trvání záznamu = – posun + maximumActivationTime

> [!IMPORTANT]
> V diagramech se předpokládá, že každá událost dorazí na stejnou instanci v poli fyzického a mediálního času. (Žádné zpožděné doručení)

## <a name="next-steps"></a>Další kroky

### <a name="try-it-out"></a>Vyzkoušet

[Kurz pro nahrávání videa založeného na událostech](event-based-video-recording-tutorial.md)

Pomocí kurzu pro nahrávání videa založeného na událostech upravte [topology.jsv](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), upravte parametry uzlu signalgateProcessor a pak postupujte podle zbývající části kurzu. Zkontrolujte nahrávky videa a analyzujte efekt parametrů.



