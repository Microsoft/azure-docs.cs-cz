---
title: Manipulace s modely
description: Manipulace s použitím vzdáleně vygenerovaných modelů přesunutím, otočením škálování a dalšími
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 48c835070329b5cb0892b10760d37708e46bfa1d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559129"
---
# <a name="tutorial-manipulating-models"></a>Kurz: manipulace s modely

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přidat vizuální a manipulační hranice kolem vzdáleně vykreslených modelů
> * Přesunutí, otočení a škálování
> * Raycast s prostorovými dotazy
> * Přidat jednoduché animace pro vzdáleně vykreslené objekty

## <a name="prerequisites"></a>Předpoklady

* Tento kurz sestaví v [kurzu: rozhraní a vlastní modely](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Dotazování vzdálených objektů na hranice a použití na místní meze

Abychom mohli pracovat se vzdálenými objekty, potřebujeme k interakci s prvními místní reprezentace. [Hranice objektů](../../../concepts/object-bounds.md) jsou užitečné pro rychlou manipulaci se vzdáleným objektem. Na vzdálené meze se dá dotázat z ARR pomocí místní entity jako reference. Po načtení modelu do vzdálené relace se dotazuje na hranice.

Meze modelu jsou definovány polem, které obsahuje celý model – stejně jako [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html)Unity, který má střed a velikost definovanou pro osy x, y, z. Ve skutečnosti budeme k reprezentaci hranic vzdáleného modelu používat **BoxCollider** Unity.

1. Vytvořte nový skript ve stejném adresáři jako **RemoteRenderedModel** a pojmenujte ho **RemoteBounds**.
1. Obsah skriptu nahraďte následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Pokud se v C# 6 zobrazí chyba funkce deklarace identity sady Visual Studio *X není dostupná. Použijte prosím jazyk verze 7,0 nebo vyšší*, tato chyba se dá bezpečně ignorovat. To se týká řešení Unity a generování projektů.

    Tento skript by měl být přidán do stejného GameObject jako skript, který implementuje  **BaseRemoteRenderedModel**. V tomto případě to znamená **RemoteRenderedModel**. Podobně jako u předchozích skriptů bude tento počáteční kód zpracovávat všechny změny stavu, události a data související se vzdálenými mezemi.

    K implementaci můžete použít dvě metody: **QueryBounds** a **ProcessQueryResult**. **QueryBounds** načte meze a **ProcessQueryResult** provede výsledek dotazu a použije ho pro místní **BoxCollider**.

    Metoda **QueryBounds** je jednoduchá: odešlete dotaz do relace vzdáleného vykreslování a naslouchat `Completed` události.

1. Nahraďte metodu **QueryBounds** následující metodou dokončeno:

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    **ProcessQueryResult** je také jednoduchá. Výsledek zkontrolujeme, abychom zjistili, jestli bylo úspěšné. Pokud ano, převeďte a aplikujte vrácené vazby ve formátu, který může **BoxCollider** přijmout.    

1. Nahraďte metodu **ProcessQueryResult** následující metodou dokončeno:

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

Když teď dojde k přidání skriptu **RemoteBounds** ke stejnému hernímu objektu jako **RemoteRenderedModel**, bude v případě potřeby přidán **BoxCollider** a když model dosáhne svého `Loaded` stavu, budou se tato hranice automaticky dotazovat a použít na **BoxCollider**.

1. Pomocí **TestModel** GameObject, který jste vytvořili dříve, přidejte komponentu **RemoteBounds** .
1. Potvrďte, že se skript přidá.

     ![Přidat komponentu RemoteBounds](./media/remote-bounds-script.png)

1. Spusťte aplikaci znovu. Krátce po načtení modelu se zobrazí hranice pro vzdálený objekt. Uvidíte něco podobného jako u následujících hodnot:

     ![Snímek obrazovky, který zobrazuje příklad svázaných vzdálených objektů.](./media/updated-bounds.png)

Teď máme místní **BoxCollider** nakonfigurovaný s přesnou hranicí objektu Unity. Meze umožňují vizualizaci a interakci pomocí stejných strategií, které bychom použili pro místně vykreslený objekt. Například skripty, které mění transformaci, fyzika a další.

## <a name="move-rotate-and-scale"></a>Přesunutí, otočení a škálování  

Přesunutí, otočení a škálování vzdáleně vygenerovaných objektů funguje stejně jako u všech ostatních objektů Unity. **RemoteRenderingCoordinator** ve své `LateUpdate` metodě volá v `Update` aktuálně aktivní relaci. Součástí toho, co `Update` je synchronizace entity místního modelu se svými vzdálenými protějšky. Chcete-li přesunout, otočit nebo škálovat vzdáleně vykreslený model, stačí pouze přesunout, otočit nebo škálovat transformaci GameObject představující vzdálený model. Tady se upraví transformace nadřazených GameObject, ke kterým je připojen skript **RemoteRenderedModel** .

Tento kurz používá MRTK pro interakci s objekty. Většina implementace konkrétního MRTK pro přesun, otočení a škálování objektu je mimo rámec tohoto kurzu. V nabídce **nástroje modelu** je k dispozici kontroler zobrazení modelu, který je předem nakonfigurován uvnitř **AppMenu**.

1. Ujistěte se, že se **TestModel** GameObject, které jste dříve vytvořili, nachází ve scéně.
1. Ujistěte se, že je **AppMenu** Prefab ve scéně.
1. Stisknutím tlačítka Přehrát v Unity přehrajete scénu a otevřete nabídku **nástroje modelu** v rámci **AppMenu**.
![Kontroler zobrazení](./media/model-with-view-controller.png)

**AppMenu** má dílčí nabídky **modelů** , které implementují kontroler zobrazení pro vazbu s modelem. Pokud GameObject obsahuje komponentu **RemoteBounds** , kontroler zobrazení přidá komponentu [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) , což je komponenta MRTK, která vykresluje ohraničující rámeček kolem objektu s **BoxCollider**. [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html), který zodpovídá za interakci. Tyto skripty dohromady umožní přesunout, otočit a škálovat vzdáleně vykreslený model.

1. Přesuňte ukazatel myši na panel hry a kliknutím dovnitř ho přidělte fokus.
1. Pokud používáte [MRTKou simulaci](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation), stiskněte a podržte levý klávesu SHIFT.
1. Nasměrujte simulovanou ruku tak, aby ruka v poli odkazovala na model testu.

    ![Bodová ruka](./media/handray-engine.png)

1. Pokud ho chcete přesunout, stiskněte levé tlačítko a přetáhněte ho.

Měl by se zobrazit vzdálený vykreslený obsah, který se bude pohybovat spolu s ohraničujícím polem. Můžete si všimnout prodlevy nebo prodlevy mezi ohraničovacím polem a vzdáleným obsahem. Tato prodleva bude záviset na latenci Internetu a šířce pásma.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Přetypování ray a prostorové dotazy na vzdálené modely

Pole kolidující kolem modelů je vhodné pro interakci s celým modelem, ale není dostatečně podrobné, aby bylo možné pracovat s jednotlivými částmi modelu. Chcete-li tento problém vyřešit, můžeme použít [vzdálené obsazení paprsků](../../../overview/features/spatial-queries.md#ray-casts). Vzdálené obsazení Ray je rozhraní API, které poskytuje vzdálené vykreslování Azure k přetypování paprsků do vzdálené scény a vrácení výsledků hledání místně. Tato technika se dá použít k výběru podřízených entit velkého modelu nebo získání informací o výsledku volání, jako je pozice, normální povrch a vzdálenost.

Testovací model má několik dílčích entit, které lze dotazovat a vybrat. Prozatím se v rámci výběru vytvoří výstup názvu vybrané entity do konzoly Unity. Pokud chcete zvýraznit vybranou entitu, podívejte se na kapitolu [materiály, světelné a důsledky](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) .

Nejprve vytvoříme statickou obálku kolem vzdálených dotazů na přetypování do pole. Tento skript přijme polohu a směr v prostoru Unity, převede ho na datové typy akceptované vzdáleným přetypováním v oboru paměti a vrátí výsledky. Skript použije `RayCastQueryAsync` rozhraní API.

1. Vytvořte nový skript s názvem **RemoteRayCaster** a nahraďte jeho obsah následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity má třídu s názvem [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)a vzdálené vykreslování Azure má třídu s názvem [**RaycastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). Velké písmeno **C** je důležitý rozdíl, aby nedocházelo k chybám při kompilaci.

    **RemoteRayCaster** poskytuje běžný přístupový bod pro přetypování vzdálených paprsků do aktuální relace. Aby bylo konkrétnější, implementujeme obslužnou rutinu MRTK ukazatele na další. Skript implementuje `IMixedRealityPointerHandler` rozhraní, které sděluje MRTKi, že chceme, aby tento skript naslouchal událostem [ukazatele hybridní reality](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) .

1. Vytvořte nový skript s názvem **RemoteRayCastPointerHandler** a nahraďte kód následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

**RemoteRayCastPointerHandler** Metoda RemoteRayCastPointerHandler `OnPointerClicked` je volána MRTK, když ukazatel "klikne" na kolidujícím objektu, jako je například kokolize našeho boxu. Poté `PointerDataToRemoteRayCast` je volána pro převedení výsledku ukazatele na bod a směr. Tento bod a směr se pak použije k přetypování vzdáleného ray ve vzdálené relaci.

![Meze aktualizovány](./media/raycast-local-remote.png)

Odesílání žádostí o obsazení paprsků po kliknutí je efektivní strategií pro dotazování vzdálených objektů. Nejedná se však o ideální uživatelské prostředí, protože ukazatel koliduje se kokolizí boxu, nikoli modelem samotného.

Můžete také vytvořit nový ukazatel MRTK, který vrhá své paprsky ve vzdálené relaci častěji. I když se jedná o složitější přístup, činnost koncového uživatele by byla lepší. Tato strategie je mimo rámec tohoto kurzu, ale příklad tohoto přístupu se může zobrazit v aplikaci s ukázkami, který najdete v [úložišti ukázek ARR](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase).

Po úspěšném dokončení přetypování do **RemoteRayCastPointerHandler** se `Entity` z `OnRemoteEntityClicked` události Unity vygeneruje. Pro reakci na tuto událost vytvoříme Pomocný skript, který přijme `Entity` a provede na něm akci. Pojďme začít získáním skriptu pro vytištění názvu `Entity` do protokolu ladění.

1. Vytvořte nový skript s názvem **RemoteEntityHelper** a nahraďte jeho obsah následujícím textem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Na **TestModel** GameObject, který jste vytvořili dříve, přidejte komponentu **RemoteRayCastPointerHandler** i komponentu **RemoteEntityHelper** .
1. Přiřaďte `EntityToDebugLog` k události metodu `OnRemoteEntityClicked` . Pokud se shoduje typ výstupu události a vstupní typ metody, můžeme použít dynamickou událost propojení Unity, která bude automaticky předávat hodnotu události do metody.
    1. Vytvoří nové pole zpětného volání. ![ Přidat zpětné volání](./media/add-callback-remote-entity-clicked.png)
    1. Přetáhněte komponentu **pomocníka vzdálené entity** do pole Object, aby odkazovala na nadřazený ![ objekt GameObject přiřazení.](./media/assign-object.png)
    1. Přiřazení `EntityToDebugLog` ![ zpětného volání pro přiřazení zpětného volání](./media/remote-entity-event.png)
1. Stisknutím přehrát v editoru Unity zahájíte scénu, připojíte se ke vzdálené relaci a nahrajete testovací model.
1. Když použijete MRTKovou simulaci, stiskněte a podržíte levý klávesu SHIFT.
1. Nasměrujte simulovanou ruku tak, aby ruka v poli odkazovala na model testu.
1. Dlouhým kliknutím simulaci vzduchem spustíte `OnPointerClicked` událost.
1. Podívejte se na konzolu Unity pro zprávu protokolu s názvem vybrané podřízené entity. Příklad: ![ příklad podřízené entity](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Synchronizace vzdáleného objektu grafu s hierarchií Unity

Až do této chvíle jsme viděli jenom jeden místní GameObject představující celý model. To je vhodné pro vykreslování celého modelu a manipulaci s nimi. Pokud však chceme použít účinky nebo manipulovat s konkrétními dílčími entitami, bude nutné vytvořit místní GameObjects, aby tyto entity představovaly. Nejprve můžeme prozkoumat ručně v testovacím modelu.

1. Spusťte scénu a načtěte testovací model.
1. Rozbalte podřízené položky **TestModel** GameObject v hierarchii Unity a vyberte **TestModel_Entity** GameObject.
1. V inspektoru klikněte na tlačítko *Zobrazit podřízené* .
![Zobrazit podřízené](./media/show-remote-children.png)
1. Pokračujte v Rozbalení podřízených objektů v hierarchii a kliknutím na možnost *Zobrazit podřízené* , dokud se nezobrazí velký seznam podřízených objektů.
![Všechny podřízené objekty](./media/test-model-children.png)

V seznamu desítek entit se teď naplní hierarchie. Když vyberete jednu z nich, zobrazí `Transform` se `RemoteEntitySyncObject` v Inspektoru komponenty a. Ve výchozím nastavení se každou entitu v každém snímku automaticky nesynchronizují, takže místní změny se `Transform` nesynchronizují se serverem. V zobrazení scény můžete sledovat *synchronizaci všech snímků* a pak přesouvat, škálovat nebo otáčet transformaci. v zobrazení scény se nezobrazuje vykreslený model. Podívejte se na herní zobrazení a zobrazte si vizuální aktualizaci pozice a otočení modelu.

Stejný postup lze provést programově a je prvním krokem při úpravě specifických vzdálených entit.

1. Upravte skript **RemoteEntityHelper** tak, aby obsahoval také následující metodu:

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Přidejte další zpětné volání do události **RemoteRayCastPointerHandler** `OnRemoteEntityClicked` a nastavte ji na `MakeSyncedGameObject` .
![Další zpětné volání](./media/additional-callback.png)
1. Když použijete MRTKovou simulaci, stiskněte a podržíte levý klávesu SHIFT.
1. Nasměrujte simulovanou ruku tak, aby ruka v poli odkazovala na model testu.
1. Dlouhým kliknutím simulaci vzduchem spustíte `OnPointerClicked` událost.
1. Zkontrolujte a rozbalte hierarchii pro zobrazení nového podřízeného objektu, který představuje kliklou entitu.
![Reprezentace GameObject](./media/gameobject-representing-entity.png)
1. Po otestování odeberte zpětné volání pro `MakeSyncedGameObject` , protože ho zařadíme jako součást dalších účinků později.

> [!NOTE]
> Synchronizace všech rámců je nutná pouze v případě, že potřebujete synchronizovat transformovaná data. Je potřeba, aby se synchronizovaly transformace, takže by se měly používat bez omezeného zatížení.

Vytvoření místní instance a automatické synchronizace je prvním krokem při manipulaci s dílčími entitami. Stejné postupy, které jsme použili k manipulaci s modelem jako celku, je možné použít i u dílčích entit. Například po vytvoření synchronizované místní instance entity můžete zadat dotaz na jeho meze a přidat obslužné rutiny pro manipulaci, aby bylo možné ho přesunout kolem pohybů uživatele.

## <a name="next-steps"></a>Další kroky

Teď můžete pracovat s vašimi vzdáleně generovanými modely. V dalším kurzu se podíváme na modifikaci materiálů, změnu osvětlení a použití efektů na dálkově generovaných modelech.

> [!div class="nextstepaction"]
> [Další: rafinace materiálů, osvětlení a efektů](../materials-lighting-effects/materials-lighting-effects.md)