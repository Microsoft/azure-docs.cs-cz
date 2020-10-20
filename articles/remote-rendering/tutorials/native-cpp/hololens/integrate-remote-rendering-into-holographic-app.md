---
title: Integrace vzdáleného vykreslování do holografické aplikace v/DirectX11 C++
description: Vysvětluje, jak integrovat vzdálené vykreslování do obyčejné/DirectX11 holografické aplikace v jazyce C++, jak je vytvořeno pomocí Průvodce projektem sady Visual Studio.
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 56e889778e3b598dc4ded5f64eef20101c542b6a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207508"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Kurz: integrace vzdáleného vykreslování do holografické aplikace HoloLens

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Použití sady Visual Studio k vytvoření holografické aplikace, která se dá nasadit na HoloLens
> * Přidejte potřebné fragmenty kódu a nastavení projektu pro kombinování místního vykreslování pomocí vzdáleného vykresleného obsahu

Tento kurz se zaměřuje na přidání nezbytných bitů do nativního `Holographic App` vzorku pro kombinování místního vykreslování se vzdáleným vykreslováním Azure. Jediný typ zpětné vazby v této aplikaci je prostřednictvím panelu výstup ladění v aplikaci Visual Studio, takže se doporučuje spustit ukázku ze sady Visual Studio. Přidání správné zpětné vazby v aplikaci je nad rámec této ukázky, protože sestavování dynamického textového panelu od začátku zahrnuje spoustu kódů. Dobrým výchozím bodem je třída `StatusDisplay` , která je součástí [ukázkového projektu aktéra vzdálené komunikace na GitHubu](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Předem zakonzervovaná verze tohoto kurzu používá místní kopii této třídy.

> [!TIP]
> [Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje výsledek tohoto kurzu jako projekt sady Visual Studio, který je připravený k použití. Je také obohacen se správnými chybami a vytvářením zpráv o stavu prostřednictvím třídy uživatelského rozhraní `StatusDisplay` . V rámci tohoto kurzu jsou všechny dodatky specifické pro ARR vymezeny nástrojem `#ifdef USE_REMOTE_RENDERING`  /  `#endif` , takže je snadné identifikovat doplňky vzdáleného vykreslování.

## <a name="prerequisites"></a>Požadavky

Pro tento kurz potřebujete:

* Informace o vašem účtu (ID účtu, klíč účtu, ID předplatného). Pokud účet nemáte, [vytvořte účet](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Nejnovější verzi sady Visual Studio 2019 [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Visual Studio Tools pro Mixed reality](/windows/mixed-reality/install-the-tools). Konkrétně jsou nutné následující instalace *úloh* :
  * **Vývoj desktopových aplikací v C++**
  * **Vývoj Univerzální platforma Windows (UWP)**
* Šablony aplikací Windows Mixed reality pro Visual Studio [(Stáhnout)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Vytvoření nové ukázkové holografické aplikace

Jako první krok vytvoříme burzovní vzorek, který je základem pro integraci vzdáleného vykreslování. Otevřete Visual Studio a vyberte vytvořit nový projekt a vyhledejte text "holografická aplikace DirectX 11 (univerzální pro Windows) (C++/WinRT)."

![Vytvoření nového projektu](media/new-project-wizard.png)

Zadejte název projektu dle vašeho výběru, zvolte cestu a klikněte na tlačítko vytvořit.
V novém projektu přepněte konfiguraci na **"ladit/ARM64"**. Nyní byste měli být schopni kompilovat a nasazovat na připojené zařízení HoloLens 2. Pokud ho spustíte na HoloLens, měli byste vidět rotující datovou krychli před vámi.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Přidání závislostí vzdáleného vykreslování přes NuGet

Prvním krokem přidání možností vzdáleného vykreslování je přidání závislostí na straně klienta. Relevantní závislosti jsou k dispozici jako balíček NuGet.
V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a v místní nabídce vyberte **"spravovat balíčky NuGet..."** .

V dialogovém okně výzvy vyhledejte balíček NuGet s názvem **Microsoft. Azure. RemoteRendering. cpp**:

![Vyhledat balíček NuGet](media/add-nuget.png)

a přidejte ho do projektu tak, že ho vyberete a pak stisknete tlačítko nainstalovat.

Balíček NuGet přidá závislosti vzdáleného vykreslování do projektu. Konkrétně se jedná o tyto:
* Odkaz na klientskou knihovnu (RemoteRenderingClient. lib).
* Nastavte závislosti. dll.
* Nastavte správnou cestu k adresáři include.

## <a name="project-preparation"></a>Příprava projektu

Musíme udělat v existujícím projektu malé změny. Tyto změny jsou drobné, ale bez nich nebude vzdálené vykreslování fungovat.

### <a name="enable-multithread-protection-on-directx-device"></a>Povolit na zařízení DirectX ochranu více vlákny
`DirectX11`Zařízení musí mít povolenou ochranu s více vlákny. Pokud ho chcete změnit, otevřete soubor DeviceResources. cpp ve složce "Common" a vložte následující kód na konec funkce `DeviceResources::CreateDeviceResources()` :

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Povolení možností sítě v manifestu aplikace
Pro nasazenou aplikaci je nutné explicitně povolit možnosti sítě. Bez toho, aby se nakonfigurovali, budou dotazy na připojení mít za následek vypršení časových limitů. Chcete-li povolit, dvakrát klikněte na `package.appxmanifest` položku v Průzkumníkovi řešení. V následujícím uživatelském rozhraní přejdete na kartu **Možnosti** a vyberte:
* Internet (klient & Server)
* Internet (klient)

![Možnosti sítě](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrace vzdáleného vykreslování

Teď, když je projekt připravený, můžeme začít s kódem. Dobrým vstupním bodem do aplikace je třída `HolographicAppMain` (soubor HolographicAppMain. h/cpp), protože má všechny nezbytné háky pro inicializaci, zrušení inicializace a vykreslování.

### <a name="includes"></a>Zahrnuje

Začneme přidáním potřebných součástí. Do souboru HolographicAppMain. h přidejte následující zahrnuté soubory:

```cpp
#include <AzureRemoteRendering.h>
```

... a tyto další `include` direktivy do souboru HolographicAppMain. cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Pro jednoduchost kódu definujeme následující zástupce oboru názvů v horní části souboru HolographicAppMain. h za `include` direktivami:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Tato zkratka je užitečná, takže nemusíme vypsat celý obor názvů všude, ale pořád dokáže rozpoznat datové struktury specifické pro ARR. Samozřejmě můžeme také použít `using namespace...` direktivu.

### <a name="remote-rendering-initialization"></a>Inicializace vzdáleného vykreslování
 
Musíme pro relaci uchovávat několik objektů atd. během životnosti aplikace. Doba života se shoduje se životností `HolographicAppMain` objektu aplikace, proto přidáme naše objekty jako členy do třídy `HolographicAppMain` . Dalším krokem je přidání následujících členů třídy do souboru HolographicAppMain. h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Vhodným místem pro vlastní implementaci je konstruktor třídy `HolographicAppMain` . Musíme provést tři typy inicializace:
1. Jednorázová inicializace systému vzdáleného vykreslování
1. Vytvoření front-endu
1. Vytvoření relace

Vše v konstruktoru provádíme postupně. V případech použití v reálném čase ale může být vhodné provést tyto kroky samostatně.

Přidejte následující kód na začátek těla konstruktoru v souboru HolographicAppMain. cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // create a new session
            RR::RenderingSessionCreationParams init;
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->GetStatus() == RR::Result::Success)
                    {
                        SetNewSession(handler->GetResult());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

Kód volá členské funkce `SetNewSession` a `SetNewState` , které budeme implementovat v dalším odstavci spolu se zbytkem stavového strojového kódu.

Všimněte si, že přihlašovací údaje jsou v ukázce pevně zakódované a je třeba je vyplnit na místě ([ID účtu, klíč účtu](../../../how-tos/create-an-account.md#retrieve-the-account-information)a [doména](../../../reference/regions.md)).

Zrušení inicializace je symetrické a v obráceném pořadí na konci těla destruktoru:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Stavový počítač

Ve vzdáleném vykreslování mají klíčové funkce vytvořit relaci a načíst model asynchronní funkce. Abychom se k tomu přihlédli, potřebujeme jednoduchý Stavový počítač, který v podstatě přechází z následujících stavů automaticky:

*Vytvoření relace inicializace->-> spuštění relace > načítání modelu (s průběhem)*

Proto jako další krok přidáme do třídy bit zpracování stavových počítačů. Deklarujeme náš vlastní výčet `AppConnectionStatus` pro různé stavy, ve kterých může být naše aplikace. Je podobný `RR::ConnectionStatus` , ale má další stav pro nezdařené připojení.

Do deklarace třídy přidejte následující členy a funkce:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::AzureSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

Na straně implementace v souboru. cpp přidejte tyto tělo funkce:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = async->GetStatus();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::AzureSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Actions();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Aktualizace za jednotlivé snímky

Musíme aktualizovat klienta jednou pro každou simulaci a provést některé další aktualizace stavu. Funkce `HolographicAppMain::Update` poskytuje dobrý vidlici pro aktualizace pro jednotlivé snímky.

#### <a name="state-machine-update"></a>Aktualizace stavového počítače

Musíme se dotázat na stav relace a zjistit, jestli se převedla do `Ready` stavu. Pokud jsme se úspěšně připojili, nakonec zahájíme načítání modelu prostřednictvím `StartModelLoading` .

Do těla funkce přidejte následující kód `HolographicAppMain::Update` :

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                if (auto propAsync = m_session->GetPropertiesAsync())
                {
                    m_sessionPropertiesAsync = *propAsync;
                    m_sessionPropertiesAsync->Completed([this](const RR::ApiHandle<RR::SessionPropertiesAsync>& async)
                        {
                            if (async->GetStatus() == RR::Result::Success)
                            {
                                auto res = async->GetResult();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::ConnectToRuntimeParams init;
                                    init.ignoreCertificateValidation = false;
                                    init.mode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectToRuntime(init);
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
    
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                            }
                            m_sessionPropertiesAsync = nullptr; // next try
                            m_needsStatusUpdate = true;
                        });
                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Aktualizace systému souřadnic

Potřebujeme souhlasit se službou vykreslování, která se má použít v systému souřadnic. Pro přístup k systému souřadnic, který chceme použít, potřebujeme `m_stationaryReferenceFrame` , aby byl vytvořen na konci funkce `HolographicAppMain::OnHolographicDisplayIsAvailableChanged` .

Tento systém souřadnic se obvykle nemění, takže se jedná o jednorázovou inicializaci. Je nutné ji volat znovu, pokud vaše aplikace změní systém souřadnic.

Kód výše nastaví souřadnicový systém jednou ve funkci hned po tom, co `Update` máme referenční systém souřadnic a připojenou relaci.

#### <a name="camera-update"></a>Aktualizace kamery

Musíme aktualizovat roviny klipu kamery tak, aby byl server kamera udržován synchronizovaný s místním fotoaparátem. Můžeme to udělat na konci `Update` funkce:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Vykreslování

Poslední věc, kterou je potřeba udělat, je vyvoláno vykreslování vzdáleného obsahu. Toto volání musíme provést v přesném pravém umístění v rámci kanálu vykreslování, poté, co cíl vykreslování vymažete a nastavíte zobrazení. Do funkce Zamknout dovnitř vložte následující fragment kódu `UseHolographicCameraResources` `HolographicAppMain::Render` :

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Exiting check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Spuštění ukázky

Ukázka by nyní měla být ve stavu, ve kterém se zkompiluje a spustí.

Když se ukázka spustí správně, zobrazí se před vámi a po vytvoření nějakého modelu a jeho načtení model motoru, který se nachází v aktuální pozici hlavní pozice. Vytvoření relace a načítání modelu může trvat až několik minut. Aktuální stav je zapsán pouze do panelu výstupu sady Visual Studio. Proto je doporučeno spustit ukázku ze sady Visual Studio.

> [!CAUTION]
> Klient se odpojí ze serveru, když funkce Tick není volána po dobu několik sekund. Takže aktivace zarážek může velmi snadno způsobit odpojení aplikace.

Pro správné zobrazení stavu pomocí textového panelu se podívejte na verzi tohoto kurzu předem zakonzervované na GitHubu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o všech krocích, které je potřeba pro přidání vzdáleného vykreslování do/DirectX11 ukázky aplikace pro vytváření **holografických aplikací** C++.
Pokud chcete převést svůj vlastní model, přečtěte si následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý start: Převod modelu pro vykreslování](../../../quickstarts/convert-model.md)