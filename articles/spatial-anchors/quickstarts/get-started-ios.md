---
title: 'Rychlý Start: Vytvoření aplikace pro iOS'
description: Naučte se vytvářet aplikace pro iOS pomocí prostorových kotev Azure programově buď v SWIFT, nebo v objektivu – C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009071"
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
- Git nainstalovaný přes HomeBrew:
  1. Jako jeden řádek v terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . 
  1. Spusťte příkazy `brew install git` a `brew install git-lfs`.
  1. Aktualizujte konfiguraci Gitu pomocí `git lfs install` (pro aktuálního uživatele) nebo `git lfs install --system` (pro celý systém).
- Vývojářem podporujícím zařízení s iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">kompatibilní s ARKit</a> .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

Pomocí terminálu proveďte následující akce.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nainstalujte potřebné lusky pomocí CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Přejděte na adresu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Přejděte na adresu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Spusťte `pod install --repo-update` pro instalaci CocoaPods pro projekt.

Nyní otevřete `.xcworkspace` v Xcode.

> [!NOTE]
> Pokud máte CocoaPod problémy po upgradu na macOS Catalina (10,15), Projděte [si postup](#cocoapods-issues-on-macos-catalina-1015) řešení potíží.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Otevřete `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Vyhledejte `spatialAnchorsAccountKey` pole a nahraďte ho `Set me` klíčem účtu.

Vyhledejte `spatialAnchorsAccountId` pole a nahraďte ho `Set me` identifikátorem účtu.

Vyhledejte `spatialAnchorsAccountDomain` pole a nahraďte ho `Set me` doménou účtu.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Otevřete `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte ho `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte ho `Set me` identifikátorem účtu.

Vyhledejte `SpatialAnchorsAccountDomain` pole a nahraďte ho `Set me` doménou účtu.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do zařízení s iOS

Připojte zařízení s iOS k počítači Mac a nastavte **aktivní schéma** na zařízení s iOS.

![Vyberte zařízení](./media/get-started-ios/select-device.png)

Vyberte **Sestavit a potom spusťte aktuální schéma**.

![Nasazení a spuštění](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-SampleObjC` Chyba, pravděpodobně jste soubor otevřeli `.xcodeproj` místo `.xcworkspace` . Otevřete `.xcworkspace` a zkuste to znovu.

V Xcode zastavte aplikaci stisknutím klávesy **stop**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problémy s CocoaPods na macOS Catalina (10,15)

Pokud jste nedávno aktualizovali na macOS Catalina (10,15) a původně jste CocoaPods nainstalovali, CocoaPods může být v nefunkčním stavu a nemusí správně konfigurovat soubory lusků a `.xcworkspace` projektů. Chcete-li tento problém vyřešit, budete muset přeinstalovat CocoaPods spuštěním následujících příkazů:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Chyba aplikace při nasazování do iOS 10.3.1 z osobního zřizovacího profilu/účtu vývojáře 

Pokud nasadíte aplikaci pro iOS v iOS 10.3.1 z osobního zřizovacího profilu/vývojářského účtu, může se zobrazit tato chyba: `Library not loaded: @rpath/ADAL...` . 

Řešení tohoto problému:

- Použijte zřizovací profil, který není osobním týmovým profilem (placený vývojářský účet).
- Nasaďte aplikaci do zařízení se systémem iOS se systémem iOS 13,3 nebo starším nebo na jednu verzi systému iOS 13,4 beta nebo release.
- Přečtěte si další informace o tomto problému na [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
