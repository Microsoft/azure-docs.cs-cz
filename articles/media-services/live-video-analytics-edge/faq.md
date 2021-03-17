---
title: Live video Analytics na IoT Edge Nejčastější dotazy – Azure
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se analýzy živých videí na IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 72a07a1a509aebcd7ba4048d0c84e913481c978e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702245"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Nejčastější dotazy k analýze videí na IoT Edge

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se analýzy živých videí na Azure IoT Edge.

## <a name="general"></a>Obecné

**Jaké systémové proměnné lze použít v definici topologie grafu?**

| Proměnná   |  Popis  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Představuje okamžitý čas UTC, obvykle vyjádřený jako datum a čas v následujícím formátu:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Představuje instanci data a času koordinovaný světový čas (UTC) ve formátu kompatibilním se soubory ISO8601 s milisekundami v následujícím formátu:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. GraphTopologyName   | Představuje topologii mediálního grafu a obsahuje podrobný plán grafu. | 
| System. GraphInstanceName |    Představuje instanci mediálního grafu, uchovává hodnoty parametrů a odkazuje na topologii. | 

## <a name="configuration-and-deployment"></a>Konfigurace a nasazení

**Můžu modul Media Edge nasadit do zařízení s Windows 10?**

Ano. Další informace najdete v tématu [kontejnery pro Linux ve Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Zachytit z kamery IP a nastavení protokolu RTSP

**Musím k odeslání streamu videa na zařízení použít speciální sadu SDK?**

Ne, Live video Analytics na IoT Edge podporuje záznamová média pomocí protokolu RTSP (Real-time streaming Protocol) pro streamování videa, která je podporovaná na většině fotoaparátů IP.

**Můžu nahrávat multimédia do živé analýzy videí v IoT Edge pomocí protokolu RTMP (Real-Time Messaging Protocol) nebo protokolu Smooth Streaming (například Media Services živé události)?**

Ne, Live video Analytics podporuje jenom RTSP pro záznam videa z fotoaparátů IP. Všechny kamery podporující streamování RTSP přes TCP/HTTP by měly fungovat. 

**Je možné resetovat nebo aktualizovat zdrojovou adresu URL RTSP v instanci grafu?**

Ano, pokud je instance grafu v *neaktivním* stavu.  

**Je simulátor RTSP dostupný k použití při testování a vývoji?**

Ano, modul pro podobu [simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) je dostupný pro použití v rychlých startech a kurzech pro podporu procesu učení. Tento modul je poskytován jako nejlepší úsilí a nemusí být vždy k dispozici. Doporučujeme *, abyste simulátor nepoužívali* po dobu delší než několik hodin. Před plánováním produkčního nasazení byste měli investovat do testování s vaším skutečným zdrojem RTSP.

**Podporujete zjišťování ONVIF IP kamer na hraničních zařízeních?**

Ne, na hraničních zařízeních nepodporujeme zjišťování ONVIF (Open Network Video Interface).

## <a name="streaming-and-playback"></a>Streamování a přehrávání

**Můžu přejít zpět prostředky zaznamenané do Azure Media Services z okraje pomocí technologie streamování, jako je HLS nebo POMLČKa?**

Ano. Můžete streamovat zaznamenané prostředky jako jakýkoliv jiný Asset v Azure Media Services. Pokud chcete streamovat obsah, musíte mít vytvořený koncový bod streamování a ve stavu spuštěno. Pomocí standardního procesu vytváření lokátoru streamování vám poskytnete přístup k rozhraní Apple HTTP Live Streaming (HLS) nebo dynamickému adaptivnímu streamování přes HTTP (POMLČKu, označované také jako MPEG-POMLČKa) pro streamování do libovolné platformy přehrávače podporující rozhraní. Další informace o vytváření a publikování manifestů HLS nebo SPOJOVNÍKů naleznete v tématu [dynamické balení](../latest/dynamic-packaging-overview.md).

**Můžu u archivovaného assetu použít standardní ochranu obsahu a funkce DRM Media Services?**

Ano. Veškerá standardní funkce pro ochranu obsahu a DRM (Správa digitálních práv) pro dynamické šifrování je k dispozici pro použití u prostředků, které jsou zaznamenávány z mediálního grafu.

**Jaké přehrávače můžu použít k zobrazení obsahu ze zaznamenaných assetů?**

Podporují se všechny standardní přehrávače podporující kompatibilní HLS verze 3 nebo verze 4. Kromě toho se podporuje i jakýkoli přehrávač, který podporuje přehrávání kompatibilní se standardem MPEG-SPOJOVNÍK.

Mezi Doporučené přehrávače pro testování patří:

* [Přehrávač médií Azure](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Přehrávač Shaka](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Microsoft Edge, Chrome nebo Safari – integrovaný přehrávač videí HTML5
* Komerční přehrávače, kteří podporují přehrávání HLS nebo POMLČKy

**Jaká jsou omezení pro streamování assetu mediálního grafu?**

Streamování živého nebo zaznamenaného assetu z Media graphu používá stejný vysoce škálovatelný koncový bod infrastruktury a streamování, který Media Services podporuje na vyžádání a živé streamování pro multimédia & zábavy, nad OTT a vysíláním zákazníků. To znamená, že můžete rychle a snadno povolit Azure Content Delivery Network, Verizon nebo Akamai, aby se váš obsah do cílové skupiny poskytoval jako malý počet prohlížečů nebo až milionů v závislosti na vašem scénáři.

Obsah můžete doručovat pomocí Apple HLS nebo MPEG-POMLČKy.

## <a name="design-your-ai-model"></a>Návrh vašeho modelu AI 

**V kontejneru Docker mám zabalené více modelů AI. Jak je mám používat se službou Live video Analytics?** 

Řešení se liší v závislosti na komunikačním protokolu, který používá server Inferencing ke komunikaci se službou Live video Analytics. Následující části popisují, jak každý protokol funguje.

*Použít protokol HTTP*:

* Jeden kontejner (jeden lvaExtension):  

   Na serveru Inferencing můžete použít jeden port, ale různé koncové body pro různé modely AI. Například pro příklad v Pythonu můžete použít různé `route` typy s pro model, jak je znázorněno zde: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   A potom ve svém nasazení živé analýzy videí nastavte při vytváření instancí grafy odvozenou adresu URL serveru pro každou instanci, jak je znázorněno zde: 

   1. instance: odvozená adresa URL serveru =`http://lvaExtension:44000/score/face_detection`<br/>
   druhá instance: odvozená adresa URL serveru =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Případně můžete vytvořit modely AI na různých portech a volat je při vytváření instancí grafů.  

* Více kontejnerů: 

   Každý kontejner je nasazen s jiným názvem. Dříve jsme v sadě dokumentace Live video Analytics ukázali, jak nasadit rozšíření s názvem *lvaExtension*. Nyní můžete vyvíjet dva různé kontejnery, z nichž každá má stejné rozhraní HTTP, což znamená, že mají stejný `/score` koncový bod. Tyto dva kontejnery nasaďte s různými názvy a zajistěte, aby obě naslouchaly na *různých portech*. 

   Například jeden kontejner s názvem `lvaExtension1` naslouchá tomuto portu `44000` a druhý kontejner s názvem `lvaExtension2` naslouchá pro port `44001` . 

   V topologii živé analýzy videí vytváříte instance dvou grafů s různými odvozenými adresami URL, jak je znázorněno zde: 

   První instance: odvozená adresa URL serveru = `http://lvaExtension1:44001/score`    
   Druhá instance: odvozená adresa URL serveru = `http://lvaExtension2:44001/score`
   
*Použijte protokol gRPC*: 

* Pokud používáte protokol gRPC (General-Way Remote Procedure Call) pro Live video 1,0 Analytics, je jediným způsobem, jak to udělat, je, že server gRPC zveřejňuje různé modely AI prostřednictvím různých portů. V [tomto příkladu kódu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)jeden port, 44000, zpřístupňuje všechny modely Yolo. Teoreticky by bylo možné přepsat server Yolo gRPC a vystavit některé modely na portu 44000 a dalších na portu 45000. 

* V případě nástroje Live video Analytics – modul 2,0 je do uzlu rozšíření gRPC přidána nová vlastnost. Tato vlastnost **extensionConfiguration** je nepovinný řetězec, který lze použít jako součást gRPC smlouvy. Pokud máte více modelů AI zabalených na jednom odvozeném serveru, nemusíte vystavit uzel pro každý model AI. Místo toho můžete pro instanci grafu definovat, jak se má jako poskytovatel rozšíření vybrat různé modely AI pomocí vlastnosti **extensionConfiguration** . Během provádění Live video Analytics předá tento řetězec serveru Inferencing, který ho může použít k vyvolání požadovaného modelu AI. 

**Vytvářím gRPC Server kolem modelu AI a chci být schopni podporovat jeho použití v několika fotoaparátech nebo instancích grafů. Jak mám sestavit Server?** 

 Nejdřív se ujistěte, že váš server může v jednom okamžiku zpracovat více než jednu žádost nebo pracovat v paralelních vláknech. 

Například výchozí počet paralelních kanálů byl nastaven v následující [ukázce gRPC (Live video Analytics](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)): 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

V předchozí instanciování serveru gRPC může server otevřít pouze tři kanály v čase na jeden fotoaparát nebo instanci topologie grafu. Nepokoušejte se připojit více než tři instance k serveru. Pokud se pokusíte otevřít více než tři kanály, žádosti čekají na vyřízení, dokud existující kanál nepřijde.  

Předchozí implementace gRPC serveru se používá v našich ukázkách Pythonu. Jako vývojář můžete implementovat svůj vlastní server nebo použít předchozí výchozí implementaci ke zvýšení počtu pracovních procesů, které nastavíte na počet kamer, které se mají použít pro kanály videa. 

Pokud chcete nastavit a používat víc fotoaparátů, můžete vytvořit instanci několika instancí topologie grafu, každou odkazující na stejný nebo jiný server odvození (například server uvedený v předchozím odstavci). 

**Chci být schopni získat více snímků z nadřazeného objektu, než se provede rozhodnutí Inferencing. Jak to můžu povolit?** 

Naše stávající [Výchozí ukázky](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fungují v *bezstavovém* režimu. Neudržují stav předchozích volání nebo dokonce zavolali. To znamená, že více instancí topologie může volat stejný odvozený Server, ale server nemůže rozlišovat, kdo volá nebo stav na jednoho volajícího. 

*Použít protokol HTTP*:

Chcete-li zachovat stav, každý volající nebo instance topologie grafu, zavolá server Inferencing pomocí parametru dotazu protokolu HTTP, který je jedinečný pro volajícího. Například adresa URL serveru odvození pro každou instanci je znázorněna zde:  

instance 1. topologie = `http://lvaExtension:44000/score?id=1`<br/>
druhá instance topologie = `http://lvaExtension:44000/score?id=2`

… 

Na straně serveru zná trasa skóre, která volá. Pokud ID = 1, může pro tuto instanci volajícího nebo topologie grafu uchovávat stav zvlášť. Přijaté video snímky pak můžete zachovat ve vyrovnávací paměti. Například použijte pole nebo slovník s klíčem DateTime a hodnota je rámec. Po přijetí *x* počtu rámců pak můžete definovat server, který se má zpracovat (odvodit). 

*Použijte protokol gRPC*: 

U rozšíření gRPC je každá relace pro jeden kanál kamery, takže není nutné zadávat ID. Nyní s vlastností extensionConfiguration můžete uložit snímky videa do vyrovnávací paměti a po přijetí *x* počtu rámců definovat server, který se má zpracovat (odvodit). 

**Má všechny ProcessMediaStreams na konkrétním kontejneru spustit stejný model AI?** 

No. Spuštění nebo zastavení volání koncového uživatele v instanci grafu představuje relaci, nebo pravděpodobně dojde k odpojení kamery nebo opětovnému připojení. Cílem je zachovat jednu relaci, pokud je kamera streamovaná videem. 

* Dva kamery odesílající video ke zpracování vytvoří dvě relace. 
* Jeden fotoaparát do grafu, který má dva uzly rozšíření gRPC, vytvoří dvě relace. 

Každá relace je plně duplexní spojení mezi živým analýzou videí a serverem gRPC a každá relace může mít jiný model nebo kanál. 

> [!NOTE]
> Pokud fotoaparát odpojíte nebo znovu připojíte, fotoaparát přejde do režimu offline po dobu mimo limity tolerance, dynamická analýza videí otevře novou relaci se serverem gRPC. Neexistuje žádný požadavek na to, aby server sledoval stav napříč těmito relacemi. 

Live video Analytics také přidává podporu více rozšíření gRPC pro jednu kameru v instanci grafu. Tato rozšíření gRPC můžete použít k paralelnímu zpracování AI, paralelně nebo jako kombinaci obou. 

> [!NOTE]
> Více souběžně spuštěných rozšíření bude mít vliv na hardwarové prostředky. Mějte na paměti, že si zvolíte hardware, který vyhovuje vašim výpočetním potřebám. 

**Jaký je maximální počet souběžných ProcessMediaStreams?** 

Live video Analytics neplatí pro toto číslo žádné omezení.  

**Jak se můžu rozhodnout, jestli má server Inferencing používat procesor nebo GPU nebo jakýkoli jiný hardwarový akcelerátor?** 

Vaše rozhodnutí závisí na složitosti vyvinutého modelu AI a na tom, jak chcete používat PROCESORové a hardwarové akcelerátory. Při vývoji modelu AI můžete určit, jaké prostředky má model použít a jaké akce by měla provést. 

**Návody ukládat obrázky s přidanými ohraničovacími poli po zpracování?** 

Dnes poskytujeme Souřadnice ohraničovacího rámečku jako zprávy o odvození. Můžete vytvořit vlastní načtení proudu ImageList MJPEG, který může používat tyto zprávy a překrýt ohraničovací rámečky na snímcích videa.  

## <a name="grpc-compatibility"></a>Kompatibilita gRPC 

**Jak zjistím, jaká jsou povinná pole pro popisovač mediálního streamu?** 

Každé pole, kterému nezadáte hodnotu, je předána [Výchozí hodnota zadaná parametrem gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live video Analytics používá verzi *proto3* pro ukládání do vyrovnávací paměti protokolu. Všechna data vyrovnávací paměti protokolu používaná smlouvami služby Live video Analytics jsou k dispozici v [souborech vyrovnávací paměti protokolu](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Jak se dá zajistit, aby používali nejnovější soubory vyrovnávací paměti protokolů?** 

Nejnovější soubory vyrovnávací paměti protokolu můžete získat na [webu soubory smlouvy](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Kdykoli aktualizujeme soubory smluv, budou v tomto umístění. Neexistuje okamžitý plán pro aktualizaci souborů protokolu, proto vyhledejte název balíčku v horní části souborů, abyste věděli, že verze. Měl by se číst: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Jakékoli aktualizace těchto souborů zvýší hodnotu "v-value" na konci názvu. 

> [!NOTE]
> Vzhledem k tomu, že Live video Analytics používá proto3 verzi jazyka, pole jsou volitelná a verze je zpětně kompatibilní. 

**Jaké funkce gRPC jsou pro mě k dispozici pro použití se službou Live video Analytics? Které funkce jsou povinné a které jsou volitelné?** 

Můžete použít jakékoli gRPC funkce na straně serveru za předpokladu, že je splněna smlouva vyrovnávací paměti protokolu (Protobuf). 

## <a name="monitoring-and-metrics"></a>Monitorování a metriky

**Můžu na okraji monitorovat mediální graf pomocí Azure Event Grid?**

Ano. Můžete využívat metriky Prometheus a publikovat je do služby Event Grid. 

**Můžu použít Azure Monitor k zobrazení stavu, metrik a výkonu mých grafů multimédií v cloudu nebo na hraničních zařízeních?**

Ano, podporujeme tento přístup. Další informace najdete v tématu [Přehled metrik Azure monitor](../../azure-monitor/essentials/data-platform-metrics.md).

**Existují nějaké nástroje, které usnadňují monitorování Media Services IoT Edge modulu?**

Visual Studio Code podporuje rozšíření Azure IoT Tools, ve kterém můžete snadno monitorovat koncové body modulu LVAEdge. Tento nástroj můžete použít k rychlému zahájení monitorování integrovaného koncového bodu služby IoT Hub pro "události" a zobrazení zpráv o odvození, které jsou směrovány z hraničního zařízení do cloudu. 

Kromě toho můžete toto rozšíření použít k úpravě vlákna modulu LVAEdge pro úpravu nastavení mediálního grafu.

Další informace najdete v článku [monitorování a protokolování](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturace a dostupnost

**Jak se fakturuje Live video Analytics na IoT Edge?**

Podrobnosti o fakturaci najdete v tématu [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Další kroky

[Rychlý Start: Začínáme se službou Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)