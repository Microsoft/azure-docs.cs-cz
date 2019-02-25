---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752141"
---
## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Nastavení pro zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Nastavení pro zařízení se systémem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsLocalSharedDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Také v **inspektoru** podokně, zadejte `Sharing Anchors Service url` (z webové aplikace ASP.NET nasazení v Azure) hodnotu `Base Sharing Url`a nahraďte `index.html` s `api/anchors`. Proto by měl vypadat: `https://<app_name>.azurewebsites.net/api/anchors`.

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="to-deploy-to-an-android-device"></a>Nasazení do zařízení s Androidem

Zapněte zařízení s Androidem, přihlaste a připojte ho k počítači pomocí kabelu USB.

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
