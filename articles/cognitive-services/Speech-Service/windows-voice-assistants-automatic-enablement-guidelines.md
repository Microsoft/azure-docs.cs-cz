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
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987389"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Pokyny pro ochranu osobních údajů pro hlasové asistenty ve Windows

Je důležité, aby uživatelé měli jasné informace o tom, jak se jejich hlasová data shromažďují a používají, a že je důležité, abyste měli kontrolu nad tím, kdy a jak se tato kolekce stane. Tyto základní charakteristiky ochrany osobních údajů – *zveřejnění* a *souhlas* – jsou obzvláště důležité pro hlasové asistenty integrované do Windows s ohledem na povahu jejich používání, která je vždycky poslouchat.

Vývojáři, kteří vytvářejí hlasové asistenty ve Windows, musí do svých aplikací zahrnout jasné prvky uživatelského rozhraní, které odrážejí možnosti poslechu pomocníka.

> [!NOTE]
> V případě, že se vám nepovedlo poskytnout vhodné zveřejnění a souhlas s pomocníkem, včetně po aktualizacích aplikací, může dojít k tomu, že se pomocník stane nedostupným pro aktivaci hlasu, dokud se problémy

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimální požadavky na zahrnutí funkcí

Uživatelé systému Windows mohou zobrazit a řídit dostupnost svých asistentních aplikací v nástroji **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Snímek obrazovky ukazuje možnosti pro řízení dostupnost Cortany. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Položka nastavení ochrany osobních údajů aktivace Windows Voice pro aplikaci pomocníka")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Pokud se má v tomto seznamu zobrazit nárok na zařazení, kontaktujte Microsoft winvoiceassistants@microsoft.com a začněte. Ve výchozím nastavení budou uživatelé muset explicitně povolit aktivaci hlasu pro nového pomocníka v **`Settings > Privacy > Voice Activation`** nástroji, který umožňuje propojení s protokolem `ms-settings:privacy-voiceactivation` . Po spuštění a použití rozhraní API se v seznamu zobrazí povolená aplikace `Windows.ApplicationModel.ConversationalAgent` . Nastavení aktivace hlasu bude možné upravovat, jakmile aplikace získá souhlas s mikrofonem od uživatele.

Vzhledem k tomu, že nastavení ochrany osobních údajů systému Windows zahrnuje informace o tom, jak funguje aktivace hlasu a má standardní uživatelské rozhraní pro řízení oprávnění, jejich zveřejnění a souhlas. Pomocník zůstane v tomto seznamu povolených hodnot, pokud ne:

* V omylu Informujte uživatele o aktivaci hlasu nebo hlasovém zpracování dat pomocníka.
* Nepatřičná rušivá vliv na jiného pomocníka
* Rozdělení jakýchkoli dalších relevantních zásad Microsoftu

Pokud je zjištěna kterákoli z výše uvedených skutečností, může společnost Microsoft odebrat pomocníka ze seznamu povolených aplikací, dokud nebudou vyřešeny problémy.

> [!NOTE]
> Ve všech případech oprávnění k aktivaci hlasu vyžaduje oprávnění mikrofonu. Pokud aplikace pomocníka nemá přístup přes mikrofon, nebude mít nárok na aktivaci hlasu a zobrazí se v nastavení soukromí aktivace hlasu v zakázaném stavu.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Další požadavky na zařazení do souhlasu mikrofonu

Pomocným autorům, kteří chtějí usnadnit a hladce se přihlášeni k aktivaci hlasu, můžou to udělat tak, že splní další požadavky, aby dostatečně splnily informace a souhlasili s tím, že se na stránku nastavení nepřipojí další cesta. Po schválení bude aktivace hlasu okamžitě k dispozici, jakmile uživatel udělí aplikaci pomocníka oprávnění k mikrofonu. Aby to bylo možné, musí pomocníkská aplikace **před** zobrazením souhlasu s mikrofonem (například pomocí `AppCapability.RequestAccessAsync` rozhraní API) provést následující akce:

1. Poskytněte jasnému a výraznému oznámení uživateli, že by aplikace chtěla naslouchat hlasu uživatele pro klíčové slovo, a *to i v případě, že aplikace není spuštěná*a přeje by se jednat o souhlas uživatele.
1. Uveďte relevantní informace o využití dat a zásadách ochrany osobních údajů, jako je odkaz na oficiální prohlášení o zásadách ochrany osobních údajů.
1. Vyhněte se jakékoli direktivě nebo úvodnímu slovu (například "při použití možnosti" klikněte na tlačítko Ano ") v toku zkušeností, který vychází z chování při zachytávání zvuku

Pokud aplikace dosáhne všech výše uvedených možností, je vhodné povolit možnost aktivace hlasu spolu s použitím souhlasu s mikrofonem. Požádejte o winvoiceassistants@microsoft.com Další informace a podívejte se na první prostředí pro použití.

> [!NOTE]
> Aktivace hlasu výše není vhodná pro automatické povolení s přístupem k mikrofonu a bude dál vyžadovat, aby uživatel navštívil stránku soukromí pro aktivaci hlasu, aby mohl pomocníka získat výše.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro hlasové asistenty ve Windows](windows-voice-assistants-best-practices.md)