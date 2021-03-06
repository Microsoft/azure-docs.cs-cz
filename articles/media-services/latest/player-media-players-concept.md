---
title: Přehled přehrávačů médií pro Media Services
description: Které přehrávače médií můžu používat s Azure Media Services? Zatím Azure Media Player, Shaka a Video.js.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282401"
---
# <a name="media-players-for-media-services"></a>Přehrávače médií pro Media Services

Pomocí Media Services můžete použít několik přehrávačů médií.

## <a name="azure-media-player"></a>Přehrávač médií Azure

Azure Media Player je přehrávač videa pro širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), a poskytuje tak obohacené možnosti adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologii Flash a Silverlight. Bez ohledu na použitou technologii přehrávání mají vývojáři sjednocené rozhraní JavaScript pro přístup k rozhraním API. Obsah poskytovaný Azure Media Services může být přehráván v široké škále zařízení a prohlížečů bez jakéhokoli dalšího úsilí.

Přečtěte si [dokumentaci Azure Media Player](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Shaka

Shaka Player je open source knihovna JavaScriptu pro adaptivní média. V prohlížeči hraje adaptivní formáty médií (například POMLČKy a HLS), aniž by používaly moduly plug-in nebo Flash. Místo toho přehrávač Shaka používá rozšíření zdroje médií Open Web Standards a Encrypted Media Extensions.

Podívejte [se, jak používat Shaka Player s Azure Media Services](player-shaka-player-how-to.md).

## <a name="videojs"></a>Video.js

Video.js je přehrávač videa, který v prohlížeči přehrává adaptivní formáty médií (například POMLČKy a HLS). Video.js používá rozšíření MediaSource (Open Web Standards) a šifrovaná rozšíření médií. Podporuje přehrávání videa na počítačích a mobilních zařízeních. Jeho oficiální dokumentaci najdete na adrese https://docs.videojs.com/ .

Podívejte se [, jak používat Video.js Player s Azure Media Services](player-how-to-video-js-player.md).


## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](../azure-media-player/azure-media-player-quickstart.md)