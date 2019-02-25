---
title: Rychlý start – vytvoření aplikace pro Android pomocí Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak k vytváření aplikací pro Android pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: ed5e33d45127a61a9c87249cc3eba88050554a54
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752183"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace pro Android pomocí Azure prostorových kotvy

Tento rychlý Start obsahuje postup pro vytvoření aplikace pro Android s využitím [prostorových kotvy Azure](../overview.md) v jazyce Java nebo C + +/ sada NDK. Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci ARCore Android, která můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Windows nebo macOS počítače s <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - K vytvoření vzorku NDK, budete také muset nainstalovat NDK a CMake 3.6 SDK Tools v nástroji Android Studio.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">developer povolené</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore podporující</a> zařízení s Androidem.
- Aplikace musí cílit na ARCore 1.5 (podpora ARCore 1.6 + budou již brzy)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete ukázkový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Pokud vytváříte ukázkové sady Android NDK, budete muset stáhnout `arcore_c_api.h` z [tady](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h) a umístit ji `Android\NDK\libraries\include`.

Otevřete Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Vyberte **otevřete existující projekt Android Studio** a vyberte projekt v `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Vyberte **otevřete existující projekt Android Studio** a vyberte projekt v `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

Dalším krokem je konfigurace aplikace použijte identifikátor účtu a klíč účtu zaznamenané dříve při nastavování zdroje prostorových ukotvení.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Otevřít `Android/Java/app/src/main/java/com/microsoft/sampleandroid/ARActive.java`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte `Set me` s identifikátor účtu.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Otevřít `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte `Set me` s identifikátor účtu.

***

## <a name="deploy-the-app-to-your-android-device"></a>Nasazení aplikace na zařízení s Androidem

Zapněte zařízení s Androidem, přihlaste a připojte ho k počítači pomocí kabelu USB.

Vyberte **spustit** z panelu nástrojů Android Studio.

![Android Studio nasadit a spustit](./media/get-started-android/android-studio-deploy-run.png)

Vyberte zařízení s Androidem v **vyberte cíl nasazení** dialogového okna a vyberte **OK** ke spuštění aplikace na zařízení s Androidem.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

Zastavte aplikaci tak, že vyberete **Zastavit** z panelu nástrojů Android Studio.

![Android Studio Stop](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
