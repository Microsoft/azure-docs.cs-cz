---
title: Přehrávání videa – Azure
description: Zástupný symbol
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87042962"
---
# <a name="video-playback"></a>Přehrávání videa 

## <a name="suggested-pre-reading"></a>Navrhované před čtením 

* [Přehled živé analýzy videí na IoT Edge](overview.md)
* [Živá analýza videí na IoT Edge terminologii](terminology.md)
* [Koncept Media graphu](media-graph-concept.md)

## <a name="overview"></a>Přehled  

Pomocí [multimediálních grafů](media-graph-concept.md) můžete nahrávat video do Azure Media Services [assetu](terminology.md#asset). V tomto dokumentu se dozvíte o krocích, které je třeba provést, aby bylo možné přehrát Asset pomocí stávajících funkcí streamování Azure Media Services.

## <a name="streaming-endpoint"></a>Koncový bod streamování 

Můžete použít Azure Media Services ke [streamování](terminology.md#streaming) prostředků do přehrávačů videa pomocí standardních protokolů pro streamování založeného na protokolu HTTP, jako je například HTTP Live Streaming (HLS) a MPEG-pomlčka. Tento převod médií z zaznamenaného obsahu na formáty streamování se zpracovává [koncovým bodem streamování](../latest/streaming-endpoint-concept.md), který je prostředkem, který musíte zřídit ve vašem účtu Azure Media Service.

## <a name="streaming-policy"></a>Zásada streamování 

Azure Media Services nabízí různé metody zabezpečení datových proudů, jak je popsáno v článku [Ochrana obsahu pomocí Media Services dynamického šifrování](../latest/content-protection-overview.md) . V nejvyšší úrovni jsou možnosti pro ochranu obsahu:

* **In-the-Clear streaming** – bez použití šifrování během streamování.
* **Použijte standard AES (Advanced Encryption Standard) (AES-128)** – a Implementujte metodu pro doručení klíčů pro dešifrování videa pouze pro ověřené diváky.
* **Používejte systémy digitálních Rights Management (DRM)** – k řízení použití, úpravy a doručování videí na zařízení, která tyto zásady vynutila.

Pro zajištění ochrany obsahu můžete v účtu služby Media Service definovat a vytvořit [zásady streamování](../latest/streaming-policy-concept.md) a použít je ke streamování všech assetů (za předpokladu, že všechny datové proudy mají stejné požadavky na zabezpečení). Můžete také použít kteroukoli z předdefinovaných zásad (například Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Lokátor streamování  

Po spuštění koncového bodu streamování v účtu mediální služby a definování zásad streamování můžete pokračovat v streamování zaznamenaného média z assetu prostřednictvím HLS nebo SPOJOVNÍKových protokolů. Webové přehrávače a mobilní aplikace potřebují adresu URL ukazující na tento HLS nebo PŘERUŠOVANý Stream. Tuto adresu URL můžete vytvořit pomocí [lokátoru streamování](../latest/streaming-locators-concept.md). Jak je popsáno v tomto článku a zobrazuje se ukázka [Vytvoření lokátoru streamování a adres URL sestavení](../latest/create-streaming-locator-build-url.md) , se adresa URL streamování skládá z koncového bodu streamování, zásad streamování a lokátoru streamování.

## <a name="content-recorded-using-file-sink"></a>Obsah zaznamenaný pomocí jímky souborů  

Jak je popsáno v části [jímka souborů v Media graphu](media-graph-concept.md#file-sink), můžete pomocí mediálních grafů nahrávat videa do místního systému souborů hraničního zařízení pomocí jímky souborů v mediálním grafu. Jímka souborů generuje soubory [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) a k přehrání takového obsahu můžete použít prvek [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) HTML5. 

## <a name="next-steps"></a>Další kroky

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
