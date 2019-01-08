---
title: Přehrávání pomocí Azure Media Playeru – Azure | Dokumentace Microsoftu
description: Toto téma obsahuje přehled služby Azure Media Playeru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: 4dd0f697d16d7a2fbeec8f712d98e6a7c439cb4e
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064674"
---
# <a name="azure-media-player-overview"></a>Přehled služby Azure Media Player

Azure Media Player je web přehrávač videa navržená tak, aby přehrávání mediálního obsahu z Microsoft Azure Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologie Flash a Silverlight. Bez ohledu na technologii přehrávání použity budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraní API. To umožňuje obsah poskytovaný Azure Media Services přehrávat napříč celou řadu zařízení a přehrávačů, aniž by vyžadovalo zvláštní úsilí.

Microsoft Azure Media Services umožňuje k přehrávání obsahu obsah tak, aby se poskytovat, než se HLS, DASH, Smooth Streaming formátů streamování. Azure Media Player bere v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz založené na možnostech platformy a prohlížeče. Media Services také umožňuje dynamické šifrování prostředků pomocí šifrování PlayReady nebo 128bitového šifrování AES envelope šifrování. Azure Media Player umožňuje k dešifrování PlayReady a AES-128 bitů šifrovaný obsah, když správně nakonfigurovaný. 

[Vyzkoušejte si bezplatnou verzi](http://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Na stránce ukázku Azure Media Playeru

### <a name="start-using"></a>Začněte používat

Můžete použít [stránky ukázku Azure Media Player](http://aka.ms/amp) přehrávání Azure Media Services ukázky nebo vlastní datový proud.  

Chcete-li přehrát nové video, vložte různé adresy URL a stiskněte klávesu **aktualizace**.

Chcete-li nakonfigurovat různé možnosti přehrávání (například Odborný, jazyka nebo šifrování), stiskněte **rozšířené možnosti**.

![Přehrávač médií Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorování diagnostiky datový proud videa

Můžete použít [stránky ukázku Azure Media Player](http://aka.ms/amp) monitorování diagnostiky datový proud videa. 

![Diagnostika Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Nastavení Azure Media Player v kódu HTML

Azure Media Player je snadné nastavení. Zabere jenom pár okamžiků získat základní přehrávání mediálního obsahu z vašeho účtu Media Services. Zobrazit [dokumentace ke službě Azure Media Player](https://aka.ms/ampdocs) podrobnosti o tom, jak vytvořit a nakonfigurovat Azure Media Playeru. 

## <a name="next-steps"></a>Další postup

- [Dokumentace ke službě Azure Media Player](https://aka.ms/ampdocs)
- [Ukázky v Azure Media Player](https://aka.ms/ampsamples)
