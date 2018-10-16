---
title: 'Rychlý start: Rozpoznávat řeč v jazyce JavaScript v prohlížeči pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce JavaScript v prohlížeči pomocí Cognitive Services SDK řeči
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 2667d398141b53c87328eec7b608c4f613c15ea4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340269"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce JavaScript v prohlížeči pomocí Cognitive Services SDK řeči

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit web přepisy převod řeči na text pomocí vazby jazyka JavaScript sady SDK pro řeč Cognitive Services.
Aplikace je založena na Microsoft Cognitive Services sadou SDK pro řeč ([stahování verze 1.0.1](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* PC nebo Mac s pracovní mikrofon.
* V textovém editoru.
* Aktuální verze prohlížeče Chrome nebo Microsoft Edge.
* Volitelně můžete na webový server, který podporuje hostování skriptů PHP.

## <a name="create-a-new-website-folder"></a>Vytvořte novou složku webu

Vytvořte novou prázdnou složku. V případě, že chcete hostovat ukázku na webovém serveru, ujistěte se, že webový server má přístup do složky.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozbalit sadou SDK pro řeč pro jazyk JavaScript do této složky

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Stáhněte si sadou SDK pro řeč jako [zazipovaný balíček s](https://aka.ms/csspeech/jsbrowserpackage) a rozbalte ho do nově vytvořené složky. Výsledkem by měl být dva soubory během rozbalování, například `microsoft.cognitiveservices.speech.sdk.bundle.js` a `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Druhá možnost souboru je volitelný a slouží jako Nápověda ladění do kódu SDK, v případě potřeby.

## <a name="create-an-indexhtml-page"></a>Vytvoření stránky index.html

Vytvořte nový soubor ve složce s názvem `index.html` a tento soubor otevřít v textovém editoru.

1. Vytvořte následující kostra HTML:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Přidejte následující kód uživatelského rozhraní do souboru, pod první komentář:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Přidejte odkaz na sadou SDK pro řeč

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Nastavit obslužné rutiny pro rozpoznávání tlačítko, výsledek rozpoznání a předplatné souvisejících polí definovaných v kódu uživatelského rozhraní:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Vytvoření tokenu zdroje (volitelné)

V případě, že chcete hostovat webové stránky na webovém serveru, můžete volitelně zadat zdroj tokenu pro ukázkovou aplikaci.
Tímto způsobem váš klíč předplatného se nikdy neopustí serveru zároveň umožní uživatelům využívat možnosti zpracování řeči bez nutnosti zadávat žádné autorizační kód sami.

1. Vytvořte nový soubor s názvem `token.php`. V tomto příkladu předpokládáme, že váš webový server podporuje skriptovacím jazyce PHP. Zadejte následující kód:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Upravit `index.html` a přidejte následující kód do souboru:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Autorizačních tokenech pouze měly omezenou životnost.
> Tato zjednodušený příklad nezobrazuje autorizačních tokenech aktualizace automaticky. Jako uživatel můžete ručně znovu načíst stránku nebo stisknutím klávesy F5 aktualizujte.

## <a name="build-and-run-the-sample-locally"></a>Sestavení a spuštění ukázky místně

Ke spuštění aplikace, dvakrát klikněte na soubor index.html nebo otevřete oblíbený webový prohlížeč index.html. Zobrazíte jednoduchý grafické uživatelské rozhraní díky tomu umožňuje zadat váš klíč předplatného a [oblasti](regions.md) a aktivovat rozpoznávání používat mikrofon.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Sestavte a spusťte ukázku pomocí webového serveru

Spuštění vaší aplikace, otevřete oblíbený webový prohlížeč a přejděte na veřejnou adresu URL, která hostujete na složce, zadejte vaše [oblasti](regions.md)a aktivovat rozpoznávání používat mikrofon. Pokud nakonfigurované, získá token ze zdroje tokenu.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/js-browser`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat naše ukázky](speech-sdk.md#get-the-samples)
