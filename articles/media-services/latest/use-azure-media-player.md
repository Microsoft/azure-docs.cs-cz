---
title: Přehrávání pomocí Azure Media Player – Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 4677fc9a56db3190073b3f310a51632055510bc2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028225"
---
# <a name="play-back-with-azure-media-player"></a>Přehrávání pomocí Azure Media Playeru

[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) je web přehrávač videa navržená tak, aby přehrávání mediálního obsahu z Microsoft Azure Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologie Flash a Silverlight. Bez ohledu na technologii přehrávání použity budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraní API. To umožňuje obsah poskytovaný Azure Media Services přehrávat napříč celou řadu zařízení a přehrávačů, aniž by vyžadovalo zvláštní úsilí.

Microsoft Azure Media Services umožňuje k přehrávání obsahu obsah tak, aby se poskytovat, než se HLS, DASH, Smooth Streaming formátů streamování. Azure Media Player bere v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz založené na možnostech platformy a prohlížeče. Media Services také umožňuje dynamické šifrování prostředků pomocí šifrování PlayReady nebo 128bitového šifrování AES envelope šifrování. Azure Media Player umožňuje k dešifrování PlayReady a AES-128 bitů šifrovaný obsah, když správně nakonfigurovaný. 

[Vyzkoušejte si bezplatnou verzi](http://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorování diagnostiky datový proud videa

Můžete použít [stránky ukázku Azure Media Player](http://aka.ms/amp) monitorování diagnostiky datový proud videa. 

! [Azure Media Player diagnostics] [amp_diagnostics]

## <a name="next-steps"></a>Další postup

- [Zaregistrujte a dostávat nejnovější informace od Azure Media Playeru](http://azuremediaplayerdemo.azurewebsites.net/amp_signup.html)
- [Dokumentace ke službě Azure Media Player](https://aka.ms/ampdocs)
- [Ukázky v Azure Media Player](https://aka.ms/ampsamples)
