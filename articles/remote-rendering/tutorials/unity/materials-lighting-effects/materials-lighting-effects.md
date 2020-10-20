---
title: Zpřesňování materiálů, osvětlení a efektů
description: Úprava modelů modelu a osvětlení. Přidejte další efekty, jako je sbalení a vyjmuté plochy.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 04cb48a3ff84a67995c1a920a323fa568a67cdf3
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203241"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Kurz: rafinace materiálů, osvětlení a efektů

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zvýraznění a náčrtace modelů a komponent modelu
> * Použití různých materiálů pro modely
> * Vytváření řezů prostřednictvím modelů s vyjmutými rovinami
> * Přidat jednoduché animace pro vzdáleně vykreslené objekty

## <a name="prerequisites"></a>Požadavky

* Tento kurz sestaví [kurz: manipulace s modely](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Zvýraznění a sbalení

Poskytnutí vizuální zpětné vazby uživateli je důležitou součástí uživatelského prostředí v libovolné aplikaci. Vzdálené vykreslování Azure poskytuje mechanismy vizuálního zpětné vazby prostřednictvím [potlačení hierarchických stavů](../../../overview/features/override-hierarchical-state.md). Přepsání hierarchických stavů jsou implementována s komponentami připojenými k místním instancím modelů. Zjistili jsme, jak vytvořit tyto místní instance v [synchronizaci grafu vzdáleného objektu s hierarchií Unity](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Nejprve vytvoříme obálku kolem komponenty [**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) . **HierarchicalStateOverrideComponent** je místní skript, který řídí přepsání na vzdálené entitě. [**Výukové materiály**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) obsahují abstraktní základní třídu s názvem **BaseEntityOverrideController**, kterou rozšiřujeme a vytvoříme obálku.

1. Vytvořte nový skript s názvem **EntityOverrideController** a nahraďte jeho obsah následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

Hlavní úlohou **LocalOverride**je vytvořit propojení mezi sebou samým a jeho `RemoteComponent` . **LocalOverride** pak umožní nastavit příznaky stavu místní komponenty, které jsou svázané se vzdálenou entitou. Přepsání a jejich stavy jsou popsány na stránce [přepsání hierarchického stavu](../../../overview/features/override-hierarchical-state.md) . 

Tato implementace jednoduše přepíná jenom jeden stav. Je však zcela možné zkombinovat více přepsání u jednotlivých entit a vytvořit kombinace na různých úrovních v hierarchii. Například kombinování `Selected` a `SeeThrough` na jedné komponentě mu poskytne obrys a zároveň je průhledný. Nebo je nastavení přepsat kořenovou entitou `Hidden` na, aby `ForceOn` bylo možné přepsání podřízené entity `Hidden` `ForceOff` Skrýt vše kromě podřízeného objektu s přepsáním.

Pro použití států u entit můžeme upravit **RemoteEntityHelper** vytvořené dříve.

1. Upravte třídu **RemoteEntityHelper** k implementaci abstraktní třídy **BaseRemoteEntityHelper** . Tato úprava umožní použití kontroleru zobrazení, který je součástí **prostředků kurzu**. Měl by vypadat nějak takto:

    ```csharp
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Přepište abstraktní metody pomocí následujícího kódu:

    ```csharp
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Tento kód zajišťuje přidání komponenty **EntityOverrideController** do cílové entity, poté volá jednu z metod přepínacího tlačítka. V případě potřeby lze na **TestModel** GameObject provést volání těchto pomocných metod přidáním **RemoteEntityHelper** jako zpětného volání do `OnRemoteEntityClicked` události v komponentě **RemoteRayCastPointerHandler** .

![Zpětná volání ukazatele](./media/pointer-event-callbacks.png)

Teď, když se tyto skripty do modelu přidaly po připojení k modulu runtime, by měl mít kontroler zobrazení **AppMenu** další rozhraní, která mají povolený interakci s skriptem **EntityOverrideController** . Pokud chcete zobrazit odemčené řadiče zobrazení, podívejte se do nabídky **nástroje modelu** .

V tomto okamžiku by komponenty **TestModel** GameObject měly vypadat přibližně takto:

![Testovací model s dalšími skripty](./media/test-model-updated.png)

Tady je příklad přepsání skládání na jednu entitu. Použili jsme `Select` a `Tint` k poskytnutí osnovy i barvy:

![Výběr barevného nádechu modelu testu](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Řezy roviny

[Vyjmuté roviny](../../../overview/features/cut-planes.md) jsou funkce, kterou je možné přidat do libovolné vzdálené entity. Nejčastěji se vytvoří nová vzdálená entita, která není přidružená k žádným datům sítě, aby obsahovala komponentu vyjmuté plochy. Pozice a orientace vyjmuté plochy je určena pozicí a orientací vzdálené entity, ke které je připojena.

Vytvoříme skript, který automaticky vytvoří vzdálenou entitu, přidá komponentu vyjmuté plochy a synchronizuje transformaci lokálního objektu s entitou vyjmuté plochy. Potom můžeme použít **CutPlaneViewController** k zabalení vyjmuté roviny v rozhraní, které nám umožní manipulovat s ní.

1. Vytvořte nový skript s názvem **RemoteCutPlane** a nahraďte jeho kód následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Tento kód rozšiřuje třídu **BaseRemoteCutPlane** , která je součástí **prostředků kurzu**. Podobně jako u vzdáleného vykresleného modelu tento skript připojí a naposlouchá `RemoteRenderingState` změny od vzdáleného koordinátora. Když koordinátor dosáhne `RuntimeConnected` stavu, pokusí se automaticky připojit, pokud má být. K dispozici je také `CutPlaneComponent` Proměnná, kterou budeme sledovat. Toto je komponenta vzdáleného vykreslování Azure, která se synchronizuje s vyjmutou rovinou ve vzdálené relaci. Pojďme se podívat na to, co musíme udělat k vytvoření vyjmuté roviny.

2. Nahraďte `CreateCutPlane()` metodu pomocí dokončené verze níže:

    ```csharp
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Tady vytváříme vzdálenou entitu a navážeme ji na místní GameObject. Ujistěte se, že se pro vzdálenou entitu nastavila synchronizovaná transformace na místní transformaci nastavením `SyncEveryFrame` na `true` . Pak použijeme `CreateComponent` volání k přidání do `CutPlaneComponent` vzdáleného objektu. Nakonec nakonfigurujeme vyjmutou rovinu s nastavením definovaným v horní části MonoBehavioury. Pojďme se podívat, co potřebuje k vyčištění vyjmuté roviny implementací `DestroyCutPlane()` metody.

3. Nahraďte `DestroyCutPlane()` metodu pomocí dokončené verze níže:

    ```csharp
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Vzhledem k tomu, že vzdálený objekt je poměrně jednoduchý a jenom čistíme vzdálené ukončení (a udržujeme náš místní objekt), je to jednoduché volání `Destroy` na vzdálený objekt a jasný odkaz na něj.

**AppMenu** zahrnuje kontroler zobrazení, který se automaticky připojí k vyjmuté rovině a umožní vám s ním pracovat. Není nutné používat **AppMenu** nebo žádný z řadičů zobrazení, ale jejich účelem je lepší prostředí. Nyní otestujte vyjmutou plochu a její kontroler zobrazení.

1. Vytvořte v scéně novou prázdnou GameObject a pojmenujte ji **CutPlane**.
1. Přidejte komponentu **RemoteCutPlane** do **CutPlane** GameObject.

   ![Vyjmout konfiguraci součásti roviny](./media/cut-plane-config.png)

1. Stisknutím přehrát v editoru Unity načtěte a připojte se ke vzdálené relaci.
1. Použití MRTK simulace, otočení a natočení (stisknutí klávesy CTRL pro otočení), aby se CutPlane přesunula kolem scény. Podívejte se na řez IT na **TestModel** a odhalte interní součásti.

![Příklad vyjmuté roviny](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Konfigurace vzdáleného osvětlení

Relace vzdáleného vykreslování podporuje plné spektrum [možností osvětlení](../../../overview/features/lights.md). Vytvoříme skripty pro [texturu nebe](../../../overview/features/sky.md) a jednoduchou mapu pro dva typy světla Unity pro použití se vzdáleným vykreslováním.

### <a name="sky-texture"></a>Nebeská textura

Existuje řada integrovaných Cubemaps, ze kterých si můžete vybrat, když měníte texturu nebe. Tyto jsou načteny do relace a aplikovány na texturu nebe. Je také možné [načíst vlastní textury](../../../concepts/textures.md) pro použití jako nebeská světla.

Vytvoříme skript **RemoteSky** , který obsahuje seznam integrovaných dostupných Cubemaps ve formě parametrů načtení. Pak umožníme uživateli vybrat a načíst jednu z možností.

1. Vytvořte nový skript s názvem **RemoteSky** a nahraďte jeho celý obsah následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSASParams> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSASParams> builtInTextures = new Dictionary<string, LoadTextureFromSASParams>()
        {
            {"Autoshop",new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    Nejdůležitější část tohoto kódu je pouze pár řádků:

    ```csharp
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Tady získáte odkaz na texturu, která se použije, když ji načteme do relace z integrovaného úložiště objektů BLOB. Pak je potřeba tuto texturu přiřadit jenom k tomu, aby `SkyReflectionTexture` ji bylo možné použít.

1. Vytvořte ve scéně prázdnou GameObject a pojmenujte ji jako **Světlík**.

1. Přidejte skript **RemoteSky** do **světlíku** GameObject.

    Přepínání mezi nebey nebe lze provést voláním `SetSky` pomocí jednoho z řetězcových klíčů definovaných v `AvailableCubemaps` . Kontroler zobrazení integrovaný do  **AppMenu** automaticky vytvoří tlačítka a zapojte jejich události pro volání `SetSky` s příslušným klíčem.
1. Stiskněte Přehrát v editoru Unity a autorizujte připojení.
1. Po připojení místního modulu runtime ke vzdálené relaci přejděte na **AppMenu-> Session Tools – > Remote nebe** a prozkoumejte různé možnosti nebe a podívejte se, jak ovlivňují **TestModel**.

### <a name="scene-lights"></a>Kvadranty scény

Mezi vzdálené Indikátory scény patří: Point, bodový a směrový. Podobně jako u vyjmuté roviny, kterou jsme vytvořili výše, jsou tyto indikátory scény vzdálené entity s připojenými komponentami. Důležitým aspektem při osvětlení vzdálené scény při pokusu o shodu s osvětlením v místní scéně. Tato strategie není vždycky možná, protože spousta aplikací Unity pro HoloLens 2 nepoužívá pro místně vykreslené objekty fyzicky založené vykreslování. Nicméně na určitou úroveň můžeme simulovat jednodušší výchozí osvětlení Unity.

1. Vytvořte nový skript s názvem **RemoteLight** a nahraďte jeho kód následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Tento skript vytvoří různé typy dálkových světla v závislosti na typu místní Unity, ke kterému je skript připojený. Vzdálené světlo bude duplikovat místní světlo v: poloha, rotace, barva a intenzita. Pokud je to možné, vzdálené světlo také nastaví další konfiguraci. Nejedná se o dokonalou shodu, protože světla Unity nejsou světla PBR.

1. Ve scéně Najděte **DirectionalLight** GameObject. Pokud jste odebrali výchozí **DirectionalLight** z scény: v horním řádku nabídek vyberte *GameObject-> Light-> DirectionalLight* pro vytvoření nového světla ve scéně.

1. Vyberte **DirectionalLight** GameObject a pomocí tlačítka **Přidat komponentu** přidejte skript **RemoteLight** .

1. Vzhledem k tomu, že tento skript implementuje základní třídu `BaseRemoteLight` , můžete k interakci se vzdáleným světlem použít poskytnutý kontroler zobrazení **AppMenu** . Přejděte na **nástroje relace AppMenu-> – > směrové světlo**.

    > [!NOTE]
    > Uživatelské rozhraní v **AppMenu** bylo omezené na jeden směrový světlo pro zjednodušení. Je však stále možné a doporučujeme přidat bodová světla a připojit skript **RemoteLight** k nim. Tyto další indikátory lze upravit úpravou vlastností světla Unity v editoru. Místní změny vzdáleného světla budete muset ručně synchronizovat pomocí místní nabídky **RemoteLight** v inspektoru:
    >
    > ![Vzdálená světlo ruční synchronizace](./media/sync-remote-light.png)

1. Stiskněte Přehrát v editoru Unity a autorizujte připojení.

1. Po připojení modulu runtime ke vzdálené relaci umístěte a nastavte svůj fotoaparát (použijte WASD a klikněte pravým tlačítkem myši na přesunout), aby se kontroler zobrazení semaforu zobrazil. 
1. Pro úpravu vlastností světla použijte kontroler zobrazení vzdáleného světla. Použití MRTK simulace, otočení a otočení (podržte klávesu CTRL pro otočení) směrového světla, abyste viděli efekt na osvětlení scény.

    ![Směrové světlo](./media/directional-light-test.png)

## <a name="editing-materials"></a>Úprava materiálů

Vzdáleně vykreslené [materiály](../../../concepts/materials.md) je možné upravit tak, aby poskytovaly další vizuální efekty, vyladit vizuály vykreslených modelů nebo poskytovat uživatelům další zpětnou vazbu. Existuje mnoho způsobů a mnoho důvodů, proč upravovat materiál. Tady vám ukážeme, jak se mění barva albedo materiálu a že se mění hrubá a materializace materiálu PBR.

> [!NOTE]
> V mnoha případech platí, že pokud může být funkce nebo efekt implementována pomocí **HierarchicalStateOverrideComponent**, je vhodné ji použít místo úprav materiálu.

Vytvoříme skript, který přijme cílovou entitu a nakonfiguruje několik `OverrideMaterialProperty` objektů, aby se změnily vlastnosti materiálu cílové entity. Začneme tím, že získáme [**MeshComponent**](../../../concepts/meshes.md#meshcomponent)cílové entity, která obsahuje seznam materiálů používaných na síti. Pro zjednodušení použijeme jenom první nalezený materiál. Tato strategie Naive může velmi snadno selhat v závislosti na tom, jak byl obsah vytvořen, takže pravděpodobně budete chtít pro výběr vhodného materiálu využít složitější přístup.

Z materiálu máme přístup k běžným hodnotám, jako je albedo. Nejprve je třeba přetypovat materiály v příslušném typu `PbrMaterial` nebo `ColorMaterial` získat jejich hodnoty, jak je vidět v metodě **GetMaterialColor** . Jakmile máme odkaz na požadovaný materiál, stačí nastavit hodnoty a šipka na ni zpracuje synchronizaci mezi místními vlastnostmi materiálu a vzdáleným materiálem.

1. Vytvořte skript s názvem **EntityMaterialController** a nahraďte jeho obsah následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

`OverrideMaterialProperty`Typ by měl být dostatečně flexibilní, aby bylo možné změnit několik dalších hodnot materiálu, pokud je to požadováno. `OverrideMaterialProperty`Typ sleduje stav přepsání, udržuje starou a novou hodnotu a k nastavení přepsání používá delegáta. Podívejte se například na `ColorOverride` :

```csharp
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Vytvoří se nový `OverrideMaterialProperty` , kde přepsání zabalí typ `Color` . Poskytujeme aktuální nebo původní barvu v době, kdy bylo přepsání vytvořeno. Také mu poskytneme materiál, který bude fungovat. Nakonec je k dispozici delegát, který použije přepsání. Delegát je metoda, která přijímá materiál ARR a typ přepisování. Tato metoda je nejdůležitější součástí porozumění tomu, jak ARR upravuje hodnoty materiálu.

`ColorOverride`Používá `ApplyMaterialColor` metodu ke své práci:

```csharp
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Tento kód akceptuje materiál a barvu. Kontroluje, jaký druh materiálu je následně přetypování materiálu na použití barvy.

`RoughnessOverride`A `MetalnessOverride` funguje podobně – použití `ApplyRoughnessValue` `ApplyMetalnessValue` metod a ke své práci.

Nyní otestujete kontroler materiálu.

1. Přidejte skript **EntityMaterialController** do **TestModel** GameObject.
1. Stisknutím přehrát v Unity spusťte scénu a připojte se k ARR.
1. Po připojení modulu runtime ke vzdálené relaci a načtení modelu přejděte na **AppMenu-> modelové nástroje – > upravit materiál** .
1. Vyberte entitu z modelu pomocí simulovaných rukou a klikněte na **TestModel**.
1. Ověřte, že kontroler zobrazení materiálu (**AppMenu->model nástroje – >upravit materiál**) se aktualizoval na cílovou entitu.
1. Pro úpravu materiálu cílové entity použijte kontroler zobrazení materiálu.

Vzhledem k tomu, že upravujeme jenom první materiál sítě, se nemusí zobrazit změna. Pomocí hierarchického přepsání **SeeThrough** můžete zjistit, jestli se materiál, který měníte, nachází uvnitř sítě.

![Úprava materiálu – příklad](./media/material-edit-example.png)

## <a name="next-steps"></a>Další kroky

Blahopřejeme! Nyní jste implementovali všechny základní funkce vzdáleného vykreslování Azure. V další části se dozvíte, jak zabezpečit vaše vzdálené vykreslování Azure a úložiště objektů BLOB. Toto jsou první kroky pro vydání komerční aplikace, která používá vzdálené vykreslování Azure.

> [!div class="nextstepaction"]
> [Další: zabezpečení vzdáleného vykreslování Azure a úložiště modelu](../security/security.md)