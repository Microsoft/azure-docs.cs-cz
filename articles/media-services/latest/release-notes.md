---
title: Azure Media Services v3 poznámky k verzi | Dokumentace Microsoftu
description: Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje nejnovější informace o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219319"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (preview) zpráva k vydání verze 

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce
* Plány pro změny

## <a name="may-07-2018"></a>07. května 2018

### <a name="net-sdk"></a>.NET SDK

Následující funkce jsou k dispozici v rozhraní .net SDK:

1. **Transformuje** a **úlohy** k zakódování nebo analyzovat mediálního obsahu. Příklady najdete v tématu [Stream soubory](stream-files-tutorial-with-api.md) a [analyzovat](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** pro publikování a streamování obsahu do zařízení koncových uživatelů
3. **StreamingPolicies** a **ContentKeyPolicies** konfigurace doručení klíče a ochrana obsahu (DRM) při doručování obsahu.
4. **LiveEvents** a **LiveOutputs** konfigurace ingestování a archivaci obsah živého streamování.
5. **Prostředky** ukládat a publikovat mediálního obsahu ve službě Azure Storage. 
6. **Koncové body streamování** ke konfiguraci a škálování dynamické balení, šifrování a streamování živě i na vyžádání multimediálního obsahu.

### <a name="known-issues"></a>Známé problémy

* Po odeslání úlohy, můžete ingestovat zdrojového videa zdroje pomocí adresy URL HTTPS, adresy URL SAS nebo cesty k souborům umístěným v úložišti objektů Blob v Azure. AMS v3 v současné době nepodporuje blokového kódování prostřednictvím adresy URL HTTPS přenosu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled](media-services-overview.md)
