---
title: Poznámky k verzi v3 Azure Media Services | Microsoft Docs
description: Udržovat přehled s nejnovější, tento článek poskytuje nejnovější informace o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788113"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 poznámky k verzi (preview) 

Udržovat přehled s nejnovější, tento článek poskytuje informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce
* Plány pro změny

## <a name="may-07-2018"></a>07 může 2018

### <a name="net-sdk"></a>.NET SDK

Následující funkce jsou dostupné v rozhraní .net SDK:

1. **Transformuje** a **úlohy** k zakódování nebo analyzovat mediální obsah. Příklady najdete v tématu [Stream soubory](stream-files-tutorial-with-api.md) a [analyzovat](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** pro publikování a vysílání datového proudu obsahu do zařízení koncového uživatele
3. **StreamingPolicies** a **ContentKeyPolicies** konfigurace doručení klíče a ochrana obsahu (DRM) při doručování obsahu.
4. **LiveEvents** a **LiveOutputs** nakonfigurovat ingestování a archivaci obsahu živého streamování.
5. **Prostředky** k ukládání a publikovat obsah média ve službě Azure Storage. 
6. **Koncové body streamování** ke konfiguraci a škálování dynamické balení, šifrování a streamování pro media živě i na vyžádání obsahu.

### <a name="known-issues"></a>Známé problémy

Známý problém:

Při odesílání úlohy s adresou URL HTTPS (JobInputHttp) odkazující na zdrojový obsah, ujistěte se, že HTTP server podporuje, HEAD, žádost. Úloha, jinak budou odmítnuty.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled](media-services-overview.md)
