---
title: Vlastní neuronové hlas – přehled – služba Speech
titleSuffix: Azure Cognitive Services
description: Custom neuronové Voice je funkce pro převod textu na řeč, která umožňuje vytvořit pro vaše aplikace jeden upravitelný vlastní syntetický hlas tím, že jako ukázku poskytnete vlastní zvuková data.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 9e230ff4e84eba7b61846540fef2915765fa4f77
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509668"
---
# <a name="what-is-custom-neural-voice"></a>Co je vlastní neuronové hlas?

Custom neuronové Voice je funkce převodu [textu na mluvené slovo](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) (TTS), která umožňuje vytvořit pro vaše aplikace jeden vlastní přizpůsobený syntetický hlas tím, že jako ukázku poskytnete vlastní zvuková data. Převod textu na řeč funguje tak, že převede text na syntetické rozpoznávání řeči pomocí modelu strojového učení, který je podobný jako vybraný hlas. Pomocí [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)můžete vašim aplikacím povolit mluvit s [předem vytvořenými hlasy](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) nebo vlastními [hlasovými](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-prepare-data) modely vyvinutými prostřednictvím vlastní funkce hlasu neuronové. Vlastní neuronové hlas je založený na technologii neuronové TTS, která vytváří přirozený zvukový hlas, který je v porovnání s lidským hlasem často nerozlišující.
Reálný a přirozený hlas vlastního hlasu neuronové může představovat značky, personify počítače a umožňuje uživatelům interakci s aplikacemi přirozeným způsobem.

> [!NOTE]
> Vlastní Hlasová funkce neuronové vyžaduje registraci a přístup k němu je omezený na základě nároku a kritérií pro použití od Microsoftu. Zákazníci, kteří chtějí použít tuto funkci, jsou vyžadováni k registraci svých případů použití prostřednictvím [formuláře pro příjem](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Základy vlastního hlasu neuronové

Základní technologie neuronové TTS používaná pro vlastní neuronové hlas se skládá ze tří hlavních součástí: analyzátor textu, neuronové akustický model a neuronové vocoder. Pokud chcete z textu vygenerovat přirozený syntetický hlas, text se nejprve zadá do analyzátoru textu, který poskytuje výstup ve formě foném sekvence. Foném je základní jednotkou zvuku, která rozlišuje jedno slovo od sebe v konkrétním jazyce. Sekvence fonémy definuje výslovnost slov, která jsou uvedena v textu. 

V dalším kroku foném sekvence přejde do akustického modelu neuronové a předpovídá akustické funkce, které definují zvukové signály, jako je Timbre, styl mluveného slova, rychlost, nevýbušnost a vzory zátěže. Nakonec neuronové vocoder převede akustické funkce na zvukové vlny, aby se vygeneroval syntetické rozpoznávání řeči.

![Úvodní obrázek pro vlastní neuronové hlas](./media/custom-voice/cnv-intro.png)

Hlasové modely neuronové TTS jsou vyškoleny pomocí neuronové sítí založených na vzorcích záznamu lidského hlasy. V tomto [blogu](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)popisujeme, jak neuronové TTS funguje se špičkovými modely syntézy neuronové řeči. Tento blog také vysvětluje, jak se dá univerzální základní model přizpůsobit s méně než 2 hodinami dat řeči (nebo menším počtem 2 000 zaznamenaných projevy) od cílového mluvčího a Naučte se mluvit do hlasu tohoto cílového mluvčího. Informace o tom, jak je neuronové vocoder, najdete v [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Díky funkci vlastního hlasu neuronové můžete přizpůsobit modul neuronové TTS, aby lépe vyhovoval vašim uživatelským scénářům. Pokud chcete vytvořit vlastní neuronové hlas, pomocí sady [Speech Studio](https://speech.microsoft.com/customvoice) nahrajte zaznamenaný zvuk a příslušné skripty, proveďte výuku modelu a nasaďte tento hlas do vlastního koncového bodu. V závislosti na případu použití se vlastní neuronové hlas dá použít k převodu textu na řeč v reálném čase (například ve inteligentním virtuálním Pomocníkovi) nebo k vygenerování zvukového obsahu offline (například používaného jako v zvukové knize nebo v pokynech v e-learningových aplikacích) s textovým vstupem, který zadal uživatel. Tato funkce je k dispozici prostřednictvím [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech), [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-text-to-speech?tabs=script%2Cwindowsinstall&pivots=programming-language-csharp)nebo [webového portálu](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Pojmy a definice

| **Označení**      | **Definition**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hlasový model   | Model převodu textu na řeč, který může napodobit jedinečné charakteristiky r cílového mluvčího. *Hlasový model* se označuje také jako *hlasový* hlas nebo *syntetického hlasu*. Hlasový model je sada parametrů v binárním formátu, který není čitelný pro člověka a neobsahuje zvukové nahrávky. Nedá se zpětně analyzovat, aby bylo možné odvodit nebo vytvořit zvuk lidského hlasu. |
| Talentů hlasu  | Jednotlivců nebo cílových mluvčích, jejichž hlasy se zaznamenávají a využívají k vytváření hlasových modelů, které jsou určené pro zvuk, jako je hlasový talentů hlas.                                                                                                                                                                                                                                                   |
| Standardní TTS  | Standardní nebo "tradiční" způsob TTS, který rozděluje mluvený hlas na fonetické fragmenty, aby mohl být znovu smíšen a porovnán pomocí klasického programování nebo statistických metod.                                                                                                                                                                                                    |
| Neuronové TTS    | Neuronové TTS syntetizuje řeč pomocí hluboce neuronové sítí, u kterých se "dozvěděl" způsob, jakým jsou slova v rámci přirozeného lidského řeči kombinována, a ne pomocí procedurálního programování nebo statistických metod. Neuronové TTS kromě záznamů cílového hlasu talentů používá zdrojovou knihovnu nebo základní model, který je sestavený pomocí hlasových nahrávek z mnoha různých mluvčích.          |
| Data pro trénink | Vlastní neuronové hlasového školení, která zahrnuje zvukové nahrávky hlasového talentůu a související text se přepisuje.                                                                                                                                                                                                                                                               |
| Nežádoucí       | Uživatel s popisem, který požadujete, aby byl tento hlas. Dobrý návrh, který je vhodný pro vytváření hlasu, bude informovat o tom, jestli je výběr dostupného hlasového modelu už vytvořený, nebo od nuly po přetypování a zaznamenání nového hlasového talentůu.                                                                                                |
| Skript        | Skript je textový soubor, který obsahuje projevy, který bude hlasem talentů řeči. (Pojem "*projevy*" zahrnuje jak úplné věty, tak kratší fráze.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Zodpovědné využití AI

Pokud se chcete dozvědět, jak používat vlastní neuronové Voice zodpovědnou, přečtěte [si poznámku o transparentnosti](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice). Poznámky transparentnosti Microsoftu mají za úkol porozumět tomu, jak naše technologie AI funguje, takže vlastníci systému můžou mít vliv na výkon a chování systému a také na důležité úvahy o celém systému, včetně technologie, lidí a prostředí.

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Custom Voice](how-to-custom-voice.md)
* [Vytvoření a použití vlastního koncového bodu hlasu](how-to-custom-voice-create-voice.md)