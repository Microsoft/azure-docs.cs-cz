---
title: Grafická vazba
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
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681880"
---
# <a name="graphics-binding"></a>Grafická vazba

Aby bylo možné používat Azure Remote Rendering ve vlastní aplikaci, musí být integrovándo kanálu vykreslování aplikace. Tato integrace je odpovědností grafické vazby.

Po nastavení umožňuje grafická vazba přístup k různým funkcím, které ovlivňují vykreslený obraz. Tyto funkce lze rozdělit do dvou kategorií: obecné funkce, které jsou vždy `Microsoft.Azure.RemoteRendering.GraphicsApiType`k dispozici, a specifické funkce, které jsou relevantní pouze pro vybrané .

## <a name="graphics-binding-in-unity"></a>Grafická vazba v Unity

V Unity je celá vazba `RemoteUnityClientInit` zpracována `RemoteManagerUnity.InitializeManager`strukturou předanou do . Chcete-li nastavit grafický `GraphicsApiType` režim, musí být pole nastaveno na zvolenou vazbu. Pole bude automaticky naplněno v závislosti na tom, zda je k dispozici zařízení XRDevice. Chování lze ručně přepsat následujícími chováními:

* **HoloLens 2**: Vždy se používá grafická vazba [Windows Mixed Reality.](#windows-mixed-reality)
* **Plochá desktopová aplikace UWP:** [Simulace](#simulation) se používá vždy. Chcete-li použít tento režim, postupujte podle pokynů v [kurzu: Nastavení projektu Unity od začátku](../tutorials/unity/project-setup.md).
* **Unity editor**: [Simulace](#simulation) se používá vždy, pokud wmr VR headset je připojen v takovém případě ARR bude zakázáno, aby bylo možné ladit non-ARR související části aplikace. Viz též [holografická odsazení do vzdálené komunikace](../how-tos/unity/holographic-remoting.md).

Jedinou další relevantní část pro Unity je přístup k [základní vazby](#access), všechny ostatní části níže lze přeskočit.

## <a name="graphics-binding-setup-in-custom-applications"></a>Nastavení grafické vazby ve vlastních aplikacích

Chcete-li vybrat grafickou vazbu, postupujte takto dvěma kroky: Nejprve musí být grafická vazba staticky inicializována při inicializace programu:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Výše uvedené volání je nezbytné k inicializaci vzdáleného vykreslování Azure do holografických api. Tato funkce musí být volána před voláním libovolného holografického rozhraní API a před přístupem k jiným rozhraním API vzdáleného vykreslování. Podobně odpovídající de-init funkce `RemoteManagerStatic.ShutdownRemoteRendering();` by měla být volána po žádné holografické api jsou volány již.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Přístup k grafické vazbě

Po nastavení klienta základní grafické vazby lze přistupovat s `AzureSession.GraphicsBinding` getter. Jako příklad lze načíst statistiku posledního rámce takto:

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

## <a name="graphic-apis"></a>Grafické apis

V současné době existují dvě grafická `WmrD3D11` rozhraní `SimD3D11`API, která lze vybrat, a . Třetí existuje, `Headless` ale ještě není podporována na straně klienta.

### <a name="windows-mixed-reality"></a>Windows smíšená realita

`GraphicsApiType.WmrD3D11`je výchozí vazba pro spuštění na HoloLens 2. Vytvoří vazbu. `GraphicsBindingWmrD3d11` V tomto režimu Azure Vzdálené vykreslování háky přímo do holografických api.

Pro přístup k odvozené grafické `GraphicsBinding` vazby, musí být přetypování základny.
Existují dvě věci, které je třeba udělat pro použití wmr vazby:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informovat vzdálené vykreslování o použitém souřadném systému

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Kde výše `ptr` uvedené musí být `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` ukazatel na nativní objekt, který definuje souřadnicový systém světového prostoru, ve kterém jsou vyjádřeny souřadnice v rozhraní API.

#### <a name="render-remote-image"></a>Vykreslení vzdáleného obrazu

Na začátku každého snímku musí být vzdálený rám vykreslen do zadní vyrovnávací paměti. To se provádí `BlitRemoteFrame`voláním , které vyplní informace o barvě i hloubce do aktuálně vázaného cíle vykreslení. Proto je důležité, aby se to provádí po vazby zpět vyrovnávací paměti jako cíl vykreslení.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulace

`GraphicsApiType.SimD3D11`je simulační vazba a `GraphicsBindingSimD3d11` pokud je vybrána, vytvoří grafickou vazbu. Toto rozhraní se používá k simulaci pohybu hlavy, například v desktopové aplikaci a vykresluje monoskopický obraz.
Nastavení je trochu více zapojena a funguje takto:

#### <a name="create-proxy-render-target"></a>Vytvořit cíl vykreslení proxy serveru

Vzdálený a místní obsah musí být vykreslen na offscreen barvu / hloubku render cíl s `GraphicsBindingSimD3d11.Update` názvem 'proxy' pomocí proxy kamery data poskytnutá funkcí. Proxy musí odpovídat rozlišení zadní vyrovnávací paměti. Jakmile je relace `GraphicsBindingSimD3d11.InitSimulation` připravena, je třeba před připojením k ní zavolat:

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

Funkce init musí být opatřena ukazateli na nativní zařízení d3d a také na barvu a hloubku textury cíle vykreslení proxy. Po inicializování `AzureSession.ConnectToRuntime` a `DisconnectFromRuntime` může být volána vícekrát, ale při přepnutí do jiné relace, `GraphicsBindingSimD3d11.DeinitSimulation` musí být volána nejprve na staré relace před `GraphicsBindingSimD3d11.InitSimulation` lze volat na jiné relace.

#### <a name="render-loop-update"></a>Aktualizace smyčky vykreslení

Aktualizace smyčky vykreslení se skládá z několika kroků:

1. Každý snímek před provedením `GraphicsBindingSimD3d11.Update` vykreslování je volán s aktuální transformací kamery, která je odeslána na server, který má být vykreslen. Současně by měla být vrácená transformace proxy použita na proxy kameru, aby se vykreslovala do cíle vykreslení proxy.
Pokud je vrácená aktualizace `SimulationUpdate.frameId` proxy serveru null, zatím nejsou k dispozici žádná vzdálená data. V tomto případě namísto vykreslování do cíle vykreslení proxy by měl být jakýkoli místní obsah vykreslen do zadní vyrovnávací paměti přímo pomocí aktuálních dat fotoaparátu a další dva kroky jsou přeskočeny.
1. Aplikace by nyní měla vázat cíl `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`vykreslení proxy serveru a volat . Tím vyplníte vzdálené informace o barvě a hloubce do cíle vykreslení proxy serveru. Jakýkoli místní obsah lze nyní vykreslit na proxy server pomocí proxy kamery transformace.
1. Dále musí být zpět vyrovnávací paměť vázána `GraphicsBindingSimD3d11.ReprojectProxy` jako cíl vykreslení a volána v tomto okamžiku může být zobrazena zadní vyrovnávací paměť.

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

* [Kurz: Nastavení projektu Unity od nuly](../tutorials/unity/project-setup.md)
