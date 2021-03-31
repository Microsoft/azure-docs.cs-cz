---
title: Konfigurace brány signálu pro záznam videa založeného na událostech – Azure
description: Tento článek poskytuje pokyny ke konfiguraci brány signálu v mediálním grafu.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410789"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurace brány signálu pro nahrávání videa založeného na událostech

V rámci mediálního grafu umožňuje [uzel procesoru signálu signálu](media-graph-concept.md#signal-gate-processor) předávané médium z jednoho uzlu do druhého, pokud je brána aktivována událostí. Když se aktivuje, brána se otevře a umožní tok multimédií po určenou dobu. V případě nepřítomnosti událostí pro aktivaci brány se zavře a zastaví se přehrávání médií. Procesor brány signálu můžete použít pro nahrávání videa založeného na událostech.

V tomto článku se dozvíte, jak nakonfigurovat procesor pro bránu signálu.

## <a name="suggested-prereading"></a>Navrhované předčtení
-   [Graf médií](media-graph-concept.md)
-   [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)


## <a name="problem"></a>Problém
Uživatel může chtít spustit záznam v určitou dobu před nebo po spuštění brány událostí. Uživatel ví přijatelnou latenci v rámci svého systému. Takže chtějí určit latenci procesoru pro bránu signálu. Také chtějí zadat minimální a maximální dobu trvání záznamu, bez ohledu na to, kolik nových událostí bylo přijato.
 
### <a name="use-case-scenario"></a>Scénář použití
Předpokládejme, že chcete nahrávat video pokaždé, když se otevře přední dvířka vaší budovy. Chcete nahrávat: 

- Před otevřením dvířek zahrňte *X* sekund. 
- Poslední alespoň *Y* sekund, pokud se dvířka znovu neotevřou 
- Poslední v řádu *Z* sekund, pokud se dvířka opakovaně otevírají. 
 
Víte, že senzor dvířek má latenci *k* (s). Chcete-li snížit pravděpodobnost, že se události nepovažují za pozdní doručení, chcete, aby byly události doručeny alespoň *K* sekundám.


## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, změňte parametry procesoru pro bránu signálu.

Chcete-li nakonfigurovat procesor brány signálu, použijte tyto čtyři parametry:
- Okno vyhodnocení aktivace
- Posun aktivačního signálu
- Minimální interval aktivace
- Maximální interval aktivace

Když se aktivuje procesor brány signálu, zůstane otevřený pro minimální dobu aktivace. Aktivační událost začíná v časovém razítku pro nejstarší událost a posun aktivačního signálu. 

Pokud se procesor brány signálu znovu aktivuje, zatímco je otevřený, časovač se resetuje a brána zůstane otevřená aspoň na minimální dobu aktivace. Procesor brány signálu nikdy nezůstane otevřený déle než maximální doba aktivace. 

Událost (Event 1), která se vyskytuje před jinou událostí (událost 2) na základě časových razítek médií, může být ignorována, pokud prodlevy systému a událost 1 dorazí na procesor brány signálu po události 2. Pokud událost 1 nepřijde mezi doručením události 2 a oknem hodnocení aktivace, je událost 1 považovat. Není předáván přes procesor brány signálu. 

ID korelace jsou nastavena pro každou událost. Tato ID se nastavují z počáteční události. Pro každou z následujících událostí jsou sekvenční.

> [!IMPORTANT]
> Doba média je založena na časovém razítku média při výskytu události v médiu. Sekvence událostí, které přicházejí do brány signálu, nemusí odrážet sekvenci událostí, které dorazí v čase média.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parametry na základě fyzického času, který události dorazí do brány signálu

* **minimumActivationTime (nejkratší možná doba nahrávání)**: minimální počet sekund, po který procesor brány signálu zůstane otevřený, když se aktivuje pro příjem nových událostí, pokud není přerušený maximumActivationTime.
* **maximumActivationTime (nejdelší možná doba pro nahrávání)**: maximální počet sekund od počáteční události, po které se procesor brány signálu otevře po aktivaci pro příjem nových událostí bez ohledu na to, jaké události se přijímají.
* **activationSignalOffset**: počet sekund mezi aktivací procesoru brány signálu a zahájením záznamu videa. Obvykle je tato hodnota záporná, protože začíná záznam před aktivací události.
* **activationEvaluationWindow**: počínaje počáteční událostí aktivace se počet sekund, ve kterých událost, ke které došlo před počáteční událostí, v čase média, musí dorazit na procesor brány signálu, než se zabere v úvahu a bude považován za pozdní doručení.

> [!NOTE]
> *Pozdní doručení* je jakákoli událost, která přijde po úspěšném dokončení okna pro vyhodnocení aktivace, ale ta dorazí do počáteční události v čase média.

### <a name="limits-of-parameters"></a>Limity parametrů

* **activationEvaluationWindow**: 0 sekund až 10 sekund
* **activationSignalOffset**:-1 minuta až 1 minuta
* **minimumActivationTime**: 1 sekunda až 1 hodina
* **maximumActivationTime**: 1 sekunda až 1 hodina


V případě použití byste nastavili parametry následujícím způsobem:

* **activationEvaluationWindow**: *KB* /s
* **activationSignalOffset**: *-X* sekund
* **minimumActivationWindow**: *Y* sekund
* **maximumActivationWindow**: *Z* sekund


Tady je příklad toho, jak by oddíl uzlu **procesoru pro bránu signálu** vypadal v topologii mediálního grafu pro následující hodnoty parametrů:
* **activationEvaluationWindow**: 1 sekunda
* **activationSignalOffset**:-5 sekund
* **minimumActivationTime**: 20 sekund
* **maximumActivationTime**: 40 sekund

> [!IMPORTANT]
> Pro každou hodnotu parametru se očekává [Formát trvání ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . Například PT1S = 1 sekunda.


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


Nyní zvažte, jak se tato konfigurace procesoru signálu signálu bude chovat v různých scénářích záznamu.

### <a name="recording-scenarios"></a>Scénáře záznamu

**Jedna událost z jednoho zdroje (*normální aktivace*)**

Procesor brány signálu, který přijímá jednu událost, má za následek záznam, který před přijetím události do brány spustí 5 sekund (aktivační signál = 5 sekund). Zbytek záznamu je 20 sekund (minimální doba aktivace = 20 sekund), protože žádné jiné události nepřicházejí před koncem minimální doby aktivace pro opětovné spuštění brány.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagram znázorňující normální aktivaci jedné události z jednoho zdroje.":::

* Trvání záznamu =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**Dvě události z jednoho zdroje (znovu *aktivované aktivace*)**

Procesor brány signálu, který obdrží dvě události, má za následek záznam, který spustí 5 sekund (posun aktivačního signálu = 5 sekund) předtím, než událost dorazí do brány. Událost 2 také dorazí 5 sekund po události 1. Vzhledem k tomu, že událost 2 dorazí do konce minimální doby aktivace události 1 (20 sekund), brána se znovu aktivuje. Zbytek záznamu je 20 sekund (minimální doba aktivace = 20 sekund), protože žádné další události nepřicházejí před koncem minimální doby aktivace z události 2 pro opětovné spuštění brány.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagram znázorňující opětovnou aktivaci dvou událostí z jednoho zdroje.":::

* Trvání záznamu =-offset + (přijetí události 2-doručení události 1) + minimumActivationTime


***N* událostí z jednoho zdroje (*maximální aktivace*)**

Procesor brány signálu, který obdrží *N* událostí, má za následek záznam, který spustí 5 sekund (posun aktivačního signálu = 5 sekund) před doručením první události do brány. Vzhledem k tomu, že každá událost dorazí před koncem minimální aktivace 20 sekund od předchozí události, je brána neustále znovu aktivována. Zůstane otevřený až do maximální doby aktivace 40 sekund od první události. Pak se brána zavře a už nebude přijímat žádné nové události.

Příklad diagramu:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagram znázorňující maximální aktivaci N událostí z jednoho zdroje.":::
 
* Trvání záznamu = – posun + maximumActivationTime

> [!IMPORTANT]
> V předchozích diagramech se předpokládá, že každá událost dorazí na stejnou dobu v poli fyzický čas a čas média. To znamená, že se předpokládá, že neexistují žádná pozdní doručení.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte [kurz pro nahrávání videa založeného na událostech](event-based-video-recording-tutorial.md). Začněte úpravou [topology.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Upravte parametry uzlu signalgateProcessor a pak postupujte podle zbývající části kurzu. Zkontrolujte nahrávky videa a analyzujte efekt parametrů.



