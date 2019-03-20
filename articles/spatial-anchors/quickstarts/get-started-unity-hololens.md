---
title: Rychlý start – vytvoření aplikace HoloLens Unity s Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak sestavit aplikaci HoloLens s Unity pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c8fc99426751e9aaaec53cbb6b64a84b6e7f6734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894321"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace HoloLens Unity pomocí prostorových kotev vztahů Azure

Tento rychlý start popisuje, jak vytvořit aplikaci pomocí HoloLens Unity [prostorových kotvy Azure](../overview.md). Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít HoloLens aplikace vytvořené s Unity, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Příprava nastavení buildu Unity
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Export projektů sady Visual Studio HoloLens
> * Nasazení a spuštění na zařízení HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítače s Windows s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> nainstalované s **vývoj pro univerzální platformu Windows** úloze a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
- Zařízení HoloLens s [vývojářský režim](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) povolena. Tento článek vyžaduje zařízení HoloLens se [Windows 10. října 2018 Update](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (označované také jako RS5). Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete **nastavení** aplikaci, přejděte na **aktualizace a zabezpečení**, vyberte **vyhledat aktualizace** tlačítko.
- Aplikaci musíte nastavit **SpatialPerception** funkce v rámci **nastavení sestavení**->**Player nastavení**->**publikování Nastavení**->**možnosti**.
- Aplikace musí povolit **podporované virtuální realitu** s **Windows SDK smíšené Reality** pod **nastavení sestavení**->**nastavení přehrávače** -> **XR nastavení**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřete Unity a otevřete projekt v `Unity` složky.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** -> **nastavení sestavení**.

V **platformy** vyberte **univerzální platformu Windows**. Potom přejděte **cílové zařízení** k **HoloLens**.

Vyberte **přepínač platformy** platformy s cílem změnit **univerzální platformu Windows**. Unity vás může požádat o nainstalovat součásti UPW podpory, pokud v nich chybí.

![Nastavení buildu Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zavřít **nastavení sestavení** okna.

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsBasicDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="export-the-hololens-visual-studio-project"></a>Export projektů sady Visual Studio HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **sestavení** otevřete dialogové okno. Vyberte složku pro export projektů sady Visual Studio HoloLens.

Po dokončení exportu do složky se zobrazí obsahující exportovaném projektu HoloLens.

## <a name="deploy-the-hololens-application"></a>Nasazení aplikace HoloLens

Ve složce, dvakrát klikněte na `HelloAR U3D.sln` otevřete projekt v sadě Visual Studio.

Změnit **konfigurace řešení** k **vydání**, změňte **platforma řešení** k **x86**a vyberte **zařízení**  z možnosti cíl nasazení.

![Visual Studio Configuration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Zapne zařízení HoloLens, přihlaste a připojte ho k počítači pomocí kabelu USB.

Vyberte **ladění** > **spustit ladění** k nasazení vaší aplikace a spusťte ladění.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

V sadě Visual Studio, zastavte aplikaci tak, že vyberete **Zastavit ladění** nebo stiskněte **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
