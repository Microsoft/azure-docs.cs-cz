---
title: 'Rychlý Start: Vytvoření aplikace Unity pro iOS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS s Unity pomocí prostorových ukotvení.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3cbf0fa4a32145add0a5475f22ea0d936584c9bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465195"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Rychlý Start: Vytvoření aplikace Unity pro iOS pomocí prostorových kotev Azure

Tento rychlý Start popisuje, jak vytvořit aplikaci Unity pro iOS pomocí [prostorových kotev Azure](../overview.md). Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít ARKit aplikaci pro iOS vytvořenou v Unity, která může ukládat a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Příprava nastavení sestavení Unity
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Export projektu Xcode
> * Nasazení a spuštění na zařízení s iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- MacOS počítač s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a>, nejnovější verzí <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> nainstalované.
- Git se nainstaloval přes HomeBrew. Do jednoho řádku terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Pak spusťte `brew install git` a `brew install git-lfs`.
- Vývojářem podporujícím zařízení s iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">kompatibilní s ARKit</a> .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stáhněte a otevřete vzorový projekt Unity.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** přejděte na `Assets/AzureSpatialAnchors.Examples/Scenes` a otevřete `AzureSpatialAnchorsBasicDemo.unity` soubor scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Scénu uložte tak, že vyberete **soubor** -> **Uložit**.

## <a name="export-the-xcode-project"></a>Export projektu Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Podle pokynů v aplikaci založte a odvoláte kotvu.

Po dokončení zastavte aplikaci stisknutím tlačítka **zastavit** v Xcode.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="rendering-issues"></a>Problémy vykreslování

Pokud při spuštění aplikace nevidíte kameru jako pozadí (například místo toho, aby se zobrazily prázdné, modré nebo jiné textury), pravděpodobně budete muset znovu naimportovat prostředky v Unity. Zastavte aplikaci. V horní nabídce v Unity vyberte **prostředky – > znovu importovat vše**. Pak znovu spusťte aplikaci.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problémy s CocoaPods na macOS Catalina (10,15)

Pokud jste nedávno aktualizovali na macOS Catalina (10,15) a původně jste CocoaPods nainstalovali, CocoaPods může být v nefunkčním stavu a nemusí správně konfigurovat soubory lusku a `.xcworkspace` projektu. Chcete-li tento problém vyřešit, budete muset přeinstalovat CocoaPods spuštěním následujících příkazů:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
