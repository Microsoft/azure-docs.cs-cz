---
title: Práce se vzdálenými entitami v Unity
description: Kurz, ve kterém se dozvíte, jak pracovat s entitami ARR
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310215"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Kurz: práce se vzdálenými entitami v Unity

[Kurz: vytvoření projektu Unity od začátku](project-setup.md) ukazuje, jak nakonfigurovat nový projekt Unity pro práci se vzdáleným vykreslováním Azure. V tomto kurzu se podíváme na nejběžnější funkce, které každý uživatel ARR potřebuje.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vyberte objekty využívající přetypování do paprsků.
> * Přepište stavy objektů, například barevný nádech, stav výběru a viditelnost.
> * Odstranění vzdálených entit
> * Přesuňte vzdálené entity kolem.
> * K vyhledání uvnitř objektů použijte vyjmuté plochy.

## <a name="prerequisites"></a>Požadavky

* Tento kurz sestaví na začátku [kurzu: nastavení projektu Unity od nuly](project-setup.md).

> [!TIP]
> [Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené projekty Unity pro všechny kurzy ve složce *Unity* , které můžete použít jako referenci.

## <a name="pick-objects"></a>Vybrat objekty

Chceme s objekty pracovat, takže první věc, kterou potřebujeme, je vyzvednutí objektů pod kurzorem myši.

Vytvořte [Nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) s názvem **RemoteRaycaster** a nahraďte jeho celý obsah následujícím kódem:

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
> Komponenta *RemoteRaycaster* vyžaduje, aby součást *ARRServiceUnity* byla připojena ke stejnému objektu. *ARRServiceUnity* je pomocná třída pro snadnější přístup k některým funkcím v kombinaci s funkcí Arr. V scéně však může být pouze jediná instance této součásti. Proto nezapomeňte přidat všechny součásti, které vyžadují *ARRServiceUnity* , do stejného GameObject.
> Chcete-li získat přístup k funkcím ARR z více herních objektů, přidejte komponentu *ARRServiceUnity* pouze k jednomu z nich a odkazujte na jiné skripty nebo přímý přístup k funkcím Arr.

Stiskněte Přehrát, připojte se k relaci a načtěte model. Nyní ukažte na objekty ve scéně a Sledujte výstup konzoly. Měl by tisknout název objektu každé části, na kterou najedete myší.

## <a name="highlight-objects"></a>Zvýraznit objekty

V dalším kroku chceme poskytnout vizuální zpětnou vazbu, na které části modelu uživatel odkazuje. K tomuto účelu připojíme [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) k entitě, kterou jsme vybrali. Tuto součást lze použít k povolení nebo zakázání různých funkcí objektu. Zde používáme k nastavení barvy odstínu a k povolení [vykreslování osnovy](../../overview/features/outlines.md).

Vytvořte další soubor skriptu s názvem **RemoteModelEntity** a nahraďte jeho obsah následujícím kódem:

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
> [!CAUTION]
> Nepřiřazujte tento skript žádnému hernímu objektu, protože ho program přiřadí programově pomocí kódu uvedeného níže.

V dalším kroku musíme prodloužit naše *RemoteRaycaster* a přidat komponentu *RemoteModelEntity* do objektu, který jsme právě vybrali.

Do implementace **RemoteRaycaster** přidejte následující kód a odstraňte duplicitní funkce:

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

Spusťte projekt a ukažte v modelu, měli byste vidět, že se zobrazuje červený nádech a obrys bílého výběru.

## <a name="isolate-the-selected-object"></a>Izolovat vybraný objekt

Další možností použití [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) je možnost přepsat viditelnost. Díky tomu můžete izolovat vybraný objekt od zbytku modelu. Otevřete skript **RemoteModelEntity** , přidejte následující kód a odstraňte duplicitní funkce:

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

Tento kód spoléhá na to, že má komponentu přepisu stavu v objektu nejvyšší úrovně v hierarchii, která zpřístupňuje všechny objekty jako neviditelné. Pak znovu potlačí viditelnost u vybraného objektu, aby byl jeden objekt viditelný. Proto musíme vytvořit komponentu pro přepsání stavu v kořenovém objektu.

Otevřete skript **RemoteRendering** a vložte následující kód na začátek funkce *LoadModel* :

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Nakonec potřebujeme způsob, jak přepnout viditelnost. Otevřete skript **RemoteRaycaster** a nahraďte funkci *Update* :

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

Spusťte kód a klikněte pravým tlačítkem na součást modelu. Zbytek modelu zmizí a zůstane viditelný jenom zvýrazněný kámen.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Odebrat instance GameObject vzdálených entit

Možná jste si všimli, že kód uchovává objekty, ale nikdy je nečistí. To je také vidět na panelu hierarchie objektů. Když rozbalíte hierarchii vzdáleného objektu během simulace, můžete zobrazit více a více objektů při každém přesunutí ukazatele myši na novou část modelu.

Příliš mnoho objektů ve scéně má negativní vliv na výkon. Vždy byste měli vyčistit objekty, které už nejsou potřeba.

Do skriptu **RemoteRaycaster** vložte následující kód a odstraňte duplicitní funkce:

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

## <a name="move-objects"></a>Přesunout objekty

Jako další krok chceme přesunout vybraný objekt kolem. Do **RemoteRaycaster** skriptu vložte tento kód a odstraňte duplicitní funkci:

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
> Pokud spustíte tento kód, všimnete si, že se nic nestane. To je proto, že změna transformace objektu automaticky nesynchronizuje změnu stavu na server z důvodů výkonu. Místo toho je třeba, aby se tato změna stavu nastavila na server ručně, nebo jste povolili **SyncEveryFrame** na komponentě *RemoteEntitySyncObject* .

Otevřete skript **RemoteModelEntity** a přidejte tento řádek:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Opětovné spuštění kódu, byste měli být schopni kliknout levým na objekt a přetáhnout ho kolem.

## <a name="add-a-cut-plane"></a>Přidat vyjmutou rovinu

Poslední funkce, kterou chceme v tomto kurzu vyzkoušet, je použití [vyjmutých ploch](../../overview/features/cut-planes.md). Vyjmutá rovina oddělí části vykreslených objektů, takže je můžete prohledat uvnitř.

Vytvoří nový GameObject v **CutPlane**scény. Vytvořte nový skript a zavolejte ho **RemoteCutPlane**. Přidejte komponentu do nového GameObject.

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

Když teď spustíte kód, měli byste vidět, jak je model vyjmutím otevřený rovinou. Můžete vybrat objekt *CutPlane* a přesunout ho a otočit v okně *scény* . Vyjmutou rovinu můžete zapnout nebo vypnout tak, že zakážete objekt vyjmout plochu.

## <a name="next-steps"></a>Další kroky

Nyní znáte nejdůležitější funkce pro interakci se vzdálenými objekty. V dalším kurzu se podíváme na přizpůsobení vzhledu scény.

> [!div class="nextstepaction"]
> [Kurz: Změna prostředí a materiálů](changing-environment-and-materials.md)
