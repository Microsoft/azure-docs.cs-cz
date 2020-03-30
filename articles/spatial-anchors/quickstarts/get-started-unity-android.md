---
title: 'Úvodní příručka: Vytvoření aplikace Unity pro Android'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android s Unity pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615441"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace Unity pro Android s prostorovými kotvami Azure

Tento rychlý start popisuje, jak vytvořit aplikaci Unity Android pomocí [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Až budete hotovi, budete mít arcore android aplikace postavena s Unity, který můžete uložit a vyvolat prostorové kotvy.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Příprava nastavení sestavení Unity
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Export projektu Android Studio
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítač se systémem Windows nebo macOS s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 nebo 2019.2</a> včetně podpory sestavení Androidu a sady SDK pro Android & moduly NDK Tools.
  - Pokud běžíte ve Windows, budete potřebovat také <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a Git <a href="https://git-lfs.github.com/">LFS</a>.
  - Pokud běžíte na macOS, nainstalujte Git přes HomeBrew. Do jednoho řádku terminálu zadejte `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`následující příkaz: . Potom spustit `brew install git` `brew install git-lfs`a .
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Vývojář povoleno</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore schopné</a> zařízení Android.
  - Pro komunikaci s vaším zařízením Android mohou být vyžadovány další ovladače zařízení. Další informace a pokyny naleznete [zde.](https://developer.android.com/studio/run/device.html)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stažení a otevření ukázkového projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

V podokně **Projektu** `Assets/AzureSpatialAnchors.Examples/Scenes` přejděte `AzureSpatialAnchorsBasicDemo.unity` na soubor scény a otevřete jej.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte scénu výběrem **možnosti Uložit** -> **soubor**.

## <a name="export-the-android-studio-project"></a>Export projektu Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte zařízení v **aplikaci Spustit zařízení** a klepněte na **tlačítko Sestavit a spustit**. Budete vyzváni k uložení `.apk` souboru, pro který můžete vybrat libovolný název.

Podle pokynů v aplikaci umístěte a vzpomenete kotvu.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="rendering-issues"></a>Problémy s vykres

Při spuštění aplikace, pokud nevidíte fotoaparát jako pozadí (například místo toho vidíte prázdné, modré nebo jiné textury), pak budete pravděpodobně muset znovu importovat datové zdroje v Unity. Zastavte aplikaci. V horní nabídce v Unity zvolte **Prostředky -> Reimport all**. Potom aplikaci spusťte znovu.

### <a name="unity-20193"></a>Jednota 2019.3

Kvůli zásadním změnám není unity 2019.3 v současné době podporována. Použijte unity 2019.1 nebo 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
