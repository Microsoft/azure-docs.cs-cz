---
title: 'Rychlý Start: Vytvoření aplikace pro Android'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro Android pomocí prostorových ukotvení.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6b7f924c7f115e8eddda93ea0c096ab94411da72
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87810390"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Rychlý Start: Vytvoření aplikace pro Android pomocí prostorových kotev Azure

Tento rychlý Start popisuje, jak vytvořit aplikaci pro Android pomocí [prostorových kotev Azure](../overview.md) v jazyce Java nebo C++/NDK. Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít ARCore aplikaci pro Android, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítač s Windows nebo macOS, který má <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Pokud používáte systém Windows, budete také potřebovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Pokud používáte macOS, načtěte Git prostřednictvím HomeBrew. Do jednoho řádku terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Pak spusťte příkaz `brew install git` a `brew install git-lfs` .
  - Chcete-li vytvořit ukázku NDK, budete také muset nainstalovat NDK a CMake 3,6 nebo vyšší SDK Tools v Android Studio.
- Zařízení s Androidem podporující <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">vývojáře</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a> .
  - Počítač může pro komunikaci se zařízením s Androidem vyžadovat další ovladače zařízení. Další informace a pokyny najdete [tady](https://developer.android.com/studio/run/device.html) .
- Vaše aplikace musí cílit na ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Stáhněte si `arcore_c_api.h` ho [odsud](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) a umístěte ho do umístění `Android\NDK\libraries\include` .

V rámci nově naklonovaného úložiště inicializujte dílčí modul spuštěním následujícího příkazu:

```console
git submodule update --init --recursive
```

---

Otevřete Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Vyberte **otevřít existující Android Studio projekt** a vyberte projekt v umístění `Android/Java/` .

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Vyberte **otevřít existující Android Studio projekt** a vyberte projekt v umístění `Android/NDK/` .

---

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

# <a name="java"></a>[Java](#tab/openproject-java)

Otevřete `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte ho `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte ho `Set me` identifikátorem účtu.

Vyhledejte `SpatialAnchorsAccountDomain` pole a nahraďte ho `Set me` doménou účtu.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Otevřete `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte ho `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte ho `Set me` identifikátorem účtu.

Vyhledejte `SpatialAnchorsAccountDomain` pole a nahraďte ho `Set me` doménou účtu.

---

## <a name="deploy-the-app-to-your-android-device"></a>Nasazení aplikace na zařízení s Androidem

Zapněte zařízení se systémem Android, přihlaste se a připojte ho k počítači pomocí kabelu USB.

Na panelu nástrojů Android Studio vyberte **Spustit** .

![Nasazení a spuštění Android Studio](./media/get-started-android/android-studio-deploy-run.png)

V dialogovém okně **vybrat cíl nasazení** vyberte zařízení s Androidem a kliknutím na **OK** spusťte aplikaci na zařízení s Androidem.

Podle pokynů v aplikaci založte a odvoláte kotvu.

Zastavte aplikaci tak, že na panelu nástrojů Android Studio vyberete **zastavit** .

![Android Studio zastavit](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
