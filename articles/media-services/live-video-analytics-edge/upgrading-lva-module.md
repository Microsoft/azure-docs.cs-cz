---
title: Upgrade živé analýzy videí v IoT Edge od 1,0 do 2,0
description: Tento článek popisuje rozdíly a různé věci, které je potřeba vzít v úvahu při upgradu LVA (Live video Analytics) v modulu Azure IoT Edge.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 9621f0a933c6102309286505f2c551c5256c5506
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901551"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Upgrade živé analýzy videí v IoT Edge od 1,0 do 2,0

Tento článek popisuje rozdíly a různé věci, které je potřeba vzít v úvahu při upgradu LVA (Live video Analytics) v modulu Azure IoT Edge.

## <a name="change-list"></a>Seznam změn

> [!div class="mx-tdCol4BreakAll"]
> |Nadpis|Live video Analytics 1,0|Live video Analytics 2,0|Popis|
> |-------------|----------|---------|---------|
> |Obrázek kontejneru|mcr.microsoft.com/media/live-video-analytics:1.0.0|mcr.microsoft.com/media/live-video-analytics:2.0.0|Obrázky Docker publikované Microsoftem pro video Analytics na Azure IoT Edge|
> |**Uzly MediaGraph** |    |   |   |
> |zdroje|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Zdroj RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub zdroj zprávy |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Zdroj RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub zdroj zprávy | MediaGraph uzly, které fungují jako zdroje pro ingestování a zprávy o médiích.|
> |Procesory|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor detekce pohybu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor filtru snímkové rychlosti </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozšíření http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozšíření Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor brány signálu |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor detekce pohybu </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Procesor filtru snímkové rychlosti**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozšíření http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozšíření Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor brány signálu | MediaGraph uzly, které vám umožní naformátovat médium před odesláním na odvozené servery AI.|
> |Jímky|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Jímka assetu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Jímka souborů </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub jímka zpráv|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Jímka assetu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Jímka souborů </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub jímka zpráv| MediaGraph uzly, které umožňují ukládat zpracovaná média.|
> |**Podporované MediaGraphs** |    |   |   |
> |Topologie|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu a nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externí analýza a nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech při pohybu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech v AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech na externí události </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu následovaná AI Inferencing |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu a nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externí analýza a nepřetržité nahrávání videa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech při pohybu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech v AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Záznam založený na událostech na externí události </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analýza pohybu následovaná AI Inferencing </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Složení AI** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Záznam zvuku a videa** </br>  | MediaGraph topologie, které umožňují definovat podrobný plán grafu s parametry jako zástupné symboly pro hodnoty.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Upgrade nástroje Live video Analytics v modulu IoT Edge z 1,0 na 2,0
Při upgradu Live video Analytics v modulu IoT Edge se ujistěte, že aktualizujete následující informace.
### <a name="container-image"></a>Obrázek kontejneru
V šabloně nasazení vyhledejte svou živou analýzu videa v modulu IoT Edge pod `modules` uzlem a aktualizujte `image` pole jako:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
Pokud jste nezměnili název živé analýzy videí v modulu IoT Edge, hledejte `lvaEdge` pod uzlem modul.

### <a name="topology-file-changes"></a>Změny souboru topologie
V souborech topologie se ujistěte, že **`apiVersion`** je nastavená na 2,0.

### <a name="mediagraph-node-changes"></a>Změny uzlu MediaGraph


* Zvuk ze zdroje kamery se teď dá předat jako příjem dat spolu s videem. K libovolnému uzlu grafu můžete předat jenom **zvuk** nebo **jenom video** nebo **zvuk a video** **`outputSelectors`** . Pokud třeba chcete vybrat video jenom ze zdroje RTSP a předat ho pro příjem dat, přidejte do zdrojového uzlu RTSP následující:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** Je volitelná vlastnost. Pokud se tato možnost nepoužívá, bude se v mediálním grafu předávat zvuk (Pokud je povolený) a video z příjemky přes příjem dat z kamery RTSP. 

* V `MediaGraphHttpExtension` `MediaGraphGrpcExtension` procesorech a si všimněte následujících změn:  
    * **Vlastnosti obrázku**
        * `MediaGraphImageFormatEncoded` již není podporován. 
        * Místo toho použijte **`MediaGraphImageFormatBmp`** nebo **`MediaGraphImageFormatJpeg`** nebo **`MediaGraphImageFormatPng`** . Příklad:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Pokud chcete použít nezpracované image, použijte **`MediaGraphImageFormatRaw`** . Pokud to chcete použít, aktualizujte uzel Image takto:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Možné hodnoty pixelFormat zahrnují: `yuv420p` , `rgb565be` , `rgb565le` , `rgb555be` , `rgb555le` , `rgb24` , `bgr24` , `argb` , `rgba` , `abgr` , `bgra`  

    * **extensionConfiguration pro procesor rozšíření Grpc**  
        * V `MediaGraphGrpcExtension` procesoru **`extensionConfiguration`** je k dispozici nová vlastnost s názvem, což je volitelný řetězec, který lze použít jako součást gRPC smlouvy. Toto pole lze použít k předání libovolných dat na odvozený Server a můžete definovat, jak server odvození používá tato data.  
        Jeden případ použití této vlastnosti je v případě, že máte více modelů AI zabalených na jednom odvozeném serveru. Pomocí této vlastnosti nebude nutné vystavit uzel pro každý model AI. Místo toho pro instanci grafu jako poskytovatel rozšíření můžete definovat, jak vybrat různé modely AI pomocí **`extensionConfiguration`** vlastnosti a během provádění, lva tento řetězec předat serveru Inferencing, který může použít k vyvolání požadovaného modelu AI.  

    * **Složení AI**
        * Live video Analytics 2,0 teď podporuje používání více než jednoho procesoru rozšíření multimediálního grafu v rámci topologie. Snímky médií z kamery RTSP můžete předat do různých modelů AI sekvenčně, paralelně nebo v kombinaci obou. Podívejte se prosím do vzorové topologie, kde se používají dva modely AI postupně.


* V uzlu **jímky souborů** teď můžete určit, kolik místa na disku může dynamická analýza videa v IoT Edge použít k uložení zpracovaných imagí. Provedete to tak, že přidáte **`maximumSizeMiB`** pole do uzlu jímky. Ukázkový uzel jímky souborů je následující:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* V uzlu **jímka assetu** můžete určit, kolik místa na disku může dynamická analýza videa v IoT Edge použít k uložení zpracovaných imagí. Provedete to tak, že přidáte **`localMediaCacheMaximumSizeMiB`** pole do uzlu jímka assetu. Uzel ukázkové jímky Assetu je následující:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  Cesta **jímky souborů** je rozdělená na základní cestu adresáře a vzor názvu souboru, zatímco cesta **jímky prostředků** zahrnuje základní cestu adresáře.  

* **`MediaGraphFrameRateFilterProcessor`** se v nástroji **Live video Analytics v modulu IoT Edge 2,0** už nepoužívá.
    * Chcete-li vzorkovat příchozí video pro zpracování, přidejte **`samplingOptions`** vlastnost do MediaGraphch procesorů ( `MediaGraphHttpExtension` nebo `MediaGraphGrpcExtension` ).  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Metriky modulů ve formátu Prometheus pomocí telegraf
V této verzi se dá telegraf použít k odeslání metrik do Azure Monitor. Tady se můžou metriky směrovat do Log Analytics nebo centra událostí.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taxonomie událostí":::

Image telegraf s vlastní konfigurací můžete snadno vytvořit pomocí Docker. Přečtěte si další informace na stránce [monitorování a protokolování](monitoring-logging.md#azure-monitor-collection-via-telegraf) .

## <a name="next-steps"></a>Další kroky

[Začínáme se službou Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)