---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: bfeb8bddf5fe3b4a76e662aed6c5a07439d2f1cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908620"
---
## <a name="set-up-your-device"></a>Nastavení zařízení

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení se systémem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsLocalSharedDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Také v **inspektoru** podokně, zadejte `Sharing Anchors Service url` (z webové aplikace ASP.NET nasazení v Azure) hodnotu `Base Sharing Url`a nahraďte `index.html` s `api/anchors`. Proto by měl vypadat: `https://<app_name>.azurewebsites.net/api/anchors`.

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="to-deploy-to-an-android-device"></a>Nasazení do zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** -> **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí zkontrolujte značky ze všech ostatních scény.

Zkontrolujte **Export projektů** zaškrtávacího políčka nemá značku zaškrtnutí. Klikněte na tlačítko **sestavíte a spustíte**. Zobrazí se dotaz, chcete-li uložit vaše `.apk` soubor, můžete vybrat libovolný název.

Postupujte podle pokynů v aplikaci. Můžete také **vytvořit & sdílenou složku ukotvení**, nebo **vyhledejte sdílené ukotvení**. První možnost vám umožňuje vytvořit Anchor, který můžete později nacházet na stejné zařízení nebo na jiném. Druhou možnost, pokud jste dříve spustili na aplikaci, a to buď na stejné zařízení nebo na jiném, vám umožní vyhledejte dříve sdílené ukotvení.

## <a name="to-deploy-to-an-ios-device"></a>Nasazení do zařízení se systémem iOS

Otevřít **nastavení sestavení** tak, že vyberete **souboru** -> **nastavení sestavení**.

V části **scény v sestavení**, zaškrtněte políčko vedle položky `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scény a zrušte zaškrtnutí zkontrolujte značky ze všech ostatních scény.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Postupujte podle pokynů v aplikaci. Můžete také **vytvořit & sdílenou složku ukotvení**, nebo **vyhledejte sdílené ukotvení**. První možnost vám umožňuje vytvořit Anchor, který můžete později nacházet na stejné zařízení nebo na jiném. Druhou možnost, pokud jste dříve spustili na aplikaci, a to buď na stejné zařízení nebo na jiném, vám umožní vyhledejte dříve sdílené ukotvení.

V Xcode, zastavte aplikaci stisknutím klávesy **Zastavit**.
