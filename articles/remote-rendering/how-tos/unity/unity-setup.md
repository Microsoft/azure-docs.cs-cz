---
title: Nastavení Remote Renderingu pro Unity
description: Jak inicializovat vzdálené vykreslování Azure v projektu Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018973"
---
# <a name="set-up-remote-rendering-for-unity"></a>Nastavení Remote Renderingu pro Unity

Pro povolení služby Azure Remote rendering (ARR) v Unity poskytujeme vyhrazené metody, které se postará o některé aspekty specifické pro Unity.

## <a name="startup-and-shutdown"></a>Spuštění a vypnutí

Chcete-li inicializovat vzdálené vykreslování, použijte `RemoteManagerUnity` . Tato třída volá do obecného, `RemoteManager` ale už pro vás implementuje podrobnosti specifické pro Unity. Například Unity používá určitý systém souřadnic. Při volání se `RemoteManagerUnity.Initialize` Nastaví správná konvence. Volání také vyžaduje, abyste zadali kameru Unity, která se má použít k zobrazení vzdáleně vykresleného obsahu.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Pro vypnutí vzdáleného vykreslování zavolejte `RemoteManagerStatic.ShutdownRemoteRendering()` .

Po `AzureSession` Vytvoření a vybrání jako primární relace vykreslování musí být zaregistrována v `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Úplný ukázkový kód

Následující kód ukazuje všechny kroky potřebné k inicializaci vzdáleného vykreslování Azure v Unity:

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

## <a name="convenience-functions"></a>Funkce usnadnění

### <a name="session-state-events"></a>Události stavu relace

`RemoteManagerUnity.OnSessionUpdate` vygeneruje události při změně stavu relace. Podrobnosti najdete v dokumentaci kódu.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` je volitelnou komponentou pro zjednodušení nastavení a správy relace. Obsahuje možnosti automatického zastavení jeho relace při ukončení aplikace nebo ukončení režimu přehrávání v editoru a také automatické prodloužení zapůjčení relace v případě potřeby. Ukládá data do mezipaměti, jako jsou vlastnosti relace (viz její `LastProperties` proměnná), a zpřístupňuje události pro změny stavu relace a chyby relací.

V jednom okamžiku nemůže existovat více než jedna instance `ARRServiceUnity` . Slouží k tomu, aby bylo možné rychleji začít s implementací některých běžných funkcí. U větší aplikace může být vhodnější provádět tyto věci sami, ale.

Příklad nastavení a použití `ARRServiceUnity` najdete v tématu [kurz: zobrazení vzdáleně generovaných modelů](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Další kroky

* [Instalace balíčku Remote Renderingu pro Unity](install-remote-rendering-unity-package.md)
* [Kurz: zobrazení vzdáleně generovaných modelů](../../tutorials/unity/view-remote-models/view-remote-models.md)
