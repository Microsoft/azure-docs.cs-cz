---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí Unity'
description: V tomto rychlém startu se dozvíte, jak pomocí prostorových ukotvení vytvořit aplikaci HoloLens s Unity.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 561b5391f125cf845eff940a0d3548fc6ade541e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277053"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Rychlý Start: Vytvoření aplikace pro HoloLens v Unity, která používá prostorové kotvy Azure

V tomto rychlém startu vytvoříte aplikaci Unity HoloLens, která používá [prostorové kotvy Azure](../overview.md). Prostorové ukotvení je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit s objekty, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít aplikaci HoloLens vytvořenou v Unity, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

- Vytvořte účet prostorových kotev.
- Připravte nastavení sestavení Unity.
- Nakonfigurujte identifikátor účtu prostorových ukotvení a klíč účtu.
- Exportujte projekt HoloLens sady Visual Studio.
- Nasaďte aplikaci a spusťte ji na zařízení HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Potřebujete počítač s Windows, na kterém je nainstalovaná <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,1</a> nebo novější a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nebo novější. Vaše instalace sady Visual Studio musí zahrnovat úlohu **vývoje Univerzální platforma Windows** a komponentu **Windows 10 SDK (10.0.18362.0 nebo novější)** . Musíte taky nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
- Potřebujete zařízení HoloLens, na kterém je povolený [vývojářský režim](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . V zařízení musí být nainstalovaná [aktualizace Windows 10 říjen 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (známá také jako RS5). Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení**a pak vyberte **Vyhledat aktualizace**.
- V aplikaci musíte povolit funkci **SpatialPerception** . Toto nastavení se nachází v **nastavení sestavení** > **přehrávač** > nastavení **publikování** > **Možnosti**.
- V aplikaci musíte povolit **virtuální realitu podporovanou** sadou **Windows Mixed reality SDK**. Toto nastavení je v **nastavení sestavení** > **nastavení přehrávače** > **Nastavení XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stáhněte a otevřete vzorový projekt Unity.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Otevřete **nastavení sestavení** tak, že vyberete **soubor** > **nastavení sestavení**.

V části **platforma** vyberte **Univerzální platforma Windows**. Změňte **cílové zařízení** na **HoloLens**.

Vyberte **přepínač platforma** pro změnu platformy na **Univerzální platforma Windows**. Unity vás může vyzvat k instalaci komponent podpory UWP, pokud chybí.

![Okno nastavení sestavení Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zavřete okno **nastavení sestavení** .

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

V podokně **projekt** , přejít na `Assets/AzureSpatialAnchors.Examples/Scenes` a otevřete `AzureSpatialAnchorsBasicDemo.unity` soubor scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Scénu uložte tak, že vyberete **soubor** > **Uložit**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportujte projekt HoloLens sady Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **sestavení**. V dialogovém okně vyberte složku, do které chcete exportovat projekt HoloLens sady Visual Studio.

Po dokončení exportu se zobrazí složka obsahující exportovaný projekt HoloLens.

## <a name="deploy-the-hololens-application"></a>Nasazení aplikace HoloLens

Ve složce poklikejte na **HELLOAR U3D. sln** a otevřete projekt v aplikaci Visual Studio.

Změňte **konfiguraci řešení** na **release**, změňte **platformu řešení** na **x86**a vyberte **zařízení** z možností cíle nasazení.

Pokud používáte HoloLens 2, používejte jako platformu pro **řešení** **ARM64** místo **x86**.

   ![Konfigurace sady Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste se a připojte zařízení k počítači pomocí kabelu USB.

Vyberte **ladit** > **Spustit ladění** a nasaďte svoji aplikaci a spusťte ladění.

Podle pokynů v aplikaci založte a odvoláte kotvu.

V aplikaci Visual Studio zastavte aplikaci tak, že vyberete buď **Zastavit ladění** , nebo Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
