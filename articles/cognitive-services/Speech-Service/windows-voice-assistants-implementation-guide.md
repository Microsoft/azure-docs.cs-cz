---
title: Hlasové asistenti ve Windows – pokyny pro implementaci zámku
titleSuffix: Azure Cognitive Services
description: Pokyny pro implementaci aktivace hlasu a vyšší možnosti zámku pro aplikaci hlasového agenta.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939863"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementace hlasových asistentů ve Windows

Tato příručka vás provede důležitými podrobnostmi implementace pro vytvoření hlasového asistenta v systému Windows.

## <a name="implementing-voice-activation"></a>Implementace aktivace hlasu

Po [nastavení prostředí](how-to-windows-voice-assistants-get-started.md) a učení, [Jak funguje Hlasová aktivace](windows-voice-assistants-overview.md#how-does-voice-activation-work), můžete spustit implementaci hlasové aktivace pro vlastní aplikaci hlasového asistenta.

### <a name="registration"></a>Registrace

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Ujistěte se, že je mikrofon dostupný a přístupný, a sledujte jeho stav.

MVA potřebuje k dispozici mikrofon, aby bylo možné detekovat aktivaci hlasu. Použijte třídy [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)a [MediaCapture](/uwp/api/windows.media.capture.mediacapture) ke kontrole přístupu k ochraně osobních údajů mikrofonu, přítomnosti zařízení a stavu zařízení (například hlasitosti a ztlumení).

### <a name="register-the-application-with-the-background-service"></a>Registrace aplikace ve službě na pozadí

Aby MVA spouštěla aplikaci na pozadí, musí být aplikace zaregistrovaná ve službě na pozadí. [Tady](/windows/uwp/launch-resume/register-a-background-task)najdete kompletní příručku pro registraci služby na pozadí.

### <a name="unlock-the-limited-access-feature"></a>Odemknout funkci omezený přístup

K odemknutí funkce hlasového asistenta použijte klíč funkce pro omezený přístup poskytnutý společností Microsoft. K tomu použijte třídu [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) z Windows SDK.

### <a name="register-the-keyword-for-the-application"></a>Registrace klíčového slova pro aplikaci

Aplikace musí zaregistrovat sám sebe, model klíčových slov a jeho jazyk v systému Windows.

Začněte načtením klíčového slova detektor. V tomto ukázkovém kódu načteme první detektor, ale můžete vybrat konkrétní detektor výběrem z `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Po načtení objektu ActivationSignalDetector zavolejte svou `ActivationSignalDetector.CreateConfigurationAsync` metodu s ID signálu, ID modelu a zobrazovaným názvem pro registraci klíčového slova a načtení aplikace `ActivationSignalDetectionConfiguration` . ID signálu a modelu by měly být identifikátory GUID, které vývojář určil a zůstávají konzistentní pro stejné klíčové slovo.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Ověřte, že je povolené nastavení aktivace hlasu.

Pro použití aktivace hlasu musí uživatel povolit aktivaci hlasu pro svůj systém a povolit aktivaci hlasu pro svoji aplikaci. Nastavení v části nastavení ochrany osobních údajů aktivace hlasu najdete v tématu Nastavení systému Windows. Chcete-li zjistit stav nastavení aktivace hlasu ve vaší aplikaci, použijte instanci `ActivationSignalDetectionConfiguration` z části registrace klíčového slova. Pole [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) v `ActivationSignalDetectionConfiguration` obsahuje hodnotu výčtu, která popisuje stav nastavení aktivace hlasu.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Načtení ConversationalAgentSession k registraci aplikace v systému MVA

`ConversationalAgentSession`Je třída v Windows SDK, která umožňuje vaší aplikaci aktualizovat Windows pomocí stavu aplikace (nečinný, detekuje, poslouchá, pracuje, mluví) a přijímá události, jako je například detekce aktivace a změny stavu systému, jako je například zamykání obrazovky. Načtení instance AgentSession také slouží k registraci aplikace ve Windows jako aktivovatelné pomocí hlasu. Osvědčeným postupem je udržovat jeden odkaz na `ConversationalAgentSession` . K načtení relace použijte `ConversationalAgentSession.GetCurrentSessionAsync` rozhraní API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Naslouchat dvěma aktivačním signálům: OnBackgroundActivated a OnSignalDetected

Systém Windows zavolá vaši aplikaci, když zjistí klíčové slovo jedním ze dvou způsobů. Pokud aplikace není aktivní (to znamená, že nemáte odkaz na neuvolněnou instanci `ConversationalAgentSession` ), pak spustí vaši aplikaci a zavolá metodu OnBackgroundActivated v souboru App. XAML. cs vaší aplikace. Pokud se pole argumenty události `BackgroundActivatedEventArgs.TaskInstance.Task.Name` shoduje s řetězcem "AgentBackgroundTrigger", pak bylo spuštění aplikace aktivované aktivací hlasu. Aplikace musí tuto metodu přepsat a načíst instanci ConversationalAgentSession k signalizaci systému Windows, který je nyní aktivní. Když je aplikace aktivní, bude systém Windows signalizovat výskyt aktivace hlasu pomocí `ConversationalAgentSession.OnSignalDetected` události. Přidejte k této události obslužnou rutinu události hned po načtení `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Ověření klíčového slova

Jakmile je aplikace hlasového agenta aktivována hlasem, dalším krokem je ověření, že bylo rozpoznání klíčového slova platné. Systém Windows neposkytuje řešení pro ověřování pomocí klíčového slova, ale umožňuje hlasovým asistentům přístup ke zvuku z aktivace hypotetické a úplné ověření.

### <a name="retrieve-activation-audio"></a>Načíst zvuk aktivace

Vytvořte [AudioGraph](/uwp/api/windows.media.audio.audiograph) a předejte ho do `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` . Tím se načte zvuková vyrovnávací paměť grafu se zvukem *začínajícím přibližně 3 sekundy, než bylo zjištěno klíčové slovo*. Tento dodatečný úvodní zvuk je zahrnutý k přizpůsobení široké škály délek klíčových slov a rychlosti mluvčího. Pak zpracujte událost [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) z zvukového grafu, aby se načetla zvuková data.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Poznámka: přední zvuk zahrnutý do zvukové vyrovnávací paměti může způsobit selhání ověření klíčového slova. Chcete-li tento problém vyřešit, vystřihujte začátek zvukové vyrovnávací paměti předtím, než odešlete zvuk pro ověření klíčového slova. Toto počáteční ořezávání by mělo být přizpůsobeno každému Pomocníkovi.

### <a name="launch-in-the-foreground"></a>Spustit v popředí

Po úspěšném ověření klíčového slova musí aplikace přejít na popředí a zobrazit uživatelské rozhraní. Zavolejte `ConversationalAgentSession.RequestForegroundActivationAsync` rozhraní API, aby se vaše aplikace přesunula na popředí.

### <a name="transition-from-compact-view-to-full-view"></a>Přechod z kompaktního zobrazení do úplného zobrazení

Při první aktivaci aplikace hlasem se spustí kompaktní zobrazení. Pokyny k různým zobrazením a přechodům mezi nimi pro hlasové asistenty ve Windows najdete v tématu [pokyny k návrhu pro ukázkovou aktivaci hlasu](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) .

Pokud chcete přejít z kompaktního zobrazení do úplného zobrazení aplikace, použijte rozhraní ApplicationView API `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementace nad aktivací zámku

Následující postup se zabývá požadavky na povolení hlasového asistenta v systému Windows, který je možné spustit výše, včetně odkazů na ukázkový kód a pokyny pro správu životního cyklu aplikace.

Pokyny pro návrh nad rámec zámků najdete v [Průvodci osvědčenými postupy](windows-voice-assistants-best-practices.md).

Když aplikace zobrazuje zámek výše, považuje se za "Celoobrazovkový režim". Další informace o implementaci aplikace, která používá celoobrazovkový režim, najdete v [dokumentaci k celoobrazovkovém režimu](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Přechod nad zámkem

Aktivace přes zámek je podobná aktivaci nižší než zámek. Pokud neexistují žádné aktivní instance aplikace, bude spuštěna nová instance na pozadí a `OnBackgroundActivated` v App. XAML. cs bude volána. Pokud existuje instance aplikace, tato instance obdrží oznámení prostřednictvím `ConversationalAgentSession.SignalDetected` události.

Pokud aplikace ještě není zobrazená nad zámkem, musí volat `ConversationalAgentSession.RequestForegroundActivationAsync` . Tím se spustí `OnLaunched` metoda v souboru App. XAML. cs, která by měla přejít k zobrazení, které se zobrazí nad zámkem.

### <a name="detecting-lock-screen-transitions"></a>Zjišťování přechodů na zamykací obrazovce

Knihovna ConversationalAgent v Windows SDK poskytuje rozhraní API k vytvoření stavu zamykací obrazovky a změnám stavu zamykací obrazovky, který je snadno přístupný. Chcete-li zjistit aktuální stav zamykací obrazovky, podívejte se do `ConversationalAgentSession.IsUserAuthenticated` pole. Chcete-li zjistit změny ve stavu zámku, přidejte do události objektu obslužnou rutinu události `ConversationalAgentSession` `SystemStateChanged` . Aktivuje se vždycky, když se obrazovka změní z odemčeno na uzamčeno nebo naopak. Pokud je hodnota argumentů události `ConversationalAgentSystemStateChangeType.UserAuthentication` , změnil se stav zamykací obrazovky.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Zjišťování výše předvolbách pro aktivaci zámku uživatele

Položka aplikace na stránce nastavení ochrany osobních údajů aktivace hlasu obsahuje přepínač pro výše uvedenou funkci zámku. Aby bylo možné aplikaci spustit nad zámkem, bude uživatel muset toto nastavení zapnout. Stav vyšších oprávnění zámku je uložen také v objektu ActivationSignalDetectionConfiguration. Stav AvailabilityInfo. HasLockScreenPermission odráží, zda uživatel udělil oprávnění zámku. Pokud je toto nastavení zakázané, hlasová aplikace může uživatele vyzvat, aby přešli na stránku příslušné nastavení na odkaz MS-Settings: Privacy-voiceactivation, kde najdete pokyny, jak povolit přeinstalaci přes zámek pro aplikaci.

## <a name="closing-the-application"></a>Ukončování aplikace

Aby bylo možné aplikaci řádně zavřít programově během výše nebo pod zámkem, použijte `WindowService.CloseWindow()` rozhraní API. Tím se aktivují všechny metody životního cyklu UWP, včetně potlačení, což umožňuje aplikaci uvolnit její `ConversationalAgentSession` instanci před zavřením.

> [!NOTE]
> Aplikace může být zavřena bez zavření [níže uvedené instance zámku](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). V takovém případě musí výše uvedené zobrazení zámku "vyčistit", což zajistí, že po odemčení obrazovky nejsou k dispozici žádné obslužné rutiny událostí ani úlohy, které by se pokusily manipulovat s výše uvedeným zobrazením zámku.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Navštivte ukázkovou aplikaci hlasového asistenta UWP pro příklady a návod k kódu.](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
