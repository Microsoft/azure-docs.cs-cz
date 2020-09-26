---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358752"
---
## <a name="android"></a>[Android](#tab/Android)

Ukázka Java Android podporuje sdílení přes zařízení.
Otevřete soubor `SharedActivity.java` ze složky Samples v Android Studio. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `SharingAnchorsServiceUrl` v `SharedActivity.java` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Ukázka "cíl-C iOS" podporuje sdílení přes zařízení.
Otevřete soubor `SharedDemoViewController.m` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `SharingAnchorsServiceUrl` v `SharedDemoViewController.m` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

Nasaďte aplikaci do svého zařízení. Po spuštění aplikace vyberte možnost **spustit sdílenou ukázku klepnutím na tlačítko** . Postupujte podle pokynů v aplikaci. Můžete vybrat **klepnutím pro vyhledání kotvy číslem kotvy** nebo **klepnutím vytvořit kotvu a uložit ji do služby**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Ukázky pro Xamarin Android i iOS podporují sdílení přes zařízení.
Otevřete soubor `AccountDetails.cs` ve složce Samples. Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `AnchorSharingServiceUrl` v `AccountDetails.cs` souboru. Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení s iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.Examples\Resources` . Vyberte `SpatialAnchorSamplesConfig`. Pak v podokně **inspektor** zadejte `Sharing Anchors Service url` (z nasazení Azure Web App ASP.NET) jako hodnotu pro `Base Sharing Url` , nahraďte hodnotou `index.html` `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

Scénu uložte výběrem Uložit **soubor**  >  **Save**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení na zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** výběrem **File**  >  **nastavení sestavení**souboru.

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

Ujistěte se, že **projekt pro export** neobsahuje značku zaškrtnutí. Vyberte **sestavení a spustit**. Zobrazí se výzva k uložení `.apk` souboru. Můžete si vybrat libovolný název.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Nasazení na zařízení s iOS

Otevřete **nastavení sestavení** výběrem **File**  >  **nastavení sestavení**souboru.

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

V Xcode zastavte aplikaci tak, že vyberete **zastavit**.
