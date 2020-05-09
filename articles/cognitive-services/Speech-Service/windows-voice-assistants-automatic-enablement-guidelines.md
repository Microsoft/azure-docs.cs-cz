---
title: Pokyny pro ochranu osobních údajů pro hlasové asistenty ve Windows
titleSuffix: Azure Cognitive Services
description: Ve výchozím nastavení se v pokynech povolují aktivace hlasu pro hlasového agenta.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997510"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Pokyny pro ochranu osobních údajů pro hlasové asistenty ve Windows

Je důležité, aby uživatelé měli jasné informace o tom, jak se jejich hlasová data shromažďují a používají, a že je důležité, abyste měli kontrolu nad tím, kdy a jak se tato kolekce stane. Tyto základní charakteristiky ochrany osobních údajů – *zveřejnění* a *souhlas* – jsou obzvláště důležité pro hlasové asistenty integrované do Windows s ohledem na povahu jejich používání, která je vždycky poslouchat.

Vývojáři, kteří vytvářejí hlasové asistenty ve Windows, musí do svých aplikací zahrnout jasné prvky uživatelského rozhraní, které odrážejí možnosti poslechu pomocníka.

> [!NOTE]
> V případě, že se vám nepovedlo poskytnout vhodné zveřejnění a souhlas s pomocníkem, včetně po aktualizacích aplikací, může dojít k tomu, že se pomocník stane nedostupným pro aktivaci hlasu, dokud se problémy 

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimální požadavky na zahrnutí funkcí

Uživatelé systému Windows mohou zobrazit a řídit dostupnost svých asistentních aplikací v **`Settings > Privacy > Voice activation`** nástroji.

 > [!div class="mx-imgBorder"]
 > [![soukromí – seznam aplikací](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Položka nastavení ochrany osobních údajů aktivace Windows Voice pro aplikaci pomocníka")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Aby bylo možné zahrnout do tohoto seznamu nárok na zařazení, musí aplikace splňovat tyto podmínky:

1. Výrazně sdělí svým uživatelům, že bude naslouchat klíčové slovo, i když aplikace není spuštěná a co toto klíčové slovo je
1. Zahrňte popis způsobu použití hlasových dat uživatele, včetně odkazu nebo odkazu na relevantní zásady ochrany osobních údajů.
1. Informování uživatelů, že kromě jakýchkoli nastavení v aplikaci mohou uživatelé zobrazit a upravit své volby ochrany osobních údajů v **`Settings > Privacy > Voice activation`**, volitelně zahrnout odkaz na `ms-settings:privacy-voiceactivation` protokol pro přímý přístup

Po splnění těchto požadavků a získání schválení od Microsoftu se v seznamu aplikací pro aktivaci hlasu zobrazí aplikace pomocníka po registraci s `Windows.ApplicationModel.ConversationalAgent` rozhraními API a uživatelé budou moct udělit souhlas k aplikaci pro aktivaci klíčového slova. Ve výchozím nastavení jsou `Off` obě tato nastavení a vyžadují, aby uživatel ručně navštívil stránku nastavení, aby povolil.

> [!NOTE]
> Ve všech případech oprávnění k aktivaci hlasu vyžaduje oprávnění mikrofonu. Pokud aplikace pomocníka nemá přístup přes mikrofon, nebude mít nárok na aktivaci hlasu a zobrazí se v nastavení soukromí aktivace hlasu v zakázaném stavu.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Další požadavky na zařazení do souhlasu mikrofonu

Pomocným autorům, kteří chtějí svým uživatelům snazší a plynulejší přihlédnout k aktivaci hlasu, může to udělat tak, že splní několik dalších požadavků na výše uvedené. Po splnění těchto kroků se standardně nastaví výchozí nastavení pro aktivaci hlasu zařízení, které `On` je odemčené zařízením (a pouze jednou), bude aplikaci udělen přístup k mikrofonu. Tím se v nastavení před aktivací pomocníka neodstraní potřeba další cesta k nastavení.

Další požadavky jsou, že aplikace pomocníka musí:

1. **Před** zobrazením výzvy k zadání souhlasu s mikrofonem (například `AppCapability.RequestAccessAsync` pomocí rozhraní API) poskytněte uživateli výrazný náznak, že aplikace pomocníka chce naslouchat hlasu uživatele pro klíčové slovo, a to i v případě, že aplikace není spuštěná a přeje by se jednat o souhlas uživatele.
2. Zahrnout všechny relevantní informace o využití dat a zásadách ochrany osobních údajů **před** vyžádáním přístupu k mikrofonu nebo pomocí `Windows.ApplicationModel.ConversationalAgent` rozhraní API
3. Vyhněte se jakékoli direktivě nebo úvodnímu slovu (například "klikněte na tlačítko Ano na následující výzvě") v toku zkušeností při zachytávání chování zachycení zvuku a vyžádání oprávnění

Po splnění těchto požadavků se v seznamu aplikací, které mají nárok na aktivaci hlasu v případě udělení přístupu k mikrofonu `enabled` , zobrazí v seznamu aplikací, které mají nárok na aktivaci hlasu ve stavu.

> [!NOTE]
> Aktivace hlasu výše není vhodná pro automatické povolení s přístupem k mikrofonu a bude dál vyžadovat, aby uživatel navštívil stránku soukromí pro aktivaci hlasu, aby mohl pomocníka získat výše.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro hlasové asistenty ve Windows](windows-voice-assistants-best-practices.md)