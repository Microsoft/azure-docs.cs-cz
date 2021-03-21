---
title: Přehled Azure Media Player
description: Přečtěte si další informace o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321727"
---
# <a name="azure-media-player-overview"></a>Přehled Azure Media Playeru #

Azure Media Player je webový přehrávač, který přehrává mediální obsah z [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) v nejrůznějších prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), a poskytuje tak obohacené možnosti adaptivního streamování.  Pokud tyto standardy nejsou k dispozici v zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologii Flash a Silverlight. Bez ohledu na použitou technologii přehrávání mají vývojáři sjednocené rozhraní JavaScript pro přístup k rozhraním API, což umožňuje přehrávání obsahu poskytovaného Azure Media Services v široké škále zařízení a prohlížečů bez dalšího úsilí.

Microsoft Azure Media Services umožňuje, aby se obsah zpracoval s POMLČKou, Smooth Streaming a HLS formáty streamování pro přehrávání obsahu. Azure Media Player brát v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz na základě schopností platforem a prohlížečů. Microsoft Azure Media Services taky poskytuje dynamické šifrování prostředků s využitím společného šifrování (PlayReady nebo Widevine) nebo šifrování pomocí šifrování AES-128. Azure Media Player umožňuje dešifrování šifrovaného obsahu PlayReady a AES-128, když je správně nakonfigurovaný.  Další informace o tom, jak nakonfigurovat přehrávač, najdete v části [chráněný obsah](azure-media-player-protected-content.md) .

Pokud chcete požádat o nové funkce, poskytněte nápady nebo zpětnou vazbu, odešlete je do [služby UserVoice pro Azure Media Player](https://aka.ms/ampuservoice). Pokud máte nějaké a specifické problémy, otázky nebo zjistíte případné chyby, pusťte nás na ampinfo@microsoft.com .

> [!NOTE]
> Upozorňujeme, že Azure Media Player podporuje jenom mediální datové proudy z Azure Media Services.

## <a name="license"></a>Licence ##

Azure Media Player je licencovaná a podléhá podmínkám, které jsou uvedené v licenčních podmínkami pro software společnosti Microsoft pro Azure Media Player. Úplné výrazy najdete v [souboru s licencí](/legal/azure-media-player/azure-media-player-license) . Další informace najdete v [prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
