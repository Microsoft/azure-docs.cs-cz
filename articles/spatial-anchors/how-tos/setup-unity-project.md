---
title: Instalace prostorových kotev Azure pro Unity
description: Konfigurace projektu Unity pro použití prostorových kotev Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536347"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurace prostorových kotev Azure v projektu Unity

V této příručce se dozvíte, jak začít se sadou SDK prostorových kotev Azure v projektu Unity.

## <a name="requirements"></a>Požadavky

Prostorová ukotvení Azure aktuálně podporuje Unity 2019,4 (LTS) s následujícími konfiguracemi.

* V prostorových ukotveních Azure 2.4.0 + je podporovaná Unity 2019,4 s AR Foundation 3,1.

## <a name="configuring-a-project"></a>Konfigurace projektu

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Přidejte do projektu balíčky správce balíčků Unity.](#tab/UPMPackage)

Prostorové kotvy Azure pro Unity jsou aktuálně distribuované pomocí balíčků správce balíčků Unity (UPM). Tyto balíčky najdete v našem [registru npm](https://bintray.com/microsoft/AzureMixedReality-NPM). Další informace o práci s vymezenými Registry balíčků v projektu Unity najdete v [oficiální dokumentaci k](https://docs.unity3d.com/Manual/upm-scoped.html)Unity.

#### <a name="add-the-registry-to-your-unity-project"></a>Přidání registru do projektu Unity

1. V Průzkumníku souborů přejděte do složky vašeho projektu Unity `Packages` . Otevřete soubor manifestu projektu, `manifest.json` v textovém editoru.
2. V horní části souboru na stejné úrovni jako v `dependencies` části přidejte následující položku pro zahrnutí registru prostorových kotev Azure do vašeho projektu. Tato `scopedRegistries` položka obsahuje informace o Unity, kde hledat balíčky sady SDK prostorových kotev Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Přidat balíčky sady SDK do projektu Unity

| Platforma | Název balíčku                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. prostorová ukotvení – sada SDK. Android |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-SDK. iOS     |
| HoloLens | com. Microsoft. Azure. prostorová ukotvení – sada SDK. Windows |

1. Pro každou platformu (Android/iOS/HoloLens), kterou chcete v projektu podporovat, přidejte položku s názvem balíčku a verzí balíčku do `dependencies` oddílu v manifestu projektu. Níže najdete příklad.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Soubor uložte a zavřete `manifest.json` . Když se vrátíte do Unity, Unity by měl automaticky detekovat změnu manifestu projektu a načíst zadané balíčky. Můžete rozbalit `Packages` složku v zobrazení projektu a ověřit, zda byly naimportovány správné balíčky.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Jenom Android: konfigurace souboru mainTemplate. Gradle

1. Přejít na **Upravit**  >  **nastavení projektu**  >  **Player**.
2. Na **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Androidu** .
3. V části **sestavení** zaškrtněte políčko **vlastní hlavní Gradle šablona** , čímž vygenerujete vlastní šablonu Gradle na adrese `Assets\Plugins\Android\mainTemplate.gradle` .
4. Otevřete `mainTemplate.gradle` soubor v textovém editoru. 
5. V `dependencies` části vložte následující závislosti:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Až to bude hotové, vaše `dependencies` část by měla vypadat přibližně takto:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Import balíčku assetů](#tab/UnityAssetPackage)

> [!WARNING]
> Po instalování sady SDK verze 2.5.0 bude distribuována sada prostředků služby Unity v sadě SDK prostorových kotev Azure.

1. Stáhněte si `AzureSpatialAnchors.unitypackage` soubor pro verzi, kterou chcete cílit ze [vydání GitHubu](https://github.com/Azure/azure-spatial-anchors-samples/releases). 
2. Postupujte podle pokynů uvedených [tady](https://docs.unity3d.com/Manual/AssetPackagesImport.html) a importujte balíček Asset Unity do svého projektu.    

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: vytváření a hledání kotev v Unity](./create-locate-anchors-unity.md)
