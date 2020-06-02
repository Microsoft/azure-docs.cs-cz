---
title: Koncept Media graphu – Azure
description: Media Graph umožňuje definovat, odkud se mají média zachytit, jak by měla být zpracována a kde se mají výsledky doručovat. Tento článek obsahuje podrobný popis konceptu Media graphu.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260964"
---
# <a name="media-graph-concept"></a>Koncept Media graphu

## <a name="suggested-pre-reading"></a>Navrhované před čtením

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)

## <a name="overview"></a>Přehled

Media Graph umožňuje definovat, odkud se mají média zachytit, jak by měla být zpracována a kde se mají výsledky doručovat. Mediální graf se skládá z uzlů zdrojového, procesoru a jímky. Diagram níže poskytuje grafické znázornění grafu médií.   

![Grafická reprezentace mediálního grafu](./media/media-graph/overview.png)


* **Zdrojové uzly** umožňují zachytávání médií do mediálního grafu. Médium v tomto kontextu je koncepční, může se jednat o zvukový stream, datový proud videa, datový proud nebo datový proud, který v jednom datovém proudu obsahuje zvuk, video nebo data dohromady.
* **Uzly procesoru** umožňují zpracování médií v mediálním grafu.
* **Uzly jímky** umožňují doručování výsledků zpracování do služeb a aplikací mimo mediální graf.

## <a name="media-graph-topologies-and-instances"></a>Topologie a instance mediálního grafu 

Live video Analytics na IoT Edge umožňuje spravovat mediální grafy prostřednictvím dvou konceptů – "topologie grafu" a "instance grafu". Topologie grafu umožňuje definovat podrobný plán grafu s parametry jako zástupné symboly pro hodnoty. Topologie definuje, které uzly se použijí v mediálním grafu a jak jsou připojené v rámci Media graphu. Hodnoty parametrů lze zadat při vytváření instancí grafu odkazujících na topologii. To umožňuje vytvořit více instancí odkazujících na stejnou topologii, ale s různými hodnotami pro parametry zadané v topologii. 

## <a name="media-graph-states"></a>Stavy mediálního grafu  

Mediální graf může být v jednom z následujících stavů:

* Neaktivní – představuje stav, ve kterém je nakonfigurované mediální graf, ale není aktivní.
* Aktivace – stav při vytváření instance mediálního grafu (tj. přechodový stav mezi neaktivním a aktivním).
* Aktivní – stav, kdy je mediální graf aktivní 

    > [!NOTE]
    >  Mediální graf může být aktivní bez toku dat (například vstupní zdroj videa přejde do režimu offline).
* Deaktivace – jedná se o stav, když je mediální graf převedený z aktivní na neaktivní.

Následující diagram znázorňuje Stavový počítač Media Graph.

![Stavový počítač Media graphu](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Zdroje, procesory a jímky  

Live video Analytics na IoT Edge podporuje následující typy uzlů v rámci mediálního grafu:

### <a name="sources"></a>zdroje 

#### <a name="rtsp-source"></a>Zdroj RTSP 

Zdroj RTSP povoluje záznamová média ze serveru [RTSP](https://tools.ietf.org/html/rfc2326) . Protokol RTSP slouží k navázání a řízení relací multimédií mezi serverem a klientem. Uzel zdroje RTSP v mediálním grafu funguje jako klient a může vytvořit relaci se zadaným serverem RTSP. Řada zařízení, jako je většina [fotoaparátů IP](https://en.wikipedia.org/wiki/IP_camera) , má integrovaný server RTSP. [ONVIF](https://www.onvif.org/) pověření RTSP, která budou podporována v definici [profilů G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) kompatibilních zařízení. Uzel zdroje RTSP v aplikaci Media Graph vyžaduje zadání adresy URL služby RTSP spolu s přihlašovacími údaji pro povolení ověřeného připojení.

#### <a name="iot-hub-message-source"></a>IoT Hub zdroj zprávy 

Stejně jako u jiných [IoT Edgech modulů](../../iot-edge/iot-edge-glossary.md#iot-edge-module)může analýza živých videí v IoT Edge modulu přijímat zprávy prostřednictvím [centra IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Tyto zprávy lze odesílat z jiných modulů nebo aplikací spuštěných na hraničním zařízení nebo z cloudu. Tyto zprávy mohou být doručeny (směrovány) do [pojmenovaného vstupu](../../iot-edge/module-composition.md#sink) v modulu. Zdroj zprávy IoT Hub umožňuje směrovat tyto zprávy do instance mediálního grafu. Tyto zprávy nebo signály se pak dají použít interně v mediálním grafu, obvykle aktivovat brány signálu (viz [procesor brány signálu](#signal-gate-processor) níže). 

Můžete mít například modul IoT Edge, který generuje zprávu při otevření dvířek. Zpráva z tohoto modulu se dá směrovat do centra IoT Edge, ze kterého se pak dá směrovat do zdroje zpráv služby IoT Hub v mediálním grafu. V mediálním grafu může zdroj zprávy služby IoT Hub předat událost procesoru brány signálu, který potom může zapnout záznam videa ze zdroje RTSP do souboru. 

### <a name="processors"></a>Procesory  

#### <a name="motion-detection-processor"></a>Procesor detekce pohybu 

Procesor detekce pohybu umožňuje detekovat pohyb v živém videu. Prověřuje příchozí video a určí, jestli se ve videu nachází pohyb. Pokud se zjistí pohyb, předá se videu do uzlu pro příjem dat a vygeneruje událost. Procesor detekce pohybu (ve spojení s jinými uzly multimediálního grafu) se dá použít k aktivaci nahrávání příchozího videa, když se zjistí pohyb.

#### <a name="frame-rate-filter-processor"></a>Procesor filtru snímkové rychlosti  

Procesor filtru frekvence snímků vám umožní vzorkovat snímky z příchozího streamu videa v zadaném kmitočtu snímků. Díky tomu můžete snížit počet rámců odeslaných do uzlů nefunkčních toků (například procesor rozšíření HTTP) pro další zpracování.

#### <a name="http-extension-processor"></a>Procesor rozšíření HTTP 

Procesor rozšíření HTTP umožňuje zapojit vlastní AI do mediálního grafu. Procesor rozšíření HTTP přebírá jako vstupní Dekódovatelné snímky videa a přenáší takové snímky na koncový bod HTTP. Procesor nabízí možnost ověřování v případě potřeby u koncového bodu HTTP. Kromě toho procesor obsahuje vestavěný formátovací modul obrázku, který umožňuje škálování a kódování snímků videa před jejich přeposíláním. Změna měřítka obsahuje možnosti pro poměr stran obrazu, které mají být zachovány, doplněny nebo roztaženy, zatímco kódování poskytuje možnosti pro různá kódování obrázků, jako je JPEG, PNG nebo BMP.

#### <a name="signal-gate-processor"></a>Procesor brány signálu  

Procesor brány signálu umožňuje podmíněné přeposílání médií z jednoho uzlu do druhého. Funguje taky jako vyrovnávací paměť, která umožňuje synchronizaci médií a událostí. Příkladem případu použití je vložení procesoru brány signálu mezi zdroj RTSP a jímku assetu a používání výstupu procesoru snímače pohybu ke spuštění brány. Pomocí takového mediálního grafu můžete aktivovat nahrávání médií jenom v případě, že se ve příchozím videu zjistí pohyb. 

### <a name="sinks"></a>Jímky  

#### <a name="asset-sink"></a>Jímka assetu  

Jímka assetu umožňuje mediálnímu grafu zapisovat data médií (videa nebo zvuku) do prostředku Azure Media Services. Další informace o prostředcích a jejich roli při zaznamenávání a přehrávání médií najdete v části [Asset](terminology.md#asset) .  

#### <a name="file-sink"></a>Jímka souborů  

Jímka souborů umožňuje mediálnímu grafu zapisovat data médií (videa nebo zvuku) do umístění v místním systému souborů IoT Edgeho zařízení. Jímka souboru musí být pro procesor brány signálu. Tím se omezí doba trvání výstupních souborů na hodnoty zadané ve vlastnostech procesoru brány signálu.

#### <a name="iot-hub-message-sink"></a>IoT Hub jímka zpráv  

IoT Hub jímka zpráv umožňuje publikovat události do centra IoT Edge. Hraniční centrum může následně směrovat data do jiných modulů nebo aplikací na hraničním zařízení nebo do IoT Hub v cloudu (na trasách uvedených v manifestu nasazení). Jímka IoT Hub zpráv může přijímat události z nadřazených procesorů, jako je procesor detekce pohybu, nebo z externí služby odvození přes procesor rozšíření HTTP.

## <a name="scenarios"></a>Scénáře

Pomocí kombinace uzlů zdrojového kódu, procesoru a jímky, které jsou definované výše, můžete vytvářet grafy médií pro celou řadu scénářů. Následují příklady scénářů:

* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Nahrávání videa založeného na událostech](event-based-video-recording-concept.md)
* [Živé video analýza bez nahrávání videa](analyze-live-video-concept.md)

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů v tématu [rychlý Start: spuštění živé analýzy videí pomocí vlastního modelu](use-your-model-quickstart.md) a podívejte se, jak můžete spustit detekci pohybů na živém informačním kanálu videa.
