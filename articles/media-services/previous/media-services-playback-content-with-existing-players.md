---
title: Pomocí stávajících přehrávačů pro přehrávání obsahu – Azure | Dokumentace Microsoftu
description: Toto téma obsahuje seznam stávajících přehrávačů, můžete použít k přehrávání obsahu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: ae057feb70d9ddb2c2dae10bae20eca1b613f76f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996928"
---
# <a name="playing-your-content-with-existing-players"></a>Přehrávání obsahu ve stávajících přehrávačích
Azure Media Services podporuje řadu oblíbených streamování formátů, jako je například technologie Smooth Streaming, HTTP Live Streaming a MPEG-Dash. Toto téma odkazuje na stávajících přehrávačů, které můžete použít k testování vašich streamů.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portál Media Services content player.
**Azure** portál nabízí přehrávač obsahu, který můžete použít k testování videa.

Klikněte na požadované video (ujistěte se, že byla [publikované](media-services-portal-publish.md)) a klikněte na tlačítko **Přehrát** tlačítko v dolní části portálu.

Musí být splněny určité předpoklady:

* Přehrávač **MEDIA SERVICES CONTENT PLAYER** přehrává z výchozího koncového bodu streamování. Pokud chcete přehrávat z jiného než výchozích koncového bodu streamování, použijte jiný přehrávač. Například [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Přehrávač médií Azure
Použití [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) k přehrávání obsahu (Vymazat nebo chráněný) v některém z následujících formátů:

* Technologie Smooth Streaming
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Přehrávače pro Flash
#### <a name="aes-encrypted-with-token"></a>Šifrováním AES s tokenem
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Hráči Silverlight

#### <a name="playready-with-token"></a>PlayReady s tokenem
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Hráči DASH
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Ostatní
K otestování HLS adresy URL můžete také použít:

* **Safari** na zařízení s iOS nebo
* **Přehrávač HLS 3ivx** na Windows.

## <a name="developing-video-players"></a>Vývoj přehrávače videa
Informace o tom, jak vyvíjet své vlastní hráče, naleznete v tématu [vývoj přehrávače videa](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
