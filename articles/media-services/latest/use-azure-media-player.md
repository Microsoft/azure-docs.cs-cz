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
ms.date: 07/10/2018
ms.author: juliako
ms.openlocfilehash: e72085d3ac9815b3ec5fd89c678860bd75761f73
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798104"
---
# <a name="azure-media-player-overview"></a>Přehled služby Azure Media Player

Azure Media Player je web přehrávač videa navržená tak, aby přehrávání mediálního obsahu z Microsoft Azure Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologie Flash a Silverlight. Bez ohledu na technologii přehrávání použity budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraní API. To umožňuje obsah poskytovaný Azure Media Services přehrávat napříč celou řadu zařízení a přehrávačů, aniž by vyžadovalo zvláštní úsilí.

Microsoft Azure Media Services umožňuje k přehrávání obsahu obsah tak, aby se poskytovat, než se HLS, DASH, Smooth Streaming formátů streamování. Azure Media Player bere v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz založené na možnostech platformy a prohlížeče. Media Services také umožňuje dynamické šifrování prostředků pomocí šifrování PlayReady nebo 128bitového šifrování AES envelope šifrování. Azure Media Player umožňuje k dešifrování PlayReady a AES-128 bitů šifrovaný obsah, když správně nakonfigurovaný. 

[Vyzkoušejte si bezplatnou verzi](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Na stránce ukázku Azure Media Playeru

### <a name="start-using"></a>Začněte používat

Můžete použít [stránky ukázku Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) přehrávání Azure Media Services ukázky nebo vlastní datový proud.  

Chcete-li přehrát nové video, vložte různé adresy URL a stiskněte klávesu **aktualizace**.

Chcete-li nakonfigurovat různé možnosti přehrávání (například Odborný, jazyka nebo šifrování), stiskněte **rozšířené možnosti**.

![Přehrávač médií Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorování diagnostiky datový proud videa

Můžete použít [stránky ukázku Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) monitorování diagnostiky datový proud videa. 

![Diagnostika Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Nastavení Azure Media Player v kódu HTML

Azure Media Player je snadné nastavení. Zabere jenom pár okamžiků získat základní přehrávání mediálního obsahu z vašeho účtu Media Services. Zobrazit [dokumentace ke službě Azure Media Player](https://aka.ms/ampdocs) podrobnosti o tom, jak vytvořit a nakonfigurovat Azure Media Playeru. 

## <a name="next-steps"></a>Další postup

- [Dokumentace k Azure Media Playeru](https://aka.ms/ampdocs)
- [Ukázky v Azure Media Player](https://aka.ms/ampsamples)
