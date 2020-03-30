---
title: 'Úvodní příručka: Vytvoření aplikace pro Android'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0501c8bb1d71c6cff6033fc937cda019c8890056
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376456"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace pro Android s prostorovými kotvami Azure

Tento rychlý start popisuje, jak vytvořit aplikaci pro Android pomocí [Azure Spatial Anchors](../overview.md) v jazyce Java nebo C++/NDK. Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Po dokončení budete mít aplikaci ARCore pro Android, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítač se systémem Windows nebo macOS se <a href="https://developer.android.com/studio/" target="_blank">systémem Android Studio 3.4+</a>.
  - Pokud běžíte ve Windows, budete potřebovat také <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a Git <a href="https://git-lfs.github.com/">LFS</a>.
  - Pokud běžíte na macOS, nainstalujte Git přes HomeBrew. Do jednoho řádku terminálu zadejte `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`následující příkaz: . Potom spustit `brew install git` `brew install git-lfs`a .
  - Chcete-li vytvořit ukázku NDK, budete také muset nainstalovat NDK a CMake 3.6 nebo větší Nástroje SDK v Android Studio.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Vývojář povoleno</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore schopné</a> zařízení Android.
  - Pro komunikaci s vaším zařízením Android mohou být vyžadovány další ovladače zařízení. Další informace a pokyny naleznete [zde.](https://developer.android.com/studio/run/device.html)
- Vaše aplikace musí cílit na ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevření ukázkového projektu

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[Ndk](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Stáhněte si `arcore_c_api.h` [zde](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) `Android\NDK\libraries\include`a umístěte jej do .

Z nově naklonovaného úložiště inicializovat submoduly spuštěním následujícího příkazu:

```console
git submodule update --init --recursive
```

---

Otevřete Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Vyberte **Otevřít existující projekt Android Studio** `Android/Java/`a vyberte projekt umístěný na .

# <a name="ndk"></a>[Ndk](#tab/openproject-ndk)

Vyberte **Otevřít existující projekt Android Studio** `Android/NDK/`a vyberte projekt umístěný na .

---

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Otevřete `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

# <a name="ndk"></a>[Ndk](#tab/openproject-ndk)

Otevřete `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

---

## <a name="deploy-the-app-to-your-android-device"></a>Nasazení aplikace do zařízení Android

Zapojte zařízení se systémem Android, přihlaste se a připojte jej k počítači pomocí kabelu USB.

Na panelu nástrojů Android Studio vyberte **Spustit.**

![Nasazení a spuštění sady Android Studio](./media/get-started-android/android-studio-deploy-run.png)

V dialogovém okně **Vybrat cíl nasazení** vyberte zařízení Android a vyberte **OK,** chcete-li aplikaci spustit na zařízení android.

Podle pokynů v aplikaci umístěte a vzpomenete kotvu.

Zastavte aplikaci výběrem **Stop** z panelu nástrojů Android Studio.

![Android Studio Stop](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
