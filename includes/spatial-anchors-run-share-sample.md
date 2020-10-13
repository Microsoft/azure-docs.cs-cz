---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971380"
---
## <a name="android"></a>[Android](#tab/Android)

Ukázka Java Android podporuje sdílení přes zařízení.

V Android Studio otevřete soubor *SharedActivity. Java* ze složky Samples. 

Zadejte adresu URL, kterou jste zkopírovali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `SharingAnchorsServiceUrl` v souboru *SharedActivity. Java* . 

Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Ukázka "cíl-C iOS" podporuje sdílení přes zařízení.

Otevřete soubor *SharedDemoViewController. m* ve složce Samples. 

Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu pro `SharingAnchorsServiceUrl` v souboru *SharedDemoViewController. m* . 

Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

Nasaďte aplikaci do svého zařízení. 

Po spuštění aplikace vyberte možnost **zahájit sdílenou ukázku klepnutím na tlačítko** a pak postupujte podle pokynů v aplikaci. Můžete vybrat **klepnutím pro vyhledání kotvy číslem kotvy** nebo **klepnutím vytvořit kotvu a uložit ji do služby**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Ukázky pro Xamarin Android i iOS podporují sdílení přes zařízení.

Otevřete soubor *AccountDetails.cs* ve složce Samples. 

Zadejte adresu URL, kterou jste získali v předchozím kroku (z nasazení Azure Web App ASP.NET) jako hodnotu `AnchorSharingServiceUrl` v souboru *AccountDetails.cs* . 

Nahraďte `index.html` adresu v adrese URL `api/anchors` . Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení s iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **projekt** , přejít na `Assets\AzureSpatialAnchors.Examples\Resources` . 

Vyberte **SpatialAnchorSamplesConfig**. Pak v podokně **inspektor** zadejte `Sharing Anchors Service` adresu URL (z nasazení Azure Web App ASP.NET) jako hodnotu pro `Base Sharing Url` . Nahraďte `index.html` za `api/anchors` (Jak velká může být moje znalostní báze?). Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors` .

Scénu uložte výběrem Uložit **soubor**  >  **Save**.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-an-android-device"></a>Nasazení na zařízení s Androidem

Přihlaste se k zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** výběrem **File**  >  **nastavení sestavení**souboru.

V části **scény v sestavách**se ujistěte, že má každá scéna vedle sebe značku zaškrtnutí.

Zajistěte, aby **projekt pro export** neobsahoval značku zaškrtnutí. Vyberte **sestavení a spustit**. Zobrazí se výzva k uložení souboru *. apk* . Můžete si vybrat libovolný název.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Nasazení na zařízení s iOS

Otevřete **nastavení sestavení** výběrem **File**  >  **nastavení sestavení**souboru.

V části **scény v sestavách**se ujistěte, že má každá scéna vedle sebe značku zaškrtnutí.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

V Xcode zastavte aplikaci tak, že vyberete **zastavit**.
