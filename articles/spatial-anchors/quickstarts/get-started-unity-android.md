---
title: Rychlý start – vytvoření aplikace pro Android Unity pomocí Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak k vytváření aplikací pro Android pomocí Unity pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961003"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace pro Android Unity s Azure prostorových kotev vztahů

Tento rychlý start popisuje, jak vytvořit aplikaci Android Unity pomocí [prostorových kotvy Azure](../overview.md). Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít ARCore Android aplikace vytvořené pomocí Unity, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Příprava nastavení buildu Unity
> * Stáhnout a naimportovat sady SDK ARCore pro Unity
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Export projektů Android Studio
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Windows nebo macOS počítače s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> a <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a> nainstalované.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">developer povolené</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore podporující</a> zařízení s Androidem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsBasicDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="export-the-android-studio-project"></a>Export projektů Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **exportovat** otevřete dialogové okno. Vyberte složku pro export projektů Android Studio.

Po dokončení exportu do složky se zobrazí obsahující exportované projekt Android Studio, s podsložce s názvem **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Nasazení aplikace pro Android

Otevřete Android Studio a vyberte **otevřete existující projekt Android Studio**. Vyberte **HelloAR U3D** podsložky v exportovaném projektu Android Studio a klikněte na **OK**.

Při otevírání, se zobrazí výzva s výzvou k použití obálky Gradle. Vyberte **OK** použití obálky Gradle a otevřete projekt.

Zapněte zařízení s Androidem, přihlaste a připojte ho k počítači pomocí kabelu USB.

Vyberte **spustit** z panelu nástrojů Android Studio.

![Android Studio nasadit a spustit](./media/get-started-unity-android/android-studio-deploy-run.png)

Vyberte zařízení s Androidem v **vyberte cíl nasazení** dialogového okna a vyberte **OK** ke spuštění aplikace na zařízení s Androidem.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

> [!NOTE]
> Při spuštění aplikace, pokud se nezobrazí fotoaparátu/kamery jako pozadí (pro instance, zobrazí se místo toho prázdnou hodnotu, modrá nebo jiných textury) pak budete pravděpodobně muset znovu importovat prostředky v Unity. Zastavte aplikaci. Z hlavní nabídky v Unity, zvolte **prostředků -> všechny opakovaný import**. Spusťte aplikaci znovu.

Zastavte aplikaci tak, že vyberete **Zastavit** z panelu nástrojů Android Studio.

![Android Studio Stop](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
