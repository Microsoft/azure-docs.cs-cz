---
title: Objekty a komponenty hry Unity
description: Popisuje metody specifické pro Unity pro práci s entitami a komponentami vzdáleného vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594142"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interakce s herními objekty a komponentami Unity

Vzdálené vykreslování Azure (ARR) je optimalizované pro rozsáhlé počty objektů (viz [omezení](../../reference/limits.md)). I když je možné spravovat velké a složité hierarchie na hostiteli, je replikace všech v Unity na zařízení s nízkým výkonem neproveditelná.

Proto když je model načten do hostitele, vzdálené vykreslování Azure zrcadlí informace o struktuře modelu v klientském zařízení (což bude účtovat síťový provoz), ale nereplikuje objekty a součásti v Unity. Místo toho očekává, že budete vyžadovat potřebné objekty a komponenty pro Unity, takže můžete omezit režii na to, co je skutečně potřeba. Tímto způsobem máte větší kontrolu nad výkonem na straně klienta.

V důsledku toho je integrace Unity pro vzdálené vykreslování Azure dodávána s dalšími funkcemi pro replikaci struktury vzdáleného vykreslování na vyžádání.

## <a name="load-a-model-in-unity"></a>Načtení modelu v Unity

Při načítání modelu získáte odkaz na kořenový objekt načteného modelu. Tento odkaz není objektem hry Unity, ale můžete ho převést na jednu pomocí metody rozšíření `Entity.GetOrCreateGameObject()` . Tato funkce očekává argument typu `UnityCreationMode` . Pokud předáte `CreateUnityComponents` , nově vytvořený objekt hry Unity se bude naplnit pomocí součástí proxy pro všechny součásti vzdáleného vykreslování, které existují na hostiteli. Doporučuje se to sice ale preferovat `DoNotCreateUnityComponents` , aby se režie vynechala minimální.

### <a name="load-model-with-unity-coroutines"></a>Načíst model s korutinami Unity

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Model zatížení pomocí vzoru await

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Výše uvedené ukázky kódu používaly cestu načtení modelu prostřednictvím SAS, protože je načtený integrovaný model. Adresování modelu prostřednictvím kontejnerů objektů BLOB (pomocí `LoadModelAsync` a `LoadModelOptions` ) funguje zcela obdobně.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Vytvoření objektu hry Unity implicitně přidá `RemoteEntitySyncObject` komponentu do objektu Game. Tato součást slouží k synchronizaci entit transformace na server. Ve výchozím nastavení `RemoteEntitySyncObject` vyžaduje, aby uživatel explicitně volal `SyncToRemote()` synchronizaci místního stavu Unity se serverem. Při povolení `SyncEveryFrame` se objekt automaticky synchronizuje.

Objekty s a `RemoteEntitySyncObject` můžou mít instance vzdálených podřízených objektů, které se zobrazí v editoru Unity přes **:::no-loc text="Show children":::** tlačítko.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Komponenty obálky

[Součásti](../../concepts/components.md) připojené k entitám vzdáleného vykreslování jsou zpřístupněny Unity prostřednictvím proxy serveru `MonoBehavior` . Tyto proxy servery reprezentují vzdálenou komponentu v Unity a předávají všechny úpravy hostitele.

Chcete-li vytvořit komponenty vzdáleného vykreslování proxy, použijte metodu rozšíření `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Spárované životnosti

Doba životnosti vzdálené [entity](../../concepts/entities.md) a objektu hry Unity je spojená, pokud jsou svázané přes `RemoteEntitySyncObject` . Pokud voláte `UnityEngine.Object.Destroy(...)` pomocí takového herního objektu, bude odebrána i vzdálená entita.

Chcete-li zničit objekt hry Unity, aniž by to ovlivnilo vzdálenou entitu, musíte nejprve zavolat `Unbind()` na `RemoteEntitySyncObject` .

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
* [Kurz: manipulace se vzdálenými entitami v Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)
