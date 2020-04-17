---
title: 'Úvodní příručka: Vytvoření aplikace HoloLens s Unity'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens s Unity pomocí prostorových kotev.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482014"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Úvodní příručka: Vytvoření aplikace Unity HoloLens, která používá prostorové kotvy Azure

V tomto rychlém startu vytvoříte aplikaci Unity HoloLens, která používá [Azure Spatial Anchors](../overview.md). Prostorové kotvy je služba pro vývojáře napříč platformami, která umožňuje vytvářet prostředí smíšené reality s objekty, které v průběhu času přetrvávají na různých zařízeních. Až budete hotovi, budete mít aplikaci HoloLens vytvořenou pomocí Unity, která dokáže uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

- Vytvořte účet prostorových kotev.
- Připravte nastavení sestavení Unity.
- Nakonfigurujte identifikátor účtu prostorových kotev a klíč účtu.
- Exportujte projekt HoloLens Visual Studio.
- Nasaďte aplikaci a spusťte ji na zařízení HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Potřebujete počítač se systémem Windows s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 nebo 2019.2</a> a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nebo novější. Instalace sady Visual Studio musí zahrnovat **úlohu vývoje platformy Universal Windows** a součást **sady Windows 10 SDK (10.0.18362.0 nebo novější).** Musíte také nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a Git <a href="https://git-lfs.github.com/">LFS</a>.
- Potřebujete zařízení HoloLens, ve kterém je povolen [režim pro vývojáře.](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) [Aktualizace Windows 10 z října 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (označovaná také jako RS5) musí být nainstalovaná v zařízení. Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení,** přejděte na **Aktualizovat & zabezpečení**a pak vyberte Vyhledat **aktualizace**.
- Ve vaší aplikaci musíte povolit schopnost **Prostorové vnímání.** Toto nastavení je v části Nastavení nastavení **sestavení** > **Player Settings** > **Možnosti****publikování** > .
- V aplikaci musíte povolit **virtuální realitu podporovanou** sadou **Windows Mixed Reality SDK**. Toto nastavení je v nastavení **nastavení** > sestavení**Nastavení** > přehrávače**XR Nastavení**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Stažení a otevření ukázkového projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Otevřete **nastavení sestavení** výběrem**položky Nastavení sestavení** **souborů** > .

V části **Platforma** vyberte **univerzální platformu Windows**. Změňte **cílové zařízení** na **HoloLens**.

Výběrem **možnosti Přepnout platformu** změníte platformu na **univerzální platformu Windows**. Unity vás může vyzvat k instalaci součástí podpory UPW, pokud chybí.

![Okno Nastavení sestavení unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zavřete okno **Nastavení sestavení.**

## <a name="configure-the-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

V podokně **Projektu** `Assets/AzureSpatialAnchors.Examples/Scenes` přejděte `AzureSpatialAnchorsBasicDemo.unity` na soubor scény a otevřete jej.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte scénu výběrem **možnosti Uložit** > **soubor**.

## <a name="export-the-hololens-visual-studio-project"></a>Export projektu HoloLens Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vyberte **Sestavit**. V dialogovém okně vyberte složku, do které chcete exportovat projekt HoloLens Visual Studio.

Po dokončení exportu se zobrazí složka obsahující exportovaný projekt HoloLens.

## <a name="deploy-the-hololens-application"></a>Nasazení aplikace HoloLens

Ve složce poklikejte na **HelloAR U3D.sln** a otevřete projekt v sadě Visual Studio.

Změňte **konfiguraci řešení** na **verzi**, změňte **platformu řešení** na **x86**a z možností cíle nasazení vyberte **Zařízení.**

Pokud používáte HoloLens 2, použijte **ARM64** jako **platformu řešení**, namísto **x86**.

   ![Konfigurace sady Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste se a připojte zařízení k počítači pomocí kabelu USB.

Vyberte **Ladění** > **Start ladění** k nasazení aplikace a spuštění ladění.

Podle pokynů v aplikaci umístěte a vzpomenete kotvu.

V Sadě Visual Studio zastavte aplikaci tak, že vyberete **Stop Debugging** nebo Shift+F5.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unity-20193"></a>Jednota 2019.3

Kvůli zásadním změnám není unity 2019.3 v současné době podporována. Použijte unity 2019.1 nebo 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev mezi zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
