---
title: 'Rychlý Start: rozpoznávání řeči, JavaScript (prohlížeč) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v JavaScriptu v prohlížeči pomocí sady Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 77afdb491e4a6cee0a3d6861ea76f57502e56278
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "71802438"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v JavaScriptu v prohlížeči pomocí sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit web pro přepis řeči na text s využitím vazby JavaScriptu na sadu Cognitive Services Speech SDK.
Aplikace je založená na sadě [Speech SDK pro JavaScript](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Předpoklady

* Klíč předplatného pro službu Speech. Další informace najdete v článku [o bezplatné rozpoznávání hlasových služeb](get-started.md).
* PC nebo Mac s funkčním mikrofonem
* Textový editor
* Aktuální verze Chrome, Microsoft Edge nebo Safari.
* Volitelně webový server s podporou hostování skriptů PHP

## <a name="create-a-new-website-folder"></a>Vytvoření složky nového webu

Vytvořte novou prázdnou složku. Pokud chcete ukázku hostovat na webovém serveru, ujistěte se, že má webový server k této složce přístup.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozbalení sady Speech SDK pro JavaScript do příslušné složky

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Stáhněte si sadu Speech SDK jako [balíček .zip](https://aka.ms/csspeech/jsbrowserpackage) a rozbalte ho do nově vytvořené složky. Výsledkem je, že se dva soubory rozbalí, `microsoft.cognitiveservices.speech.sdk.bundle.js` a `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Druhý soubor je nepovinný a je vhodný pro ladění kódu SDK.

## <a name="create-an-indexhtml-page"></a>Vytvoření stránky index.html

Vytvořte ve složce nový soubor `index.html` a otevřete ho v textovém editoru.

1. Vytvořte následující kostru HTML:

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
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

1. Obslužné rutiny pro tlačítko pro rozpoznávání, výsledek rozpoznávání a pole související s předplatným, které jsou definovány kódem uživatelského rozhraní:

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

> [!NOTE]
> Tato metoda nefunguje v prohlížeči Safari.
> V prohlížeči Safari musí být Ukázková webová stránka hostovaná na webovém serveru. Safari nepovoluje webům načtených z místního souboru, aby používaly mikrofon.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Sestavení a spuštění ukázky pomocí webového serveru

Pokud chcete aplikaci spustit, otevřete oblíbený webový prohlížeč a přejděte na veřejnou adresu URL, na které složku hostujete. Zadejte vaši [oblast](regions.md) a aktivujte rozpoznávání s využitím mikrofonu. Pokud je tak nakonfigurovaná, aplikace získá token z vašeho zdroje tokenu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky JavaScriptu na GitHubu](https://aka.ms/csspeech/samples)
