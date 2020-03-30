---
title: 'Úvodní příručka: Vytvoření aplikace Xamarin pro Android'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android s Xamarin pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465178"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace Xamarin pro Android s prostorovými kotvami Azure

Tento rychlý start popisuje, jak vytvořit aplikaci pro Android s Xamarin pomocí [Azure Prostorové kotvy](../overview.md). Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Po dokončení budete mít aplikaci pro Android, která může uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:
- Počítač se systémem Windows nebo macOS:
  - Pokud používáte Windows:
    - Aktuální verze Visual Studia <a href="https://www.visualstudio.com/downloads/" target="_blank">2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Pokud používáte macOS:
    - Aktuální verze Visual Studia <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">pro Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git pro macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- Nejnovější verze Xamarin.Android nainstalován a běží na vaší platformě volby. Průvodce instalací Xamarin.Android naleznete v příručkách [pro instalaci Xamarin.Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/index)
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Vývojář povoleno</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore schopné</a> zařízení Android.
  - Pro komunikaci s vaším zařízením Android mohou být vyžadovány další ovladače zařízení. Další informace naleznete [zde](https://developer.android.com/studio/run/device.html).
- Vaše aplikace musí cílit na ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevření ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřít `Xamarin/SampleXamarin.sln` v sadě Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

Otevřete `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

## <a name="deploy-the-app-to-your-android-device"></a>Nasazení aplikace do zařízení Android

Zapojte zařízení se systémem Android, přihlaste se a připojte jej k počítači pomocí kabelu USB.

Nastavte projekt spuštění na **SampleXamarin.Android**, změňte **konfiguraci řešení** na **verzi**a vyberte zařízení, do kterého chcete nasadit v rozevíracím okně pro výběr zařízení.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Konfigurace sady Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Vyberte **Ladění** > **Start ladění** k nasazení a spuštění aplikace.

# <a name="macos"></a>[Macos](#tab/deploy-macos)

![Konfigurace sady Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Chcete-li nasadit a spustit aplikaci, vyberte **spustit** > **spustit spustit bez ladění.**

---

V aplikaci vyberte **Základní** spustit ukázku a postupujte podle pokynů umístit a odvolat kotvu.

> ![Snímek](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![obrazovky](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![1 Snímek obrazovky 2 Snímek obrazovky 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
