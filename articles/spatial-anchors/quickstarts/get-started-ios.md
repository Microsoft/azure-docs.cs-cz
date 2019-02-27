---
title: Rychlý start – vytvoření aplikace pro iOS pomocí Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvářet aplikace pro iOS pomocí prostorových kotvy.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: bb64e2b633957ca92636a03333be40aa275dfd25
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870240"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Rychlý start: Vytvoření aplikace pro iOS pomocí Azure prostorových ukotvení v Swift a Objective-C

Tento rychlý start popisuje, jak vytvořit aplikaci s iOS pomocí [prostorových kotvy Azure](../overview.md) v Swift a Objective-C. Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci pro iOS ARKit, která můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Nasazení a spuštění na zařízení s iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítače s macOS pro vývojáře povolené s <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10 +</a> a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> nainstalované.
- Vývojář povolené <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit kompatibilní</a> zařízení s Iosem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete ukázkový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instalace nezbytné podů pomocí CocoaPods:

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

***

Spustit `pod install --repo-update` nainstalujte CocoaPods pro projekt.

Nyní otevřete `.xcworkspace` v Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

Dalším krokem je konfigurace aplikace použijte identifikátor účtu a klíč účtu zaznamenané dříve při nastavování zdroje prostorových ukotvení.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Otevřít `iOS/Swift/SampleSwift/ViewController.swift`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte `Set me` s identifikátor účtu.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Otevřít `iOS/Objective-C/SampleObjC/ViewController.m`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte `Set me` s identifikátor účtu.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace na zařízení s Iosem

Připojit zařízení s Iosem do Mac a nastavte **aktivní schéma** na zařízení s Iosem.

![Vybrat zařízení](./media/get-started-ios/select-device.png)

Vyberte **sestavení a poté spusťte aktuální schéma**.

![Nasazení a spuštění](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-SampleObjC` chyba, pravděpodobně otevřením `.xcodeproj` souborů namísto `.xcworkspace`. Otevřít `.xcworkspace` a zkuste to znovu.

V Xcode, zastavte aplikaci stisknutím klávesy **Zastavit**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
