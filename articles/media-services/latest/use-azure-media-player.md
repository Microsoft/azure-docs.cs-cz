---
title: Přehrávání pomocí programu Azure Media Player – Azure | Dokumenty společnosti Microsoft
description: Azure Media Player je webový přehrávač videa vytvořený pro přehrávání mediálního obsahu ze služby Microsoft Azure Media Services v široké škále prohlížečů a zařízení.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673578"
---
# <a name="azure-media-player-overview"></a>Přehled Azure Media Playeru

Azure Media Player je webový přehrávač videa vytvořený pro přehrávání mediálního obsahu ze služby Microsoft Azure Media Services v široké škále prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroje médií (MSE) a rozšíření šifrovaných médií (EME) k poskytování obohaceného adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá Flash a Silverlight jako záložní technologie. Bez ohledu na použitou technologii přehrávání budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraním API. To umožňuje přehrávat obsah obsluhovaný mediálními službami Azure v celé řadě zařízení a prohlížečů bez dalšího úsilí.

Microsoft Azure Media Services umožňuje obsah, který se bude zobrazovat pomocí formátů HLS, DASH a Smooth Streaming streaming pro přehrávání obsahu. Azure Media Player bere v úvahu tyto různé formáty a automaticky přehraje nejlepší odkaz na základě možností platformy nebo prohlížeče. Mediální služba také umožňuje dynamické šifrování datových zdrojů pomocí šifrování PlayReady nebo šifrování obálek AES-128 bit. Azure Media Player umožňuje dešifrování PlayReady a AES-128 bit šifrovaný obsah, pokud je správně nakonfigurován. 

> [!NOTE]
> Pro šifrovaný obsah Widevine je vyžadováno přehrávání https.

## <a name="use-azure-media-player-demo-page"></a>Použití ukázkové stránky přehrávače Azure Media Player

### <a name="start-using"></a>Začněte používat

Demo [stránku Azure Media Player](https://aka.ms/azuremediaplayer) můžete použít k přehrávání ukázek Azure Media Services nebo vlastního datového proudu.  

Chcete-li přehrát nové video, vložte jinou adresu URL a stiskněte **tlačítko Aktualizovat**.

Chcete-li nakonfigurovat různé možnosti přehrávání (například technologie, jazyk nebo šifrování), stiskněte **klávesu Upřesnit možnosti**.

![Přehrávač médií Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Sledování diagnostiky datového proudu videa

Demo [stránku Přehrávače Azure Media Player](https://aka.ms/azuremediaplayer) můžete použít ke sledování diagnostiky datového proudu videa. 

![Diagnostika přehrávače médií Azure](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Nastavení azure media playeru ve vašem HTML

Azure Media Player se snadno nastavuje. Získání základního přehrávání mediálního obsahu z účtu Media Services trvá jen několik okamžiků. Podrobnosti o nastavení a konfiguraci programu Azure Media Player najdete v [dokumentaci](https://aka.ms/ampdocs) k programu Azure Media Player. 

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky

- [Dokumentace k Azure Media Playeru](https://aka.ms/ampdocs)
- [Ukázky přehrávače Azure Media Player](https://aka.ms/ampsamples)
