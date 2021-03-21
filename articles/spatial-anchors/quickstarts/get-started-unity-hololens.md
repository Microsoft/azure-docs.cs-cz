---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí Unity'
description: V tomto rychlém startu se dozvíte, jak pomocí prostorových ukotvení vytvořit aplikaci HoloLens s Unity.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b99c2efcb746a056adc76fdc161c8d8a8e2799e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670116"
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

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Potřebujete počítač s Windows s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> a sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nebo novější. Použijte **unity 2020 LTS** se systémem ASA sdk verze 2,9 nebo novější (který používá [modul plug-in Unity XR](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) nebo **Unity 2019 LTS** se službou ASA SDK verze 2,8 nebo starší. Vaše instalace sady Visual Studio musí zahrnovat úlohu **vývoje Univerzální platforma Windows** a komponentu **Windows 10 SDK (10.0.18362.0 nebo novější)** . Musíte taky nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a <a href="https://git-lfs.github.com/">Git LFS</a>.
- Potřebujete zařízení HoloLens, na kterém je povolený [vývojářský režim](/windows/mixed-reality/using-visual-studio) . V zařízení musí být nainstalovaná [aktualizace Windows 10 květen 2020](/windows/mixed-reality/whats-new/release-notes-may-2020) . Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení** a pak vyberte **Vyhledat aktualizace**.
- V aplikaci musíte povolit funkci **SpatialPerception** . Toto nastavení je v **nastavení sestavení**  >    >  Možnosti nastavení **publikování** přehrávače nastavení  >  .
- V aplikaci musíte povolit **virtuální realitu podporovanou** sadou **Windows Mixed reality SDK**. Toto nastavení se nachází v nastavení **sestavení**  >  **přehrávače** nastavení  >  **XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stáhněte a otevřete vzorový projekt Unity.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Otevřete **nastavení sestavení** výběrem   >  **nastavení sestavení** souboru.

V části **platforma** vyberte **Univerzální platforma Windows**. Změňte **cílové zařízení** na **HoloLens**.

Vyberte **přepínač platforma** pro změnu platformy na **Univerzální platforma Windows**. Unity vás může vyzvat k instalaci komponent podpory UWP, pokud chybí.

![Okno nastavení sestavení Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zavřete okno **nastavení sestavení** .

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Exportujte projekt HoloLens sady Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **sestavení**. V dialogovém okně vyberte složku, do které chcete exportovat projekt HoloLens sady Visual Studio.

Po dokončení exportu se zobrazí složka obsahující exportovaný projekt HoloLens.

## <a name="deploy-the-hololens-application"></a>Nasazení aplikace HoloLens

Ve složce poklikejte na **HELLOAR U3D. sln** a otevřete projekt v aplikaci Visual Studio.

Změňte **konfiguraci řešení** na **release**, změňte **platformu řešení** na **x86** a vyberte **zařízení** z možností cíle nasazení.

Pokud používáte HoloLens 2, používejte jako platformu pro **řešení** **ARM64** místo **x86**.

   ![Konfigurace sady Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste se a připojte zařízení k počítači pomocí kabelu USB.

Vyberte **ladit**  >  **Spustit ladění** pro nasazení aplikace a spuštění ladění.

V aplikaci vyberte **BasicDemo** pomocí šipek a pak stiskněte tlačítko **Přejít.** tlačítko pro spuštění ukázky. Použijte pokyny k umístění a odvolání kotvy.

![Snímek obrazovky 1 snímek obrazovky 2 snímek obrazovky ](./media/get-started-unity-hololens/screenshot-1.jpg)
 ![ ](./media/get-started-unity-hololens/screenshot-2.jpg)
 ![ 3 ](./media/get-started-unity-hololens/screenshot-3.jpg)
 ![ snímek obrazovky 4](./media/get-started-unity-hololens/screenshot-4.jpg)

V aplikaci Visual Studio zastavte aplikaci tak, že vyberete buď **Zastavit ladění** , nebo Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Postupy: Konfigurace prostorových kotev Azure v projektu Unity](../how-tos/setup-unity-project.md)