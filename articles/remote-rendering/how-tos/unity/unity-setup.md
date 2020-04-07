---
title: Nastavení vzdáleného vykreslování pro jednotu
description: Jak inicializovat vzdálené vykreslování Azure v projektu Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681139"
---
# <a name="set-up-remote-rendering-for-unity"></a>Nastavení vzdáleného vykreslování pro jednotu

Chcete-li povolit Azure vzdálené vykreslování (ARR) v Unity, poskytujeme vyhrazené metody, které se starají o některé aspekty specifické pro jednotu.

## <a name="startup-and-shutdown"></a>Spuštění a vypnutí

Chcete-li inicializovat `RemoteManagerUnity`vzdálené vykreslování, použijte . Tato třída volá `RemoteManager` do obecné, ale již implementuje podrobnosti specifické pro jednotu pro vás. Například Unity používá konkrétní souřadný systém. Při `RemoteManagerUnity.Initialize`volání bude nastavena správná konvence. Volání také vyžaduje, abyste poskytli unity kameru, která by měla být použita pro zobrazení vzdáleně vykresleného obsahu.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Pro vypnutí vzdáleného vykreslování volejte `RemoteManagerStatic.ShutdownRemoteRendering()`.

`AzureSession` Po vytvoření a výběru jako primární relace vykreslování musí být registrována u `RemoteManagerUnity`aplikace :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Úplný ukázkový kód

Následující kód ukazuje všechny kroky potřebné k inicializaci vzdáleného vykreslování Azure v unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Komfortní funkce

### <a name="session-state-events"></a>Události stavu relace

`RemoteManagerUnity.OnSessionUpdate`vydává události při změně stavu relace, podrobnosti naleznete v dokumentaci ke kódu.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`je volitelná součást pro zjednodušení správy nastavení a relací. Obsahuje možnosti automatického zastavení relace při ukončení aplikace nebo ukončení režimu přehrávání v editoru a také automatické obnovení zapůjčení relace v případě potřeby. Ukládá data, jako jsou vlastnosti `LastProperties` relace (viz jeho proměnná), a zveřejňuje události pro změny stavu relace a chyby relace.

Nemůže existovat více než jedna `ARRServiceUnity` instance najednou. Je určen pro rychlejší spuštění implementací některých běžných funkcí. Pro větší aplikace to může být vhodnější dělat ty věci sami, ačkoli.

Příklad, jak nastavit a `ARRServiceUnity` používat, viz [Výuka: Nastavení projektu Unity od začátku](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Další kroky

* [Instalace balíčku vzdáleného vykreslování pro unity](install-remote-rendering-unity-package.md)
* [Kurz: Nastavení projektu Unity od nuly](../../tutorials/unity/project-setup.md)
