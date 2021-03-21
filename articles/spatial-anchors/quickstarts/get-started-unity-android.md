---
title: 'Rychlý Start: Vytvoření aplikace Unity pro Android'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android s využitím prostorových ukotvení.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1554b1728b120145a06124e4703065a98a4e466
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670087"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Rychlý Start: Vytvoření aplikace Unity pro Android pomocí prostorových kotev Azure

Tento rychlý Start popisuje, jak vytvořit aplikaci Unity pro Android pomocí [prostorových kotev Azure](../overview.md). Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít ARCore aplikaci pro Android vytvořenou v Unity, která může ukládat a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Příprava nastavení sestavení Unity
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Exportovat Android Studio projekt
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítač s Windows nebo macOS s využitím <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a>, včetně **podpory buildu pro Android** , **Android SDK se &mi NDK nástroji** a moduly **OpenJDK** . Použijte **unity 2020 LTS** se systémem ASA sdk verze 2,9 nebo novější (který používá [modul plug-in Unity XR](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) nebo **Unity 2019 LTS** se službou ASA SDK verze 2,8 nebo starší.
  - Pokud používáte systém Windows, budete také potřebovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Pokud používáte macOS, načtěte Git prostřednictvím HomeBrew. Do jednoho řádku terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Pak spusťte příkaz `brew install git` a `brew install git-lfs` .
- Zařízení s Androidem podporující <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">vývojáře</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a> .
  - Počítač může pro komunikaci se zařízením s Androidem vyžadovat další ovladače zařízení. Další informace a pokyny najdete [tady](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stáhněte a otevřete vzorový projekt Unity.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exportovat Android Studio projekt

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte zařízení v **běhu** a pak vyberte **Sestavit a spustit**. Zobrazí se výzva k uložení `.apk` souboru, ve kterém můžete vybrat libovolný název.

V aplikaci vyberte **BasicDemo** pomocí šipek a pak stiskněte tlačítko **Přejít.** tlačítko pro spuštění ukázky. Použijte pokyny k umístění a odvolání kotvy.

![Snímek obrazovky 1 ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ snímek obrazovky 2 snímek ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ obrazovky 3](./media/get-started-unity-android/screenshot-3.jpg)

Podle pokynů v aplikaci založte a odvoláte kotvu.

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="rendering-issues"></a>Problémy vykreslování

Pokud při spuštění aplikace nevidíte kameru jako pozadí (například pokud vidíte prázdnou, modrou nebo jinou texturu), pravděpodobně budete muset znovu importovat prostředky v Unity. Zastavte aplikaci. V horní nabídce v Unity vyberte **assets – > znovu importovat vše**. Pak znovu spusťte aplikaci.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Postupy: Konfigurace prostorových kotev Azure v projektu Unity](../how-tos/setup-unity-project.md)
