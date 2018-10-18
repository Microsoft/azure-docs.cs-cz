---
title: Co je služba Speech?
description: 'Služba Speech, která je součástí služeb Microsoft Cognitive Services, sjednocuje několik hlasových služeb Azure, které byly dříve k dispozici samostatně: Zpracování řeči Bingu (skládající se z rozpoznávání řeči a převodu textu na řeč), Custom Speech a Překlad řeči.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: b014144dd819aa354596175068bfb2c5905a4377
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857988"
---
# <a name="what-is-the-speech-service"></a>Co je služba Speech?


Podobně jako ostatní hlasové služby Azure i služba Speech využívá hlasové technologie používané v produktech, jako je Cortana a Microsoft Office.

Služba Speech sjednocuje hlasové funkce Azure, které byly dříve dostupné prostřednictvím služeb [Zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) a [Custom Voice](http://customvoice.ai/). Teď přístup ke všem těmto funkcím poskytuje jedno předplatné.

## <a name="main-speech-service-functions"></a>Hlavní funkce služby Speech

Hlavními funkcemi služby Speech jsou převod řeči na text (označuje se také jako rozpoznávání nebo přepis řeči), převod textu na řeč (syntéza řeči) a překlad řeči.

|Funkce|Funkce|
|-|-|
|[Převod řeči na text](speech-to-text.md)| <ul><li>Přepisuje plynulou řeč v reálném čase do textové podoby.<li>Dokáže dávkově přepisovat řeč ze zvukových nahrávek. <li>Podporuje okamžité výsledky, detekci konce řeči, automatické formátování textu a maskování vulgarismů. <li>Může volat službu [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), která z přepsané řeči odvodí záměr uživatele.\*|
|[Převod textu na řeč](text-to-speech.md)| <ul><li>Převádí text do přirozeně znějící řeči. <li>Nabízí několik pohlaví a dialektů pro celou řadu podporovaných jazyků. <li>Podporuje vstup v podobě prostého textu nebo jazyk SSML (Speech Synthesis Markup Language). |
|[Překlad řeči](speech-translation.md)| <ul><li>Téměř v reálném čase překládá streamovaný zvuk.<li> Dokáže zpracovat také záznam řeči.<li>Poskytuje výsledky v podobě textu nebo syntetizované řeči. |

\* *K rozpoznávání záměru se vyžaduje předplatné LUIS.*

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

Služba Speech nabízí také rozhraní [REST API](rest-apis.md), které funguje s jakýmkoli programovacím jazykem, který dokáže provádět požadavky HTTP. Rozhraní REST nenabízí funkci streamování v reálném čase jako sada SDK.

|<br>Metoda|Řeč<br>na text|Text na<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Ano|Ne|Ano|Nativní rozhraní API pro jazyky C#, C++ a Java pro zjednodušení vývoje.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché rozhraní API založené na HTTP, které usnadňuje přidání hlasových služeb do aplikací.|

### <a name="websockets"></a>WebSockets

Služba Speech nabízí také protokoly WebSocket pro streamování převodu řeči na text a překladu řeči. Sady Speech SDK pomocí těchto protokolů komunikují se službou Speech. Použijte sadu Speech SDK a nesnažte se implementovat vlastní komunikaci protokolu WebSocket se službou Speech.

Pokud už máte kód využívající Zpracování řeči Bingu nebo Translator Speech přes protokoly WebSocket, můžete ho aktualizovat tak, aby využíval službu Speech. Protokoly WebSocket jsou kompatibilní, liší se pouze koncové body.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

Sada [Speech Devices SDK](speech-devices-sdk.md) je integrovaná hardwarová a softwarová platforma pro vývojáře zařízení s podporou hlasových služeb. Náš partner pro hardware poskytuje referenční návrhy a vývojové jednotky. Microsoft poskytuje sadu SDK optimalizovanou pro zařízení, která naplno využívá možnosti hardwaru.

## <a name="why-move-to-the-speech-service"></a>Proč přejít na službu Speech?

Služba Speech nabízí mimo jiné všechny funkce rozhraní API pro zpracování řeči Bingu a tří dalších hlasových služeb Azure: Custom Speech, Custom Voice a Translator Speech. Uživatelům těchto služeb doporučujeme migrovat na službu Speech.

Služba Speech zahrnuje řadu upgradů těchto dalších služeb, včetně následujících:

* Přesnější rozpoznávání řeči. Modely, které se ve službě používají, pravidelně vylepšujeme.

* Větší škálovatelnost. Služba dokáže lépe zpracovat více souběžných žádostí a tím snižuje latenci.

* Služba Speech používá cenový model založený na čase. Podrobnosti najdete na stránce s [cenami za službu Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

* Služba Speech je dostupná v [několika oblastech](regions.md) a splní tak požadavky zákazníků po celém světě. Pro každou oblast, kterou vaše aplikace využívá, potřebujete [předplatné Azure](https://docs.microsoft.com/azure/cognitive-services/welcome).

* Jediný klíč předplatného služby Speech poskytuje přístup k následujícím funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

    * [Převod řeči na text](speech-to-text.md)
    * [Vlastní převod řeči na text](https://cris.ai/CustomSpeech)
    * [Převod textu na řeč](text-to-speech.md)
    * [Vlastní hlasy pro převod textu na řeč](https://cris.ai/CustomVoice)
    * [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](https://docs.microsoft.com/azure/cognitive-services/translator/translator-info-overview))

* Funkce převodu řeči na text služby Speech se integruje se [službou Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) a umožňuje tak rozpoznávání záměru mluvčího. Se službou Speech je možné použít také klíč koncového bodu služby LUIS. Podrobnosti najdete v [kurzu rozpoznávání záměru](how-to-recognize-intents-from-speech-csharp.md).

* Převod řeči na text už nevyžaduje zadání režimu rozpoznávání.

* Služba Speech podporuje 24kHz hlasy pro převod textu na řeč zajišťující lepší kvalitu zvuku. V době psaní tohoto článku existují dva takové hlasy (pouze v americké angličtině): `Jessa24kRUS` a `Guy24kRUS`.

* [Dávkový přepis](batch-transcription.md) služby Speech umožňuje efektivní přepis velkých objemů záznamů řeči, například nahrávek z call centra, na text, aby se mohly snadno analyzovat a prohledávat.

* Při používání sady Speech SDK neexistuje žádný časový limit streamování přepisu řeči na text.

* Sada [Speech SDK](speech-sdk.md) poskytuje konzistentní rozhraní API pro službu Speech pro různé programovací jazyky a různá spouštěcí prostředí (včetně Windows 10, UPW a .NET Core) a tím usnadňuje vývoj zejména na několika platformách.

* Služba Speech je kompatibilní s rozhraními REST API a protokolem WebSockets, které používají ostatní hlasové služby Azure, a díky tomu usnadňuje migraci stávajících klientských aplikací do služby Speech.

## <a name="speech-scenarios"></a>Scénáře pro službu Speech

Mezi případy použití služby Speech patří:

> [!div class="checklist"]
> * Vytváření aplikací aktivovaných hlasem
> * Přepis nahrávek z call centra
> * Implementace hlasových robotů

### <a name="voice-user-interface"></a>Hlasové uživatelské prostředí

Hlasový vstup představuje skvělý způsob, jak zajistit flexibilitu a možnost rychlého používání aplikace bez pomoci rukou. Uživatelé, kteří používají aplikace s podporou hlasu, se mohou na požadované informace jednoduše zeptat.

Pokud je vaše aplikace určená pro veřejnost, můžete využít výchozí modely rozpoznávání řeči. Dokážou rozpoznat širokou škálu řečníků v běžných prostředích.

Pokud se vaše aplikace používá v určitém oboru, například v medicíně nebo IT, můžete vytvořit [jazykový model](how-to-customize-language-model.md). Pomocí tohoto modelu naučíte službu Speech speciální terminologii, která se v aplikaci používá.

Pokud se vaše aplikace používá v hlučném prostředí, například v továrně, můžete vytvořit vlastní [akustický model](how-to-customize-acoustic-models.md). Tento model pomáhá službě Speech odlišit řeč od hluku.

Začít můžete jednoduše. Stáhněte si sadu [Speech SDK](speech-sdk.md) a postupujte podle příslušného článku s [rychlým startem](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Přepis nahrávek z call centra

Nahrávky z call centra se často analyzují jen v případě, že z volání vzejde nějaký problém. S využitím služby Speech je snadné přepsat všechny nahrávky do textové podoby. Text můžete snadno indexovat pro [fulltextové vyhledávání](https://docs.microsoft.com/azure/search/search-what-is-azure-search) nebo použít [Analýzu textu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) k rozpoznání mínění, jazyka a klíčových frází.

Pokud nahrávky z call centra obsahují odbornou terminologii (například názvy produktů nebo žargon IT), můžete vytvořit [jazykový model](how-to-customize-language-model.md), kterým službu Speech tuto slovní zásobu naučíte. Vlastní [akustický model](how-to-customize-acoustic-models.md) může službě Speech pomoct porozumět ne zcela optimálním telefonním spojením.

Další informace o tomto scénáři najdete v článku o [dávkovém přepisu](batch-transcription.md) pomocí služby Speech.

### <a name="voice-bots"></a>Hlasoví roboti

[Roboti](https://dev.botframework.com/) představují oblíbený způsob, jak spojit uživatele s požadovanými informacemi a zákazníky s požadovanými firmami. Když do webu nebo aplikace přidáte konverzační uživatelské rozhraní, zajistíte snadnější nalezení a rychlejší přístup k této funkčnosti. Se službou Speech získává plynulost této konverzace nový rozměr díky tomu, že služba věcně reaguje na mluvené dotazy.

Pokud chcete robota s podporou řeči doplnit o jedinečnou osobnost, můžete mu dát vlastní hlas. Vytvoření vlastního hlasu je dvoustupňový proces. Nejprve [pořídíte nahrávky](record-custom-voice-samples.md) hlasu, který chcete použít. Pak [tyto nahrávky odešlete](how-to-customize-voice-font.md) (společně s textovým přepisem) na [portál pro přizpůsobení hlasu](https://cris.ai/Home/CustomVoice) služby Speech, která se postará o zbytek. Až vlastní hlas vytvoříte, je postup jeho použití v aplikaci zřejmý.

## <a name="next-steps"></a>Další kroky

Získejte klíč předplatného pro službu Speech.

> [!div class="nextstepaction"]
> [Vyzkoušejte si službu Speech zdarma](get-started.md)
