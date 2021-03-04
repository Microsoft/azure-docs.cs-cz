---
title: 'Rychlý Start: vytvoření modelu ukotvení objektů, který se má použít v aplikaci'
description: V tomto rychlém startu se dozvíte, jak vytvořit model ukotvení objektu z 3D model.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 93443a321f22d232f8b2bfa6a0fe5a05a5e18175
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748549"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Rychlý Start: vytvoření modelu ukotvení objektů z 3D model

Kotvy objektů Azure je spravovaná cloudová služba, která převádí 3D modely na modely AI, které pro HoloLens umožňují pracovat s různými objekty. Tento rychlý Start popisuje, jak vytvořit model ukotvení objektu z 3D model pomocí sady C#/.NET Core SDK.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu pro kotvy objektů
> * Převedení 3D model pro vytvoření modelu kotev objektu

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Počítač s Windows A sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git pro Windows</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">Sada .NET Core 3,1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Vytvoření účtu pro kotvy objektů

Nejprve je třeba vytvořit účet se službou kotvy objektů.

1. Otevřete [Azure Portal](https://portal.azure.com/) a vyberte **vytvořit prostředek**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Vytvořit nový prostředek":::

2. Vyhledejte prostředek **kotvy objektů** .

   Vyhledejte "kotvy objektů".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Vyberte prostředek ukotvení objektů.":::

   Ve výsledcích hledání **objektu kotvy objektů** vyberte **kotvy objektů Create->**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Vytvoření prostředku ukotvení objektů":::

3. V dialogovém okně **účet kotvy objektů** :
    * Zadejte jedinečný název prostředku.
    * Vyberte předplatné, ke kterému chcete prostředek připojit.
    * Vytvořte nebo použijte existující skupinu prostředků.
    * Vyberte oblast, ve které se má prostředek vyskytovat.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Zadejte podrobnosti účtu zdroje kotev objektů.":::

    Vyberte **vytvořit** a začněte vytvářet prostředek.

4. Jakmile bude prostředek vytvořen, vyberte **Přejít k prostředku**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Přejít k prostředku":::

5. Na stránce Přehled:

   Poznamenejte si **doménu účtu**. Budete ho potřebovat později.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Zkopírujte doménu účtu pro prostředek ukotvení objektu.":::

   Poznamenejte si **ID účtu**. Budete ho potřebovat později.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Zkopírujte ID účtu pro prostředek ukotvení objektu.":::

   Přejděte na stránku **přístupové klíče** a poznamenejte si **primární klíč**. Budete ho potřebovat později.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Zkopírujte klíč účtu pro prostředek ukotvení objektu.":::

## <a name="get-the-sample-project"></a>Získání ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Převést 3D model

Nyní můžete pokračovat a převést 3D model.

1. Otevřete `quickstarts/conversion/Conversion.sln` v aplikaci Visual Studio. Toto řešení obsahuje projekt konzoly jazyka C#.

2. Otevřete `Configuration.cs` soubor umístěný v kořenovém adresáři projektu a nahraďte `set-me` hodnoty v následujících polích:

   | Pole         | Popis                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | **Doména účtu** účtu kotvy objektů byla vytvořena výše. |
   | AccountId     | **ID účtu** účtu ukotvení objektu, který byl vytvořen výše.     |
   | AccountKey    | **Primární klíč** účtu ukotvení objektu, který byl vytvořen výše     |

   Existují čtyři další pole, která je třeba ověřit:

    | Pole                    | Popis                       |
    | ---                      | ---                               |
    | InputAssetPath           | Absolutní cesta k 3D model v místním počítači (ve `assets/models` složce, kterou můžete použít) je ukázkový model. Podporované formáty souborů jsou `fbx` , `ply` ,, a `obj` `glb` `gltf` . |
    | AssetDimensionUnit       | Jednotka měření 3D model. Ke všem podporovaným jednotkám měření se dá dostat pomocí `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` výčtu. |
    | Gravitace                  | Směr vektoru gravitace 3D model. Tento 3D vektor poskytuje směr směrem dolů v systému souřadnic vašeho modelu. Například pokud záporná `y` představuje směr směrem dolů v prostorovém prostoru modelu, tato hodnota by byla `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Sestavte a spusťte projekt, abyste nahráli 3D model, zaregistrujte novou úlohu převodu se službou a počkejte, než se dokončí. Po dokončení úlohy se model ukotvení objektu stáhne vedle souboru zadaného v `InputAssetPath` . Měl by se zobrazit něco podobného jako u následujícího výstupu konzoly:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Poznamenejte si **ID úlohy** pro budoucí referenci. Může být užitečné při ladění nebo řešení potíží.

4. Po úspěšném dokončení úlohy by se měl `<Model-Filename-Without-Extension>_<JobID>.ou` v zadaném umístění výstupu zobrazit soubor s formátem. Pokud je například název souboru 3D model `chair.ply` a ID úlohy je, bude se `00000000-0000-0000-0000-000000000000` jednat o název souboru, který bude obsahovat výstupy služby `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet ukotvení objektu a převedli 3D model pro vytvoření modelu ukotvení objektů. Chcete-li se dozvědět, jak tento model integrovat se sadou SDK ukotvení objektů v aplikaci hybridní reality, pokračujte některým z následujících článků:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [HoloLens v Unity s MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
