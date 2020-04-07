---
title: Změna prostředí a materiálů
description: Kurz, který ukazuje, jak upravit mapu oblohy a objektové materiály ve scéně Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679615"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Kurz: Změna prostředí a materiálů

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Změňte mapu prostředí scény.
> * Upravte parametry materiálu.
> * Načtěte vlastní textury.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste obeznámeni s [kurzem: Práce se vzdálenými entitami v Unity](working-with-remote-entities.md). Potřebujete však pouze projekt Unity, pomocí kterého se můžete připojit k relacím a načíst model, jak je znázorněno v [kurzu: Nastavení projektu Unity od začátku](project-setup.md).

> [!TIP]
> [Úložiště vzorků ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené unity projekty pro všechny kurzy ve složce *Unity,* které můžete použít jako odkaz.

## <a name="change-the-environment-map"></a>Změna mapy prostředí

Azure Remote Rendering podporuje použití [sky boxů](../../overview/features/sky.md) (někdy také nazývaných "mapy prostředí") k simulaci okolního osvětlení. To je užitečné zejména v případě, že objekty používají *[fyzicky založené vykreslování](../../overview/features/pbr-materials.md)*, jako naše ukázkové modely. ARR také přichází s řadou vestavěných textur oblohy, které budeme používat v tomto tutoriálu.

Vytvořte nový skript s názvem **RemoteSky** a přidejte jej do nového GameObject. Otevřete soubor skriptu a nahraďte jej následujícím kódem:

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

Všimněte `LoadTextureFromSASAsync` si, že varianta se používá výše, protože jsou načteny předdefinované textury. V případě načtení z [propojených úložišť objektů blob](../../how-tos/create-an-account.md#link-storage-accounts)použijte variantu. `LoadTextureAsync` Příklad toho, jak to funguje u modelů, naleznete v [části načítání modelu](../../concepts/models.md#loading-models).

Když spustíte kód a přepínáte mapy oblohy, všimnete si drasticky odlišného osvětlení vašeho modelu. Pozadí však zůstane černé a nevidíte skutečnou texturu oblohy. To je úmyslné, protože vykreslování pozadí by bylo rušivé se zařízením rozšířené reality. Ve správné aplikaci byste měli použít textury oblohy, které jsou podobné vašemu reálnému světu, protože to pomůže, aby objekty vypadaly skutečněji.

## <a name="modify-materials"></a>Úprava materiálů

V předchozím kurzu jsme použili [komponenty přepsání stavu](../../overview/features/override-hierarchical-state.md) ke změně barvy tónu vybraných objektů. Nyní chceme dosáhnout podobného efektu, ale uděláte to úpravou [materiálu](../../concepts/materials.md) objektu.

Nejprve potřebujeme skript pro výběr objektů, jak jsme to [udělali ve druhém tutoriálu](working-with-remote-entities.md). Pokud ještě nemáte skript **RemoteRaycaster,** vytvořte jej nyní. Nahraďte jeho obsah následujícím kódem:

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

Přidejte komponentu do objektu hry *RemoteRendering.* Je zodpovědný za výběr objektů pod myší a přidání *komponent RemoteModelEntity* do vybraných objektů. Tato třída komponent je místo, kde implementujeme skutečné funkce změny materiálu.

Pokud ještě nemáte skript **RemoteModelEntity,** vytvořte jej a nahraďte jeho obsah tímto kódem:

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

Při spuštění tohoto kódu se zvýrazní objekty, které najedete myší. Efekt je podobný tomu, co jsme udělali v tutoriálu 2, ale způsob, jakým je dosaženo, je jiný. Zde získáme seznam materiálů na vyskladněný objekt a pak upravíme první, který má jinou barvu albedo.

> [!IMPORTANT]
> Uvědomte si, zda tato metoda zvýrazní správné části modelu, závisí na tom, jak je model vytvořen. Bude fungovat perfektně, pokud každý objekt používá přesně jeden materiál. Pokud však model nemá vztah 1:1 mezi díly a materiály, výše uvedený naivní kód neudělá správnou věc.

## <a name="use-a-different-texture"></a>Použití jiné textury

[Textury](../../concepts/textures.md) jsou obvykle součástí zdrojového modelu. Během [převodu modelu](../../quickstarts/convert-model.md)jsou všechny textury převedeny do potřebného formátu runtime a zabaleny do konečného souboru modelu. Chcete-li nahradit texturu za běhu, musíte ji uložit ve [formátu dds souboru](https://en.wikipedia.org/wiki/DirectDraw_Surface) a nahrát ji do úložiště objektů blob Azure. V [tomto průvodci rychlým startem](../../quickstarts/convert-model.md) se podívejte, jak vytvořit kontejner objektů blob Azure. Jakmile budete mít kontejner objektů blob, můžete ho otevřít v Průzkumníkovi Azure Storage Explorer a nahrát soubor pomocí přetažení.

Na straně runtime můžete prostředek textury v úložišti objektů blob adresovat dvěma odlišnými způsoby:

* Textura adresy podle identifikátoru URI SAS. Za tímto skutečností klikněte pravým tlačítkem myši na nahraný soubor a z kontextové nabídky vyberte možnost**Získat sdílený přístupový podpis.** Použijte tento identifikátor URI `LoadTextureFromSASAsync` SAS s variantou funkce (viz ukázkový kód níže).
* Adresute texturu parametry úložiště objektů blob přímo v případě, že [úložiště objektů blob je propojeno s účtem](../../how-tos/create-an-account.md#link-storage-accounts). Relevantní nakládací `LoadTextureAsync`funkce v tomto případě je .

Nyní otevřete skript **RemoteModelEntity,** přidejte následující kód a odeberte duplicitní funkce:

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

Spusťte tento kód a najeďte na model. Pokud má váš model správné UV souřadnice, měla by se zobrazit textura. V opačném případě si můžete všimnout pouze změny barvy.

## <a name="next-steps"></a>Další kroky

Tím končí naše úvodní série o tom, jak používat azure vzdálené vykreslování s Unity. Jako další krok byste se měli seznámit sami některé základní pojmy ARR, jako jsou [relace](../../concepts/sessions.md), [entity](../../concepts/entities.md)a [modely](../../concepts/models.md) vybudovat hlubší porozumění. Existují také různé funkce, jako jsou [světla](../../overview/features/lights.md), [vykreslování osnovy](../../overview/features/outlines.md), [přepsání hierarchického stavu](../../overview/features/override-hierarchical-state.md)a [materiály,](../../concepts/materials.md) které byste měli prozkoumat podrobněji.

> [!div class="nextstepaction"]
> [Unity herní objekty a komponenty](../../how-tos/unity/objects-components.md)
