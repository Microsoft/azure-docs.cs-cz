---
title: Hlasové asistenti v systému Windows – nejčastější dotazy
titleSuffix: Azure Cognitive Services
description: Běžné otázky, které se často vyskytují během vývoje Windows Voice agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84457000"
---
# <a name="samples-and-faqs"></a>Ukázky a nejčastější dotazy

## <a name="the-uwp-voice-assistant-sample"></a>Ukázka hlasového asistenta UWP

Ukázka hlasového asistenta UWP je hlasový asistent ve Windows, který slouží k

- předvedení používání rozhraní API pro Windows ConversationalAgent
- Zobrazit osvědčené postupy pro hlasového agenta ve Windows
- Poskytněte přizpůsobitelnou aplikaci a znovu použitelné komponenty pro vaši aplikaci agenta MVP.
- kompletní prostředí hlasového agenta můžete použít spolu s rozhraními API pro Windows ConversationalAgent, která ukazují, jak se dá používat funkce Direct line Speech, společně s architekturou bot nebo vlastními příkazy.

Dokumentace dodaná s ukázkovou aplikací projde cestou kódu pro aktivaci hlasu a správu agenta z požadavků na spuštění prostřednictvím správného vyčištění.

> [!div class="nextstepaction"]
> [Podívejte se na úložiště GitHub pro ukázku UWP.](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Návody kontaktujte Microsoft pro prostředky, jako jsou tokeny funkcí omezeného přístupu a soubory modelů klíčových slov?

Kontaktujte winvoiceassistants@microsoft.com pro vyžádání těchto prostředků.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Moje aplikace se zobrazuje v malém okně při aktivaci pomocí hlasu. Jak můžu přejít z kompaktního zobrazení do celého okna aplikace?

Při první aktivaci aplikace hlasem se spustí kompaktní zobrazení. Pokyny k různým zobrazením a přechodům mezi nimi pro hlasové asistenty ve Windows najdete v tématu [pokyny k návrhu pro ukázkovou aktivaci hlasu](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) .

Pokud chcete přejít z kompaktního zobrazení do úplného zobrazení aplikace, použijte rozhraní appView API `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Proč jsou funkce hlasového asistenta ve Windows povolené jenom pro aplikace UWP?

Vzhledem k rizikům ochrany osobních údajů přidruženým k funkcím, jako je aktivace hlasu, je potřeba, aby byly funkce platformy UWP v dostatečném zabezpečení funkcí hlasového asistenta ve Windows.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>Ukázka hlasového asistenta UWP používá přímý hlasový vstup. Musím pro hlasového asistenta v systému Windows použít funkci Direct line Speech?

Ukázková aplikace UWP byla vyvinutá pomocí přímého Lineového rozpoznávání řeči a sady Speech Services SDK jako ukázka, jak používat službu dialogových oken s možností Windows pro konverzaci. Můžete ale použít libovolnou službu pro ověřování pomocí klíčových slov pro místní a cloudové ověřování, převod řeči na text, dialog robot a převod textu na řeč.