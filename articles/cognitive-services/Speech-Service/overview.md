---
title: Co jsou hlasové služby Azure?
titleSuffix: Azure Cognitive Services
description: Hlasové služby Azure jsou sjednocení speech to text, převodu textu na řeč a překladu řeči v rámci jednoho předplatného Azure. Je snadno přidat rozpoznávání řeči, aplikace, nástroje a zařízení se sadou SDK pro řeč, sadou SDK pro řeč zařízení nebo rozhraní REST API. Přidání funkcemi pro zpracování řeči do stávající chatovací robot, převést převod textu na řeč v aplikaci překladu nebo přepisy velké objemy dat center volání.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d9664ac9fb72a5f094674856a20230199270f01d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603151"
---
# <a name="what-are-the-speech-services"></a>Co jsou služby Speech?

Hlasové služby Azure jsou sjednocení speech to text, převod textu na řeč a překladu řeči na jedno předplatné Azure. Je snadné rozpoznávání řeči povolit aplikace, nástroje a zařízení s [sadou SDK pro řeč](speech-sdk-reference.md), [sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-quickstart), nebo [rozhraní REST API](rest-apis.md).

> [!IMPORTANT]
> Hlasové služby nahradil, rozhraní Bing Speech API, Translator Speech a Custom Speech. Zobrazit *provede postupy > Migrace* pro pokyny k migraci.

Tyto funkce tvoří hlasové služby Azure. Další informace o běžných případů použití pro jednotlivé funkce nebo procházet Reference k rozhraní API pomocí odkazů v této tabulce.

| Služba | Funkce | Popis | Sada SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Převod řeči na text | Převod řeči na text transcribes audiostreamy na text v reálném čase, který může využívat nebo zobrazení vaší aplikace, nástroje nebo zařízení. Použít převod řeči na text pomocí [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) odvodit záměry uživatele z přepisu řeči a zákona o hlasové příkazy. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Přepis služby batch](batch-transcription.md) | Přepis Batch umožňuje asynchronní určené k transkripci řeči na text velkých svazků s daty. Toto je nějakou službu založenou na REST, která využívá stejný koncový bod jako přizpůsobení a správa modelů. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| | [Přepis konverzace](conversation-transcription-service.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikace mluvčího a diarization. Jde o ideální řešení pro přepisování schůzky osobně s možností rozlišení mluvčí. | Ano | Ne |
| | [Vytvořit modely řeči](#customize-your-speech-experience) | Pokud používáte speech to text pro rozpoznávání a přepis v jedinečné prostředí, můžete vytvořit a trénování vlastních akustických, jazyk a výslovnost modelů hluk adresu nebo slovníku specifických pro dané odvětví. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převádí vstupní text pomocí podobnou té lidské řečového [řeči syntézu Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Vyberte standardní hlasy a hlasy neuronových sítí (naleznete v tématu [jazykovou podporu](language-support.md)). | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Vytvořit vlastní hlasů](#customize-your-speech-experience) | Vytvoření vlastního hlasového písma jedinečné pro vaší značce nebo produktu. | Ne | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje v reálném čase, vícejazykového překlad řeči do aplikací, nástroje a zařízení. Pomocí této služby pro překlad řeči řeči a rozpoznávání řeči na text. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Ne |
| [Voice-first Virtual Assistants](voice-first-virtual-assistants.md) | Asistenti virtuální první hlasu | Vlastní virtuálních asistentů pomocí hlasové služby Azure umožní vývojářům vytvářet konverzační rozhraní přirozené, podobnou té lidské pro jejich aplikace a prostředí. Kanál s přímým přístupem řeči řádku rozhraní Bot Framework rozšiřuje tyto možnosti tím, že poskytuje koordinované, iniciovat organizovaně, což vstupní bod do kompatibilní bot umožňující hlasové v hlasových si interakce s nízkou latencí a vysokou spolehlivostí. | [Ano](voice-first-virtual-assistants.md) | Ne |

## <a name="news-and-updates"></a>Novinky a aktualizace

Zjistěte, co je nového v hlasových služeb Azure.

* 2019 dne
    * Vydáno řeči SDK 1.6.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v tématu [poznámky k verzi](releasenotes.md).
* . Května 2019 – dokumentace je nyní k dispozici pro [konverzace určené k transkripci](conversation-transcription-service.md), [přepis volání Center](call-center-transcription.md), a [virtuálních asistentů hlasové první](voice-first-virtual-assistants.md).
* . Května 2019.
    * Vydáno řeči SDK 1.5.1. Úplný seznam aktualizací, vylepšení a známých problémů najdete v tématu [poznámky k verzi](releasenotes.md).
    * Vydáno řeči SDK 1.5.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v tématu [poznámky k verzi](releasenotes.md).
* 2019. dubna – vydali sadou SDK pro řeč 1.4.0 s podporou pro převod textu na řeč (beta verze) jazyka C++, C#a Java na Windows a Linux. Kromě toho sada SDK teď podporuje formáty zvuku MP3 a díle/Ogg jazyka C++ a C# v Linuxu. Úplný seznam aktualizací, vylepšení a známých problémů najdete v tématu [poznámky k verzi](releasenotes.md).
* 2019. března – nový koncový bod pro převod textu na ŘEČ, která vrací úplný seznam dostupných hlasy v určité oblasti je teď k dispozici. Kromě toho nové oblasti jsou nyní podporovány pro převod textu na ŘEČ. Další informace najdete v tématu [referenční dokumentace rozhraní API pro převod textu na řeč (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Vyzkoušejte hlasové služby

Nabízíme rychlé starty v nejoblíbenějších programovacích jazyků, každý navržené tak, aby se spouštěním kódu za méně než 10 minut. Tato tabulka obsahuje nejoblíbenějších šablon rychlý start pro jednotlivé funkce. Pomocí levém navigačním panelu můžete prozkoumat další jazyky a platformy.

| Převod řeči na text (SDK) | Převod textu na řeč (SDK) | Překlad (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, Rozhraní .NET framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (prohlížeč)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, Rozhraní .NET framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Převod řeči na text a převod textu na řeč také mít koncové body REST a přidružené šablony rychlý start.

Poté, co jste využili příležitost dobře se použijte hlasové služby, vyzkoušejte v našem kurzu, která vás naučí, jak rozpoznat záměry z řeči pomocí sadou SDK pro řeč a LUIS.

* [Kurz: Rozpoznat záměry z řeči se sadou SDK pro řeč a LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Kurz: Sestavení aplikace Flask překlad textu, analýza mínění a syntetizace přeloženého textu na řeč, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Ukázkový kód je k dispozici na Githubu pro každou hlasových služeb Azure. Tyto ukázky zahrnují běžné scénáře, jako jsou čtení zvuk ze souboru nebo datový proud, rozpoznávání průběžné a jednorázová, prostředků a práci s vlastní modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

* [Ukázky překladu řeči na text, převodu textu na řeč a řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Přepis ukázek služby Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Převod textu na řeč ukázky (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro zpracování řeči

Hlasové služby Azure dobře funguje pro předdefinované modely, ale můžete chtít dále přizpůsobit a optimalizovat prostředí pro váš produkt nebo prostředí. Přizpůsobení možností v rozsahu od akustický model ladění jedinečný hlasová písma pro vlastní značky. Po začlenění vlastního modelu, můžete s libovolnou hlasových služeb Azure.

| Speech Service | Model | Popis |
|----------------|-------|-------------|
| Řeč na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvoření vlastního akustického modelu pro aplikace, nástroje, nebo zařízení, která se používá především prostředích, jako je v automobilu nebo ve výrobním závodě, každý s konkrétní záznam podmínky. Mezi příklady patří s diakritikou řeči, zvuků na pozadí konkrétní nebo použitím mikrofonu konkrétní záznam. |
| | [Jazykový model](how-to-customize-language-model.md) | Vytvoření vlastního jazykového modelu pro zlepšení přepis slovník konkrétní pole a gramatiku, jako jsou lékařské terminologie nebo žargonu IT. |
| | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastních výslovnost modelu můžete definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché. |
| Převod textu na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasové písmo umožňují vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky. Trvá jen malé množství dat, abyste mohli začít. Čím více dat, že poskytují více přirozený a podobnou té lidské bude hlasové písmo zvuku. |

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
