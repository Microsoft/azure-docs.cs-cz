---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903999"
---
## <a name="set-up-your-device-in-unity"></a>Nastavení zařízení v Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Nastavení zařízení s Androidem

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Nastavení zařízení s iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** přejděte na `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřete `AzureSpatialAnchorsLocalSharedDemo.unity` soubor scény.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.Examples\Resources`. Vyberte `SpatialAnchorSamplesConfig`. Pak v podokně **inspektor** `Sharing Anchors Service url` zadejte (z nasazení Azure Web App ASP.NET) jako hodnotu `api/anchors`pro `Base Sharing Url`, nahraďte `index.html` hodnotou. Mělo by to vypadat takto: `https://<app_name>.azurewebsites.net/api/anchors`.

Scénu uložte výběrem**Uložit** **soubor** > .

## <a name="deploy-to-your-device"></a>Nasazení zařízení

### <a name="deploy-to-android-device"></a>Nasazení na zařízení s Androidem

Přihlaste se na zařízení s Androidem a připojte ho k počítači pomocí kabelu USB.

Otevřete **nastavení sestavení** výběrem**nastavení sestavení** **souboru** > .

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

Ujistěte se, že **projekt pro export** neobsahuje značku zaškrtnutí. Vyberte **sestavení a spustit**. Zobrazí se výzva k uložení `.apk` souboru. Můžete si vybrat libovolný název.

Po spuštění aplikace v dialogovém okně **Zvolte ukázku** použijte šipky vlevo nebo vpravo a vyberte možnost **LocalShare** a klepněte na tlačítko **Přejít!** . Postupujte podle pokynů v aplikaci. Můžete vybrat možnost **vytvořit & sdílet kotvu** nebo **Vyhledat sdílené kotvy**.

První scénář vám umožní vytvořit kotvu, která může být umístěná později na stejném zařízení nebo na jiném.
Druhý scénář, pokud jste již aplikaci spustili, buď na stejném zařízení, nebo na jiném, umožňuje vyhledat dříve sdílené kotvy. Po výběru scénáře vás aplikace provede dalšími pokyny k tomu, co dělat. Budete například požádáni o přesunutí zařízení do složky pro shromáždění informací o prostředí. Později umístíte kotvu do světa, počkejte na její uložení atd.

### <a name="deploy-to-an-ios-device"></a>Nasazení na zařízení s iOS

Otevřete **nastavení sestavení** výběrem**nastavení sestavení** **souboru** > .

V části **scény v sestavení**zajistěte, aby všechny scény měly vedle sebe značku zaškrtnutí.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Po spuštění aplikace v dialogovém okně **Zvolte ukázku** použijte šipky vlevo nebo vpravo a vyberte možnost **LocalShare** a klepněte na tlačítko **Přejít!** . Postupujte podle pokynů v aplikaci. Můžete vybrat možnost **vytvořit & sdílet kotvu** nebo **Vyhledat sdílené kotvy**.

První scénář vám umožní vytvořit kotvu, která může být umístěná později na stejném zařízení nebo na jiném.
Druhý scénář, pokud jste již aplikaci spustili, buď na stejném zařízení, nebo na jiném, umožňuje vyhledat dříve sdílené kotvy. Po výběru scénáře vás aplikace provede dalšími pokyny k tomu, co dělat. Budete například požádáni o přesunutí zařízení do složky pro shromáždění informací o prostředí. Později umístíte kotvu do světa, počkejte na její uložení atd.

V Xcode zastavte aplikaci tak, že vyberete **zastavit**.
