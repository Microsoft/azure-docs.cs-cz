---
title: Live video Analytics na IoT Edge Nejčastější dotazy – Azure
description: Toto téma obsahuje odpovědi na živé video analýzy na základě nejčastějších dotazů IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011511"
---
# <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

Toto téma obsahuje odpovědi na živé video analýzy na základě nejčastějších dotazů IoT Edge.

## <a name="general"></a>Obecné

Jaké jsou systémové proměnné, které se dají použít v definici topologie grafu?

|Proměnná   |Popis|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu.|
|System. GraphTopologyName   |Představuje topologii mediálního grafu, obsahuje podrobný plán grafu.|
|System. GraphInstanceName|  Představuje instanci mediálního grafu, uchovává hodnoty parametrů a odkazuje na topologii.|

## <a name="configuration-and-deployment"></a>Konfigurace a nasazení

Můžu modul Media Edge nasadit do zařízení s Windows 10?
    * Ano. Podívejte se na článek o [kontejnerech Linux ve Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Zachytit z kamery IP a nastavení protokolu RTSP

* Musím k odeslání streamu videa na zařízení použít speciální sadu SDK?
    * Ne. Live video Analytics na IoT Edge podporuje záznamová média pomocí protokolu RTSP pro streamování videa (který se podporuje u většiny fotoaparátů protokolu IP).
* Můžu nahrávat multimédia do živé analýzy videí v IoT Edge pomocí RTMP nebo hladkého (jako je Media Services živá událost)?
    * Ne. LVA podporují pouze RTSP pro zachycení videa z fotoaparátů IP.
    * Všechny kamery podporující streamování RTSP přes TCP/HTTP by měly fungovat. 
* Můžu u instance grafu resetovat nebo aktualizovat adresu URL zdroje RTSP?
    * Ano, pokud je instance grafu v neaktivním stavu.  
* Je k dispozici simulátor RTSP pro použití při testování a vývoji?
    * Ano. K dispozici je modul Edge [simulátoru RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pro použití v rychlém startu a kurzech pro podporu procesu učení. Při poskytování tohoto modulu se snažíme všem maximálně vyhovět, ale ne vždy musí být dostupný. Důrazně doporučujeme, abyste tuto dobu nepoužívali déle než několik hodin. Než začnete vytvářet plány nasazení v produkčním prostředí, měli byste investovat do testování s vaším skutečným zdrojem RTSP.
* Podporujete zjišťování ONVIF IP kamer na hraničních zařízeních?
    * Ne, zjišťování ONVIF na hraničních zařízeních se nepodporuje.

## <a name="streaming-and-playback"></a>Streamování a přehrávání

* Můžou se prostředky zaznamenané do AMS z Edge přehrát zpátky pomocí Media Services technologie streamování, jako je HLS nebo POMLČKa?
    * Ano. Nahrané prostředky mohou být streamované jako jakékoli jiné prostředky v Azure Media Services. Pokud chcete streamovat obsah, musíte mít vytvořený koncový bod streamování a ve stavu spuštěno. Pomocí standardního procesu vytváření lokátoru streamování získáte přístup k HLS nebo POMLČKám manifest pro streamování do libovolných rozhraní přehrávače, které podporuje. Podrobnosti o vytváření publikovaných manifestů HLS nebo SPOJOVNÍKů naleznete v tématu [dynamické balení](../latest/dynamic-packaging-overview.md).
* Můžu u archivovaného assetu použít standardní ochranu obsahu a funkce DRM Media Services?
    * Ano. Veškerá standardní funkce Ochrana obsahu a DRM v dynamickém šifrování je k dispozici pro použití na assetech zaznamenaných z mediálního grafu.
* Jaké přehrávače můžu použít k zobrazení obsahu ze zaznamenaných assetů?
   * Podporují se všechny standardní přehrávače podporující rozhraní Apple HTTP Live Streaming (HLS) verze 3 nebo verze 4. Kromě toho se podporuje i jakýkoli přehrávač, který podporuje přehrávání kompatibilní se standardem MPEG-SPOJOVNÍK.
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
* Jaká jsou omezení pro streamování assetu mediálního grafu?
    * Streamování živého nebo zaznamenaného assetu z mediálního grafu používá stejnou infrastrukturu s vysokým škálováním a streamování, kterou Media Services podporuje na vyžádání a živé streamování pro multimédia & zábavy, OTT a vysílání pro zákazníky. To znamená, že můžete rychle a snadno povolit Azure CDN, Verizon nebo Akamai pro doručování obsahu posluchačům malým počtem návštěvníků nebo až milionům v závislosti na vašem scénáři.

    Obsah lze doručovat pomocí Apple HTTP Live Streaming (HLS) nebo MPEG-POMLČKy.

## <a name="monitoring-and-metrics"></a>Monitorování a metriky

* Můžu na okraji monitorovat mediální graf pomocí Event Grid?
    * Ne. Aktuálně Event Grid není podporován.
* Můžu použít Azure Monitor k zobrazení stavu, metrik a výkonu mých grafů multimédií v cloudu nebo na hraničních zařízeních?
    * Ne.
* Existují nějaké nástroje, které usnadňují monitorování Media Services IoT Edge modulu?
    * Visual Studio Code podporuje rozšíření "Azure IoT Tools", které umožňuje snadno monitorovat koncové body modulu LVAEdge. Tento nástroj můžete použít k rychlému zahájení monitorování IoT Hub integrovaného koncového bodu pro "události" a zobrazení zpráv odvození, které jsou směrovány ze zařízení Edge do cloudu. 

    Kromě toho můžete toto rozšíření použít k úpravě vlákna modulu LVAEdge pro úpravu nastavení mediálního grafu.

Další informace najdete v článku [monitorování a protokolování](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturace a dostupnost

* Jak se LiveVideo Analytics na IoT Edge fakturovaná?
    * Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Další kroky

[Rychlý Start: Začínáme – Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
