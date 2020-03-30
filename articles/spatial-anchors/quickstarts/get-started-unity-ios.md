---
title: 'Úvodní příručka: Vytvoření aplikace Unity pro iOS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS s Unity pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240587"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace Unity pro iOS s prostorovými kotvami Azure

Tento rychlý start popisuje, jak vytvořit aplikaci Unity pro iOS pomocí [azure prostorových kotev](../overview.md). Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Až budete hotovi, budete mít vytvořenou aplikaci ARKit pro iOS s Unity, která dokáže uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Příprava nastavení sestavení Unity
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Export projektu Xcode
> * Nasazení a spuštění na iOS zařízení

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- MacOS stroj s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 nebo 2019.2</a>, nejnovější verze <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>a <a href="https://cocoapods.org" target="_blank">KakaoPody</a> nainstalován.
- Git nainstalován přes HomeBrew. Do jednoho řádku terminálu zadejte `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`následující příkaz: . Potom spustit `brew install git` `brew install git-lfs`a .
- Vývojář povolil <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zařízení iOS kompatibilní s ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stažení a otevření ukázkového projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

V podokně **Projektu** `Assets/AzureSpatialAnchors.Examples/Scenes` přejděte `AzureSpatialAnchorsBasicDemo.unity` na soubor scény a otevřete jej.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte scénu výběrem **možnosti Uložit** -> **soubor**.

## <a name="export-the-xcode-project"></a>Export projektu Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Podle pokynů v aplikaci umístěte a vzpomenete kotvu.

Po dokončení zastavte aplikaci stisknutím **klávesy Stop** v Xcode.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="rendering-issues"></a>Problémy s vykres

Při spuštění aplikace, pokud nevidíte fotoaparát jako pozadí (například místo toho vidíte prázdné, modré nebo jiné textury), pak budete pravděpodobně muset znovu importovat datové zdroje v Unity. Zastavte aplikaci. V horní nabídce v Unity zvolte **Prostředky -> Přeimportovat vše**. Potom aplikaci spusťte znovu.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problémy na macOS Catalina (10.15)

Pokud jste nedávno aktualizovali na macOS Catalina (10.15) a měli cocoaPods nainstalovánpředem, CocoaPods může `.xcworkspace` být v rozbitém stavu a nepodaří správně nakonfigurovat lusky a soubory projektu. Chcete-li tento problém vyřešit, budete muset přeinstalovat CocoaPods spuštěním následujících příkazů:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Jednota 2019.3

Kvůli zásadním změnám není unity 2019.3 v současné době podporována. Použijte unity 2019.1 nebo 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
