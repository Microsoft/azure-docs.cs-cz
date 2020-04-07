---
title: Práce se vzdálenými entitami v Unity
description: Kurz, který ukazuje, jak pracovat s entitami ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 97b2a0e48907ebe5b1cafdf92d14e9a4c6a28360
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679531"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Kurz: Práce se vzdálenými entitami v Unity

[Kurz: Nastavení projektu Unity od začátku ukázalo,](project-setup.md) jak nakonfigurovat nový projekt Unity pro práci s azure remote rendering. V tomto kurzu se podíváme na nejběžnější funkce, které potřebuje každý uživatel ARR.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vyberte objekty pomocí paprskových nádechů.
> * Přepsat stavy objektu, jako je barva tónu, stav výběru a viditelnost.
> * Odstranit vzdálené entity.
> * Přesuňte vzdálené entity.
> * Pomocí rovin řezu se můžete podívat dovnitř objektů.

## <a name="prerequisites"></a>Požadavky

* Tento výukový program navazuje na [kurz: Nastavení projektu Unity od nuly](project-setup.md).

> [!TIP]
> [Úložiště vzorků ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené unity projekty pro všechny kurzy ve složce *Unity,* které můžete použít jako odkaz.

## <a name="pick-objects"></a>Výběr objektů

Chceme komunikovat s objekty, takže první věc, kterou potřebujeme, je vybírání objektů pod kurzorem myši.

Vytvořte [nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) s názvem **RemoteRaycaster** a nahradit celý jeho obsah s kódem níže:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Přidejte tuto komponentu do objektu *RemoteRendering* ve scéně.

> [!WARNING]
>
> Komponenta *RemoteRaycaster* vyžaduje, aby byla komponenta *ARRServiceUnity* připojena ke stejnému objektu. *ARRServiceUnity* je pomocná třída pro snadný přístup k některým funkcím ARR. Může však existovat pouze jedna instance této součásti ve scéně. Proto nezapomeňte přidat všechny součásti, které vyžadují *ARRServiceUnity* do stejného GameObject.
> Pokud chcete získat přístup k funkcím ARR z více herních objektů, přidejte komponentu *ARRServiceUnity* pouze do jednoho z nich a nazvěte ji v ostatních skriptech nebo přímo získejte přístup k funkci ARR.

Stiskněte tlačítko přehrát, připojte se k relaci a načtěte model. Nyní ukažte na objekty ve scéně a sledujte výstup konzoly. Měl by vytisknout název objektu každé části, na kterou najedete.

## <a name="highlight-objects"></a>Zvýraznit objekty

Jako další krok chceme poskytnout vizuální zpětnou vazbu, na které části modelu uživatel ukazuje. K dosažení tohoto cíle připojíme [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) k entitě, kterou jsme vybrali. Tuto komponentu lze použít k povolení nebo zakázání různých funkcí objektu. Zde jej používáme k nastavení barvy tónu a povolení [vykreslování obrysů](../../overview/features/outlines.md).

Vytvořte jiný soubor skriptu s názvem **RemoteModelEntity** a nahraďte jeho obsah následujícím kódem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```

Další na řadě, musíme rozšířit naše *RemoteRaycaster* přidat *RemoteModelEntity* komponenty do objektu, který jsme právě vybrali.

Přidejte následující kód do implementace **RemoteRaycaster** a odeberte duplicitní funkce:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Spusťte projekt a přejděte na model, měli byste vidět, že získává červený obraz a bílý obrys výběru.

## <a name="isolate-the-selected-object"></a>Izolujte vybraný objekt

Jiné použití [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) je schopnost přepsat viditelnost. To umožňuje izolovat vybraný objekt od zbytku modelu. Otevřete skript **RemoteModelEntity,** přidejte následující kód a odeberte duplicitní funkce:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Tento kód závisí na tom, že má komponentu přepsání stavu na objektu zcela nahoře v hierarchii, což činí všechny objekty neviditelnými. Poté znovu přepíše viditelnost u vybraného objektu, aby byl tento jeden objekt viditelný. Proto musíme vytvořit komponentu přepsání stavu v kořenovém objektu.

Otevřete skript **RemoteRendering** a vložte níže uvedený kód do horní části funkce *LoadModel:*

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Konečně potřebujeme způsob, jak přepnout viditelnost. Otevřete skript **RemoteRaycaster** a nahraďte funkci *Aktualizovat:*

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Spusťte kód a klikněte pravým tlačítkem myši na část modelu. Zbytek modelu zmizí a pouze zvýrazněný kus zůstane viditelný.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Odebrání instancí GameObject vzdálených entit

Možná jste si všimli, že kód udržuje vytváření objektů, ale nikdy je nečistí. To je také viditelné v panelu hierarchie objektů. Když během simulace rozbalíte hierarchii vzdálených objektů, zobrazí se při každém najetí na novou část modelu stále více objektů.

Mít mnoho objektů ve scéně negativně ovlivňuje výkon. Vždy byste měli vyčistit objekty, které již nejsou potřeba.

Vložte níže uvedený kód do skriptu **RemoteRaycaster** a odeberte duplicitní funkce:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Přesunutí objektů

Jako další krok chceme přesunout vybraný objekt. Ve skriptu **RemoteRaycaster** vložte tento kód a odeberte duplicitní funkci:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Pokud spustíte tento kód, všimnete si, že se nic neděje. Je to proto, že změna transformace objektu automaticky nesynchronizuje změnu stavu na server z důvodů výkonu. Místo toho budete muset tuto změnu stavu ručně posunout na server nebo povolit **syncEveryFrame** v komponentě *RemoteEntitySyncObject.*

Otevřete skript **RemoteModelEntity** a přidejte tento řádek:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Spuštění kódu znovu, měli byste být schopni levým tlačítkem myši na objekt a přetáhněte jej kolem.

## <a name="add-a-cut-plane"></a>Přidání roviny řezu

Poslední funkce, kterou chceme vyzkoušet v tomto tutoriálu, je použití [řezaných rovin](../../overview/features/cut-planes.md). Rovina řezu odřízne části vykreslených objektů tak, abyste se do nich mohli podívat.

Vytvořte nový GameObject ve scéně **CutPlane**. Vytvořte nový skript a nazvěte jej **RemoteCutPlane**. Přidejte komponentu do nového objektu GameObject.

Otevřete soubor skriptu a nahraďte jeho obsah následujícím kódem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Při spuštění kódu nyní, měli byste vidět, jak je model rozříznut a rovinou. Můžete vybrat objekt *CutPlane* a přesunout ho a otočit ho v okně *Scéna.* Rovinu řezu můžete zapínat a vypínat zakázáním objektu roviny řezu.

## <a name="next-steps"></a>Další kroky

Nyní znáte nejdůležitější funkce pro interakci se vzdálenými objekty. V dalším tutoriálu se podíváme na přizpůsobení vzhledu scény.

> [!div class="nextstepaction"]
> [Kurz: Změna prostředí a materiálů](changing-environment-and-materials.md)
