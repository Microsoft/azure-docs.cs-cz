---
title: Použít existující přehrávače k přehrávání obsahu – Azure | Microsoft Docs
description: Toto téma obsahuje seznam existující přehrávače, můžete použít k přehrávání obsahu.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23860343"
---
# <a name="playing-your-content-with-existing-players"></a>Přehrávání obsahu s existující hráči
Azure Media Services podporuje mnoho oblíbených streamování formátů, jako je například technologie Smooth Streaming, HTTP Live Streaming a MPEG-Dash. Toto téma vám ukazuje na existující přehrávačů, které můžete použít k testování vaše datové proudy.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portálu Media Services obsahu přehrávač.
**Azure** portál nabízí přehrávač obsahu, který můžete použít k testování videa.

Klikněte na požadované video (zajistěte, aby byl [publikovaná](media-services-portal-publish.md)) a klikněte na tlačítko **přehrání** tlačítko v dolní části portálu.

Musí být splněny určité předpoklady:

* Přehrávač **MEDIA SERVICES CONTENT PLAYER** přehrává z výchozího koncového bodu streamování. Pokud chcete přehrávat z jiného než výchozích koncového bodu streamování, použijte jiný přehrávač. Například [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Přehrávač médií Azure
Použití [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) k přehrávání obsahu (Vymazat nebo chráněného) v některém z následujících formátů:

* Technologie Smooth Streaming
* MPEG DASH
* HLS
* Progresivní MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>AES šifrované pomocí tokenu
[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Vybavené přehrávače prostředí Silverlight
#### <a name="monitoring"></a>Monitorování
[http://smf.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady pomocí tokenu
[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Přehrávače čárka
[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Ostatní
K testování HLS adresy URL můžete použít také:

* **Safari** na zařízení s iOS nebo
* **Přehrávač HLS 3ivx** v systému Windows.

## <a name="developing-video-players"></a>Vývoj přehrávačů videa
Informace o tom, jak vyvíjet vlastní přehrávače najdete v tématu [vývoj přehrávačů videa](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
