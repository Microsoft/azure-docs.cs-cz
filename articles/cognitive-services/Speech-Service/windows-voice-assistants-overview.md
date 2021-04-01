---
title: Hlasové asistenti ve Windows – přehled
titleSuffix: Azure Cognitive Services
description: Přehled hlasových asistentů ve Windows, včetně dostupných možností a prostředků pro vývoj
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95024751"
---
# <a name="voice-assistants-on-windows"></a>Hlasové asistenti ve Windows

Ve Windows 10 verze 2004 a novějších můžou aplikace hlasového asistenta využít výhod rozhraní Windows ConversationalAgent API k dosažení kompletního prostředí pomocníka s podporou hlasu.

## <a name="voice-assistant-features"></a>Funkce hlasového asistenta

Aplikace hlasového agenta je možné aktivovat mluveným klíčovým slovem, které umožňuje praktické zážitky na hlas. Aktivace hlasu funguje, když je aplikace zavřená a když je obrazovka uzamčená.

Systém Windows navíc poskytuje sadu nastavení ochrany osobních údajů pro aktivaci hlasu, která uživatelům poskytuje kontrolu nad aktivací hlasu a nad aktivací na základě aplikace.

Po aktivaci hlasu bude Windows správně spravovat několik aktivních agentů a upozorní každého hlasového asistenta, pokud dojde k jeho přerušení nebo deaktivaci. Díky tomu můžou aplikace správně spravovat přerušení a další události mezi agenty.

## <a name="how-does-voice-activation-work"></a>Jak funguje Hlasová aktivace?

Modul runtime aktivace agenta (AAR) je nepřetržitý proces ve Windows, který spravuje aktivaci aplikace na mluveném klíčovém slově nebo stisknutí tlačítka. Spouští se systémem Windows, pokud je v systému k dispozici alespoň jedna aplikace, která je zaregistrována v systému. Aplikace komunikují s AAR prostřednictvím rozhraní API ConversationalAgent v Windows SDK.

Když uživatel domluví klíčové slovo, zobrazí se v systému spotter klíčové slovo software nebo hardware, které AAR, že se zjistilo klíčové slovo, které poskytuje ID klíčového slova. AAR zase pošle požadavek na BackgroundService, aby se spustila aplikace s odpovídajícím ID aplikace.

### <a name="registration"></a>Registrace

Při prvním spuštění aplikace aktivované hlasem zaregistruje ID aplikace a informace o klíčových slovech prostřednictvím rozhraní API ConversationalAgent. AAR registruje všechny konfigurace v globálním mapování s klíčovým slovem hardware nebo software spotter v systému, aby bylo možné detekovat klíčové slovo aplikace. Aplikace také [zaregistruje službu na pozadí](/windows/uwp/launch-resume/register-a-background-task).

Všimněte si, že to znamená, že aplikaci nelze aktivovat pomocí hlasu, dokud nebude spuštěna jednou a bylo povoleno dokončení registrace.

### <a name="receiving-an-activation"></a>Příjem aktivace

Při přijetí požadavku z AAR služba na pozadí spustí aplikaci. Aplikace obdrží signál prostřednictvím metody OnBackgroundActivated životního cyklu v `App.xaml.cs` rámci jedinečného argumentu události. Tento argument oznamuje aplikaci, že byla aktivována nástrojem AAR a že má spustit ověřování pomocí klíčového slova.

Pokud aplikace úspěšně ověří klíčové slovo, může vytvořit žádost, která se zobrazí v popředí. Po úspěšném dokončení tohoto požadavku aplikace zobrazí uživatelské rozhraní a pokračuje v interakci s uživatelem.

AAR stále signalizuje aktivní aplikace, když je jejich klíčové slovo mluvené. Místo signalizace metodou životního cyklu v `App.xaml.cs` , ale signalizuje prostřednictvím události v rozhraních ConversationalAgent API.

### <a name="keyword-verification"></a>Ověření klíčového slova

Klíčové slovo spotter, které aktivuje spuštění aplikace, dosáhlo nízké spotřeby energie tím, že zjednodušuje model klíčových slov. To umožňuje, aby klíčové slovo spotter bylo "Always On" bez vysokého dopadu na výkon, ale také znamená, že klíčové slovo spotter bude pravděpodobně mít vysoký počet "false Akceptuj", kde zjistí klíčové slovo, a to i v případě, že žádné klíčové slovo nebylo mluvené. To je důvod, proč systém aktivace hlasem spouští aplikaci na pozadí: aby aplikace mohla mít možnost ověřit, že klíčové slovo bylo mluvené před přerušením aktuální relace uživatele. AAR uloží zvuk, protože bylo několik sekund předtím, než bylo klíčové slovo Spotted a zpřístupňuje je aplikaci. Aplikace může tuto možnost použít ke spuštění spolehlivější klíčové slovo spotter na stejném zvukovém zařízení.

## <a name="next-steps"></a>Další kroky

- **Přečtěte si pokyny pro návrh:** Naše [pokyny pro návrh](windows-voice-assistants-best-practices.md) rozloží klíčovou práci potřebnou k tomu, aby poskytovala nejlepší možné prostředí pro aktivaci hlasu ve Windows 10.
- **Navštivte stránku Začínáme:** Začněte [zde](how-to-windows-voice-assistants-get-started.md) , pokud chcete začít s implementací hlasových asistentů v systému Windows, od nastavení vývojového prostředí prostřednictvím úvodní Příručky k implementaci.
- **Vyzkoušejte si ukázkovou aplikaci**: Chcete-li se podívat na tyto možnosti sami, přejděte na stránku [ukázek hlasového pomocníka UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) a postupujte podle pokynů pro získání ukázkového klienta se systémem.