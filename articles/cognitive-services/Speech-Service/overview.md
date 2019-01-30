---
title: Co je služba Speech?
titleSuffix: Azure Cognitive Services
description: 'Speech Service součástí Azure Cognitive Services, sjednotí několik hlasové služby, které byly dříve k dispozici samostatně: (Zahrnující rozpoznávání řeči a převod textu na řeč) pro zpracování řeči Bingu, vlastní řeč a překladu řeči.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: beb1382d2e076052583dd5bbc546259c8aae2f96
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217964"
---
# <a name="what-is-speech-services"></a>Co je hlasové služby?

Stejně jako jiné služby Azure řeči hlasové služby využívají řeči technologií používaných pro produkty, jako je Cortana a Microsoft Office.

Hlasové služby sjednocení Azure Řečové funkce na dříve k dispozici prostřednictvím [rozhraní API pro zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home), a [vlastní Hlasové](http://customvoice.ai/) služby. Teď přístup ke všem těmto funkcím poskytuje jedno předplatné.

## <a name="main-speech-services-functions"></a>Funkce Main hlasové služby

Primární funkce hlasové služby jsou řeči na text (také nazývané řeči nebo určené k transkripci), převod textu na řeč (syntézu řeči) a překladu řeči.

|Funkce|Funkce|
|-|-|
|[Převod řeči na text](speech-to-text.md)| <li>Přepisuje plynulou řeč v reálném čase do textové podoby.<li>Dokáže dávkově přepisovat řeč ze zvukových nahrávek. <li>Podporuje okamžité výsledky, detekci konce řeči, automatické formátování textu a maskování vulgarismů. <li>Může volat službu [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), která z přepsané řeči odvodí záměr uživatele.\*|
|[Text-to-Speech](text-to-speech.md)| <li>**NOVÉ**: Poskytuje neuronových sítí převod textu na řeč hlasy téměř nerozeznatelná od lidské řeči (v angličtině). <li>Převádí text do přirozeně znějící řeči. <li>Nabízí několik pohlaví a dialektů pro celou řadu podporovaných jazyků. <li>Podporuje vstup v podobě prostého textu nebo jazyk SSML (Speech Synthesis Markup Language). |
|[Překlad řeči](speech-translation.md)| <li>Téměř v reálném čase překládá streamovaný zvuk.<li> Dokáže zpracovat také záznam řeči.<li>Poskytuje výsledky v podobě textu nebo syntetizované řeči. |


## <a name="customize-speech-features"></a>Přizpůsobení funkcí řeči

K trénování modelů, které tvoří základ funkcí pro převod řeči na text a převod textu na řeč služby Speech, můžete použít vlastní data.

|Funkce|Model|Účel|
|-|-|-|
|Převod řeči na text|[Akustický model](how-to-customize-acoustic-models.md)|Pomáhá s přepisem u konkrétních řečníků a prostředí, jako jsou auta nebo továrny.|
||[Jazykový model](how-to-customize-language-model.md)|Pomáhá s přepisem slovní zásoby a gramatiky specifických pro určitý obor, jako je lékařský nebo IT žargon.|
||[Model výslovnosti](how-to-customize-pronunciation.md)|Pomáhá s přepisem zkratek a akronymů, jako je IOU znamenající „I owe you“ (Dlužím ti). |
|Převod textu na řeč|[Hlasové písmo](how-to-customize-voice-font.md)|Poskytuje vaší aplikaci vlastní hlas díky trénování modelu na ukázkách lidské řeči.|

Vlastní modely můžete použít všude, kde ve své aplikaci používáte standardní modely pro funkce převodu řeči na text nebo převodu textu na řeč.

## <a name="use-the-speech-service"></a>Použití služby Speech

Pro zjednodušení vývoje aplikací s podporou hlasových služeb poskytuje Microsoft sadu [Speech SDK](speech-sdk.md), kterou můžete použít se službou Speech. Sada Speech SDK poskytuje konzistentní a nativní rozhraní API pro převod řeči na text a překlad řeči pro jazyky C#, C++ a Java. Pokud při vývoji používáte jeden z těchto jazyků, zjednoduší vám sada Speech SDK vývoj tím, že se postará o podrobnosti sítě za vás.

Hlasové služby má také [rozhraní REST API](rest-apis.md) , který funguje s libovolným programovacím jazykem, který umí vytvářet požadavky HTTP. Rozhraní REST nenabízí funkci streamování v reálném čase jako sada SDK.

|<br>Metoda|Řeč<br>na text|Text na<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Ano|Ne|Ano|Nativní rozhraní API pro jazyky C#, C++ a Java pro zjednodušení vývoje.|
|[Rozhraní REST API](rest-apis.md)|Ano|Ano|Ne|Jednoduché rozhraní API založené na HTTP, které usnadňuje přidání hlasových služeb do aplikací.|

### <a name="websockets"></a>WebSockets

Hlasové služby také podporují protokoly WebSocket pro streamování speech to text i překladu řeči. Sady Speech SDK pomocí těchto protokolů komunikují se službou Speech. Použijte sadu Speech SDK a nesnažte se implementovat vlastní komunikaci protokolu WebSocket se službou Speech.

Pokud již máte kód, který se používá pro zpracování řeči Bingu nebo Translator Speech přes protokoly Websocket, můžete aktualizovat na používání hlasové služby. Protokoly WebSocket jsou kompatibilní, ale jsou různé koncové body.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

Sada [Speech Devices SDK](speech-devices-sdk.md) je integrovaná hardwarová a softwarová platforma pro vývojáře zařízení s podporou hlasových služeb. Náš partner pro hardware poskytuje referenční návrhy a vývojové jednotky. Microsoft poskytuje sadu SDK optimalizovanou pro zařízení, která naplno využívá možnosti hardwaru.


## <a name="speech-scenarios"></a>Scénáře pro službu Speech

Případy použití pro hlasové služby zahrnují:

> [!div class="checklist"]
> * Vytváření aplikací aktivovaných hlasem
> * Přepis nahrávek z call centra
> * Implementace hlasových robotů

### <a name="voice-user-interface"></a>Hlasové uživatelské prostředí

Hlasový vstup představuje skvělý způsob, jak zajistit flexibilitu a možnost rychlého používání aplikace bez pomoci rukou. Uživatelé, kteří používají aplikace s podporou hlasu, se mohou na požadované informace jednoduše zeptat.

Pokud je vaše aplikace určená pro veřejnost, můžete využít výchozí modely rozpoznávání řeči. Dokážou rozpoznat širokou škálu řečníků v běžných prostředích.

Pokud se vaše aplikace používá v určitém oboru, například v medicíně nebo IT, můžete vytvořit [jazykový model](how-to-customize-language-model.md). Můžete použít tento model představuje hlasové služby o speciální terminologii používané v aplikaci.

Pokud se vaše aplikace používá v hlučném prostředí, například v továrně, můžete vytvořit vlastní [akustický model](how-to-customize-acoustic-models.md). Tento model pomáhá hlasových služeb pro rozlišení řeči od šumu.

### <a name="call-center-transcription"></a>Přepis nahrávek z call centra

Nahrávky z call centra se často analyzují jen v případě, že z volání vzejde nějaký problém. S využitím služby Speech je snadné přepsat všechny nahrávky do textové podoby. Text můžete snadno indexovat pro [fulltextové vyhledávání](https://docs.microsoft.com/azure/search/search-what-is-azure-search) nebo použít [Analýzu textu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) k rozpoznání mínění, jazyka a klíčových frází.

Pokud vaše volání nahrávání zahrnují speciální terminologie, jako jsou názvy produktů nebo žargonu IT, můžete vytvořit [jazykový model](how-to-customize-language-model.md) představuje hlasové služby slovníku. Vlastní [akustický model](how-to-customize-acoustic-models.md) nápovědy hlasové služby by rozuměla méně než optimální phone připojení.

Další informace o tomto scénáři najdete v článku o [dávkovém přepisu](batch-transcription.md) pomocí služby Speech.

### <a name="voice-bots"></a>Hlasoví roboti

[Roboti](https://dev.botframework.com/) představují oblíbený způsob, jak spojit uživatele s požadovanými informacemi a zákazníky s požadovanými firmami. Když do webu nebo aplikace přidáte konverzační uživatelské rozhraní, zajistíte snadnější nalezení a rychlejší přístup k této funkčnosti. Se službou Speech získává plynulost této konverzace nový rozměr díky tomu, že služba věcně reaguje na mluvené dotazy.

Pokud chcete robota s podporou řeči doplnit o jedinečnou osobnost, můžete mu dát vlastní hlas. Vytvoření vlastního hlasu je dvoustupňový proces. Nejprve [pořídíte nahrávky](record-custom-voice-samples.md) hlasu, který chcete použít. Pak [tyto nahrávky odešlete](how-to-customize-voice-font.md) (společně s textovým přepisem) na [portál pro přizpůsobení hlasu](https://cris.ai/Home/CustomVoice) služby Speech, která se postará o zbytek. Až vlastní hlas vytvoříte, je postup jeho použití v aplikaci zřejmý.

## <a name="next-steps"></a>Další postup

Získáte klíč předplatného pro hlasové služby.

> [!div class="nextstepaction"]
> [Vyzkoušejte hlasové služby zdarma](get-started.md)
