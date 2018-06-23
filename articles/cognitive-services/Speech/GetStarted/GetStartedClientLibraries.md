---
title: Začínáme s rozhraním API pro rozpoznávání řeči Microsoft pomocí klientské knihovny | Microsoft Docs
description: Použití knihovny klienta služby Microsoft řeči v kognitivní služby Microsoft k vývoji aplikací, které mluvené zvuk převedeno na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342630"
---
# <a name="get-started-with-speech-service-client-libraries"></a>Začínáme s knihovny klienta služby řeči

Kromě vytvoření přímé požadavky HTTP pomocí rozhraní REST API, služba rozpoznávání řeči poskytuje vývojářům klientské knihovny řeči v různých jazycích. Rozpoznávání řeči klientské knihovny:

- V rozpoznávání řeči, jako je například mezilehlých výsledků v reálném čase, dlouho zvukový datový proud (až 10 minut) a průběžné rozpoznávání podporují pokročilejší možnosti.
- Poskytují jednoduché a idiomatickou rozhraní API v jazyce vaši volbu.
- Skryjte nízké úrovně komunikace – podrobnosti.

V současné době jsou k dispozici následujících klientských knihoven řeči:

- [Plochy knihovna jazyka C#](GetStartedCSharpDesktop.md)
- [Služba knihovna jazyka C#](GetStartedCSharpServiceLibrary.md)
- [Knihovna JavaScript](GetStartedJSWebsockets.md)
- [Knihovna Java pro Android](GetStartedJavaAndroid.md)
- [Knihovna jazyka Objective-C pro iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Další zdroje informací:

- [Ukázky](../samples.md) stránka obsahuje kompletní ukázky použití řeči klientské knihovny.
- Pokud potřebujete klientské knihovny, která není dosud podporován, můžete vytvořit si vlastní sadu SDK. Implementace [protokol WebSocket řeči](../API-Reference-REST/websocketprotocol.md) na platformě a použít jazyk podle vašeho výběru.

## <a name="license"></a>Licence

Všechny sady SDK kognitivní služeb a ukázky licenci s licencí MIT. Další informace najdete v tématu [licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
