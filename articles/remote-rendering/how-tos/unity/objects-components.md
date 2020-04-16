---
title: Unity herní objekty a komponenty
description: Popisuje unity specifické metody pro práci s entity vzdáleného vykreslování a součásti.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409856"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interakce s herními objekty a komponentami Unity

Azure Remote Rendering (ARR) je optimalizován pro obrovské množství objektů (viz [Omezení).](../../reference/limits.md) I když je možné spravovat velké a složité hierarchie na hostiteli, replikace je všechny v Unity na zařízeních s nízkou výkonem je neproveditelné.

Proto při načtení modelu na hostitele Azure Remote Rendering zrcadlí informace o struktuře modelu na klientském zařízení (které bude mít za následek síťový provoz), ale nereplikuje objekty a součásti v Unity. Místo toho očekává, že budete požadovat potřebné unity herní objekty a komponenty ručně, tak, aby můžete omezit režii na to, co je skutečně potřeba. Tímto způsobem máte větší kontrolu nad výkonem na straně klienta.

V důsledku toho integrace Unity vzdáleného vykreslování Azure přichází s další funkce replikovat strukturu vzdáleného vykreslování na vyžádání.

## <a name="load-a-model-in-unity"></a>Načtení modelu v unity

Při načtení modelu získáte odkaz na kořenový objekt načteného modelu. Tento odkaz není objekt hry Unity, ale můžete jej změnit `Entity.GetOrCreateGameObject()`na jeden pomocí metody rozšíření . Tato funkce očekává argument `UnityCreationMode`typu . Pokud předáte `CreateUnityComponents`, nově vytvořený objekt hry Unity bude navíc naplněn součástmi proxy pro všechny součásti vzdáleného vykreslování, které existují na hostiteli. Doporučuje se však dát `DoNotCreateUnityComponents`přednost , aby režie byla minimální.

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

### <a name="load-model-with-unity-coroutines"></a>Načíst model s unity coroutines

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

### <a name="load-model-with-await-pattern"></a>Načíst model se vzorem await

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Výše uvedené ukázky kódu používaly cestu načítání modelu prostřednictvím SAS, protože vestavěný model je načten. Adresování modelu pomocí kontejnerů `LoadModelAsync` `LoadModelParams`objektů blob (pomocí a ) funguje plně analogicky.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Vytvoření objektu hry Unity `RemoteEntitySyncObject` implicitně přidá komponentu do herního objektu. Tato komponenta se používá k synchronizaci transformace entity na server. Ve `RemoteEntitySyncObject` výchozím nastavení vyžaduje, `SyncToRemote()` aby uživatel explicitně volat synchronizovat místní stav Unity na server. Povolením `SyncEveryFrame` se objekt automaticky synchronizuje.

Objekty `RemoteEntitySyncObject` s mohou mít své vzdálené podřízené instance a zobrazí se v editoru Unity pomocí tlačítka **Zobrazit děti.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Komponenty obálky

[Součásti](../../concepts/components.md) připojené k entitám vzdáleného vykreslování jsou vystaveny unity prostřednictvím proxy `MonoBehavior`s. Tyto proxy servery představují vzdálenou součást unity a předávají hostiteli všechny změny.

Chcete-li vytvořit součásti vzdáleného `GetOrCreateArrComponent`vykreslování proxy serveru, použijte metodu rozšíření :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Vázané životnosti

Životnost vzdálené [entity](../../concepts/entities.md) a objektu hry Unity je spojena, `RemoteEntitySyncObject`zatímco jsou vázány prostřednictvím . Pokud zavoláte `UnityEngine.Object.Destroy(...)` s takovým objektem hry, bude odebrána i vzdálená entita.

Chcete-li zničit objekt hry Unity, aniž byste ovlivnili vzdálenou entitu, musíte nejprve volat `Unbind()` na `RemoteEntitySyncObject`.

Totéž platí pro všechny součásti proxy. Chcete-li zničit pouze reprezentaci na `Unbind()` straně klienta, musíte nejprve volat komponentu proxy:

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
* [Kurz: Práce se vzdálenými entitami v Unity](../../tutorials/unity/working-with-remote-entities.md)
