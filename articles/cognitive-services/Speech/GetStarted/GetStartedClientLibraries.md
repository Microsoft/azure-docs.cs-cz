---
title: Začínáme s rozhraním API pro rozpoznávání Zpracování řeči Bingu pomocí klientských knihoven | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Pomocí Zpracování řeči Bingu klientských knihoven v Microsoft Cognitive Services můžete vyvíjet aplikace, které převádějí mluvený zvuk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966850"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Začínáme s klientskými knihovnami služby Zpracování řeči Bingu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Kromě přímého zpracování požadavků HTTP prostřednictvím REST API služba Zpracování řeči Bingu poskytuje vývojářům hlasové knihovny řeči v různých jazycích. Knihovny klienta pro řeč:

- Podporují pokročilejší funkce rozpoznávání řeči, jako jsou mezilehlé výsledky v reálném čase, dlouhý datový proud (až 10 minut) a průběžné rozpoznávání.
- Poskytněte jednoduché a idiomatickou rozhraní API v jazyce vaší předvolby.
- Skrýt podrobnosti o komunikaci na nízké úrovni.

V současné době jsou k dispozici následující klientské knihovny Zpracování řeči Bingu:

- [C#Desktopová knihovna](GetStartedCSharpDesktop.md)
- [C#Knihovna služeb](GetStartedCSharpServiceLibrary.md)
- [Knihovna JavaScript](GetStartedJSWebsockets.md)
- [Knihovna Java pro Android](GetStartedJavaAndroid.md)
- [Cíl – knihovna C pro iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Další zdroje

- Na stránce [ukázek](../samples.md) najdete kompletní ukázky pro používání knihoven klienta Speech.
- Pokud potřebujete klientskou knihovnu, která zatím není podporovaná, můžete vytvořit vlastní sadu SDK. Implementujte na platformě [protokol WebSocket pro rozpoznávání řeči](../API-Reference-REST/websocketprotocol.md) a použijte jazyk podle vašeho výběru.

## <a name="license"></a>Licence

Všechny sady SDK a ukázky pro Cognitive Services jsou licencované pomocí licence MIT. Další informace najdete v tématu [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
