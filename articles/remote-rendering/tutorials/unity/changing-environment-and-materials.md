---
title: Změna prostředí a materiálů
description: Kurz, ve kterém se dozvíte, jak změnit mapu nebe a materiály pro objekty v rámci scény Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679615"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Kurz: Změna prostředí a materiálů

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Změní mapu prostředí scény.
> * Úprava parametrů materiálu.
> * Načíst vlastní textury.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste obeznámeni s [kurzem: práce se vzdálenými entitami v Unity](working-with-remote-entities.md). Potřebujete ale jenom projekt Unity, se kterým se můžete připojit k relacím a načíst model, jak je znázorněno v [kurzu: nastavení projektu Unity od začátku](project-setup.md).

> [!TIP]
> [Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené projekty Unity pro všechny kurzy ve složce *Unity* , které můžete použít jako referenci.

## <a name="change-the-environment-map"></a>Změna mapy prostředí

Vzdálené vykreslování Azure podporuje použití [polí nebe](../../overview/features/sky.md) (někdy také nazývané "mapy prostředí") pro simulaci okolního osvětlení. To je užitečné hlavně v případě, že vaše objekty používají *[fyzicky založené vykreslování](../../overview/features/pbr-materials.md)*, jako naše ukázkové modely. Šipka dolů také nabízí celou řadu integrovaných nebeních textur, které budeme používat v tomto kurzu.

Vytvořte nový skript s názvem **RemoteSky** a přidejte ho do nového GameObject. Otevřete soubor skriptu a nahraďte ho následujícím kódem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Všimněte si, `LoadTextureFromSASAsync` že je použita varianta, protože jsou načteny předdefinované textury. V případě načítání z [propojených úložišť objektů BLOB](../../how-tos/create-an-account.md#link-storage-accounts)použijte `LoadTextureAsync` variantu. Příklad toho, jak to funguje pro modely, najdete v [části načítání modelů](../../concepts/models.md#loading-models).

Když spustíte kód a přepínáte mapy nebe, všimnete si, že váš model má výrazně odlišné osvětlení. Pozadí ale zůstane černé a neuvidíte skutečnou texturu nebe. To je úmyslné, protože vykreslování pozadí by bylo rušivé u zařízení s rozšířenou realitou. Ve vhodné aplikaci byste měli používat nebeská textura, která jsou podobná vašemu reálnému okolí, protože vám to pomůže s tím, aby se objekty zobrazovaly ještě v reálném čase.

## <a name="modify-materials"></a>Úprava materiálů

V předchozím kurzu jsme použili [komponenty přepisu stavu](../../overview/features/override-hierarchical-state.md) ke změně barvy odstínu vybraných objektů. Nyní chceme dosáhnout podobného efektu, ale udělejte to tak, že upravíte [materiál](../../concepts/materials.md) objektu.

Nejdřív potřebujeme skript pro výběr objektů, stejně jako v [druhém kurzu](working-with-remote-entities.md). Pokud ještě nemáte skript **RemoteRaycaster** , vytvořte ho hned teď. Nahraďte jeho obsah následujícím kódem:

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
}
```

Přidejte komponentu do *RemoteRendering* herního objektu. Zodpovídá za vyzvednutí objektů pod myší a přidání součástí *RemoteModelEntity* do vydaných objektů. Tato třída komponenty je místo, kde implementujeme aktuální funkce změny materiálu.

Pokud ještě nemáte skript **RemoteModelEntity** , vytvořte ho a nahraďte jeho obsah tímto kódem:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Při spuštění tohoto kódu se zvýrazní objekty, na které najedete myší. Efekt je podobný jako v kurzu 2, ale způsob, jakým se dosáhne, se liší. Tady získáme seznam materiálů pro vydaný objekt a pak upravíte první z nich tak, aby měl jinou barvu albedo.

> [!IMPORTANT]
> Mějte na paměti, že tato metoda zvýrazňuje správné části modelu, závisí na tom, jak je model vytvořen. Bude fungovat dokonale, pokud každý objekt používá přesně jeden materiál. Pokud však model nemá vztah 1:1 mezi částmi a materiály, Naive kód výše neprovede správnou věc.

## <a name="use-a-different-texture"></a>Použít jinou texturu

[Textury](../../concepts/textures.md) jsou obvykle součástí zdrojového modelu. Během [převodu modelu](../../quickstarts/convert-model.md)jsou všechny textury převedeny do potřebného formátu za běhu a zabaleny do finálního souboru modelu. Chcete-li za běhu nahradit texturu, je nutné ji uložit ve [formátu souboru dds](https://en.wikipedia.org/wiki/DirectDraw_Surface) a odeslat ji do úložiště objektů BLOB v Azure. V [tomto průvodci rychlým startem](../../quickstarts/convert-model.md) najdete postup vytvoření kontejneru objektů BLOB v Azure. Jakmile máte kontejner objektů blob, můžete ho otevřít v Průzkumník služby Azure Storage a nahrát soubor pomocí přetažení.

Na straně modulu runtime můžete vyřešit prostředek textury v úložišti objektů BLOB dvěma různými způsoby:

* Vyřešte texturu podle identifikátoru URI SAS. V takovém případě klikněte pravým tlačítkem na nahraný soubor a v kontextové nabídce vyberte**získat sdílený přístupový podpis...**. Použijte tento identifikátor URI SAS s `LoadTextureFromSASAsync` variantou funkce (viz ukázkový kód níže).
* Vyřešte texturu podle parametrů služby Blob Storage přímo pro případ, že [je úložiště objektů BLOB propojené s účtem](../../how-tos/create-an-account.md#link-storage-accounts). V tomto případě se jedná `LoadTextureAsync`o relevantní funkci načtení.

Nyní otevřete skript **RemoteModelEntity** , přidejte následující kód a odstraňte duplicitní funkce:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Spusťte tento kód a najeďte na model. Pokud má váš model správné souřadnice UV, měla by se zobrazit vaše textura. V opačném případě můžete všimnout změny barev.

## <a name="next-steps"></a>Další kroky

Tím se dokončí naše úvodní série, jak používat vzdálené vykreslování Azure pomocí Unity. V dalším kroku byste se měli seznámit s některými základními koncepcemi ARR, například s [relacemi](../../concepts/sessions.md), [entitami](../../concepts/entities.md)a [modely](../../concepts/models.md) , abyste sestavili hlubší porozumění. K dispozici jsou také různé funkce jako [světla](../../overview/features/lights.md), [vykreslování osnovy](../../overview/features/outlines.md), [hierarchické přepisy stavu](../../overview/features/override-hierarchical-state.md)a [materiály](../../concepts/materials.md) , které byste měli prozkoumat podrobněji.

> [!div class="nextstepaction"]
> [Objekty a komponenty hry Unity](../../how-tos/unity/objects-components.md)
