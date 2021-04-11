---
title: Operace prostorových analýz
titleSuffix: Azure Cognitive Services
description: Operace prostorových analýz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 4b4ee9d1e583241f8ec9b467ae9ddfdb1360fb52
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284698"
---
# <a name="spatial-analysis-operations"></a>Operace prostorových analýz

Prostorová analýza umožňuje analýzu streamování videa v reálném čase ze zařízení fotoaparátu. Pro každé zařízení kamery, které nakonfigurujete, se operace pro prostorovou analýzu vygenerují výstupní proud zpráv JSON odeslaných do vaší instance Azure IoT Hub. 

Kontejner prostorových analýz implementuje následující operace:

| Identifikátor operace| Description|
|---------|---------|
| cognitiveservices Account. Vision. spatialanalysis-personcount | Spočítá lidi v určené zóně v poli zobrazení kamery. Aby PersonCount mohl zaznamenat přesný součet, musí být zóna plně pokrytá jednou kamerou. <br> Vygeneruje počáteční událost _personCountEvent_ a pak _personCountEvent_ události při změně počtu.  |
| cognitiveservices Account. Vision. spatialanalysis-personcrossingline | Sleduje, když osoba v poli zobrazení kamery přeškrtne určený řádek. <br>Vygeneruje událost _personLineEvent_ , když osoba přeškrtne řádek a poskytne směrové informace. 
| cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon | Vygeneruje událost _personZoneEnterExitEvent_ , když uživatel zadá nebo ukončí zónu a poskytne směrové informace s očíslovanou stranou zóny, která byla překročena. Vygeneruje _personZoneDwellTimeEvent_ , když osoba opustí zónu a poskytne směrové informace a počet milisekund, které osoba strávila v zóně. |
| cognitiveservices Account. Vision. spatialanalysis-persondistance | Sleduje, když lidé narušují pravidlo vzdálenosti. <br> Pravidelně generuje _personDistanceEvent_ s umístěním každého porušení vzdálenosti. |
| cognitiveservices Account. Vision. spatialanalysis | Obecná operace, kterou lze použít ke spuštění všech výše uvedených scénářů. Tato možnost je užitečnější, pokud chcete spouštět více scénářů ve stejné kameře nebo používat systémové prostředky (například GPU) efektivněji. |

Všechny výše uvedené operace jsou také k dispozici ve `.debug` verzi, které mají schopnost vizualizovat snímky videa při jejich zpracování. Aby `xhost +` bylo možné vizualizaci snímků a událostí videa povolit, bude nutné spustit na hostitelském počítači.

| Identifikátor operace| Description|
|---------|---------|
| cognitiveservices Account. Vision. spatialanalysis-personcount. Debug | Spočítá lidi v určené zóně v poli zobrazení kamery. <br> Vygeneruje počáteční událost _personCountEvent_ a pak _personCountEvent_ události při změně počtu.  |
| cognitiveservices Account. Vision. spatialanalysis-personcrossingline. Debug | Sleduje, když osoba v poli zobrazení kamery přeškrtne určený řádek. <br>Vygeneruje událost _personLineEvent_ , když osoba přeškrtne řádek a poskytne směrové informace. 
| cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon. Debug | Vygeneruje událost _personZoneEnterExitEvent_ , když uživatel zadá nebo ukončí zónu a poskytne směrové informace s očíslovanou stranou zóny, která byla překročena. Vygeneruje _personZoneDwellTimeEvent_ , když osoba opustí zónu a poskytne směrové informace a počet milisekund, které osoba strávila v zóně. |
| cognitiveservices Account. Vision. spatialanalysis-persondistance. Debug | Sleduje, když lidé narušují pravidlo vzdálenosti. <br> Pravidelně generuje _personDistanceEvent_ s umístěním každého porušení vzdálenosti. |
| cognitiveservices Account. Vision. spatialanalysis. Debug | Obecná operace, kterou lze použít ke spuštění všech výše uvedených scénářů. Tato možnost je užitečnější, pokud chcete spouštět více scénářů ve stejné kameře nebo používat systémové prostředky (například GPU) efektivněji. |

Prostorová analýza se dá také spustit s [živým video analýzou](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) jako jejich modul video AI. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identifikátor operace| Description|
|---------|---------|
| cognitiveservices Account. Vision. spatialanalysis-personcount. livevideoanalytics | Spočítá lidi v určené zóně v poli zobrazení kamery. <br> Vygeneruje počáteční událost _personCountEvent_ a pak _personCountEvent_ události při změně počtu.  |
| cognitiveservices Account. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Sleduje, když osoba v poli zobrazení kamery přeškrtne určený řádek. <br>Vygeneruje událost _personLineEvent_ , když osoba přeškrtne řádek a poskytne směrové informace. 
| cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Vygeneruje událost _personZoneEnterExitEvent_ , když uživatel zadá nebo ukončí zónu a poskytne směrové informace s očíslovanou stranou zóny, která byla překročena. Vygeneruje _personZoneDwellTimeEvent_ , když osoba opustí zónu a poskytne směrové informace a počet milisekund, které osoba strávila v zóně.  |
| cognitiveservices Account. Vision. spatialanalysis-persondistance. livevideoanalytics | Sleduje, když lidé narušují pravidlo vzdálenosti. <br> Pravidelně generuje _personDistanceEvent_ s umístěním každého porušení vzdálenosti. |
| cognitiveservices Account. Vision. spatialanalysis. livevideoanalytics | Obecná operace, kterou lze použít ke spuštění všech výše uvedených scénářů. Tato možnost je užitečnější, pokud chcete spouštět více scénářů ve stejné kameře nebo používat systémové prostředky (například GPU) efektivněji. |

K dispozici jsou také operace živé analýzy videí ve `.debug` verzi (např. cognitiveservices Account. Vision. spatialanalysis-personcount. livevideoanalytics. Debug), které mají schopnost vizualizovat video snímky při zpracování. Aby `xhost +` bylo možné vizualizaci snímků a událostí videa povolit, bude nutné spustit na hostitelském počítači.

> [!IMPORTANT]
> Modely AI pro počítačové zpracování obrazu zjišťují a hledají přítomnost lidí v video záběrech a výstupech pomocí ohraničujícího rámečku kolem lidského těla. Modely AI se nesnaží zjistit identity nebo demografické údaje jednotlivců.

Jedná se o parametry, které vyžaduje každá z těchto prostorových analytických operací.

| Provozní parametry| Description|
|---------|---------|
| ID operace | Identifikátor operace z tabulky výše|
| enabled | Logická hodnota: true nebo false|
| VIDEO_URL| Adresa URL služby RTSP pro zařízení kamery (příklad: `rtsp://username:password@url` ) Prostorová analýza podporuje datový proud s kódováním H. 264 pomocí protokolu RTSP, http nebo MP4. Video_URL lze zadat jako zašifrovanou hodnotu řetězce Base64 pomocí šifrování AES a pokud je adresa URL videa zakódována `KEY_ENV` a je `IV_ENV` třeba ji zadat jako proměnné prostředí. Ukázkový nástroj, který vygeneruje klíče a šifrování, najdete [tady](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Popisný název zařízení kamery nebo streamu videa. Tím se vrátí výstup JSON události.|
| VIDEO_IS_LIVE| True pro zařízení kamery; hodnota false pro zaznamenaná videa|
| VIDEO_DECODE_GPU_INDEX| Který grafický procesor k dekódování snímku videa. Ve výchozím nastavení je 0. By měl být stejný jako `gpu_index` v jiné konfiguraci uzlu jako `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Šířka rámečku vstupního videa nebo streamu (např. 1920). Toto pole je volitelné a pokud je k dispozici, bude se tento snímek škálovat na tuto dimenzi a přitom zachovává poměr stran.|
| DETECTOR_NODE_CONFIG | JSON určující, na kterém GPU se má spustit uzel detektoru Měl by být v následujícím formátu: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Konfigurace JSON pro zónu a řádek, jak je uvedeno níže.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` Pokud chcete, aby se zjistilo, že uživatelé mají ve videu v datovém proudu více uživatelů, `False` zakažte ho Ve výchozím nastavení je tato hodnota zakázaná. Detekce masky obličeje vyžaduje, aby byl parametr šířky vstupní videa 1920 `"INPUT_VIDEO_WIDTH": 1920` . Atribut masky obličeje nebude vrácen, pokud se zjištěné osoby netýkají kamery nebo jsou příliš daleko od ní. Další informace najdete v průvodci [umístěním kamery](spatial-analysis-camera-placement.md) . |

### <a name="detector-node-parameter-settings"></a>Nastavení parametrů uzlu detektoru
Toto je příklad parametrů DETECTOR_NODE_CONFIG pro všechny operace prostorové analýzy.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Název | Typ| Description|
|---------|---------|---------|
| `gpu_index` | řetězec| Index GPU, na kterém se tato operace spustí|
| `do_calibration` | řetězec | Indikuje, že je zapnutá kalibrace. `do_calibration` aby funkce **cognitiveservices Account. Vision. spatialanalysis-persondistance** fungovala správně, musí mít hodnotu true. ve výchozím nastavení je do_calibration nastaveno na hodnotu true. |
| `enable_recalibration` | bool | Určuje, zda je zapnuta automatická rekalibrace. Výchozí je `true`.|
| `calibration_quality_check_frequency_seconds` | int | Minimální počet sekund mezi jednotlivými změnami kvality k určení, jestli je nutná znovu kalibrace. Výchozí hodnota je `86400` (24 hodin). Používá se pouze v případě `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Minimální počet sekund mezi shromažďováním nových vzorků dat pro rekalibraci a kontrolu kvality. Výchozí hodnota je `300` (5 minut). Používá se pouze v případě `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | Minimální počet nových vzorků dat, které se mají shromáždit na konci kolekce vzorků Výchozí je `10`. Používá se pouze v případě `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | Maximální počet ukázek dat, které se mají uložit při kalibraci modelu kamery Výchozí je `1000`. Používá se pouze v případě `enable_recalibration=True` .|
| `enable_breakpad`| bool | Určuje, zda chcete povolit Breakpad, který se používá ke generování výpisu stavu systému pro použití při ladění. `false`Ve výchozím nastavení je to. Pokud ho nastavíte na `true` , budete také muset přidat `"CapAdd": ["SYS_PTRACE"]` do `HostConfig` části kontejneru `createOptions` . Ve výchozím nastavení se výpis stavu systému nahraje do aplikace [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter, pokud chcete, aby se výpisy paměti nahrály do vlastní aplikace AppCenter, můžete proměnnou prostředí přepsat `RTPT_APPCENTER_APP_SECRET` pomocí tajného kódu aplikace.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Konfigurace a výstup operací prostorové analýzy
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Konfigurace zóny pro cognitiveservices Account. Vision. spatialanalysis-personcount

 Toto je příklad vstupu JSON pro parametr SPACEANALYTICS_CONFIG, který konfiguruje zónu. Pro tuto operaci můžete nakonfigurovat více zón.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Název | Typ| Description|
|---------|---------|---------|
| `zones` | list| Seznam zón. |
| `name` | řetězec| Popisný název této zóny.|
| `polygon` | list| Každá dvojice hodnot představuje x, y pro vrcholy mnohoúhelníku. Mnohoúhelník představuje oblasti, ve kterých jsou lidé sledováni nebo počítány, a body mnohoúhelníku jsou založeny na normalizovaných souřadnicích (0-1), kde je levý horní roh (0,0, 0,0) a pravý dolní roh je (1,0, 1,0).   
| `threshold` | float| Pokud je spolehlivost modelů AI větší nebo rovna této hodnotě, dojde k odchozímu přenosu událostí. |
| `type` | řetězec| Pro **cognitiveservices Account. Vision. spatialanalysis-personcount** by to mělo být `count` .|
| `trigger` | řetězec| Typ triggeru pro odeslání události. Podporované hodnoty jsou `event` pro odesílání událostí při změně počtu nebo `interval` pro pravidelné posílání událostí bez ohledu na to, jestli se počet změnil nebo ne.
| `output_frequency` | int | Rychlost, s jakou jsou události odchozí Když `output_frequency` je = X, každá událost X je výstupní, ex. `output_frequency` = 2 znamená, že každá další událost je výstup. `output_frequency`Je použitelný pro i `event` `interval` . |
| `focus` | řetězec| Umístění bodu v ohraničujícím poli osoby používané k výpočtu událostí. Hodnota fokusu může být `footprint` (nároky na osobu), (v ohraničujícím poli ve středu osoby) (v `bottom_center` `center` ohraničujícím poli středu).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Konfigurace řádku pro cognitiveservices Account. Vision. spatialanalysis-personcrossingline

Toto je příklad vstupu JSON pro parametr SPACEANALYTICS_CONFIG, který konfiguruje čáru. Pro tuto operaci můžete nakonfigurovat více překřížených čar.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Název | Typ| Description|
|---------|---------|---------|
| `lines` | list| Seznam řádků|
| `name` | řetězec| Popisný název tohoto řádku|
| `line` | list| Definice řádku Toto je směrná čára, která vám umožní pochopit "vstup" vs. "Exit".|
| `start` | dvojice hodnot| souřadnice x, y pro počáteční bod řádku Hodnoty float označují polohu vrcholu vzhledem k hornímu a levému rohu. Chcete-li vypočítat absolutní hodnoty x, y, vynásobte tyto hodnoty velikostí rámečku. |
| `end` | dvojice hodnot| souřadnice x, y pro koncový bod řádku Hodnoty float označují polohu vrcholu vzhledem k hornímu a levému rohu. Chcete-li vypočítat absolutní hodnoty x, y, vynásobte tyto hodnoty velikostí rámečku. |
| `threshold` | float| Pokud je spolehlivost modelů AI větší nebo rovna této hodnotě, dojde k odchozímu přenosu událostí. Výchozí hodnota je 16. To je doporučená hodnota pro dosažení maximální přesnosti. |
| `type` | řetězec| Pro **cognitiveservices Account. Vision. spatialanalysis-personcrossingline** by to mělo být `linecrossing` .|
|`trigger`|řetězec|Typ triggeru pro odeslání události.<br>Podporované hodnoty: událost: aktivuje se, když někdo přeškrtne řádek.|
| `focus` | řetězec| Umístění bodu v ohraničujícím poli osoby používané k výpočtu událostí. Hodnota fokusu může být `footprint` (nároky na osobu), (v ohraničujícím poli ve středu osoby) (v `bottom_center` `center` ohraničujícím poli středu). Výchozí hodnota je.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Konfigurace zóny pro cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon

Toto je příklad vstupu JSON pro parametr SPACEANALYTICS_CONFIG, který konfiguruje zónu. Pro tuto operaci můžete nakonfigurovat více zón.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Název | Typ| Description|
|---------|---------|---------|
| `zones` | list| Seznam zón. |
| `name` | řetězec| Popisný název této zóny.|
| `polygon` | list| Každá dvojice hodnot představuje x, y pro vrcholy mnohoúhelníku. Mnohoúhelník představuje oblasti, ve kterých jsou lidé sledováni nebo počítáni. Hodnoty float označují polohu vrcholu vzhledem k hornímu a levému rohu. Chcete-li vypočítat absolutní hodnoty x, y, vynásobte tyto hodnoty velikostí rámečku. 
| `threshold` | float| Pokud je spolehlivost modelů AI větší nebo rovna této hodnotě, dojde k odchozímu přenosu událostí. Výchozí hodnota je 48, pokud je typ zonecrossing a 16, pokud je čas DwellTime. Tyto hodnoty jsou doporučené pro dosažení maximální přesnosti.  |
| `type` | řetězec| Pro **cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon** by to mělo být `zonecrossing` nebo `zonedwelltime` .|
| `trigger`|řetězec|Typ triggeru pro odeslání události<br>Podporované hodnoty: Event: aktivuje se, když někdo zadá nebo ukončí zónu.|
| `focus` | řetězec| Umístění bodu v ohraničujícím poli osoby používané k výpočtu událostí. Hodnota fokusu může být `footprint` (nároky na osobu), (v ohraničujícím poli ve středu osoby) (v `bottom_center` `center` ohraničujícím poli středu). Výchozí hodnota je.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Konfigurace zóny pro cognitiveservices Account. Vision. spatialanalysis-persondistance

Toto je příklad vstupu JSON pro parametr SPACEANALYTICS_CONFIG, který konfiguruje zónu pro **cognitiveservices Account. Vision. spatialanalysis-persondistance**. Pro tuto operaci můžete nakonfigurovat více zón.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Název | Typ| Description|
|---------|---------|---------|
| `zones` | list| Seznam zón. |
| `name` | řetězec| Popisný název této zóny.|
| `polygon` | list| Každá dvojice hodnot představuje x, y pro vrcholy mnohoúhelníku. Mnohoúhelník představuje oblasti, ve kterých se uživatelé počítají, a vzdálenost mezi lidmi. Hodnoty float označují polohu vrcholu vzhledem k hornímu a levému rohu. Chcete-li vypočítat absolutní hodnoty x, y, vynásobte tyto hodnoty velikostí rámečku. 
| `threshold` | float| Pokud je spolehlivost modelů AI větší nebo rovna této hodnotě, dojde k odchozímu přenosu událostí. |
| `type` | řetězec| Pro **cognitiveservices Account. Vision. spatialanalysis-persondistance** by to mělo být `people_distance` .|
| `trigger` | řetězec| Typ triggeru pro odeslání události. Podporované hodnoty jsou `event` pro odesílání událostí při změně počtu nebo `interval` pro pravidelné posílání událostí bez ohledu na to, jestli se počet změnil nebo ne.
| `output_frequency` | int | Rychlost, s jakou jsou události odchozí Když `output_frequency` je = X, každá událost X je výstupní, ex. `output_frequency` = 2 znamená, že každá další událost je výstup. `output_frequency`Je použitelný pro i `event` `interval` .|
| `minimum_distance_threshold` | float| Vzdálenost ve stopách, která aktivuje událost "TooClose", pokud jsou lidé méně, než je vzdálenost od sebe.|
| `maximum_distance_threshold` | float| Vzdálenost ve stopách, která spustí událost "TooFar", pokud jsou lidé větší než vzdálenost.|
| `aggregation_method` | řetězec| Metoda pro agregovaný výsledek persondistance Aggregation_method platí pro i `mode` `average` .|
| `focus` | řetězec| Umístění bodu v ohraničujícím poli osoby používané k výpočtu událostí. Hodnota fokusu může být `footprint` (nároky na osobu), (v ohraničujícím poli ve středu osoby) (v `bottom_center` `center` ohraničujícím poli středu).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Konfigurace pro cognitiveservices Account. Vision. spatialanalysis
Toto je příklad vstupu JSON pro parametr SPACEANALYTICS_CONFIG, který konfiguruje řádek a zónu pro **cognitiveservices Account. Vision. spatialanalysis**. Pro tuto operaci můžete nakonfigurovat více řádků nebo zón a každý řádek nebo zóna může mít různé události.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Konfigurace kamery

Další informace o tom, jak nakonfigurovat zóny a řádky, najdete v pokynech k [umístění kamery](spatial-analysis-camera-placement.md) .

## <a name="spatial-analysis-operation-output"></a>Výstup operace prostorové analýzy

Události z každé operace jsou v Azure IoT Hub ve formátu JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formát JSON pro cognitiveservices Account. Vision. spatialanalysis-personcount AI Insights

Ukázkový formát JSON pro výstup události v této operaci.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Název pole události | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID události|
| `type` | řetězec| Typ události|
| `detectionsId` | array| Pole o velikosti 1 jedinečného identifikátoru detekce osob, která aktivovala tuto událost|
| `properties` |  – kolekce| Kolekce hodnot|
| `trackinId` | řetězec| Jedinečný identifikátor zjištěné osoby|
| `zone` | řetězec | Pole Name (název) mnohoúhelníku, které představuje zónu, která byla překročena.|
| `trigger` | řetězec| Typ triggeru je Event nebo interval v závislosti na hodnotě `trigger` v SPACEANALYTICS_CONFIG|

| Název pole detekce | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID detekce|
| `type` | řetězec| Typ detekce|
| `region` |  – kolekce| Kolekce hodnot|
| `type` | řetězec| Typ oblasti|
| `points` |  – kolekce| Levý horní a dolní pravý bod, pokud je typ oblasti obdélník |
| `confidence` | float| Spolehlivost algoritmu|
| `face_mask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) označuje zjištěnou osobu, která má formu o přítvářku. |
| `face_nomask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) znamená, že zjištěná osoba **nepoužívá** masku obličeje. |

| Název pole SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID kamery|
| `timestamp` | date| Datum UTC při vygenerování datové části JSON|
| `width` | int | Šířka rámečku videa|
| `height` | int | Výška snímku videa|
| `frameId` | int | Identifikátor snímku|
| `cameraCallibrationInfo` |  – kolekce | Kolekce hodnot|
| `status` | řetězec | Stav kalibrace ve formátu `state[;progress description]` . Stav může být `Calibrating` , `Recalibrating` (Pokud je povolena rekalibrace) nebo `Calibrated` . Část popisu průběhu je platná pouze v případě, že je `Calibrating` ve `Recalibrating` stavu a, který se používá k zobrazení průběhu aktuálního procesu kalibrace.|
| `cameraHeight` | float | Výška kamery nad vozovkou v metrech Toto je odvozeno z automatické kalibrace. |
| `focalLength` | float | Délka ohniska kamery v pixelech Toto je odvozeno z automatické kalibrace. |
| `tiltUpAngle` | float | Úhel naklonění kamery od svislého. Toto je odvozeno z automatické kalibrace.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formát JSON pro cognitiveservices Account. Vision. spatialanalysis-personcrossingline AI Insights

Ukázka JSON pro detekci výstupu pomocí této operace.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Název pole události | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID události|
| `type` | řetězec| Typ události|
| `detectionsId` | array| Pole o velikosti 1 jedinečného identifikátoru detekce osob, která aktivovala tuto událost|
| `properties` |  – kolekce| Kolekce hodnot|
| `trackinId` | řetězec| Jedinečný identifikátor zjištěné osoby|
| `status` | řetězec| Směr křížových čar, buď ' CrossLeft ' nebo ' CrossRight '. Směr je založen na předstání na začátku na konci řádku. CrossRight je překřížený zleva doprava. CrossLeft je překřížený zprava doleva.|
| `zone` | řetězec | Pole Name (název) řádku, který byl překročen|

| Název pole detekce | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID detekce|
| `type` | řetězec| Typ detekce|
| `region` |  – kolekce| Kolekce hodnot|
| `type` | řetězec| Typ oblasti|
| `points` |  – kolekce| Levý horní a dolní pravý bod, pokud je typ oblasti obdélník |
| `confidence` | float| Spolehlivost algoritmu|
| `face_mask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) označuje zjištěnou osobu, která má formu o přítvářku. |
| `face_nomask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) znamená, že zjištěná osoba **nepoužívá** masku obličeje. |

| Název pole SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID kamery|
| `timestamp` | date| Datum UTC při vygenerování datové části JSON|
| `width` | int | Šířka rámečku videa|
| `height` | int | Výška snímku videa|
| `frameId` | int | Identifikátor snímku|


> [!IMPORTANT]
> Model AI detekuje osobu bez ohledu na to, jestli je někdo na kameře nebo pryč. Model AI nespouští rozpoznávání tváře a negeneruje žádné biometrické informace. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formát JSON pro cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon AI Insights

Ukázka JSON pro detekci výstupu pomocí této operace s `zonecrossing` typem SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Ukázka JSON pro detekci výstupu pomocí této operace s `zonedwelltime` typem SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Název pole události | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID události|
| `type` | řetězec| Typ události Hodnota může být buď _personZoneDwellTimeEvent_ , nebo _personZoneEnterExitEvent_ .|
| `detectionsId` | array| Pole o velikosti 1 jedinečného identifikátoru detekce osob, která aktivovala tuto událost|
| `properties` |  – kolekce| Kolekce hodnot|
| `trackinId` | řetězec| Jedinečný identifikátor zjištěné osoby|
| `status` | řetězec| Směr křížení mnohoúhelníku, buď ENTER, nebo Exit|
| `side` | int| Číslo strany mnohoúhelníku, kterou osoba přejíždí. Každá strana je očíslovanou hranou mezi dvěma vrcholy mnohoúhelníku, které představují vaši zónu. Hrana mezi prvními dvěma vrcholy mnohoúhelníku představuje první stranu. ' Side ' je prázdné, pokud událost není přidružena k určité straně z důvodu překrytí. Například k ukončení došlo, když osoba zmizí, ale neviděla se nad stranou zóny, nebo když se osoba objevila v zóně, ale neviděla se stranou.|
| `durationMs` | float | Počet milisekund, které reprezentují čas strávený osobou v zóně. Toto pole je k dispozici, pokud je typ události _personZoneDwellTimeEvent_|
| `zone` | řetězec | Pole Name (název) mnohoúhelníku, které představuje zónu, která byla překročena.|

| Název pole detekce | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID detekce|
| `type` | řetězec| Typ detekce|
| `region` |  – kolekce| Kolekce hodnot|
| `type` | řetězec| Typ oblasti|
| `points` |  – kolekce| Levý horní a dolní pravý bod, pokud je typ oblasti obdélník |
| `confidence` | float| Spolehlivost algoritmu|
| `face_mask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) označuje zjištěnou osobu, která má formu o přítvářku. |
| `face_nomask` | float | Hodnota spolehlivosti atributu s rozsahem (0-1) znamená, že zjištěná osoba **nepoužívá** masku obličeje. |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formát JSON pro cognitiveservices Account. Vision. spatialanalysis-persondistance AI Insights

Ukázka JSON pro detekci výstupu pomocí této operace.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Název pole události | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID události|
| `type` | řetězec| Typ události|
| `detectionsId` | array| Pole o velikosti 1 jedinečného identifikátoru detekce osob, která aktivovala tuto událost|
| `properties` |  – kolekce| Kolekce hodnot|
| `personCount` | int| Počet lidí zjištěných při vygenerování události|
| `averageDistance` | float| Průměrná vzdálenost mezi všemi zjištěnými lidmi ve stopách|
| `minimumDistanceThreshold` | float| Vzdálenost ve stopách, která aktivuje událost "TooClose", pokud jsou lidé menší než vzdálenost.|
| `maximumDistanceThreshold` | float| Vzdálenost ve stopách, která spustí událost "TooFar", pokud jsou lidé větší než vzdálenost od sebe.|
| `eventName` | řetězec| Název události je `TooClose` `minimumDistanceThreshold` porušená, `TooFar` Pokud `maximumDistanceThreshold` je porušená, nebo `unknown` když se automatická kalibrace nedokončila.|
| `distanceViolationPersonCount` | int| Počet lidí zjištěných při porušení `minimumDistanceThreshold` nebo `maximumDistanceThreshold`|
| `zone` | řetězec | Pole Name (název) mnohoúhelníku, které představuje zónu monitorovanou pro distancing mezi lidmi|
| `trigger` | řetězec| Typ triggeru je Event nebo interval v závislosti na hodnotě `trigger` v SPACEANALYTICS_CONFIG|

| Název pole detekce | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID detekce|
| `type` | řetězec| Typ detekce|
| `region` |  – kolekce| Kolekce hodnot|
| `type` | řetězec| Typ oblasti|
| `points` |  – kolekce| Levý horní a dolní pravý bod, pokud je typ oblasti obdélník |
| `confidence` | float| Spolehlivost algoritmu|
| `centerGroundPoint` | 2 hodnoty float| `x`, `y` hodnoty se souřadnicemi odvozených umístění osoby na zemi ve stopách. `x` a `y` jsou souřadnicemi roviny podlahy za předpokladu, že je podlahová úroveň. Umístění kamery je původní. |

Při výpočtu `centerGroundPoint` `x` je vzdálenost od kamery k osobě podél čáry kolmé k rovině obrazu kamery. `y` je vzdálenost od kamery k osobě podél čáry rovnoběžná s rovinou obrazu kamery. 

![Příklad středového bodu na střed](./media/spatial-analysis/x-y-chart.png) 

V tomto příkladu `centerGroundPoint` je `{x: 4, y: 5}` . To znamená, že někdo 4 nohy od kamery a 5 metrů napravo od ruky, a to v horní části místnosti.


| Název pole SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | řetězec| ID kamery|
| `timestamp` | date| Datum UTC při vygenerování datové části JSON|
| `width` | int | Šířka rámečku videa|
| `height` | int | Výška snímku videa|
| `frameId` | int | Identifikátor snímku|
| `cameraCallibrationInfo` |  – kolekce | Kolekce hodnot|
| `status` | řetězec | Stav kalibrace ve formátu `state[;progress description]` . Stav může být `Calibrating` , `Recalibrating` (Pokud je povolena rekalibrace) nebo `Calibrated` . Část popisu průběhu je platná pouze v případě, že je `Calibrating` ve `Recalibrating` stavu a, který se používá k zobrazení průběhu aktuálního procesu kalibrace.|
| `cameraHeight` | float | Výška kamery nad vozovkou v metrech Toto je odvozeno z automatické kalibrace. |
| `focalLength` | float | Délka ohniska kamery v pixelech Toto je odvozeno z automatické kalibrace. |
| `tiltUpAngle` | float | Úhel naklonění kamery od svislého. Toto je odvozeno z automatické kalibrace.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>Formát JSON pro cognitiveservices Account. Vision. spatialanalysis AI Insights

Výstup této operace závisí na konfiguraci `events` , například pokud existuje `zonecrossing` událost konfigurovaná pro tuto operaci, výstup bude stejný jako `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>Použít výstup generovaný kontejnerem

Do své aplikace můžete chtít integrovat detekci prostorové analýzy nebo události. Tady je několik přístupů, které je potřeba vzít v úvahu: 

* K připojení ke koncovému bodu Azure IoT Hub a příjmu událostí použijte sadu Azure Event hub SDK pro zvolený programovací jazyk. Další informace najdete v tématu [čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
* Nastavte **směrování zpráv** na IoT Hub Azure pro odesílání událostí do jiných koncových bodů nebo uložte události do úložiště dat. Další informace najdete v tématu [IoT Hub směrování zpráv](../../iot-hub/iot-hub-devguide-messages-d2c.md) . 
* Nastavte úlohu Azure Stream Analytics pro zpracování událostí v reálném čase při jejich doručování a vytváření vizualizací. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Škálování provozu prostorových analytických operací ve velkém měřítku (více kamer)

Aby se dosáhlo nejlepšího výkonu a využití GPU, můžete nasadit všechny operace prostorové analýzy ve více fotoaparátech pomocí instancí grafu. Níže je ukázka spuštění `cognitiveservices.vision.spatialanalysis-personcrossingline` operace na patnácti fotoaparátech.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Název | Typ| Description|
|---------|---------|---------|
| `batch_size` | int | Pokud mají všechny kamery stejné rozlišení, nastavte `batch_size` počet fotoaparátů, které se v této operaci použijí, jinak nastavte `batch_size` na hodnotu 1 nebo nechte jako výchozí (1), což znamená, že žádná dávka není podporovaná. |

## <a name="next-steps"></a>Další kroky

* [Nasazení webové aplikace počítající lidi](spatial-analysis-web-app.md)
* [Protokolování a řešení potíží](spatial-analysis-logging.md)
* [Průvodce umístěním kamery](spatial-analysis-camera-placement.md)
* [Průvodce umístěním zóny a linky](spatial-analysis-zone-line-placement.md)
