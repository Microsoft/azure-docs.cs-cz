---
title: 'Rychlý Start: Vytvoření aplikace pro iOS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS pomocí prostorových ukotvení.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 277a669484201a060a2bb5455d6154165bbb8e84
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465162"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Rychlý Start: Vytvoření aplikace pro iOS pomocí prostorových kotev Azure v SWIFT nebo objektivní-C

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS pomocí [prostorových kotev Azure](../overview.md) buď v SWIFT, nebo v cíli-C. Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít ARKit aplikaci pro iOS, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení s iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Vývojář s povoleným macOS počítačem s nainstalovanou nejnovější verzí <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> .
- Git se nainstaloval přes HomeBrew. Do jednoho řádku terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Pak spusťte `brew install git` a `brew install git-lfs`.
- Vývojářem podporujícím zařízení s iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">kompatibilní s ARKit</a> .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

Pomocí terminálu proveďte následující akce.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nainstalujte potřebné lusky pomocí CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Přejděte na adresu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Přejděte na adresu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Spusťte `pod install --repo-update` pro instalaci CocoaPods pro projekt.

Nyní otevřete `.xcworkspace` v Xcode.

> [!NOTE]
> Pokud máte CocoaPod problémy po upgradu na macOS Catalina (10,15), Projděte [si postup](#cocoapods-issues-on-macos-catalina-1015) řešení potíží.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Otevřít `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Vyhledejte pole `spatialAnchorsAccountKey` a nahraďte `Set me` klíčem účtu.

Vyhledejte pole `spatialAnchorsAccountId` a nahraďte `Set me` identifikátorem účtu.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Otevřít `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Vyhledejte pole `SpatialAnchorsAccountKey` a nahraďte `Set me` klíčem účtu.

Vyhledejte pole `SpatialAnchorsAccountId` a nahraďte `Set me` identifikátorem účtu.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do zařízení s iOS

Připojte zařízení s iOS k počítači Mac a nastavte **aktivní schéma** na zařízení s iOS.

![Vyberte zařízení.](./media/get-started-ios/select-device.png)

Vyberte **Sestavit a potom spusťte aktuální schéma**.

![Nasazení a spuštění](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Pokud se zobrazí chyba `library not found for -lPods-SampleObjC`, pravděpodobně jste otevřeli soubor `.xcodeproj` namísto `.xcworkspace`. Otevřete `.xcworkspace` a zkuste to znovu.

V Xcode zastavte aplikaci stisknutím klávesy **stop**.

## <a name="troubleshooting"></a>Řešení potíží

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
