---
title: Přímý line Speech – Speech Service
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro hlasové asistenty pomocí přímého Lineového rozpoznávání se sadou Speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: e8c7e21b0784aacc85bd02e3c1702e1a710e76ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021877"
---
# <a name="what-is-direct-line-speech"></a>Co je Direct Line Speech?

**Přímý line Speech** je robustní ucelené řešení pro vytváření flexibilního a rozšiřitelného hlasového asistenta. Používá se pro robot Framework a jeho přímý spojnicový hlasový kanál, který je optimalizovaný pro hlasové interakce hlasu s roboty.

[Hlasové asistenti](voice-assistants.md) naslouchají uživatelům a probírají akci v reakci, často se domluví zpět. Používají [Převod řeči na text](speech-to-text.md) k přepisovat řeči uživatele a pak provádějí akci s použitím přirozeného jazyka pro porozumění textu. Tato akce často zahrnuje mluvený výstup z pomocníka generovaného [převodem textu na řeč](text-to-speech.md).

Přímý line Speech nabízí nejvyšší úrovně přizpůsobení a sofistikovanější pro hlasové asistenty. Je navržený pro konverzační scénáře, které jsou otevřené, přirozené nebo hybridy těchto dvou s dokončováním úkolů nebo pomocí příkazu a řízení. Tento vysoký stupeň flexibility přináší větší složitost a scénáře, které jsou vymezeny na dobře definované úlohy pomocí vstupu v přirozeném jazyce, můžou pro zjednodušené prostředí řešení zvážit [vlastní příkazy (Preview)](custom-commands.md) .

## <a name="getting-started-with-direct-line-speech"></a>Začínáme s funkcí Direct line Speech

Prvním postupem při vytváření hlasového asistenta pomocí funkce Direct line Speech je [získání klíče předplatného pro rozpoznávání řeči](overview.md#try-the-speech-service-for-free), vytvoření nové robota přidruženého k tomuto předplatnému a připojení robota k kanálu přímého line Speech.

   ![Koncepční diagram toku služby orchestrace zpracování textu v přímém řádku](media/voice-assistants/overview-directlinespeech.png "Tok kanálu řeči")

Podrobný průvodce vytvořením jednoduchého hlasového pomocníka pomocí funkce Direct line Speech najdete [v kurzu rozpoznávání řeči – povolení robota se sadou Speech SDK a kanálu Direct line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Nabízíme také rychlé starty, které jsou navržené tak, aby vám poskytovaly kód a rychle přeučení rozhraní API. Tato tabulka obsahuje seznam rychlých startů hlasového asistenta uspořádaných podle jazyka a platformy.

| Rychlý start | Platforma | referenční dokumentace k rozhraní API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procházet](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Procházet](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Procházet](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro vytvoření hlasového asistenta je k dispozici na GitHubu. Tyto ukázky zahrnují klientskou aplikaci pro připojení k vašemu asistentovi v několika oblíbených programovacích jazycích.

* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)
* [Kurz: hlas – povolení pomocníka se sadou Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Přizpůsobení

Hlasové asistenti sestavené pomocí služby Speech Services můžou používat celou škálu možností přizpůsobení, které jsou k dispozici pro [Převod řeči na text](speech-to-text.md), [Převod textu na řeč](text-to-speech.md)a [vlastní výběr klíčového slova](./custom-keyword-basics.md).

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](./language-support.md)).

Funkce Direct line Speech a její přidružená funkce pro hlasové asistenty jsou ideálním doplňkem k [řešení Virtual Assistant a šabloně organizace](/azure/bot-service/bot-builder-enterprise-template-overview). I když je přímým line Speech možné pracovat s jakýmkoli kompatibilním robotem, poskytují tyto prostředky opakovaně použitelný směrný plán pro vysoce kvalitní konverzační prostředí a také společné podpůrné dovednosti a modely pro rychlé zahájení práce.

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
* [Získat sadu Speech SDK](speech-sdk.md)
* [Vytvoření a nasazení základního robota](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Získat řešení a šablonu společnosti Virtual Assistant](https://github.com/Microsoft/AI)