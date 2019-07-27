---
title: Rychlý Start – vytvoření aplikace Unity pro Android pomocí prostorových kotev Azure | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android s využitím prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 24a3424a73fb21530f3cde227aa9f05f16bd6ad0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562447"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace Unity pro Android pomocí prostorových kotev Azure

Tento rychlý Start popisuje, jak vytvořit aplikaci Unity pro Android pomocí [prostorových kotev Azure](../overview.md). Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít ARCore aplikaci pro Android vytvořenou v Unity, která může ukládat a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Příprava nastavení sestavení Unity
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Exportovat Android Studio projekt
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítač s Windows nebo macOS s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a> včetně modulů podpora sestavení pro Android a Android SDK & NDK nástrojů a <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Pokud používáte systém Windows, budete také potřebovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
  - Pokud používáte macOS, načtěte Git prostřednictvím HomeBrew. Do jednoho řádku terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Potom spusťte `brew install git`příkaz.
- Zařízení s Androidem podporující <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">vývojáře</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a> .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stáhněte a otevřete vzorový projekt Unity.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** přejděte na `Assets/AzureSpatialAnchors.Examples/Scenes` a otevřete `AzureSpatialAnchorsBasicDemo.unity` soubor scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Scénu uložte výběrem**Uložit** **soubor** -> .

## <a name="export-the-android-studio-project"></a>Exportovat Android Studio projekt

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

V nabídce **Spustit zařízení** vyberte zařízení a klikněte na **Sestavit a spustit**. Zobrazí se výzva k uložení `.apk` souboru, pro který můžete vybrat libovolný název.

Podle pokynů v aplikaci založte a odvoláte kotvu.

## <a name="troubleshooting"></a>Řešení potíží

Pokud při spuštění aplikace nevidíte kameru jako pozadí (například místo toho, aby se zobrazily prázdné, modré nebo jiné textury), pravděpodobně budete muset znovu naimportovat prostředky v Unity. Zastavte aplikaci. V horní nabídce v Unity vyberte assets **– > znovu importovat vše**. Pak znovu spusťte aplikaci.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílet prostorové kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
