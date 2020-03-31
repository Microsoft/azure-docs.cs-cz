---
title: 'Úvodní příručka: Vytvoření aplikace pro iOS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6304077a26f5c0ecb91e1ec4936bd79b3d839d95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471213"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Úvodní příručka: Vytvoření aplikace pro iOS s prostorovými kotvami Azure, ať už v Rychlém nebo Objektiv-C

Tento rychlý start popisuje, jak vytvořit aplikaci pro iOS pomocí [azure prostorových kotev](../overview.md) v Swift nebo Objective-C. Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Až budete hotovi, budete mít aplikaci ARKit pro iOS, která může uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na iOS zařízení

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Vývojářský počítač s macOS s nainstalovanou nejnovější verzí <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> a <a href="https://cocoapods.org" target="_blank">CocoaPods.</a>
- Git nainstalován přes HomeBrew:
  1. V terminálu zadejte následující příkaz `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`jako jeden řádek: . 
  1. Spusťte příkazy `brew install git` a `brew install git-lfs`.
  1. Aktualizujte git config s `git lfs install` (pro `git lfs install --system` aktuálního uživatele) nebo (pro celý systém).
- Vývojář povolil <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zařízení iOS kompatibilní s ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevření ukázkového projektu

Pomocí terminálu proveďte následující akce.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nainstalujte potřebné lusky pomocí KakaoPody:

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

Spusťte `pod install --repo-update` instalaci KakaoPods pro projekt.

Nyní otevřete `.xcworkspace` v Xcode.

> [!NOTE]
> Pokud máte problémy s KakaoPodem po upgradu na macOS Catalina (10.15), přečtěte si postup řešení [potíží zde.](#cocoapods-issues-on-macos-catalina-1015)

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Otevřete `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Vyhledejte `spatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `spatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Otevřete `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do iOS zařízení

Připojte iOS zařízení k Macu a nastavte **aktivní schéma** k iOS zařízení.

![Vyberte zařízení](./media/get-started-ios/select-device.png)

Vyberte **Sestavit a spusťte aktuální schéma**.

![Nasazení a spuštění](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Pokud se `library not found for -lPods-SampleObjC` zobrazí chyba, pravděpodobně `.xcodeproj` otevřete `.xcworkspace`soubor namísto . Otevřete `.xcworkspace` a akci opakujte.

V Xcode zastavte aplikaci stisknutím **klávesy Stop**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problémy na macOS Catalina (10.15)

Pokud jste nedávno aktualizovali na macOS Catalina (10.15) a měli cocoaPods nainstalovánpředem, CocoaPods může `.xcworkspace` být v rozbitém stavu a nepodaří správně nakonfigurovat lusky a soubory projektu. Chcete-li tento problém vyřešit, budete muset přeinstalovat CocoaPods spuštěním následujících příkazů:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Při nasazování do iOS 10.3.1 z osobního zřizovacího profilu nebo vývojářského účtu dojde k chybě aplikace. 

Pokud aplikaci pro iOS nasadíte na iOS 10.3.1 z osobního `Library not loaded: @rpath/ADAL...`zřizovacího profilu nebo vývojářského účtu, může se zobrazit tato chyba: . 

Chcete-li tento problém vyřešit:

- Použijte zřizovací profil, který není profil osobního týmu (placený vývojářský účet).
- Nasaďte aplikaci na iOS zařízení se systémem iOS 13.3 nebo starším nebo na zařízení s beta verzí iOS 13.4 nebo verzí.
- Přečtěte si další informace o tomto problému na [přetečení zásobníku](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
