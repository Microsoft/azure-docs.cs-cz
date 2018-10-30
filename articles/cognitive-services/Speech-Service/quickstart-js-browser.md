---
title: 'Rychlý start: Rozpoznávání řeči v JavaScriptu v prohlížeči s využitím sady Speech SDK'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v JavaScriptu v prohlížeči s využitím sady Speech SDK.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: b01746c20dbef7726f129badac045c1fb440f602
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467459"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-service-sdk"></a>Rychlý start: Rozpoznávání řeči v JavaScriptu v prohlížeči s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit web pro přepis řeči na text s využitím vazby JavaScriptu na sadu Cognitive Services Speech SDK.
Tato aplikace je založená na sadě Microsoft Cognitive Services Speech SDK ([stáhnout verzi 1.0.1](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu Speech. Viz [Vyzkoušejte si službu Speech zdarma](get-started.md).
* PC nebo Mac s funkčním mikrofonem
* Textový editor
* Aktuální verze prohlížeče Chrome nebo Microsoft Edge
* Volitelně webový server s podporou hostování skriptů PHP

## <a name="create-a-new-website-folder"></a>Vytvoření složky nového webu

Vytvořte novou prázdnou složku. Pokud chcete ukázku hostovat na webovém serveru, ujistěte se, že má webový server k této složce přístup.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozbalení sady Speech SDK pro JavaScript do příslušné složky

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Stáhněte si sadu Speech SDK jako [balíček .zip](https://aka.ms/csspeech/jsbrowserpackage) a rozbalte ho do nově vytvořené složky. Měly by se rozbalit dva soubory, tj. `microsoft.cognitiveservices.speech.sdk.bundle.js` a `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Druhý soubor je volitelný a v případě potřeby slouží jako pomůcka při ladění kódu sady SDK.

## <a name="create-an-indexhtml-page"></a>Vytvoření stránky index.html

Vytvořte ve složce nový soubor `index.html` a otevřete ho v textovém editoru.

1. Vytvořte následující kostru HTML:

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

1. Pod první komentář v souboru přidejte následující kód uživatelského rozhraní:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Přidejte odkaz na sadu Speech SDK.

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Nastavte obslužné rutiny pro tlačítko rozpoznávání, výsledek rozpoznávání a pole související s předplatným definovaná v kódu uživatelského rozhraní:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Vytvoření zdroje tokenu (volitelné)

Pokud chcete webovou stránku hostovat na webovém serveru, můžete pro ukázkovou aplikaci volitelně zadat zdroj tokenu.
Díky tomu váš klíč předplatného nikdy neopustí váš server a uživatelé budou moct využívat řečové funkce, aniž by sami museli zadávat jakýkoli autorizační kód.

1. Vytvořte nový soubor s názvem `token.php`. V tomto příkladu předpokládáme, že váš webový server podporuje skriptovací jazyk PHP. Zadejte následující kód:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Upravte soubor `index.html` a přidejte do něj následující kód:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Autorizační tokeny mají pouze omezenou dobu života.
> Tento zjednodušený příklad neukazuje, jak autorizační tokeny automaticky aktualizovat. Jako uživatel můžete stránku ručně znovu načíst nebo aktualizovat stisknutím klávesy F5.

## <a name="build-and-run-the-sample-locally"></a>Místní sestavení a spuštění ukázky

Pokud chcete aplikaci spustit, dvakrát klikněte na soubor index.html nebo tento soubor otevřete v oblíbeném webovém prohlížeči. Zobrazí se jednoduché grafické uživatelské rozhraní, kde můžete zadat klíč předplatného a [oblast](regions.md) a aktivovat rozpoznávání s využitím mikrofonu.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Sestavení a spuštění ukázky pomocí webového serveru

Pokud chcete aplikaci spustit, otevřete oblíbený webový prohlížeč a přejděte na veřejnou adresu URL, na které složku hostujete. Zadejte vaši [oblast](regions.md) a aktivujte rozpoznávání s využitím mikrofonu. Pokud je tak nakonfigurovaná, aplikace získá token z vašeho zdroje tokenu.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/js-browser`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte naše ukázky](speech-sdk.md#get-the-samples)
