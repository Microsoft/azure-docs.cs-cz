---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: b46a2b18309851bbe2934980137a53d2de6f6efc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135352"
---
## <a name="set-up-your-device-in-unity"></a>Nastavit vaše zařízení v Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

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

V části **scény v sestavení**, zkontrolujte všechny informace pro pokročilé uživatele se zobrazí zaškrtnutí vedle sebe.

Ujistěte se, že **Export projektů** nemá značku zaškrtnutí. Vyberte **sestavíte a spustíte**. Budete vyzváni k uložení vaší `.apk` souboru. Můžete vybrat libovolný název.

Po spuštění aplikace, **ukázku A zvolte** dialogového okna, vyberte pomocí šipky vlevo nebo vpravo **LocalShare** možnost a klepněte na **Přejít!** . Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**.

První scénář umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný.
Druhý scénář, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, umožňuje najít dříve sdílené ukotvení. Jakmile vyberete váš scénář, aplikace vás provede další pokyny kolem co dělat. Například budete vyzváni k pohyb zařízení ke shromažďování informací o prostředí. Později budete umístit ukotvení na světě, počkejte na Uložit a podobně.

### <a name="deploy-to-an-ios-device"></a>Nasazení do zařízení se systémem iOS

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V části **scény v sestavení**, zkontrolujte všechny informace pro pokročilé uživatele se zobrazí zaškrtnutí vedle sebe.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Po spuštění aplikace, **ukázku A zvolte** dialogového okna, vyberte pomocí šipky vlevo nebo vpravo **LocalShare** možnost a klepněte na **Přejít!** . Postupujte podle pokynů v aplikaci. Můžete vybrat **vytvořit & sdílenou složku ukotvení** nebo **vyhledejte sdílené ukotvení**.

První scénář umožňuje vytvářet anchor, který může být umístěn později na stejné zařízení nebo na jiný.
Druhý scénář, pokud jste již spustili aplikaci, na stejné zařízení nebo na jiný, umožňuje najít dříve sdílené ukotvení. Jakmile vyberete váš scénář, aplikace vás provede další pokyny kolem co dělat. Například budete vyzváni k pohyb zařízení ke shromažďování informací o prostředí. Později budete umístit ukotvení na světě, počkejte na Uložit a podobně.

V Xcode, zastavte aplikaci tak, že vyberete **Zastavit**.
