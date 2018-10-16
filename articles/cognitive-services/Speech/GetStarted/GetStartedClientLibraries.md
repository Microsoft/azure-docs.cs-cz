---
title: Začínáme s využitím klientské knihovny s rozhraní API pro rozpoznávání řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Použití klientské knihovny pro zpracování řeči Bingu ve službě Microsoft Cognitive Services k vývoji aplikací, které převést mluvené slovo na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 2682236ae9f9256b91d1259248e72139c907c8e5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345012"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Začínáme s klientskými knihovnami Speech Service Bingu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Kromě toho, s přímým přístupem požadavky HTTP přes rozhraní REST API, Speech Service Bingu poskytuje vývojářům klientské knihovny pro rozpoznávání řeči v různých jazycích. Klientské knihovny pro zpracování řeči:

- Podporuje rozšířené možnosti v rozpoznávání řeči, jako je například mezilehlých výsledků v reálném čase, long zvukový datový proud (až 10 minut) a průběžné rozpoznávání.
- Poskytují jednoduché a idiomatickou rozhraní API v jazyce dle požadavků.
- Skryjete podrobnosti nízké úrovně komunikace.

V současné době jsou k dispozici následujících klientských knihoven pro zpracování řeči Bingu:

- [Klasické pracovní plochy knihovny jazyka C#](GetStartedCSharpDesktop.md)
- [Knihovna služby jazyka C#](GetStartedCSharpServiceLibrary.md)
- [Knihovna JavaScript](GetStartedJSWebsockets.md)
- [Knihovna Java pro Android](GetStartedJavaAndroid.md)
- [Knihovna jazyka Objective-C pro iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Další zdroje informací:

- [Ukázky](../samples.md) stránka poskytuje kompletní ukázky použití klientské knihovny pro rozpoznávání řeči.
- Pokud potřebujete klientskou knihovnu, která ještě není podporovaný, můžete vytvořit vlastní sadu SDK. Implementace [protokol WebSocket řeči](../API-Reference-REST/websocketprotocol.md) na platformy a použití jazyka podle vašeho výběru.

## <a name="license"></a>Licence

Všechny Cognitive Services SDK a ukázky jsou licencovány s licencí MIT. Další informace najdete v tématu [licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

