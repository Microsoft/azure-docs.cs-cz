---
title: Streamování vysoce dostupnosti azure media services
description: Zjistěte, jak převzetí služeb při selhání sekundární účet Služby Media Services, pokud dojde k výpadku nebo selhání místního datového centra.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899227"
---
# <a name="media-services-high-availability-streaming"></a>Streamování s vysokou dostupností mediálních služeb

Azure Media Services aktuálně neposkytuje okamžité převzetí služeb při selhání služby, pokud dojde k výpadku místního datového centra nebo selhání podkladové součásti nebo závislých služeb. Tento článek obsahuje pokyny, jak vytvářet streamování videa na vyžádání napříč oblastmi.

## <a name="prerequisites"></a>Požadavky

Přečtěte [si, jak vytvořit meziregionální kódovací systém](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak vytvořit streamování videa na vyžádání napříč oblastmi 

* Streamování napříč oblastmi videa zahrnuje duplikaci [datových zdrojů](assets-concept.md), [zásady klíče obsahu](content-key-policy-concept.md) (pokud jsou použity), [zásady streamování](streaming-policy-concept.md)a [lokátory streamování](streaming-locators-concept.md). 
* Budete muset vytvořit zásady v obou oblastech a udržovat je aktuální. 
* Při vytváření lokátorů streamování budete chtít použít stejnou hodnotu ID lokátoru, hodnotu ContentKey ID a hodnotu ContentKey ID a ContentKey.  
* Pokud kódujete obsah, doporučujeme zakódovat obsah v oblasti A a publikovat jej, pak kódovaný obsah zkopírujte do oblasti B a publikujte jej pomocí stejných hodnot jako z oblasti A.
* Traffic Manager můžete použít u názvů hostitelů pro původ a službu doručování klíčů (v konfiguraci služby Media Services to bude vypadat jako adresa URL vlastního serveru klíčů).

## <a name="next-steps"></a>Další kroky

Mrkni se:

* [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video](stream-files-dotnet-quickstart.md)
* [ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
