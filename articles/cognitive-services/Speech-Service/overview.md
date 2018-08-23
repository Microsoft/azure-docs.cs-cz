---
title: Co je Speech service (preview)?
description: 'Speech service součástí společnosti Microsoft Cognitive Services, sjednotí několik služeb Azure řeči, které byly dříve k dispozici samostatně: (zahrnující rozpoznávání řeči a převod textu na řeč) pro zpracování řeči Bingu, vlastní řeč a překladu řeči.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "41988455"
---
# <a name="what-is-the-speech-service-preview"></a>Co je Speech service (preview)?

Speech service s jedním oděrem poskytuje vývojářům snadný způsob, jak přidat výkonné funkce, které podporou řeči do svých aplikací. Své aplikace můžete nyní funkcí hlasových příkazů, přepis, diktování, syntézu řeči a překladu řeči.

Speech service využívá k tomu technologií používaných pro ostatní produkty Microsoftu, včetně Cortana a Microsoft Office.

> [!NOTE]
> Speech service je aktuálně ve verzi public preview. Vraťte se sem pravidelně aktualizací do dokumentace, ukázky kódu Další a další.

## <a name="speech-service-features"></a>Funkce služby rozpoznávání řeči

|Funkce|Popis|
|-|-|
|[Převod řeči na text](speech-to-text.md)| Transcribes audiostreamy do textu, který vaše aplikace může přijmout jako vstup. Se také integruje s [služby Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) k odvození z projevy záměru uživatele.|
|[Převod textu na řeč](text-to-speech.md)| Převede prostého textu do mluvené řeči přirozeného to nezní, doručí do vaší aplikace v zvukový soubor. Více hlasů ve pohlaví nebo zvýraznění, jsou k dispozici pro mnoho podporované jazyky. |
|[Překlad řeči](speech-translation.md)| Je možné přeložit zvukové streamy v téměř reálném čase nebo zpracovat zaznamenané řeči. |
|Vlastní převod řeči na text|Převod řeči na text můžete přizpůsobit tak, že vytvoříte vlastní [akustický](how-to-customize-acoustic-models.md) a [jazyk](how-to-customize-language-model.md) modelů a určením vlastních [výslovnost](how-to-customize-pronunciation.md) pravidla. |
|[Vlastní převod textu na řeč](how-to-customize-voice-font.md)|Můžete vytvořit vlastní hlasy pro převod textu na řeč.|
|[Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)| Se zavedením jednotné Speech service Microsoft a příslušní partneři nabízí platformu integrované hardware a software pro optimalizovaný pro vývoj zařízení s podporou řeči |

## <a name="access-to-the-speech-service"></a>Přístup ke službě řeči

Speech service je k dispozici dvě možnosti. [Sada SDK](speech-sdk.md) vyčleňuje podrobnosti síťových protokolů pro jednodušší vývoj na podporovaných platformách. [Rozhraní REST API](rest-apis.md) funguje s libovolným programovacím jazykem, ale neposkytuje všechny funkce nabízené prostřednictvím sady SDK.

|<br>Metoda|Řeč<br>na Text|Text, který se<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Sady SDK](speech-sdk.md)|Ano|Ne|Ano|Knihovny pro konkrétní programovací jazyky, využívat založené na protokolu Websocket získáváním, které zjednodušují vývoj.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché založené na protokolu HTTP rozhraní API, která umožňuje snadno přidat rozpoznávání řeči do vaší aplikace.|

## <a name="speech-scenarios"></a>Scénáře řeči

Několik běžných použití technologii pro řečovou jsou stručně popsané dále. [Sadou SDK pro řeč](speech-sdk.md) je zásadním většina z těchto scénářů.

> [!div class="checklist"]
> * Vytvářejte aplikace aktivované hlasu
> * Přepisy volání nahrávání
> * Implementace hlasové robotů

### <a name="voice-triggered-apps"></a>Aktivuje hlasových aplikacích

Hlasový vstup je skvělý způsob, jak vaše aplikace můžou být flexibilní, bezobslužné a rychle použít. V aplikaci podporou hlasových uživatelé stačí požádat o informace, které chtějí spíše než by bylo potřeba kliknutím nebo klepnutím na Přejít k němu.

Pokud vaše aplikace je určena pro použití širokou veřejností, můžete použít model rozpoznávání řeči směrného plánu poskytovaný službou řeči. Dobrá práce rozpoznávání široké škály přednášející v typické prostředí dělá.

Pokud se vaše aplikace bude používat v určité doméně (například lékařství nebo IT), můžete vytvořit [jazykový model](how-to-customize-language-model.md) představuje Speech service o speciální terminologii používané v aplikaci.

Pokud se vaše aplikace bude používat v hlučného prostředí, například objekt pro vytváření, můžete vytvořit vlastní [akustický model](how-to-customize-acoustic-models.md) lépe umožňující Speech service k rozlišení řeči od šumu.

Začínáme se službou je stejně jednoduché jako stahování [sadou SDK pro řeč](speech-sdk.md) a relevantní [rychlý Start](quickstart-csharp-dotnet-windows.md) článku.

### <a name="transcribe-call-center-recordings"></a>Přepisy volání nahrávání

Nahrávání volání jsou často konzultaci pouze pokud nastane problém s voláním. Se službou Speech je snadné přepisy každý záznam na text. Jakmile se dostanou textu, můžete index snadno je pro [fulltextové vyhledávání](https://docs.microsoft.com/azure/search/search-what-is-azure-search) nebo použít [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) pro zjištění mínění, jazyk a klíčové fráze.

Pokud vaše volání nahrávání často obsahují speciální terminologie (například názvy produktů nebo žargonu IT), můžete vytvořit [jazykový model](how-to-customize-language-model.md) představuje Speech service tohoto slovníku. Vlastní [akustický model](how-to-customize-acoustic-models.md) nápovědy Speech service by rozuměla méně než optimální phone připojení.

Další informace o tomto scénáři, přečtěte si další informace o [batch určené k transkripci](batch-transcription.md) službou Speech.

### <a name="voice-bots"></a>Roboti hlasu

[Roboti](https://dev.botframework.com/) chtějí stále oblíbeným způsobem připojování uživatelů s informacemi a zákazníkům s firmy, mají rádi. Přidáte konverzační uživatelského rozhraní na váš web nebo aplikace, budete jeho funkce snadněji a rychleji se přístup. Se službou Speech tato komunikace zabere na dimenzi fluency skutečně reakcí na mluvené slovo dotazů s řečového.

Přidejte do svého robota hlasové služby jedinečný vzhled (a posílení značky), můžete jí hlasový vstup své vlastní. Vytvoření vlastní hlasový vstup je dvoustupňový proces. Nejprve je potřeba je [zkontrolujte záznamy](record-custom-voice-samples.md) hlasu, jak chcete použít. Pak můžete [odeslání těchto záznamů](how-to-customize-voice-font.md) (spolu s text přepisu) ke službě řeči [hlasové přizpůsobení portálu](https://cris.ai/Home/CustomVoice), které udělá zbytek. Jakmile vytvoříte vlastní hlasové, je jednoduché použití ve vaší aplikaci.

## <a name="next-steps"></a>Další postup

Získání klíče předplatného pro službu rozpoznávání řeči.

> [!div class="nextstepaction"]
> [Bezplatné vyzkoušení Speech service](get-started.md)
