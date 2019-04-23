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
ms.openlocfilehash: ce9bca3f11911677a6009b4cfe0c3f6413b9f837
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240583"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace HoloLens Unity, která používá Azure prostorových kotvy

V tomto rychlém startu vytvoříte aplikaci HoloLens Unity, který používá [prostorových kotvy Azure](../overview.md). Prostorový kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí pro hybridní realitu se objekty, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít HoloLens aplikace vytvořené s Unity, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

- Vytvoření účtu prostorových ukotvení.
- Příprava nastavení buildu Unity.
- Konfigurace účtu prostorových kotvy klíče identifikátor a účet.
- Exportujte projektů sady Visual Studio HoloLens.
- Tuto aplikaci nasadit a spustit ho v zařízení HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:


- Potřebujete počítač Windows, na kterém <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> nebo novější a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> nebo později jsou nainstalovány. Musí zahrnovat instalaci sady Visual Studio **vývoj pro univerzální platformu Windows** pracovního vytížení. Je také zapotřebí nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
- Je třeba HoloLens zařízení, na kterém [vývojářský režim](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) povolena. [Windows 10. října 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (označované také jako RS5) musí být nainstalován na zařízení. Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete **nastavení** aplikaci, přejděte na **aktualizace a zabezpečení**a pak vyberte **vyhledávat aktualizace**.
- V aplikaci, je potřeba povolit **SpatialPerception** funkce. Toto nastavení je v **nastavení sestavení** > **nastavení přehrávače** > **nastavení publikování**  >   **Možnosti**.
- V aplikaci, je potřeba povolit **podporované virtuální realitu** s **Windows SDK smíšené Reality**. Toto nastavení je v **nastavení sestavení** > **nastavení přehrávače** > **XR nastavení**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

V Unity otevřete projekt ve složce Unity.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V **platformy** vyberte **univerzální platformu Windows**. Změnit **cílové zařízení** k **HoloLens**.

Vyberte **přepínač platformy** platformy s cílem změnit **univerzální platformu Windows**. Unity může výzvu k instalaci součástí UPW podpory, pokud se v nich chybí.

   ![Okno nastavení sestavení Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zavřít **nastavení sestavení** okna.

## <a name="configure-the-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte na `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsBasicDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte výběrem scény **souboru** > **Uložit**.

## <a name="export-the-hololens-visual-studio-project"></a>Export projektů sady Visual Studio HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **sestavení**. V dialogovém okně vyberte složku, do kterého se mají exportovat projekt aplikace Visual Studio HoloLens.

Po dokončení exportu složku obsahující exportované HoloLens projekt, který se zobrazí.

## <a name="deploy-the-hololens-application"></a>Nasazení aplikace HoloLens

Ve složce, klikněte dvakrát na **HelloAR U3D.sln** otevřete projekt v sadě Visual Studio.

Změnit **konfigurace řešení** k **vydání**, změnit **platforma řešení** k **x86**a vyberte **zařízení**  z možnosti cíl nasazení.

Pokud v HoloLens 2, použijte **ARM** jako **platforma řešení**, namísto **x86**.

   ![Konfigurace Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste a připojte zařízení k počítači pomocí kabelu USB.

Vyberte **ladění** > **spustit ladění** k nasazení vaší aplikace a spusťte ladění.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

V sadě Visual Studio, zastavte aplikaci tak, že vyberete buď **Zastavit ladění** nebo Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílet prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
