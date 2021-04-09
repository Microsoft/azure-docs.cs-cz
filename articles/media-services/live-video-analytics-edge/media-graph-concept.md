---
title: Koncept Media graphu – Azure
description: Mediální graf umožňuje definovat, odkud se mají média zachytit, jak by měla být zpracována a kde by měly být doručeny výsledky. Tento článek obsahuje podrobný popis konceptu Media graphu.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: ad23acbbbdd0c15e92e471ee22a229470a8a3a75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557662"
---
# <a name="media-graph"></a>Graf médií

## <a name="suggested-pre-reading"></a>Navrhované před čtením

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)

## <a name="overview"></a>Přehled

Mediální graf umožňuje definovat, odkud se mají média zachytit, jak by měla být zpracována a kde by měly být doručeny výsledky. To provedete tak, že podle potřeby propojíte součásti nebo uzly. Diagram níže poskytuje grafické znázornění grafu médií.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Graf médií":::

Live video Analytics na IoT Edge podporuje různé typy zdrojů, procesorů a jímky.

* **Zdrojové uzly** umožňují zachytávání médií do mediálního grafu. Médium v tomto kontextu je koncepční, může se jednat o zvukový stream, datový proud videa, datový proud nebo datový proud, který v jednom datovém proudu obsahuje zvuk, video nebo data dohromady.
* **Uzly procesoru** umožňují zpracování médií v mediálním grafu.
* **Uzly jímky** umožňují doručování výsledků zpracování do služeb a aplikací mimo mediální graf.

## <a name="media-graph-topologies-and-instances"></a>Topologie a instance mediálního grafu 

Live video Analytics na IoT Edge umožňuje spravovat mediální grafy prostřednictvím dvou konceptů – "topologie grafu" a "instance grafu". Topologie grafu umožňuje definovat podrobný plán grafu s parametry jako zástupné symboly pro hodnoty. Topologie definuje, které uzly se použijí v mediálním grafu a jak jsou připojené v rámci Media graphu. Pokud byste například chtěli nahrávat informační kanál z kamery, museli byste graf se zdrojovým uzlem, který přijímá video, a uzlem jímky, který zapisuje video.

Hodnoty pro parametry v topologii jsou určeny při vytváření instancí grafu, které odkazují na topologii. To umožňuje vytvořit více instancí odkazujících na stejnou topologii, ale s různými hodnotami pro parametry zadané v topologii. V předchozím příkladu byste mohli použít parametry, které reprezentují IP adresu kamery, a název zaznamenaného videa. Můžete vytvořit mnoho instancí grafu s touto topologií-jedna instance pro každou kameru v budově, třeba, každou s konkrétní IP adresou a konkrétním názvem.

## <a name="media-graph-states"></a>Stavy mediálního grafu  

Životní cyklus topologií grafu a instancí grafu je zobrazený v následujícím diagramu stavu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Životní cyklus topologie grafu a životního cyklu instance grafu":::

Začnete s [vytvářením topologie grafu](direct-methods.md#graphtopologyset). Potom pro každý živý informační kanál videa, který chcete s touto topologií zpracovávat, [vytvoříte instanci grafu](direct-methods.md#graphinstanceset). 

Instance grafu bude ve `Inactive` stavu nečinnosti.

Až budete připraveni odeslat živý kanál videa do instance grafu, [aktivujete](direct-methods.md#graphinstanceactivate) ho. Instance grafu se krátce prochází přechodovým `Activating` stavem a pokud je úspěšná, přejděte do `Active` stavu. Ve `Active` stavu bude zpracováno médium (Pokud instance grafu obdrží vstupní data).

> [!NOTE]
>  Instance grafu může být aktivní bez toku dat (například fotoaparát přejde do režimu offline).
> Vaše předplatné Azure bude účtováno, pokud je instance grafu v aktivním stavu.

Můžete opakovat proces vytváření a aktivace jiných instancí grafu pro stejnou topologii, pokud máte další aktivní kanály videa pro zpracování.

Po dokončení zpracování živého kanálu videa můžete instanci grafu [deaktivovat](direct-methods.md#graphinstancedeactivate) . Instance grafu se krátce prochází přechodovým `Deactivating` stavem, vyprázdní všechna data, která má, a pak se vrátí do `Inactive` stavu.

Instanci grafu lze [Odstranit](direct-methods.md#graphinstancedelete) pouze v případě, že je ve `Inactive` stavu.

Po odstranění všech instancí grafu odkazujících na konkrétní topologii grafu můžete [Odstranit topologii grafu](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Zdroje, procesory a jímky  

Live video Analytics na IoT Edge podporuje následující typy uzlů v rámci mediálního grafu:

### <a name="sources"></a>zdroje 

#### <a name="rtsp-source"></a>Zdroj RTSP 

Zdrojový uzel RTSP vám umožní ingestovat média ze serveru [RTSP](https://tools.ietf.org/html/rfc2326) . Fotoaparáty a kamery založené na protokolu IP odesílají data do protokolu s názvem RTSP (Real-time-streaming Protocol), který se liší od jiných typů zařízení, jako jsou telefony a video kamery. Tento protokol se používá k navázání a řízení relací multimédií mezi serverem (fotoaparátem) a klientem. Uzel zdroje RTSP v mediálním grafu funguje jako klient a může vytvořit relaci se serverem RTSP. Řada zařízení, jako je většina [fotoaparátů IP](https://en.wikipedia.org/wiki/IP_camera) , má integrovaný server RTSP. [ONVIF](https://www.onvif.org/) pověření RTSP, která budou podporována v definici [profilů G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) kompatibilních zařízení. Zdrojový uzel RTSP vyžaduje, abyste zadali adresu URL protokolu RTSP spolu s přihlašovacími údaji, aby bylo možné povolit ověřované připojení.

#### <a name="iot-hub-message-source"></a>IoT Hub zdroj zprávy 

Stejně jako u jiných [IoT Edgech modulů](../../iot-fundamentals/iot-glossary.md#iot-edge)může analýza živých videí v IoT Edge modulu přijímat zprávy prostřednictvím [centra IoT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge-hub). Tyto zprávy lze odesílat z jiných modulů nebo aplikací spuštěných na hraničním zařízení nebo z cloudu. Tyto zprávy jsou doručeny (směrovány) do [pojmenovaného vstupu](../../iot-edge/module-composition.md#sink) v modulu. Uzel zdroje zprávy IoT Hub umožňuje takovým zprávám dosáhnout multimediálního grafu. Tyto zprávy nebo signály se pak dají v mediálním grafu použít interně, obvykle aktivovat brány signálu (viz oznámení [signálů](#signal-gate-processor) níže). 

Můžete mít například modul IoT Edge, který generuje zprávu při otevření dvířek. Zpráva z tohoto modulu se dá směrovat do centra IoT Edge, ze kterého se pak dá směrovat do zdroje zpráv služby IoT Hub v mediálním grafu. V mediálním grafu může zdroj zprávy služby IoT Hub předat událost procesoru brány signálu, který potom může zapnout záznam videa ze zdroje RTSP do souboru. 

### <a name="processors"></a>Procesory  

#### <a name="motion-detection-processor"></a>Procesor detekce pohybu 

Uzel procesoru pro detekci pohybu umožňuje detekovat pohyb v živém videu. Prověřuje příchozí snímky videa a určí, zda se ve videu nachází pohyb. Pokud se zjistí pohyb, předá se snímky videa komponentě pro příjem dat a vygeneruje událost. Uzel procesoru pro detekci pohybu (ve spojení s ostatními uzly) se dá použít k aktivaci nahrávání příchozího videa, když se zjistí pohyb.

#### <a name="frame-rate-filter-processor"></a>Procesor filtru snímkové rychlosti  

Uzel procesor filtru frekvence snímků umožňuje vzorkovat snímky z příchozího streamu videa v zadané míře. Díky tomu můžete snížit počet rámců poslaných komponentám mimo datový proud (například uzel procesoru rozšíření HTTP) pro další zpracování.
>[!WARNING]
> Tento procesor je **zastaralý** v nejnovější verzi nástroje Live video Analytics v modulu IoT Edge. Správa frekvence snímků se teď podporuje v rámci samotného procesoru rozšíření grafu.

#### <a name="http-extension-processor"></a>Procesor rozšíření HTTP

Uzel procesoru rozšíření HTTP umožňuje připojit vlastní modul IoT Edge k mediálnímu grafu. Tento uzel přijímá jako vstup Dekódovatelné snímky videa a přenáší takové snímky do koncového bodu HTTP REST vystaveného vaším modulem. V případě potřeby je možné tento uzel ověřit pomocí koncového bodu REST. Kromě toho má uzel vestavěný formátovací modul obrázků pro škálování a kódování snímků videa před jejich přenosem do koncového bodu REST. Měřítko obsahuje možnosti pro poměr stran obrázku, který má být zachován, doplněn nebo roztažen. Image Encoder podporuje formáty JPEG, PNG nebo BMP. Přečtěte si další informace [o procesoru.](media-graph-extension-concept.md#http-extension-processor)

#### <a name="grpc-extension-processor"></a>procesor rozšíření gRPC

Uzel procesoru rozšíření gRPC přijímá jako vstup Dekódovatelné snímky videa a přenáší takové snímky do koncového bodu [gRPC](terminology.md#grpc) vystaveného vaším modulem. Uzel podporuje přenos dat pomocí [sdílené paměti](https://en.wikipedia.org/wiki/Shared_memory) nebo přímý vkládání obsahu do textu zpráv gRPC. Kromě toho má uzel vestavěný formátovací modul obrázků pro škálování a kódování snímků videa před jejich přenosem na koncový bod gRPC. Měřítko obsahuje možnosti pro poměr stran obrázku, který má být zachován, doplněn nebo roztažen. Image Encoder podporuje formáty JPEG, PNG nebo BMP. Přečtěte si další informace [o procesoru.](media-graph-extension-concept.md#grpc-extension-processor)

#### <a name="signal-gate-processor"></a>Procesor brány signálu  

Uzel procesoru brány signálu umožňuje podmíněně předává média z jednoho uzlu do druhého. Funguje taky jako vyrovnávací paměť, která umožňuje synchronizaci médií a událostí. Typickým případem použití je vložení uzlu procesoru brány signálu mezi zdrojovým uzlem RTSP a uzlem jímky assetu a použitím výstupu uzlu procesoru snímače pohybu aktivovat tuto bránu. V takovém mediálním grafu budete nahrávat video jenom v případě, že se zjistí pohyb.

### <a name="sinks"></a>Jímky  

#### <a name="asset-sink"></a>Jímka assetu  

Uzel jímka assetu umožňuje zapisovat data médií (videa nebo zvuku) do prostředku Azure Media Services. V mediálním grafu může být pouze jeden uzel jímky assetu. Další informace o prostředcích a jejich roli při zaznamenávání a přehrávání médií najdete v části [Asset](terminology.md#asset) . Podrobnosti o tom, jak se používají vlastnosti tohoto uzlu, najdete v článku o [průběžném nahrávání videa](continuous-video-recording-concept.md) .

#### <a name="file-sink"></a>Jímka souborů  

Uzel jímky souborů umožňuje zapisovat data médií (videa nebo zvuku) do umístění v místním systému souborů IoT Edgeho zařízení. V mediálním grafu může být jenom jeden uzel jímky souborů a musí se jednat o uzel procesoru brány signálu. Tím se omezí doba trvání výstupních souborů na hodnoty zadané ve vlastnostech uzlu procesoru brány signálu. Aby bylo zajištěno, že vaše hraniční zařízení nemá nedostatek místa na disku, můžete také nastavit maximální velikost, kterou může aplikace Live video Analytics v modulu IoT Edge použít k ukládání dat.  
> [!NOTE]
Pokud se jímka souboru zaplní, Live video Analytics v modulu IoT Edge začne odstraňovat nejstarší data a nahradí je novým.
#### <a name="iot-hub-message-sink"></a>IoT Hub jímka zpráv  

Uzel jímky zpráv IoT Hub umožňuje publikovat události do centra IoT Edge. Centrum IoT Edge pak může směrovat data do jiných modulů nebo aplikací na hraničním zařízení nebo do IoT Hub v cloudu (na trasách uvedených v manifestu nasazení). Uzel jímky zpráv IoT Hub může přijímat události z nadřazených procesorů, jako je například uzel procesoru detekce pohybu, nebo externí odvozenou službu přes uzel procesoru rozšíření HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Pravidla pro použití uzlů

Viz [omezení pro topologie grafů](quotas-limitations.md#limitations-on-graph-topologies-at-preview) pro další pravidla, jak lze v mediálním grafu použít různé uzly.

## <a name="scenarios"></a>Scénáře

Pomocí kombinace zdrojů, procesorů a umyvadel definovaných výše můžete vytvořit graf médií pro celou řadu scénářů, které zahrnují analýzu živého videa. Příklady scénářů:

* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Analýza živého videa bez nahrávání videa](analyze-live-video-concept.md)

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak můžete spustit detekci pohybu na živém informačním kanálu, přečtěte si téma [rychlý Start: spuštění živé analýzy videí pomocí vlastního modelu](use-your-model-quickstart.md).
