---
title: Začínáme se službou vlastní řeči v Azure | Microsoft Docs
description: Objednat předplatné služby vlastní řeči a propojit aktivity služby předplatného Azure k natrénování modelu a provést nasazení.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342485"
---
# <a name="get-started-with-custom-speech-service"></a>Začínáme se službou vlastní řeči

Prozkoumejte hlavní funkce služby řeči vlastní a zjistěte, jak vytvořit, nasadit a používat modely acoustic a jazyk pro potřebám vaší aplikace. Rozsáhlejší dokumentaci a podrobné pokyny najdete po přihlášení na portálu služby řeči vlastní.

## <a name="samples"></a>Ukázky  
Je dobrý vzorku, který jsme poskytnout k získání můžete přejít, které můžete najít [zde](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Požadavky  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Přihlášení k odběru do vlastní řeči služby a získat klíč předplatného
Před přehrávání pomocí výše uvedeného příkladu musíte přihlášení k odběru do vlastní řeči služby a získat předplatné klíče najdete v tématu [odběry](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) nebo postupujte podle vysvětlení [zde](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Jak primární a sekundární klíč lze použít v tomto kurzu. Dodržujte doporučené postupy pro zachování váš tajný klíč klíče rozhraní API a zabezpečení.

### <a name="get-the-client-library-and-example"></a>Nainstalujte klienta knihovny a příklad
Můžete ho stáhnout klientské knihovny a příklad prostřednictvím [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Musí být extrahovány do složky podle svého výběru souboru zip staženého, mnoho uživatelů vyberte složku, Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Vytvoření vlastní akustickými modelu
Přizpůsobit akustickými modelu pro konkrétní domény, kolekce řečových dat je potřeba. Tato kolekce se skládá z sadu zvukových souborů řeči dat a soubor text transcriptions zvukové soubory. Zvuk data by měla vystihovat scénáře, ve kterém byste chtěli použít nástroj rozpoznávání rukopisu

Například: Pokud chcete lépe rozpoznávat řeč v prostředí aktivní výrobce, zvukových souborů by měla obsahovat osoby hovořícího v objekt aktivní.
Pokud vás zajímá optimalizace výkonu pro jeden mluvčího, například chcete transcribe všechny na FDR Fireside konverzace a potom zvukových souborů by měla obsahovat mnoho příkladů tohoto mluvčího jenom.

Podrobný popis můžete najít na tom, jak vytvořit vlastní akustickými model [zde](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Vytvoření vlastní jazyk modelu
Postup pro vytvoření modelu vlastní jazyk je podobná vytvoření akustickými modelu, s výjimkou neexistuje žádná zvuková data pouze text. Text by měla obsahovat mnoho příkladů dotazů nebo utterances budou uživatelé k vyslovení nebo přihlášení uživatelé oznámením (nebo zadáním) ve vaší aplikaci.

Podrobný popis můžete najít na tom, jak vytvořit vlastní jazyk model [zde](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Vytváření vlastní koncový bod řeči na text
Pokud jste vytvořili vlastní akustickými modely nebo jazyk modely, můžete nasadit v koncový bod vlastní řeči na text. Pokud chcete vytvořit nový vlastní koncový bod, v nabídce "CRIS" horní části stránky klikněte na "Nasazení". Tím přejdete na tabulce s názvem "Nasazení" aktuální vlastní koncové body. Pokud jste ještě nevytvořili žádné koncové body, v tabulce bude prázdný. Aktuální národní prostředí se projeví v názvu tabulky. Pokud chcete vytvořit nasazení pro jiný jazyk, klikněte na "Změnu národního prostředí". Další informace o podporovaných jazyků najdete v části na změnu národního prostředí.

Podrobný popis můžete najít na tom, jak vytvořit koncový bod vlastní text řeči [zde](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Pomocí koncový bod vlastní řeči
Požadavky můžete odeslat do koncového bodu CRIS řeči na text velmi podobným způsobem jako výchozí koncový bod řeči kognitivní služby společnosti Microsoft. Všimněte si, že fungují stejně jako jsou výchozí koncové body rozhraní API pro rozpoznávání řeči těchto koncových bodů. Stejné funkce, která je k dispozici prostřednictvím klientské knihovny nebo REST API pro rozpoznávání řeči rozhraní API je proto také k dispozici pro svůj vlastní koncový bod.

Podrobný popis můžete najít na použití vlastní koncový bod řeči na text [zde](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Upozorňujeme, že koncové body vytvořené prostřednictvím CRIS může zpracovat různý počet souběžných požadavků v závislosti na vrstvě, že je přidružen k odběru. Pokud jsou požadovány další uznání než, vrátí kód chyby 429 (příliš mnoho požadavků). Další informace naleznete [informace o cenách](https://www.microsoft.com/cognitive-services/en-us/pricing). Kromě toho je měsíční kvóta požadavků pro úroveň free. V případech přistupujete váš koncový bod v úrovni free výše vaší měsíční kvóta službu vrátí kód chyby 403 Zakázáno.

Služba předpokládá, že zvuk se přenášejí v reálném čase. Pokud je odeslat rychleji, požadavek bude považovat za spuštění, dokud jeho trvání v reálném čase byla úspěšná.

* [Přehled](cognitive-services-custom-speech-home.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
