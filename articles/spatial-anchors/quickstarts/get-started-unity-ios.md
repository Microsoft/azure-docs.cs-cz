---
title: Rychlý start – vytvoření aplikace iOS Unity s Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak k vytváření aplikací pro iOS pomocí Unity pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 41189d4945ef22f8a587a51717b7dd6f40ac39ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690549"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace iOS Unity s Azure prostorových kotvy

Tento rychlý start popisuje, jak vytvořit aplikaci iOS Unity pomocí [prostorových kotvy Azure](../overview.md). Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Až budete hotovi, budete mít aplikaci pro iOS ARKit vytvořených pomocí Unity, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Příprava nastavení buildu Unity
> * Stáhnout a naimportovat modul plug-in ARKit Unity
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Export projektů Xcode
> * Nasazení a spuštění na zařízení s iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítače s macOS s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a>, a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> nainstalované.
- Git instalovaných pomocí instalace HomeBrew. Jako jeden řádek, terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Potom spusťte `brew install git`.
- Vývojář povolené <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit kompatibilní</a> zařízení s Iosem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsBasicDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="export-the-xcode-project"></a>Export projektů Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

> [!NOTE]
> Při spuštění aplikace, pokud se nezobrazí fotoaparátu/kamery jako pozadí (pro instance, zobrazí se místo toho prázdnou hodnotu, modrá nebo jiných textury) pak budete pravděpodobně muset znovu importovat prostředky v Unity. Zastavte aplikaci. Z hlavní nabídky v Unity, zvolte **prostředků -> znovu importovat všechny**. Spusťte aplikaci znovu.

V Xcode, zastavte aplikaci stisknutím klávesy **Zastavit**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
