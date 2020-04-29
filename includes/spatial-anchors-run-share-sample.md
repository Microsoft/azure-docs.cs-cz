---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "72882312"
---
## <a name="android"></a>[Android](#tab/Android)

Ukázka Java Android podporuje sdílení přes zařízení.
Otevřete soubor `SharedActivity.java` ze složky Samples v Android Studio. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `SharingAnchorsServiceUrl` v `SharedActivity.java` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Ukázka "cíl-C iOS" podporuje sdílení přes zařízení.
Otevřete soubor `SharedDemoViewController.m` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `SharingAnchorsServiceUrl` v `SharedActivity.java` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Ukázky pro Xamarin Android i iOS podporují sdílení přes zařízení.
Otevřete soubor `AccountDetails.cs` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `AnchorSharingServiceUrl` v `SharedActivity.java` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení s iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** přejděte na `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřete soubor `AzureSpatialAnchorsLocalSharedDemo.unity` scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.Examples\Resources`. Vyberte `SpatialAnchorSamplesConfig`. Pak v podokně **inspektor** `Sharing Anchors Service url` zadejte (z nasazení Azure Web App ASP.NET) jako hodnotu `Base Sharing Url`pro, nahraďte `index.html` hodnotou. `api/anchors` Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

Scénu uložte výběrem Uložit **soubor** > **Save**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení na zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** výběrem**nastavení sestavení** **souboru** > .

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

Ujistěte se, že **projekt pro export** neobsahuje značku zaškrtnutí. Vyberte **sestavení a spustit**. Zobrazí se výzva k uložení `.apk` souboru. Můžete si vybrat libovolný název.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Nasazení na zařízení s iOS

Otevřete **nastavení sestavení** výběrem**nastavení sestavení** **souboru** > .

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

V Xcode zastavte aplikaci tak, že vyberete **zastavit**.
