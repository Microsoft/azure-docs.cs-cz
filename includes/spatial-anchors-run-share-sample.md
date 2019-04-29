---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681188"
---
## <a name="set-up-your-device"></a>Nastavení zařízení

V Unity, otevřete projekt v `Unity` složky.

![Okno Unity](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení se systémem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsLocalSharedDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V **inspektoru** podokně, zadejte `Sharing Anchors Service url` (z webové aplikace ASP.NET nasazení v Azure) hodnotu `Base Sharing Url`a nahraďte `index.html` s `api/anchors`. By měl vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

Uložte výběrem scény **souboru** > **Uložit**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení do zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte k počítači pomocí kabelu USB.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí z jiných scény.

Ujistěte se, že **Export projektů** nemá značku zaškrtnutí. Vyberte **sestavíte a spustíte**. Budete vyzváni k uložení vaší `.apk` souboru. Můžete vybrat libovolný název.

Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**. První scénář umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný. Druhý scénář, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, umožňuje najít dříve sdílené ukotvení. Jakmile vyberete váš scénář, aplikace vás provede s další pokyny kolem jak. Například budete vyzváni k pohyb zařízení ke shromažďování informací o prostředí. Později je budete umístit ukotvení na světě, bude Počkejte, dokud se odešle a tak dále.

### <a name="deploy-to-an-ios-device"></a>Nasazení do zařízení se systémem iOS

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí z jiných scény.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**. První scénář umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný. Druhý scénář, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, umožňuje najít dříve sdílené ukotvení. Jakmile vyberete váš scénář, aplikace vás provede s další pokyny kolem jak. Například budete vyzváni k pohyb zařízení ke shromažďování informací o prostředí. Později je budete umístit ukotvení na světě, bude Počkejte, dokud se odešle a tak dále.

V Xcode, zastavte aplikaci tak, že vyberete **Zastavit**.
