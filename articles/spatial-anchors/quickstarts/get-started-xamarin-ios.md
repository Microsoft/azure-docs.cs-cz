---
title: 'Úvodní příručka: Vytvoření aplikace Xamarin pro iOS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro iOS s Xamarin pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465141"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace Pro iOS Xamarin s prostorovými kotvami Azure

Tento rychlý start popisuje, jak vytvořit aplikaci pro iOS s Xamarin pomocí [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Po dokončení budete mít aplikaci pro iOS, která může uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na iOS zařízení

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:
- Mac se systémem macOS High Sierra (10.13) nebo vyšším s:
  - Nejnovější verze Xcode a iOS SDK nainstalovaná z [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Aktuální verze Visual Studia <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">pro Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git pro macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevření ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřít `Xamarin/SampleXamarin.sln` v sadě Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

Otevřete `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do iOS zařízení

Zapojte iOS zařízení, přihlaste se a připojte ho k počítači pomocí kabelu USB.

Nastavte projekt spuštění na **SampleXamarin.iOS**, změňte **konfiguraci řešení** na **verzi**a vrozeném výběru zařízení vyberte zařízení, do kterého chcete nasadit.

![Konfigurace sady Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Chcete-li nasadit a spustit aplikaci, vyberte **spustit** > **spustit spustit bez ladění.**

V aplikaci vyberte **Základní** spustit ukázku a postupujte podle pokynů umístit a odvolat kotvu.

> ![Snímek](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![obrazovky](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![1 Snímek obrazovky 2 Snímek obrazovky 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
