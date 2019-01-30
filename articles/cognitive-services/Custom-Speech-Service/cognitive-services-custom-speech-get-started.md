---
title: Začínáme s Custom Speech Service v Azure | Dokumentace Microsoftu
description: K odběru služby Custom Speech a aktivity služby propojit s předplatným Azure pro trénování modelu a nasazení.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 7392459f0b80558aac22bd585c0d30bf4105d76f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224441"
---
# <a name="get-started-with-custom-speech-service"></a>Začínáme s Custom Speech Service

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Prozkoumejte hlavní funkce služby Custom Speech Service a zjistěte, jak vytvořit, nasadit a používat akustických a jazykových modelů pro potřeby vaší aplikace. Více si rozsáhlou dokumentaci k a podrobné pokyny najdete po přihlášení na portálu pro vlastní hlasové služby.

## <a name="samples"></a>Ukázky  
Nachází dobrý příklad, který nabízíme vám pomůže se zorientovat, které můžete vyhledat [tady](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Požadavky  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Přihlášení služby Custom Speech Service a získejte klíč předplatného
Před zobrazením pomocí výše uvedeného příkladu musíte předplatit Custom Speech Service a získat předplatné klíče najdete v tématu [předplatná](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) nebo postupujte podle vysvětlení [tady](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). V tomto kurzu můžete použít primární i sekundární klíč. Nezapomeňte dodržovat osvědčené postupy pro zajištění utajení a zabezpečení vašeho klíče rozhraní API.

### <a name="get-the-client-library-and-example"></a>Získání klientské knihovny a příkladu
Si můžete stáhnout klientské knihovny a příklad prostřednictvím [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Stažený soubor ZIP je potřeba extrahovat do složky podle vašeho výběru, velká část uživatelů volí složku sady Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Vytvoření vlastního akustického modelu
K přizpůsobení akustického modelu pro konkrétní doménu je nutná kolekce hlasových dat. Tato kolekce zahrnuje sadu zvukových souborů s hlasovými daty a textový soubor s přepisy všech zvukových souborů. Zvuková data by měla vystihovat scénáře, ve kterém byste chtěli použít nástroj pro rozpoznávání

Příklad: Pokud byste chtěli lépe rozpoznávat řeč v hlučném provozním prostředí, měly by zvukové soubory zahrnovat řeč lidí v hlučném provozu.
Pokud vás zajímají optimalizace výkonu pro jeden mluvčího, například chcete přepisy všechny konverzace Fireside díky technologii FDR společnosti a potom zvukové soubory by měla obsahovat mnoho příkladů pouze tohoto mluvčího.

Najdete podrobný popis postupu vytvoření vlastního akustického modelu [tady](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Vytvoření vlastního jazykového modelu
Postup pro vytvoření vlastního jazykového modelu je podobné jako vytvoření akustický model s tím rozdílem, neexistuje žádná zvuková data pouze text. Mnoho příkladů dotazů by měla obsahovat text nebo projevy očekáváte, že uživatelům Řekněme, že nebo přihlášení uživatelé říká (nebo zadáním) ve vaší aplikaci.

Najdete podrobný popis postupu vytvoření vlastního jazykového modelu [tady](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Vytvoření vlastního koncového bodu převodu řeči na text
Po vytvoření vlastních akustických modelů a/nebo jazykové modely, je možné nasadit vlastní koncový bod speech to text. Chcete-li vytvořit nové vlastní koncový bod, v nabídce "CRIS" horní části stránky klikněte na "Nasazení". Tím přejdete do tabulky nazvané "Nasazení" aktuální vlastní koncové body. Pokud jste ještě nevytvořili žádné koncové body, bude tabulka prázdná. Aktuální národní prostředí se odráží v názvu tabulky. Pokud chcete vytvořit nasazení pro jiný jazyk, klikněte na "Změnu národního prostředí". Další informace o podporovaných jazyků najdete v části o změnu národního prostředí.

Podrobný popis najdete na tom, jak vytvořit koncový bod vlastní převod řeči na text [tady](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Pomocí koncového bodu vlastní převod řeči
Požadavky je odeslat do koncového bodu převodu řeči na text CRIS velmi podobným způsobem jako výchozí koncový bod služby Microsoft Cognitive Services řeči. Všimněte si, že tyto koncové body jsou funkčně stejný jako výchozí koncové body rozhraní API pro rozpoznávání řeči. Stejné funkce, které jsou k dispozici prostřednictvím klientskou knihovnu nebo rozhraní REST API pro rozhraní API pro rozpoznávání řeči je tedy také k dispozici pro váš vlastní koncový bod.

Podrobný popis najdete na použití vlastního koncového bodu převodu řeči na text [tady](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Všimněte si, že koncové body vytvořené prostřednictvím CRIS může zpracovat různý počet souběžných požadavků v závislosti na úrovni předplatného se prosím k. Pokud jsou požadovány další rozpoznávání než, vrátí kód chyby 429 (příliš mnoho požadavků). Další informace najdete [informace o cenách](https://www.microsoft.com/cognitive-services/en-us/pricing). Kromě toho se kvóta měsíčního počtu požadavků na úrovni free. V případech přistupujete koncový bod služby na úrovni free nad vaší měsíční kvóty služby vrátí kód chyby 403 Zakázáno.

Služba předpokládá, že zvuk se přenášejí v reálném čase. Pokud je odeslána rychleji, požadavek se budou považovat za spuštění, dokud jeho délky v reálném čase.

* [Přehled](cognitive-services-custom-speech-home.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
