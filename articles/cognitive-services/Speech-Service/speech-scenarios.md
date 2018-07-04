---
title: Azure Cognitive Services scénáře řeči | Azure dokumentace Microsoftu
description: Scénáře
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 6ea55f5d329a037ef35984cb9276b27a532824ec
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347390"
---
# <a name="speech-scenarios"></a>Scénáře pro službu Speech

Existuje mnoho scénářů, které lze poskytnout použitím technologii pro Řečovou. Jsme některé z nejčastěji používaných analýzy a upozorňují na důležité funkce v dokumentaci. Pro většinu obsahu [SDK](speech-sdk.md) je zásadním při povolování těchto scénářů.

Na stránce Tento článek popisuje postup:
> [!div class="checklist"]
> * Vytvářejte aplikace aktivované hlasem
> * Přepisy volání center zvuku volání
> * Roboti hlasu

## <a name="voice-triggered-apps"></a>Hlasové aktivuje aplikace

Mnoho uživatelů chcete povolit hlasový vstup u svých aplikací. Hlasový vstup je skvělý způsob, jak vytvořit aplikaci flexibilní, jde o použití rukou zdarma (například v automobilu) nebo urychlení různé úkoly, třeba dotaz pokynů informace zprávy nebo počasí. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Hlasové aktivaci aplikací pomocí základní modely

Pokud vaše aplikace pro širokou veřejností v prostředích, kde není nadměrné šum na pozadí, nejjednodušší a nejrychlejší způsob, jak to udělat jednoduše stahovat naše [sadou SDK pro řeč](speech-sdk.md) a příslušné [ Ukázky](quickstart-csharp-windows.md). Sada SDK používá technologii vaše [klíč předplatného Azure](https://azure.microsoft.com/try/cognitive-services/) vývojářům umožňuje nahrát zvuk modely pro rozpoznávání řeči standardních hodnot, které stojí za Cortana a Skype. Mdoels jsou nejmodernější optimalizaci pspo a jsou používány výše uvedené produkty. Zprovoznění může trvat několik minut.

### <a name="voice-triggered-apps-with-custom-models"></a>Hlasové aktivuje aplikací s využitím vlastních modelů

Pokud vaše aplikace řeší konkrétní doménu, (Řekněme chemii, biologie nebo zvláštními potřebami podávání), můžete chtít zvážit přizpůsobit [jazykový model](how-to-customize-language-model.md). Přizpůsobení jazykového modelu naučí dekodér o většiny běžných frází a slova, která vaše aplikace používá. Dekodér budete moct přesněji přepisy hlasový vstup pomocí vlastního jazykového modelu pro konkrétní doménu místo modelu směrného plánu. Podobně pokud šum na pozadí, kde se bude používat vaše aplikace je viditelného můžete k přizpůsobení akustického modelu. Projděte si dokumentaci pro další případy, ve kterém [přizpůsobení jazyka](how-to-customize-language-model.md) a [akustické přizpůsobení](how-to-customize-acoustic-models.md) zadejte hodnotu a navštivte naše [přizpůsobení portálu](https://customspeech.ai) pro spuštění s oznámením prostředí pro vytváření modelu. Podobně jako základní modely, vlastní modely jsou volány prostřednictvím našich [sadou SDK pro řeč](speech-sdk.md) a příslušné [ukázky](quickstart-csharp-windows.md).

## <a name="transcribe-call-center-audio-calls"></a>Přepisy volání center zvuku volání

Volání centra shromažďovat velké množství zvuku. Skrýt v rámci těchto zvukové soubory leží hodnotu, která může získat i když určené k transkripci. Doba trvání volání, mínění, spokojenost zákazníka a obecné hodnoty hovor volajícímu se dají zobrazit tak získání volání záznamy o studiu.

Nejlepší počátečním bodem je [určené k transkripci rozhraní API služby Batch](batch-transcription.md) spolu se souvisejícími [ukázka](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

Budete muset nejprve získejte [klíč předplatného Azure](https://azure.microsoft.com/try/cognitive-services/) a pak budete muset najdete [dokumentaci]([Batch transcription API](batch-transcription.md)).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Přepisy volání center zvuku volání s základní modely

Rozhodnutí, kterou je potřeba provést se, jestli budete používat interní základní modely Pokud chcete provést přepis, přizpůsobení jazyka nebo akustický model nebo obojí. Použít základní modely pouze rozhraní API vyžaduje klíč rozhraní API. Rozhraní API se interně vyvolat nejlepší model pro vaše data a přizpůsobit.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Přepisy volání center zvuku volání pomocí vlastních modelů

Pokud budete chtít použít vlastní model, musíte ID tohoto modelu spolu s klíčem rozhraní API. ID modelu je získaných [přizpůsobení portálu](https://customspeech.ai). Toto není ID koncového bodu, které se nachází v zobrazení Podrobnosti o koncovém bodu, ale ID modelu, který můžete získat po kliknutí na "O" tohoto modelu.

## <a name="voice-bots"></a>Roboti hlasu

Pro vývojáře můžete Zlepšete možnosti svých aplikacích pomocí hlasu výstup. Speech Service můžete synthetize mluvené řeči pro celou řadou [jazyky](supported-languages.md) a poskytuje [koncové body](rest-apis.md) pro přístup k a přidání této funkce do vaší aplikace.

Kromě toho pro uživatele, které chcete přidat další posouzení vašich osobnostních a jedinečnosti pro své roboty, služba Speech umožňuje vývojářům přizpůsobit písmo jedinečný hlas. Podobně jako přizpůsobení rozpoznávání řeči, že modely hlasová písma vyžadují uživatelská data. Vývojáři se nahrávání tato data v našich [hlasové přizpůsobení portálu](https://customspeech.ai) a začněte vytvářet vlastní jedinečné značky hlasu pro svého robota. Podrobnosti jsou popsány [tady](how-to-text-to-speech.md) také [nejčastější dotazy k](faq-text-to-speech.md) stránky 

## <a name="next-steps"></a>Další postup

* [Získejte zkušební verzi předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Začínáme s rozhraním Speech SDK](speech-sdk.md)
