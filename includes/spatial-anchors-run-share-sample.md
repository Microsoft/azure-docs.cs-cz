---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882312"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

Ukázka Java Android podporuje sdílení přes zařízení.
Otevřete soubor `SharedActivity.java` ze složky Samples v Android Studio. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu pro `SharingAnchorsServiceUrl` v souboru `SharedActivity.java`. Nahraďte `index.html` v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

Ukázka "cíl-C iOS" podporuje sdílení přes zařízení.
Otevřete soubor `SharedDemoViewController.m` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu pro `SharingAnchorsServiceUrl` v souboru `SharedActivity.java`. Nahraďte `index.html` v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Ukázky pro Xamarin Android i iOS podporují sdílení přes zařízení.
Otevřete soubor `AccountDetails.cs` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu pro `AnchorSharingServiceUrl` v souboru `SharedActivity.java`. Nahraďte `index.html` v adrese URL `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>[Jednot](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení s iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** přejděte na `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřete `AzureSpatialAnchorsLocalSharedDemo.unity` soubor scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.Examples\Resources`. Vyberte `SpatialAnchorSamplesConfig`. Pak v podokně **inspektor** zadejte `Sharing Anchors Service url` (z nasazení Azure web app ASP.NET) jako hodnotu `Base Sharing Url`a nahraďte `index.html` `api/anchors`. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

Scénu uložte tak, že vyberete **soubor** > **Uložit**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení na zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** tak, že vyberete **soubor** > **nastavení sestavení**.

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

Ujistěte se, že **projekt pro export** neobsahuje značku zaškrtnutí. Vyberte **sestavení a spustit**. Zobrazí se výzva k uložení souboru `.apk`. Můžete si vybrat libovolný název.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Nasazení na zařízení s iOS

Otevřete **nastavení sestavení** tak, že vyberete **soubor** > **nastavení sestavení**.

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

V Xcode zastavte aplikaci tak, že vyberete **zastavit**.
