---
title: Objekty a komponenty hry Unity
description: Popisuje metody specifické pro Unity pro práci s entitami a komponentami vzdáleného vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409856"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interakce s herními objekty a komponentami Unity

Vzdálené vykreslování Azure (ARR) je optimalizované pro rozsáhlé počty objektů (viz [omezení](../../reference/limits.md)). I když je možné spravovat velké a složité hierarchie na hostiteli, je replikace všech v Unity na zařízení s nízkým výkonem neproveditelná.

Proto když je model načten do hostitele, vzdálené vykreslování Azure zrcadlí informace o struktuře modelu v klientském zařízení (což bude účtovat síťový provoz), ale nereplikuje objekty a součásti v Unity. Místo toho očekává, že budete vyžadovat potřebné objekty a komponenty pro Unity, takže můžete omezit režii na to, co je skutečně potřeba. Tímto způsobem máte větší kontrolu nad výkonem na straně klienta.

V důsledku toho je integrace Unity pro vzdálené vykreslování Azure dodávána s dalšími funkcemi pro replikaci struktury vzdáleného vykreslování na vyžádání.

## <a name="load-a-model-in-unity"></a>Načtení modelu v Unity

Při načítání modelu získáte odkaz na kořenový objekt načteného modelu. Tento odkaz není objektem hry Unity, ale můžete ho převést na jednu pomocí metody `Entity.GetOrCreateGameObject()`rozšíření. Tato funkce očekává argument typu `UnityCreationMode`. Pokud předáte `CreateUnityComponents`, nově vytvořený objekt hry Unity se bude naplnit pomocí součástí proxy pro všechny součásti vzdáleného vykreslování, které existují na hostiteli. Doporučuje se to sice ale preferovat `DoNotCreateUnityComponents`, aby se režie vynechala minimální.

### <a name="load-model-with-task"></a>Načíst model s úkolem

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Načíst model s korutinami Unity

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Model zatížení pomocí vzoru await

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Výše uvedené ukázky kódu používaly cestu načtení modelu prostřednictvím SAS, protože je načtený integrovaný model. Adresování modelu prostřednictvím kontejnerů objektů BLOB `LoadModelAsync` ( `LoadModelParams`pomocí a) funguje zcela obdobně.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Vytvoření objektu hry Unity implicitně přidá `RemoteEntitySyncObject` komponentu do objektu Game. Tato součást slouží k synchronizaci entit transformace na server. Ve výchozím `RemoteEntitySyncObject` nastavení vyžaduje, aby uživatel explicitně `SyncToRemote()` volal synchronizaci místního stavu Unity se serverem. Při `SyncEveryFrame` povolení se objekt automaticky synchronizuje.

Objekty s objektem `RemoteEntitySyncObject` můžou mít instance vzdálených podřízených objektů a zobrazují se v editoru Unity tlačítkem **Zobrazit podřízené** .

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Komponenty obálky

[Součásti](../../concepts/components.md) připojené k entitám vzdáleného vykreslování jsou zpřístupněny Unity prostřednictvím `MonoBehavior`proxy serveru. Tyto proxy servery reprezentují vzdálenou komponentu v Unity a předávají všechny úpravy hostitele.

Chcete-li vytvořit komponenty vzdáleného vykreslování proxy, použijte metodu `GetOrCreateArrComponent`rozšíření:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Spárované životnosti

Doba životnosti vzdálené [entity](../../concepts/entities.md) a objektu hry Unity je spojená, pokud jsou svázané přes `RemoteEntitySyncObject`. Pokud voláte `UnityEngine.Object.Destroy(...)` pomocí takového herního objektu, bude odebrána i vzdálená entita.

Chcete-li zničit objekt hry Unity, aniž by to ovlivnilo vzdálenou entitu, musíte `Unbind()` nejprve zavolat `RemoteEntitySyncObject`na.

Totéž platí pro všechny součásti proxy serveru. Chcete-li zničit pouze reprezentaci na straně klienta, je třeba `Unbind()` nejprve zavolat na součást proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Další kroky

* [Nastavení Remote Renderingu pro Unity](unity-setup.md)
* [Kurz: práce se vzdálenými entitami v Unity](../../tutorials/unity/working-with-remote-entities.md)
