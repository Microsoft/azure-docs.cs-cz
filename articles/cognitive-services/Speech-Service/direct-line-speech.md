---
title: Řeč přímé linky – služba řeči
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, funkcí a omezení pro hlasové asistenty pomocí direct line řeči s sadou Speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6dc125b68af8f9a07731aaae9d858c19c51f934f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402392"
---
# <a name="what-is-direct-line-speech"></a>Co je Direct Line Speech?

**Direct Line Speech** je robustní komplexní řešení pro vytvoření flexibilního a rozšiřitelného hlasového asistenta. Je poháněn Bot Framework a jeho Direct Line řeči kanál, který je optimalizován pro hlas-in, voice-out interakce s roboty.

[Hlasoví asistenti poslouchají](voice-assistants.md) uživatele a reagují na ně a často se ozývou. Používají [převod řeči na text](speech-to-text.md) k přepisu řeči uživatele a poté akce na pochopení textu v přirozeném jazyce. Tato akce často zahrnuje mluvený výstup z pomocníka generovaného [převodem textu na řeč](text-to-speech.md).

Direct Line Speech nabízí nejvyšší úroveň přizpůsobení a sofistikovanosti pro hlasové asistenty. Je určen pro konverzační scénáře, které jsou otevřené, přírodní nebo hybridy dvou s dokončením úkolu nebo použití příkazu a řízení. Tento vysoký stupeň flexibility přichází s větší složitostí a scénáře, které jsou vymezeny na dobře definované úkoly pomocí vstupu v přirozeném jazyce, mohou chtít zvážit [vlastní příkazy (Preview)](custom-commands.md) pro zjednodušené řešení.

## <a name="getting-started-with-direct-line-speech"></a>Začínáme s přímou přímou řečí

Prvním krokem pro vytvoření hlasového asistenta pomocí přímé řeči je [získat klíč předplatného řeči](get-started.md), vytvořit nového robota přidruženého k tomuto předplatnému a připojit robota k kanálu Řeči přímé čáry.

   ![Koncepční diagram toku služby orchestrace řeči přímé čáry](media/voice-assistants/overview-directlinespeech.png "Tok řečového kanálu")

Úplný podrobný návod k vytvoření jednoduchého hlasového asistenta pomocí funkce Direct Line Speech najdete [v kurzu pro pomocníka pro řeč pomocí sady Speech SDK a kanálu Přímé řeči](tutorial-voice-enable-your-bot-speech-sdk.md).

Nabízíme také rychlé starty navržené tak, abyste mohli rychle spouštět kód a učit se api. Tato tabulka obsahuje seznam rychlých startů hlasového asistenta uspořádaných podle jazyka a platformy.

| Rychlý start | Platforma | API – referenční informace |
|------------|----------|---------------|
| C#, UWP | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Procházet](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procházet](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro vytvoření hlasového asistenta je k dispozici na GitHubu. Tyto ukázky pokrývají klientskou aplikaci pro připojení k asistentovi v několika oblíbených programovacích jazycích.

* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)
* [Kurz: Hlasové povolení asistenta pomocí sady Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Přizpůsobení

Hlasoví asistenti vytvoření pomocí služby Rozpoznávání řeči mohou používat celou řadu možností přizpůsobení, které jsou k dispozici pro [převod řeči na text](speech-to-text.md), převod textu na [řeč](text-to-speech.md)a vlastní [výběr klíčových slov](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti vlastního nastavení se liší podle jazyka/národního prostředí (viz [Podporované jazyky](supported-languages.md)).

Direct Line Speech a s ní spojené funkce pro hlasové asistenty jsou ideálním doplňkem [k řešení virtuálního asistenta a podnikové šabloně](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Ačkoli direct line řeč může pracovat s jakýmkoli kompatibilním robotem, tyto prostředky poskytují opakovaně použitelný směrný plán pro vysoce kvalitní konverzační prostředí, stejně jako společné podpůrné dovednosti a modely pro rychlé zahájení.

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
* [Vytvoření a nasazení základního chatbota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Získejte řešení virtuální asistentky a podnikovou šablonu](https://github.com/Microsoft/AI)
