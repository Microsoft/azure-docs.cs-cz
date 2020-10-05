---
title: Co je rozšíření Media graphu – Azure
description: Live video Analytics na IoT Edge umožňuje rozšířit možnosti zpracování mediálního grafu prostřednictvím uzlu rozšíření grafu.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89569321"
---
# <a name="media-graph-extension"></a>Rozšíření grafu médií

Live video Analytics na IoT Edge umožňuje rozšířit možnosti zpracování mediálního grafu prostřednictvím uzlu rozšíření grafu. Váš modul plug-in pro analýzu analýz může využívat tradiční techniky zpracování obrazu nebo modely AI pro počítačové vidění. Rozšíření grafu jsou povolena zahrnutím uzlu rozšíření procesoru v mediálním grafu. Uzel procesoru rozšíření přenáší video snímky do nakonfigurovaného koncového bodu a funguje jako rozhraní pro vaše rozšíření. Připojení se dá vytvořit v místním nebo vzdáleném koncovém bodě a v případě potřeby ho můžete zabezpečit pomocí ověřování a šifrování TLS. Kromě toho uzel procesoru rozšíření grafu umožňuje volitelné škálování a kódování snímků videa předtím, než se odešlou do vašeho vlastního rozšíření.

Live video Analytics podporuje dva druhy procesorů rozšíření pro Media Graph:

* [Procesor rozšíření HTTP](media-graph-concept.md#http-extension-processor)
* [procesor rozšíření gRPC](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>Procesor rozšíření HTTP

Procesor rozšíření HTTP umožňuje scénáře rozšiřitelnosti pomocí protokolu HTTP, kde výkon nebo optimální využití prostředků nejsou primárním problémem. Vlastní AI můžete vystavit do mediálního grafu prostřednictvím koncového bodu HTTP REST. 

Uzel procesoru rozšíření HTTP použijte v těchto případech:

* Chcete lepší interoperabilitu s existujícími systémy Inferencing HTTP.
* Přenos dat s nízkým výkonem je přijatelný.
* K živé analýze videí chcete použít jednoduché rozhraní Request-Response.

## <a name="grpc-extension-processor"></a>procesor rozšíření gRPC

procesor rozšíření gRPC umožňuje scénáře rozšiřitelnosti pomocí gRPC, vysoce výkonného strukturovaného protokolu. Je ideální pro scénáře, kde je výkon nebo optimální využití prostředků prioritou. Procesor rozšíření gRPC vám umožňuje získat kompletní výhody definic strukturovaných dat. gRPC nabízí vysoký výkon přenosu obsahu pomocí:

* [sdílená paměť v krabicích](https://en.wikipedia.org/wiki/Shared_memory) nebo 
* přímý vkládání obsahu do textu zpráv gRPC 

Procesor rozšíření gRPC se dá použít k posílání vlastností média spolu s výměnou odvozených zpráv.
Proto použijte uzel procesoru rozšíření gRPC při:

* Chcete použít strukturovaný kontrakt (například strukturované zprávy pro žádosti a odpovědi)
* Pro komunikaci je vhodné použít vyrovnávací paměti protokolu ([protobuf](https://developers.google.com/protocol-buffers)) jako svůj základní formát pro výměnu zpráv.
* Chcete komunikovat se serverem gRPC v relaci s jedním datovým proudem namísto tradičního modelu požadavek-odpověď, který potřebuje vlastní obslužnou rutinu požadavků pro analýzu příchozích požadavků a volání správné implementační funkce. 
* Nízká latence a komunikace s vysokou propustností mezi živým analýzou videí a vaším modulem.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Použití modelu Inferencing s živým analýzou videí

Rozšíření mediálních grafů umožňují spouštět modely odvození podle vašeho výběru na jakémkoli dostupném modulu runtime pro odvození, jako je ONNX, TensorFlow, PyTorch nebo jiné ve vašem vlastním kontejneru Docker. Vlastní rozšíření Inferencing by se mělo nasadit společně s modulem Edge Analytics pro video, aby se co nejlépe vyvolalo, a pak se vyvolá prostřednictvím procesoru rozšíření HTTP nebo procesoru rozšíření gRPC, který je součástí topologie grafu. Kromě toho je možné omezit frekvenci volání do vlastního rozšíření tím, že v případě potřeby přidáte [procesor snímače pohybu](media-graph-concept.md#motion-detection-processor) a přesměruje [procesor filtru snímkového přenosu](media-graph-concept.md#frame-rate-filter-processor) do procesoru rozšíření médií.

Následující diagram znázorňuje tok dat vysoké úrovně:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="Odvozená služba AI":::

## <a name="samples"></a>ukázky

Zarezervujte některé ukázky [Jupyter poznámkových bloků](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) pro Live video Analytics. Tyto poznámkové bloky vám poskytnou podrobné pokyny pro **rozšíření grafu multimédií** na těchto místech:

* Postup vytvoření image kontejneru Docker pro službu rozšíření
* Jak nasadit službu rozšíření jako kontejner spolu s kontejnerem živé analýzy videí
* Jak používat Live video Analytics Media Graph s klientem rozšíření a nasměrovat ho na koncový bod rozšíření (HTTP/gRPC)