---
title: Azure Media Services streamování s vysokou dostupností
description: Přečtěte si, jak převzít služby při selhání na sekundární Media Services účet, pokud dojde k výpadku nebo selhání regionálního datacentra.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899227"
---
# <a name="media-services-high-availability-streaming"></a>Media Services streamování s vysokou dostupností

Azure Media Services v současné době neposkytuje okamžitý převzetí služeb při selhání služby, pokud dojde k výpadku místního datového centra nebo selhání základní komponenty nebo závislých služeb. Tento článek obsahuje pokyny, jak vytvořit streamování mezi oblastmi videa na vyžádání.

## <a name="prerequisites"></a>Požadavky

Přečtěte si [, jak vytvořit systém kódování v různých](media-services-high-availability-encoding.md) oblastech.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak vytvořit streamování mezi oblastmi videa na vyžádání 

* Streamování mezi jednotlivými oblastmi videa na vyžádání zahrnuje duplikaci [prostředků](assets-concept.md), [zásady klíčů obsahu](content-key-policy-concept.md) (Pokud se používají), [zásady streamování](streaming-policy-concept.md)a [Lokátory streamování](streaming-locators-concept.md). 
* V obou oblastech budete muset vytvořit zásady a udržovat je aktuální. 
* Když vytváříte Lokátory streamování, budete chtít použít stejnou hodnotu ID lokátoru, hodnotu ID ContentKey a hodnotu ContentKey.  
* Pokud obsah kódujete, doporučuje se kódovat obsah v oblasti A a publikovat ho, pak zkopírovat kódovaný obsah do oblasti B a publikovat ho pomocí stejných hodnot jako z oblasti A.
* V názvech hostitelů pro zdroj a službu doručení klíčů můžete použít Traffic Manager. (v Media Services konfigurace bude to vypadat jako adresa URL serveru s vlastním klíčem).

## <a name="next-steps"></a>Další kroky

Mrkni se:

* [Kurz: kódování vzdáleného souboru na základě adresy URL a streamu videa](stream-files-dotnet-quickstart.md)
* [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
