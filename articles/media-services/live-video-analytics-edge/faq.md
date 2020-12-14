---
title: Live video Analytics na IoT Edge Nejčastější dotazy – Azure
description: Toto téma obsahuje odpovědi na živé video analýzy na základě nejčastějších dotazů IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401572"
---
# <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

Toto téma obsahuje odpovědi na živé video analýzy na základě nejčastějších dotazů IoT Edge.

## <a name="general"></a>Obecné

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Jaké jsou systémové proměnné, které se dají použít v definici topologie grafu?

|Proměnná   |Popis|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Představuje čas v čase UTC, který se obvykle vyjadřuje jako datum a denní doba (základní reprezentace yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Představuje instanci data a času UTC ve formátu kompatibilním se soubory ISO8601 s milisekundami (základní reprezentace yyyyMMddTHHmmss. fffZ).|
|System. GraphTopologyName   |Představuje topologii mediálního grafu, obsahuje podrobný plán grafu.|
|System. GraphInstanceName|  Představuje instanci mediálního grafu, uchovává hodnoty parametrů a odkazuje na topologii.|

## <a name="configuration-and-deployment"></a>Konfigurace a nasazení

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Můžu modul Media Edge nasadit do zařízení s Windows 10?

Ano. Podívejte se na článek o [kontejnerech Linux ve Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Zachytit z kamery IP a nastavení protokolu RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Musím k odeslání streamu videa na zařízení použít speciální sadu SDK?

No. Live video Analytics na IoT Edge podporuje záznamová média pomocí protokolu RTSP pro streamování videa (který se podporuje u většiny fotoaparátů protokolu IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Můžu nahrávat multimédia do živé analýzy videí v IoT Edge pomocí RTMP nebo hladkého (jako je Media Services živá událost)?

* No. Live video Analytics podporuje jenom RTSP pro digitalizaci videa z fotoaparátů IP.
* Všechny kamery podporující streamování RTSP přes TCP/HTTP by měly fungovat. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Můžu u instance grafu resetovat nebo aktualizovat adresu URL zdroje RTSP?

Ano, pokud je instance grafu v neaktivním stavu.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Je k dispozici simulátor RTSP pro použití při testování a vývoji?

Ano. K dispozici je modul Edge [simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pro použití v rychlém startu a kurzech pro podporu procesu učení. Při poskytování tohoto modulu se snažíme všem maximálně vyhovět, ale ne vždy musí být dostupný. Důrazně doporučujeme, abyste tuto dobu nepoužívali déle než několik hodin. Než začnete vytvářet plány nasazení v produkčním prostředí, měli byste investovat do testování s vaším skutečným zdrojem RTSP.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Podporujete zjišťování ONVIF IP kamer na hraničních zařízeních?

Ne, zjišťování ONVIF na hraničních zařízeních se nepodporuje.

## <a name="streaming-and-playback"></a>Streamování a přehrávání

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Můžou se prostředky zaznamenané do AMS z Edge přehrát zpátky pomocí Media Services technologie streamování, jako je HLS nebo POMLČKa?

Ano. Nahrané prostředky mohou být streamované jako jakékoli jiné prostředky v Azure Media Services. Pokud chcete streamovat obsah, musíte mít vytvořený koncový bod streamování a ve stavu spuštěno. Pomocí standardního procesu vytváření lokátoru streamování získáte přístup k HLS nebo POMLČKám manifest pro streamování do libovolných rozhraní přehrávače, které podporuje. Podrobnosti o vytváření publikovaných manifestů HLS nebo SPOJOVNÍKů naleznete v tématu [dynamické balení](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Můžu u archivovaného assetu použít standardní ochranu obsahu a funkce DRM Media Services?

Ano. Veškerá standardní funkce Ochrana obsahu a DRM v dynamickém šifrování je k dispozici pro použití na assetech zaznamenaných z mediálního grafu.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Jaké přehrávače můžu použít k zobrazení obsahu ze zaznamenaných assetů?

Podporují se všechny standardní přehrávače podporující rozhraní Apple HTTP Live Streaming (HLS) verze 3 nebo verze 4. Kromě toho se podporuje i jakýkoli přehrávač, který podporuje přehrávání kompatibilní se standardem MPEG-SPOJOVNÍK.

Mezi Doporučené přehrávače pro testování patří:

* [Přehrávač médií Azure](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Přehrávač Shaka](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Aplikace Edge, Chrome nebo Safari sestavené v přehrávači videa HTML5
* Komerční přehrávače, kteří podporují přehrávání HLS nebo POMLČKy

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Jaká jsou omezení pro streamování assetu mediálního grafu?

Streamování živého nebo zaznamenaného assetu z mediálního grafu používá stejnou infrastrukturu s vysokým škálováním a streamování, kterou Media Services podporuje na vyžádání a živé streamování pro multimédia & zábavy, OTT a vysílání pro zákazníky. To znamená, že můžete rychle a snadno povolit Azure CDN, Verizon nebo Akamai pro doručování obsahu posluchačům malým počtem návštěvníků nebo až milionům v závislosti na vašem scénáři.

Obsah lze doručovat pomocí Apple HTTP Live Streaming (HLS) nebo MPEG-POMLČKy.

## <a name="design-your-ai-model"></a>Návrh vašeho modelu AI 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>V kontejneru Docker mám zabalené více modelů AI. Jak je mám používat se službou Live video Analytics? 

Řešení se liší v závislosti na komunikačním protokolu používaném serverem Inferencing ke komunikaci se službou Live video Analytics. Níže jsou uvedeny některé způsoby, jak to provést.

#### <a name="http-protocol"></a>Protokol HTTP:

* Jeden kontejner (jeden lvaExtension):  

   Na serveru Inferencing můžete použít jeden port, ale různé koncové body pro různé modely AI. Například pro příklad v Pythonu můžete použít různé `route` typy s na model jako: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   A pak ve svém nasazení živé analýzy videí nastavte při vytváření instancí grafy odvozenou adresu URL serveru pro každou instanci jako: 

   1. instance: odvozená adresa URL serveru =`http://lvaExtension:44000/score/face_detection`<br/>
   druhá instance: odvozená adresa URL serveru =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Alternativně můžete také zpřístupnit modely AI na různých portech a volat je při vytváření instancí grafů.  

* Více kontejnerů: 

   Každý kontejner je nasazen s jiným názvem. V sadě dokumentace Live video Analytics jste v současnosti ukázali, jak nasadit rozšíření s názvem: **lvaExtension**. Nyní můžete vyvíjet dva různé kontejnery. Každý kontejner má stejné rozhraní HTTP (což znamená stejný `/score` koncový bod). Tyto dva kontejnery nasaďte s různými názvy a ujistěte se, že obě naslouchají na **různých portech**. 

   Například jeden kontejner s názvem `lvaExtension1` naslouchá pro port `44000` , jiný kontejner s názvem `lvaExtension2` naslouchá pro port `44001` . 

   V topologii živé analýzy videí vytváříte instance dvou grafů s různými odvozenými adresami URL jako: 

   První instance: odvozená adresa URL serveru = `http://lvaExtension1:44001/score`    
   Druhá instance: odvozená adresa URL serveru = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>Protokol GRPC: 

V případě nástroje Live video Analytics 1,0, pokud používáte protokol gRPC, může být jediným způsobem, pokud server gRPC vystavil různé modely AI prostřednictvím různých portů. V [tomto příkladu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)je k dispozici jeden port, 44000, který zveřejňuje všechny modely Yolo. Teoreticky je možné přepsat Yolo gRPC Server a vystavit některé modely v 44000, dalších na 45000,... 

V případě nástroje Live video Analytics – modul 2,0 je do uzlu rozšíření gRPC přidána nová vlastnost. Tato vlastnost se nazývá **extensionConfiguration** , což je volitelný řetězec, který lze použít jako součást gRPC smlouvy. Pokud máte více modelů AI zabalených na jednom odvozeném serveru, nebudete muset vystavit uzel pro každý model AI. Místo toho poskytovatel rozšíření (vy) pro instanci grafu může definovat, jak vybrat různé modely AI pomocí vlastnosti **extensionConfiguration** a během provádění bude dynamická analýza videa Tento řetězec předat serveru Inferencing, který může použít k vyvolání požadovaného modelu AI. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Vytvářím gRPC Server kolem modelu AI a chcete být schopni podporovat používání více instancí kamery/grafu. Jak mám sestavit Server? 

 Nejprve se ujistěte, že váš server může zpracovávat více než jednu žádost současně. Nebo se ujistěte, že váš server funguje v paralelních vláknech. 

Například v jednom z [UKÁZEK GRPC (Live video Analytics](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)) je nastaven výchozí počet paralelních kanálů. Přečtěte si: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Ve výše uvedených instancích serveru gRPC může server současně otevřít pouze tři kanály na kameru (tedy na instanci topologie grafu). Neměli byste se pokoušet připojit k serveru více než tři instance. Pokud se pokusíte otevřít více než tři kanály, požadavky budou vyřízeny, dokud nepřijdete o stávající.  

Výše uvedené implementace gRPC serveru se používá v našich ukázkách Pythonu. Vývojáři mohou implementovat své vlastní servery nebo ve výše uvedené výchozí implementaci může zvýšit počet pracovních procesů nastavených na počet kamer používaných k získání informačního kanálu z videa. 

Chcete-li nastavit a používat více kamer, mohou vývojáři vytvořit instanci více instancí topologie grafu, kde každá instance odkazuje na stejný nebo jiný server odvození (například server uvedený v předchozím odstavci). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Chci být schopni získat více snímků z nadřazeného objektu, než se provede rozhodnutí Inferencing. Jak to můžu povolit? 

Aktuální [Výchozí ukázky](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fungují v režimu bez stavu. Tato ukázka neudržuje stav předchozích volání a dokonce i volající (což znamená, že vícenásobná instance topologie může volat stejný odvozený Server a server nebude moci rozlišit, kdo je volající a stav na jednoho volajícího). 

#### <a name="http-protocol"></a>Protokol HTTP

Při použití protokolu HTTP: 

Aby bylo možné zachovat stav, každý volající (instance topologie grafu) bude volat Server Inferencing s parametrem dotazu HTTP jedinečným pro volajícího. Například odvození adresy URL serveru pro  

instance 1. topologie = `http://lvaExtension:44000/score?id=1`<br/>
druhá instance topologie = `http://lvaExtension:44000/score?id=2`

… 

Na straně serveru bude trasa skóre informovat o tom, kdo volá. Pokud ID = 1, může se stát, že se stav pro daného volajícího (instance topologie grafu) udržuje samostatně. Přijaté video snímky pak můžete zachovat ve vyrovnávací paměti (například pole nebo slovníku s klíčem DateTime a hodnotou je rámec) a potom můžete definovat server, který se má zpracovat (odvodit) po přijetí snímků x. 

#### <a name="grpc-protocol"></a>Protokol GRPC 

Při použití protokolu gRPC: 

U rozšíření gRPC je každá relace pro jeden kanál kamery, takže není nutné zadávat ID. Takže teď s vlastností extensionConfiguration můžete snímky videa ukládat do vyrovnávací paměti a definovat server, který se má zpracovat (odvodit) po přijetí snímků x. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Má všechny ProcessMediaStreams na konkrétním kontejneru spustit stejný model AI? 

No.  

Spuštění/zastavení volání koncového uživatele v instanci grafu znamená relaci, nebo možná dojde k odpojení nebo opětovnému připojení kamery. Cílem je zachovat jednu relaci, pokud je kamera streamovaná videem. 

* Dva kamery odesílající video ke zpracování vytvoří dvě relace. 
* Jeden fotoaparát do grafu, který má dva uzly gRPCExtension, vytvoří dvě relace. 

Každá relace je plně duplexní spojení mezi živým analýzou videí a serverem gRPC a každá relace může mít jiný model nebo kanál. 

> [!NOTE]
> Pokud fotoaparát odpojíte nebo znovu připojíte (s fotoaparátem přejdete do režimu offline po dobu delší než limity tolerance), Live video Analytics otevře novou relaci se serverem gRPC. Neexistuje žádný požadavek na to, aby server sledoval stav napříč těmito relacemi. 

Live video Analytics také přidalo podporu více rozšíření gRPC pro jednu kameru v instanci grafu. Tato rozšíření gRPC budete moct používat k provádění zpracování AI sekvenčně nebo paralelně nebo i k kombinaci obou. 

> [!NOTE]
> Více souběžně spuštěných rozšíření bude mít vliv na hardwarové prostředky a Vy si ho budete muset zapamatovat při volbě hardwaru, který bude vyhovovat vašim výpočetním potřebám. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Jaký je maximální počet souběžných ProcessMediaStreams? 

Neexistuje žádné omezení, které se vztahuje na Live video Analytics.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Jak se rozhodnout, jestli má server Inferencing používat procesor nebo GPU nebo jakýkoli jiný hardwarový akcelerátor? 

Tato funkce je zcela závislá na tom, jak složitý model AI je vyvinutý a jak vývojář chce používat PROCESORové a hardwarové akcelerátory. Při vývoji modelu AI můžou vývojáři určit, jaké prostředky by měl model použít k provedení akcí. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Návody ukládat obrázky s ohraničujícími poli po zpracování? 

Dnes poskytujeme Souřadnice ohraničovacího rámečku jako zprávy o odvození. Vývojáři mohou vytvořit vlastní načtení proudu ImageList MJPEG, který může tyto zprávy použít, a překrýt ohraničovací rámečky přes snímky videa.  

## <a name="grpc-compatibility"></a>Kompatibilita gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Jak zjistím, jaká jsou povinná pole pro popisovač mediálního streamu? 

Všem hodnotám pole, které nejsou zadány, bude předána výchozí hodnota [zadaná parametrem gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live video Analytics používá **proto3** verzi jazyka vyrovnávací paměti protokolu. Všechna data vyrovnávací paměti, která používají kontrakty služby Live video Analytics, jsou k dispozici v souborech vyrovnávací paměti protokolů, které jsou [zde definovány](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Jak mám zajistit, aby používaly nejnovější soubory vyrovnávací paměti protokolů? 

Nejnovější soubory vyrovnávací paměti protokolu lze [získat zde](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Kdykoli aktualizujeme soubory smluv, zobrazí se v tomto umístění. I když neexistuje okamžitý plán pro aktualizaci souborů protokolu, vyhledejte název balíčku v horní části souborů, abyste věděli, že verze. Měl by se číst: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Jakékoli aktualizace těchto souborů zvýší hodnotu "v-value" na konci názvu. 

> [!NOTE]
> Vzhledem k tomu, že Live video Analytics používá proto3 verzi jazyka, pole jsou volitelná a to je zpětně a dopředně kompatibilní. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Jaké funkce gRPC jsou pro mě k dispozici pro použití se službou Live video Analytics? Které funkce jsou povinné a které jsou volitelné? 

K dispozici jsou jakékoli funkce gRPC na straně serveru, které se dají použít k splnění smlouvy protobuf. 

## <a name="monitoring-and-metrics"></a>Monitorování a metriky

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Můžu na okraji monitorovat mediální graf pomocí Event Grid?

Ano. Metriky Prometheus můžete využívat a publikovat je do Event gridu. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Můžu použít Azure Monitor k zobrazení stavu, metrik a výkonu mých grafů multimédií v cloudu nebo na hraničních zařízeních?

Ano. Tento scénář se podporuje. Přečtěte si další informace o [použití metrik Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Existují nějaké nástroje, které usnadňují monitorování Media Services IoT Edge modulu?

Visual Studio Code podporuje rozšíření "Azure IoT Tools", které umožňuje snadno monitorovat koncové body modulu LVAEdge. Tento nástroj můžete použít k rychlému zahájení monitorování IoT Hub integrovaného koncového bodu pro "události" a zobrazení zpráv odvození, které jsou směrovány ze zařízení Edge do cloudu. 

Kromě toho můžete toto rozšíření použít k úpravě vlákna modulu LVAEdge pro úpravu nastavení mediálního grafu.

Další informace najdete v článku [monitorování a protokolování](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturace a dostupnost

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Jak se fakturuje Live video Analytics na IoT Edge?

Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Další kroky

[Rychlý Start: Začínáme – Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
