---
title: Hlasová asistenti ve Windows – začít
titleSuffix: Azure Cognitive Services
description: Postup pro zahájení vývoje hlasového agenta systému Windows, včetně odkazu na vzorový rychlý Start kódu.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997378"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Začínáme se hlasovými asistenty ve Windows

Tento průvodce vás provede jednotlivými kroky pro zahájení vývoje hlasového asistenta ve Windows.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Pokud chcete začít vyvíjet hlasového asistenta pro Windows, budete se muset ujistit, že máte správné vývojové prostředí.

- **Visual Studio:**   Budete muset nainstalovat [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition nebo novější.
- **Verze Windows**: počítač s rychlým sestavením Windows Insider Fast Ring pro Windows a verzí Windows SDK Windows Insider.Tento vzorový kód se ověří při práci na buildu Windows Insider release build 19025.vb_release_analog .191112-1600 pomocí Windows SDK 19018.Jakékoli sestavení nebo sada SDK nad zadanými verzemi by měly být kompatibilní.
- **Vývojové nástroje pro UWP**: Univerzální platforma Windows vývojové úlohy v aplikaci Visual Studio.Pokud chcete připravit počítač pro vývoj aplikací UWP, podívejte se na stránku s [nastavením](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) pro UWP.
- **Pracovní mikrofon a zvukový výstup**

## <a name="obtain-resources-from-microsoft"></a>Získání prostředků od Microsoftu

Některé prostředky nezbytné pro kompletně přizpůsobeného hlasového agenta ve Windows budou vyžadovat prostředky od Microsoftu. [Ukázka hlasového asistenta UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) poskytuje ukázkové verze těchto prostředků pro počáteční vývoj a testování, takže tento oddíl není potřebný pro počáteční vývoj.

- **Model klíčového slova:**   Aktivace hlasu vyžaduje, aby od Microsoftu byl model klíčových slov ve formě souboru. bin. Soubor. bin uvedený v ukázce hlasového asistenta UWP je vyškolený pro klíčové slovo contoso.
- **Token funkce omezeného přístupu:** Vzhledem k tomu, že rozhraní API ConversationalAgent poskytují přístup ke zvukovým mikrofonům, jsou chráněna omezeními funkcí omezeného přístupu.Pokud chcete použít funkci omezeného přístupu, budete muset získat token funkcí s omezeným přístupem, který je připojený k identitě balíčku vaší aplikace od Microsoftu.

## <a name="establish-a-dialog-service"></a>Vytvoření služby dialogu

Pro kompletní prostředí hlasového asistenta bude aplikace potřebovat službu dialog

- Zjištění klíčového slova v daném zvukovém souboru
- Poslech vstupu uživatele a jeho převod na text
- Zadat text na robota
- Přeložit textovou odpověď robota na zvukový výstup

Toto jsou požadavky na vytvoření základní služby dialogových oken pomocí funkce Direct line Speech.

- **Předplatné služby Speech Services:** Předplatné pro rozpoznávání řeči hlasové služby pro převod řeči na text a konverzi textu na řeč. Vyzkoušejte [si](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)službu Speech Services zdarma.
- Robot **Framework bot bot:**  Robot vytvořený pomocí rozhraní bot Framework verze 4,2 nebo vyšší, který je přihlášený k odběru hlasového [řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech) , aby umožnil hlasové vstupy a výstupy. [Tato příručka](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) obsahuje podrobné pokyny, jak nastavit "echo robot" a přihlásit se k odběru pomocí funkce Direct line Speech. Další informace o tom, jak vytvořit přizpůsobenou robota, najdete [v tomto tématu](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) , abyste si ho přihlásili k odběru pomocí [tohoto postupu:](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk)

## <a name="try-out-the-sample-app"></a>Vyzkoušejte si ukázkovou aplikaci

S vaším předplatným klíčového slova a číslem bot robota pro hlasové služby jste připraveni vyzkoušet [ukázku hlasového asistenta UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Podle pokynů v souboru Readme spusťte aplikaci a zadejte své přihlašovací údaje.

## <a name="create-your-own-voice-assistant-for-windows"></a>Vytvoření vlastního hlasového asistenta pro Windows

Jakmile obdržíte token funkcí s omezeným přístupem a soubor bin od Microsoftu, můžete začít používat vlastní hlasového asistenta v systému Windows.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si příručku implementace hlasového pomocníka](windows-voice-assistants-implementation-guide.md)
