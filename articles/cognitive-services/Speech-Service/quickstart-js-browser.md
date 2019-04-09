---
title: 'Rychlý start: Rozpoznávání řeči, jazyka JavaScript (prohlížeči) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce JavaScript v prohlížeči pomocí sadou SDK pro řeč
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: a418966ce72876d58d39b939ecb7dd5a05e41b9b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59008972"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v jazyce JavaScript v prohlížeči pomocí sadou SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit web pro přepis řeči na text s využitím vazby JavaScriptu na sadu Cognitive Services Speech SDK.
Aplikace je založena na sadou SDK pro řeč pro jazyk JavaScript ([stahování verze 1.4.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu Speech. Zobrazit [hlasové služby si můžete vyzkoušet zdarma](get-started.md).
* PC nebo Mac s funkčním mikrofonem
* Textový editor
* Aktuální verzi Chrome, Microsoft Edge nebo Safari.
* Volitelně webový server s podporou hostování skriptů PHP

## <a name="create-a-new-website-folder"></a>Vytvoření složky nového webu

Vytvořte novou prázdnou složku. Pokud chcete ukázku hostovat na webovém serveru, ujistěte se, že má webový server k této složce přístup.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozbalení sady Speech SDK pro JavaScript do příslušné složky

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Stáhněte si sadu Speech SDK jako [balíček .zip](https://aka.ms/csspeech/jsbrowserpackage) a rozbalte ho do nově vytvořené složky. Výsledkem je dva soubory během rozbalování `microsoft.cognitiveservices.speech.sdk.bundle.js` a `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Druhá možnost soubor je volitelný a je užitečné pro ladění do kódu SDK.

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

1. Nastavit obslužné rutiny pro rozpoznávání tlačítko, výsledek rozpoznání a týkajících se předplatného polí definovaných v kódu uživatelského rozhraní:

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
> V Safari musí být hostované na webovém serveru; ukázková webová stránka Safari neumožňuje weby, které jsou načteny z místního souboru používat mikrofon.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Sestavení a spuštění ukázky pomocí webového serveru

Pokud chcete aplikaci spustit, otevřete oblíbený webový prohlížeč a přejděte na veřejnou adresu URL, na které složku hostujete. Zadejte vaši [oblast](regions.md) a aktivujte rozpoznávání s využitím mikrofonu. Pokud je tak nakonfigurovaná, aplikace získá token z vašeho zdroje tokenu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky jazyka JavaScript na Githubu](https://aka.ms/csspeech/samples)
