---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882312"
---
## <a name="android"></a>[Android](#tab/Android)

Ukázka systému Java android podporuje sdílení mezi zařízeními.
Otevřete `SharedActivity.java` soubor ze složky ukázky v Android Studio. Zadejte adresu URL, kterou jste získali v předchozím kroku (z ASP.NET nasazení webové aplikace Azure) jako hodnotu v `SharingAnchorsServiceUrl` souboru. `SharedActivity.java` Nahraďte `index.html` v `api/anchors`adrese url s . Mělo by to `https://<app_name>.azurewebsites.net/api/anchors`vypadat takto: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Ukázka objective-c iOS podporuje sdílení mezi zařízeními.
Otevřete `SharedDemoViewController.m` soubor ve složce ukázky. Zadejte adresu URL, kterou jste získali v předchozím kroku (z ASP.NET nasazení webové aplikace Azure) jako hodnotu v `SharingAnchorsServiceUrl` souboru. `SharedActivity.java` Nahraďte `index.html` v `api/anchors`adrese url s . Mělo by to `https://<app_name>.azurewebsites.net/api/anchors`vypadat takto: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Ukázky Xamarin u Androidu i iOS podporují sdílení mezi zařízeními.
Otevřete `AccountDetails.cs` soubor ve složce ukázky. Zadejte adresu URL, kterou jste získali v předchozím kroku (z ASP.NET nasazení webové aplikace Azure) jako hodnotu v `AnchorSharingServiceUrl` souboru. `SharedActivity.java` Nahraďte `index.html` v `api/anchors`adrese url s . Mělo by to `https://<app_name>.azurewebsites.net/api/anchors`vypadat takto: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení iOS zařízení

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

V podokně **Projektu** `Assets/AzureSpatialAnchorsPlugin/Examples` přejděte `AzureSpatialAnchorsLocalSharedDemo.unity` na soubor scény a otevřete jej.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **Projektu** `Assets\AzureSpatialAnchors.Examples\Resources`přejděte na . Vyberte `SpatialAnchorSamplesConfig`. Potom v podokně **Inspektor** `Sharing Anchors Service url` zadejte (z ASP.NET nasazení webové aplikace `Base Sharing Url`Azure) `index.html` `api/anchors`jako hodnotu pro , nahrazující . Mělo by to `https://<app_name>.azurewebsites.net/api/anchors`vypadat takto: .

Uložte scénu výběrem **možnosti Uložit** > **soubor**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení do zařízení Android

Přihlaste se na zařízení Android a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** výběrem**položky Nastavení sestavení** **souborů** > .

V části **Scény v sestavení**zajistěte, aby všechny scény byly zaškrtnuty vedle sebe.

Ujistěte se, že **exportní projekt** nemá zaškrtnutí. Vyberte **sestavit a spustit**. Budete vyzváni k uložení `.apk` souboru. Můžete si pro něj vybrat libovolné jméno.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Nasazení na iOS zařízení

Otevřete **nastavení sestavení** výběrem**položky Nastavení sestavení** **souborů** > .

V části **Scény v sestavení**zajistěte, aby všechny scény byly zaškrtnuty vedle sebe.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

V Xcode zastavte aplikaci tak, že vyberete **Zastavit**.
