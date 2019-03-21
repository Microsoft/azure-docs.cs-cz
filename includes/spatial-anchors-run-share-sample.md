---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305278"
---
## <a name="set-up-your-device"></a>Nastavení zařízení

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení se systémem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsLocalSharedDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V **inspektoru** podokně, zadejte `Sharing Anchors Service url` (z webové aplikace ASP.NET nasazení v Azure) hodnotu `Base Sharing Url`a nahraďte `index.html` s `api/anchors`. By měl vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

Uložte výběrem scény **souboru** > **Uložit**.

## <a name="to-deploy-the-app-to-an-android-device"></a>K nasazení aplikace na zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte k počítači pomocí kabelu USB.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí z jiných scény.

Ujistěte se, že **Export projektů** nemá značku zaškrtnutí. Vyberte **sestavíte a spustíte**. Budete vyzváni k uložení vaší `.apk` souboru. Můžete vybrat libovolný název.

Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**. První možnost vám umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný. Druhá možnost, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, vám umožní vyhledejte dříve sdílené ukotvení.

## <a name="to-deploy-the-app-to-an-ios-device"></a>K nasazení aplikace do zařízení s iOS

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí z jiných scény.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**. První možnost vám umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný. Druhá možnost, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, vám umožní vyhledejte dříve sdílené ukotvení.

V Xcode, zastavte aplikaci tak, že vyberete **Zastavit**.
