---
title: Grafika – vazba
description: Nastavení grafických vazeb a případů použití
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681880"
---
# <a name="graphics-binding"></a>Grafika – vazba

Aby bylo možné používat vzdálené vykreslování Azure ve vlastní aplikaci, musí být integrovány do kanálu vykreslování aplikace. Tato integrace je odpovědností vazby grafiky.

Po nastavení vám vazba grafiky poskytne přístup k různým funkcím, které mají vliv na vykreslený obrázek. Tyto funkce mohou být rozděleny do dvou kategorií: Obecné funkce, které jsou vždy dostupné a specifické funkce, které jsou relevantní pouze `Microsoft.Azure.RemoteRendering.GraphicsApiType`pro vybrané.

## <a name="graphics-binding-in-unity"></a>Vazba grafiky v Unity

V Unity je celá vazba zpracována pomocí předané `RemoteUnityClientInit` struktury `RemoteManagerUnity.InitializeManager`. Chcete-li nastavit režim grafiky, `GraphicsApiType` musí být pole nastaveno na zvolenou vazbu. Pole se vyplní automaticky v závislosti na tom, jestli je k dispozici XRDevice. Chování se dá ručně přepsat pomocí následujícího chování:

* **HoloLens 2**: je vždycky používána vazba grafiky [Windows Mixed reality](#windows-mixed-reality) .
* **Plochá desktopová aplikace UWP**: [simulace](#simulation) se vždycky používá. Pokud chcete použít tento režim, nezapomeňte postupovat podle kroků v části [kurz: nastavení projektu Unity od začátku](../tutorials/unity/project-setup.md).
* **Editor Unity**: [simulace](#simulation) se vždycky používá, pokud se nepřipojí náhlavní souprava WMR VR. v takovém případě se zakáže, aby bylo možné ladit části aplikace související s nezávislou na Arr. Viz také [holografická Vzdálená komunikace](../how-tos/unity/holographic-remoting.md).

Jediná jiná relevantní část pro Unity přistupuje k [základní vazbě](#access). všechny ostatní níže uvedené oddíly lze vynechat.

## <a name="graphics-binding-setup-in-custom-applications"></a>Nastavení vazeb grafiky ve vlastních aplikacích

Chcete-li vybrat vazbu grafiky, proveďte následující dva kroky: nejprve je nutné, aby byla vazba grafiky staticky inicializována při inicializaci programu:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Výše uvedené volání je nezbytné k inicializaci vzdáleného vykreslování Azure do holografických rozhraní API. Tato funkce musí být volána před voláním jakéhokoli holografického rozhraní API a před tím, než budou k dispozici další rozhraní API pro vzdálené vykreslení. Podobně by měla být volána odpovídající funkce `RemoteManagerStatic.ShutdownRemoteRendering();` de-init po tom, co již nejsou volána žádná holografická rozhraní API.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Přístup k vazbě grafiky

Po nastavení klienta je k základní datové vazbě možné přistupovat pomocí `AzureSession.GraphicsBinding` metody getter. Jako příklad můžete získat statistiku posledního snímku takto:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Rozhraní API grafiky

V `WmrD3D11` současné době existují dvě grafická rozhraní API, která lze vybrat `SimD3D11`a. Třetí `Headless` existuje, ale na straně klienta se ještě nepodporuje.

### <a name="windows-mixed-reality"></a>Windows Mixed reality

`GraphicsApiType.WmrD3D11`je výchozí vazba spouštěná na HoloLens 2. Vytvoří `GraphicsBindingWmrD3d11` vazbu. V tomto režimu se vzdálené vykreslování Azure zavěsí přímo do holografických rozhraní API.

Chcete-li získat přístup k odvozeným grafickým vazbám, musí být základem `GraphicsBinding` přetypování.
K použití vazby WMR je potřeba udělat dvě věci:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informování o vzdáleném vykreslování použitého systému souřadnic

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Tam, kde `ptr` výše musí být ukazatel na nativní `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` objekt, který definuje systém souřadnic World Space, ve kterém jsou vyjádřeny souřadnice v rozhraní API.

#### <a name="render-remote-image"></a>Vykreslit vzdálenou bitovou kopii

Na začátku každého snímku musí být vzdálený rámec vykreslen do vyrovnávací paměti pro zpětnou kopii. To se provádí voláním `BlitRemoteFrame`metody, která do aktuálně vázaného cíle vykreslování vyplní informace o barvě a hloubce. Proto je důležité, aby se to dokončilo po vytvoření vazby back-bufferu jako cíle vykreslování.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulace

`GraphicsApiType.SimD3D11`je vazba simulace a pokud je vybrána, vytvoří se `GraphicsBindingSimD3d11` vazba grafiky. Toto rozhraní se používá k simulaci přesunu hlav, například v desktopové aplikaci, a vykreslí monoscopic obrázek.
Instalace je trochu větší a funguje takto:

#### <a name="create-proxy-render-target"></a>Vytvořit cíl vykreslování proxy

Vzdálený a místní obsah je potřeba vykreslit pro vykreslování barvy mimo obrazovku s názvem "proxy" pomocí dat z kamery proxy, které poskytuje `GraphicsBindingSimD3d11.Update` funkce. Proxy se musí shodovat s rozlišením pro zpětnou vyrovnávací paměť. Jakmile je relace připravena, `GraphicsBindingSimD3d11.InitSimulation` je nutné ji volat před připojením k této relaci:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Funkci init je nutné poskytnout ukazatelům na nativní zařízení D3D a také k barvě a hloubkové textuře cíle vykreslování proxy serveru. Po inicializaci `AzureSession.ConnectToRuntime` a `DisconnectFromRuntime` může být volána několikrát, ale při přepnutí na jinou relaci je nutné nejprve `GraphicsBindingSimD3d11.DeinitSimulation` volat původní relaci, aby bylo `GraphicsBindingSimD3d11.InitSimulation` možné je volat v jiné relaci.

#### <a name="render-loop-update"></a>Aktualizace smyčky vykreslování

Aktualizace smyčky vykreslování se skládá z několika kroků:

1. Každý rámec, před tím, než proběhne jakékoli `GraphicsBindingSimD3d11.Update` vykreslování, je volána s aktuální transformací kamery, která je odeslána na server, který má být vykreslen. V současné době by se měla vrátit vrácená transformace proxy serveru na kameru proxy serveru, aby se vykreslila do cíle vykreslování proxy serveru.
Pokud je vrácená aktualizace `SimulationUpdate.frameId` proxy null, zatím neexistují žádná Vzdálená data. V tomto případě, místo vykreslování do cíle vykreslování proxy, by měl být veškerý místní obsah vykreslen do vyrovnávací paměti přímo pomocí aktuálních dat kamery a následující dva kroky byly vynechány.
1. Aplikace by nyní měla vytvořit vazby k cíli vykreslování proxy a `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`volání. Tím se zaplní informace o vzdálené barvě a hloubce do cíle vykreslování proxy serveru. Libovolný místní obsah se teď dá na proxy server vykreslovat pomocí transformace kamery proxy.
1. Dále musí být zpětná vyrovnávací paměť vázána jako cíl vykreslování a `GraphicsBindingSimD3d11.ReprojectProxy` volána, aby bylo možné zobrazit zpětnou vyrovnávací paměť.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Další kroky

* [Kurz: vytvoření projektu Unity od začátku](../tutorials/unity/project-setup.md)
