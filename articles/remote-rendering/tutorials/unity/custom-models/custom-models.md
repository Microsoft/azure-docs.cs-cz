---
title: Rozhraní a vlastní modely
description: Přidání řadičů zobrazení a ingestování vlastních modelů, které se mají vykreslovat pomocí vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 5793e2958edce0a4c97660a75d0ecefa914c12d2
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559078"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Kurz: rozhraní a vlastní modely

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přidat sadu nástrojů Mixed reality do projektu
> * Spravovat stav modelu
> * Konfigurace služby Azure Blob Storage pro ingestování modelů
> * Nahrávání a zpracování modelů pro vykreslování

## <a name="prerequisites"></a>Předpoklady

* Tento kurz sestaví [kurz: zobrazení vzdáleně vykresleného modelu](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Začínáme se sadou nástrojů Mixed reality Toolkit (MRTK)

Mixed reality Toolkit (MRTK) je sada nástrojů pro různé platformy, která slouží k vytváření hybridních prostředí realit. Pro své funkce interakce a vizualizace použijeme MRTK 2.5.1.

Pokud chcete přidat MRTK, postupujte podle [požadovaných kroků](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) uvedených v [Průvodci instalací MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

Jde o následující kroky:
 - [Získat nejnovější balíčky Unity pro MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - I když říká "poslední", použijte prosím verzi 2.5.1 ze stránky verze MRTK.
     - V tomto kurzu používáme jenom balíček *Foundation* . Balíčky *rozšíření*, *nástroje* a *Příklady* se nevyžadují.
 - [Import balíčků MRTK do projektu Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Přepnout projekt Unity na cílovou platformu](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Tento krok byste si měli udělat už v první kapitole, ale teď je dobrý čas na dvojí kontrolu.
 - [Přidat MRTK do nové scény nebo nového projektu](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Můžete přidat MRTK do nové scény a znovu přidat koordinátora a objekty nebo skripty modelu, nebo můžete přidat MRTK k existující scéně pomocí sady *Mixed reality Toolkit – > přidat do scény a nakonfigurovat* příkaz nabídky.

## <a name="import-assets-used-by-this-tutorial"></a>Importovat assety používané v tomto kurzu

Od této kapitoly implementujeme pro většinu pokrytého materiálu jednoduchý [vzor modelu zobrazení modelu](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) . *Vzorová* část vzoru je specifický kód pro vzdálené vykreslení Azure a Správa stavu související se vzdáleným vykreslováním Azure. Části *zobrazení* a *kontroléru* vzoru jsou implementovány pomocí prostředků MRTK a některých vlastních skriptů. V tomto kurzu je možné použít *model* bez *zobrazení* , které je zde implementováno. Toto oddělení umožňuje snadno integrovat kód, který se nachází v tomto kurzu, do vlastní aplikace, kde se převezme část *zobrazení* v rámci vzoru návrhu.

Po zavedení MRTK se do projektu dá přidat spousta skriptů, prefabs a prostředků, které se teď dají přidat do projektu, aby se podporovaly interakce a vizuální zpětná vazba. Tyto prostředky, na které se říká **výukové materiály**, se zahrnou do [balíčku Asset Unity](https://docs.unity3d.com/Manual/AssetPackages.html), který je zahrnutý v [GitHubu vzdáleného vykreslování Azure](https://github.com/Azure/azure-remote-rendering) v části "\Unity\TutorialAssets\TutorialAssets.unitypackage".

1. Klonování nebo stažení úložiště Git [vzdálené vykreslování Azure](https://github.com/Azure/azure-remote-rendering), pokud se stahuje, extrahuje soubor zip do známého umístění.
1. V projektu Unity vyberte *assets-> importovat balíček-> vlastní balíček*.
1. V Průzkumníku souborů přejděte do adresáře, ve kterém jste naklonováni nebo zrušte použití úložiště vzdáleného vykreslování Azure, a pak vyberte soubor. unitypackage, který najdete v **Unity-> TutorialAssets-> TutorialAssets. unitypackage** .
1. Výběrem tlačítka **importovat** importujte obsah balíčku do projektu.
1. V editoru Unity vyberte *Mixed reality Toolkit-> Utility-> upgradujte MRTK Standard shader pro odlehčený kanál vykreslování* z horního panelu nabídek a podle pokynů upgradujte shader.

Po MRTK a výukových kurzů v projektu přepneme profil MRTK na ten, který je pro tento kurz vhodný.

1. V hierarchii scény vyberte **MixedRealityToolkit** GameObject.
1. V inspektoru v rámci součásti **MixedRealityToolkit** přepněte konfigurační profil na *ARRMixedRealityToolkitConfigurationProfile*.
1. Změny uložte stisknutím *kombinace kláves CTRL + S* .

Tím se nakonfigurují MRTK, primárně s výchozími profily HoloLens 2. Zadané profily jsou předem nakonfigurovány následujícími způsoby:
 - Vypnutím profileru (stisknutím klávesy 9 ho můžete zapnout nebo vypnout) nebo vyslovením příkazu Zobrazit/skrýt Profiler na zařízení).
 - Vypněte ukazatel pohledu pro oči.
 - Povolí kliknutí myší Unity, takže můžete kliknout na MRTK prvky uživatelského rozhraní myší namísto simulované ruky.

## <a name="add-the-app-menu"></a>Přidat nabídku aplikace

Většina řadičů zobrazení v tomto kurzu pracuje s abstraktními základními třídami, nikoli s konkrétními třídami. Tento model poskytuje větší flexibilitu a umožňuje nám poskytovat řadiče zobrazení pro vás a pořád vám pomáhá naučit se kód vzdáleného vykreslování Azure. Pro zjednodušení třída **RemoteRenderingCoordinator** nemá poskytnutou abstraktní třídu a její kontroler zobrazení funguje přímo proti konkrétní třídě.

Teď můžete do scény přidat Prefab **AppMenu** , abyste mohli vizuální zpětnou vazbu aktuálního stavu relace. Tento kontroler zobrazení bude při implementaci a integraci dalších funkcí ARR do scény "odemčený" další dílčí nabídkou zobrazení. Prozatím bude **AppMenu** vizuální indikaci stavu ARR a prezentovat modální panel, který uživatel používá k autorizaci aplikace pro připojení k Arr.

1. Vyhledání **AppMenu** Prefab v *assets/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Přetáhněte **AppMenu** Prefab do scény.
1. Pravděpodobně se zobrazí dialogové okno pro **importování TMP**, protože se jedná o první, kdy do scény patří assety pro práci s *textovými oky pro* . Podle pokynů **importujte základy TMP**. Pak zavřete dialog pro import, příklady a další funkce nejsou potřeba.
1. **AppMenu** se nakonfiguruje tak, aby se automaticky připojila a poskytovala modální spojení pro připojení k relaci, takže můžeme odebrat vynechání, které jste nastavili dříve. Na GameObject **RemoteRenderingCoordinator** odeberte nepoužitou položku pro autorizaci, kterou jsme dřív implementovali, a to tak, že stisknete tlačítko "–" na **vyžádání události při žádosti o autorizaci** .
 ![Odebrat vynechat ](./media/remove-bypass-event.png) .
1. Kliknutím na tlačítko **Přehrát** v editoru Unity otestujte kontroler zobrazení.
1. V editoru teď, když je MRTK nakonfigurovaný, můžete pomocí klíčů WASD změnit umístění vašeho zobrazení a podržet pravé tlačítko myši + přesunout ukazatel myši a změnit směr zobrazení. Vyzkoušejte "jízda" kolem scény, abyste získali dojem pro ovládací prvky.
1. Na zařízení můžete dovolávat svůj Palm k předvolání **AppMenu** v editoru Unity, a to pomocí klávesové zkratky '.
1. Pokud jste ztratili pohled na nabídku, stiskněte klávesu ' m ' k předvolání nabídky. Nabídka se umístí poblíž kamery a umožní tak snadnou interakci.
1. Autorizace se teď zobrazí jako žádost napravo od **AppMenu**, od této chvíle, kterou použijete k autorizaci aplikace ke správě relací vzdáleného vykreslování.
 ![Autorizace uživatelského rozhraní](./media/authorize-request-ui.png)
1. Pokud chcete pokračovat v kurzu, zastavte si Unity.

## <a name="manage-model-state"></a>Spravovat stav modelu

Nyní implementujeme nový skript **RemoteRenderedModel** , který slouží ke sledování stavu, reagování na události, vyvolávání událostí a konfiguraci. V podstatě **RemoteRenderedModel** ukládá vzdálenou cestu pro data modelu v `modelPath` . Bude naslouchat změnám stavu v **RemoteRenderingCoordinator** , aby bylo možné zjistit, zda by měl model, který definuje, automaticky načíst nebo uvolnit. GameObject, která má **RemoteRenderedModel** připojenou, bude místní nadřazenou položkou vzdáleného obsahu.

Všimněte si, že skript **RemoteRenderedModel** implementuje **BaseRemoteRenderedModel**, který je zahrnutý v **výukových prostředcích**. Tím umožníte, aby kontroler zobrazení vzdáleného modelu navázal vazby s vaším skriptem.

1. Vytvořte nový skript s názvem **RemoteRenderedModel** ve stejné složce jako **RemoteRenderingCoordinator**. Celý obsah nahraďte následujícím kódem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Ve většině základních podmínek obsahuje **RemoteRenderedModel** data potřebná k načtení modelu (v tomto případě identifikátor URI SAS nebo *Builtin://* ) a sleduje stav vzdáleného modelu. Když je čas načíst, je `LoadModel` metoda volána na **RemoteRenderingCoordinator** a entita obsahující model je vrácena pro referenci a uvolnění.

## <a name="load-the-test-model"></a>Načíst model testu

Pojďme nový skript otestovat načtením testovacího modelu znovu. Vytvoříme herní objekt, který bude obsahovat skript a musí být nadřazený testovacímu modelu.

1. Vytvořte nový prázdný herní objekt na scéně a pojmenujte ho **TestModel**.
1. Přidejte skript *RemoteRenderedModel* do **TestModel**.
![Přidat komponentu RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Vyplňte v `Model Display Name` `Model Path` uvedeném pořadí a zadejte "*TestModel*" a "*Builtin://Engine*".
![Zadat podrobnosti modelu](./media/add-model-script.png)
1. Umístěte objekt **TestModel** před fotoaparát na pozici **x = 0, y = 0, z = 3**.
![Pozice – objekt](./media/test-model-position.png)
1. Ujistěte se, že je zapnutá funkce **AutomaticallyLoad** .
1. Kliknutím na tlačítko **Přehrát** v editoru Unity otestujete aplikaci.
1. Udělte autorizaci kliknutím na tlačítko *připojit* a umožněte aplikaci vytvořit relaci, která se připojí k relaci a automaticky načte model.

Sledujte konzolu, když aplikace pokračuje prostřednictvím svých stavů. Pamatujte si, že některé stavy můžou nějakou dobu trvat a nebude se zobrazovat průběh. Nakonec se zobrazí protokoly z načítání modelu a potom se model testu vykreslí na scéně.

Zkuste přesunout a otočit **TestModel** GameObject přes transformaci v inspektoru nebo v zobrazení scény. Uvidíte, že se model přesune a otočí v zobrazení herních zařízení.

![Protokol Unity](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Zřizování Blob Storage v Azure a přijímání vlastních modelů

Teď můžeme zkusit načíst váš vlastní model. Abyste to mohli udělat, musíte nakonfigurovat Blob Storage a v Azure, nahrát a převést model a potom model nahrajeme pomocí skriptu **RemoteRenderedModel** . Pokud nemáte vlastní model, který se má v tuto chvíli načíst, můžete postup načítání vlastního modelu bezpečně přeskočit.

Postupujte podle kroků uvedených v [rychlém startu: převod modelu pro vykreslování](../../../quickstarts/convert-model.md). Pro účely tohoto kurzu přeskočte oddíl **vložení nového modelu do ukázkové aplikace pro rychlé** zprovoznění. Až budete mít identifikátor URI *sdíleného přístupového podpisu (SAS)* pro ingestný model, pokračujte dalším krokem.

## <a name="load-and-rendering-a-custom-model"></a>Načtení a vykreslení vlastního modelu

1. Vytvořte ve scéně novou prázdnou GameObject a pojmenujte ji jako svůj vlastní model.
1. Přidejte skript *RemoteRenderedModel* do nově vytvořené GameObject.
 ![Přidat komponentu RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Zadejte `Model Display Name` pro svůj model příslušný název.
1. Zadejte `Model Path` identifikátor URI *sdíleného přístupového podpisu (SAS)* modelu, který jste vytvořili v krocích příjmu výše.
1. Umístěte GameObject před fotoaparátem na pozici **x = 0, y = 0, z = 3.**
1. Ujistěte se, že je zapnutá funkce **AutomaticallyLoad** .
1. Kliknutím na tlačítko **Přehrát** v editoru Unity otestujete aplikaci.

    Uvidíte, že se konzola začne naplnit aktuálním stavem, a nakonec bude modelovat zprávy o průběhu. Váš vlastní model se pak nahraje do scény.

1. Odeberte objekt vlastního modelu z scény. Nejlepší prostředí pro tento kurz bude používat model testování. I když je v ARR podporováno více modelů, byl tento kurz napsán tak, aby nejlépe podporoval jeden vzdálený model v jednom okamžiku.

## <a name="next-steps"></a>Další kroky

Nyní můžete načíst vlastní modely do vzdáleného vykreslování Azure a zobrazit je ve vaší aplikaci. V dalším kroku Vás provedeme manipulací s vašimi modely.

> [!div class="nextstepaction"]
> [Další: manipulace s modely](../manipulate-models/manipulate-models.md)
