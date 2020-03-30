---
title: 'Úvodní příručka: Vytvoření aplikace HoloLens s Rozhraním DirectX'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí prostorových kotev.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 01a9a8239e2997335f5123d63cfc664027cffa02
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376354"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Úvodní příručka: Vytvoření aplikace HoloLens s prostorovými kotvami Azure v Jazyce C++/WinRT a DirectX

Tento rychlý start popisuje, jak vytvořit aplikaci HoloLens pomocí [prostorových ukotvení Azure](../overview.md) v Jazyce C++/WinRT a DirectX. Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Až budete hotovi, budete mít aplikaci HoloLens, která dokáže uložit a vyvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:
- Počítač s Windows s <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nainstalovaným s **úlohou vývoje univerzální platformy Windows** a součástí Sady Windows **10 SDK (10.0.18362.0 nebo novější).** Musíte také nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a Git <a href="https://git-lfs.github.com/">LFS</a>.
- Rozšíření Visual Studio (VSIX) pro Visual Studio [(C++/WinRT)](https://aka.ms/cppwinrt/vsix) pro Visual Studio by mělo být nainstalováno z [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Zařízení HoloLens s [povoleným režimem pro vývojáře.](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) Tento článek vyžaduje zařízení HoloLens s [aktualizací windows 10 října 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označované také jako RS5). Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení,** přejděte na **Aktualizovat & zabezpečení**a pak vyberte tlačítko Vyhledat **aktualizace.**
- Vaše aplikace musí nastavit **schopnost prostorového vnímání** v manifestu AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevření ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřít `HoloLens\DirectX\SampleHoloLens.sln` v sadě Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru účtu a klíče

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

Otevřete `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Vyhledejte `SpatialAnchorsAccountKey` pole `Set me` a nahraďte jej klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole `Set me` a nahraďte jej identifikátorem účtu.

## <a name="deploy-the-app-to-your-hololens"></a>Nasazení aplikace do HoloLens

Změňte **konfiguraci řešení** na **verzi**, změňte **platformu řešení** na **x86**a z možností cíle nasazení vyberte **Zařízení.**

Pokud používáte HoloLens 2, použijte **ARM64** jako **platformu řešení**, namísto **x86**.

![Konfigurace sady Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Zapojte zařízení HoloLens, přihlaste se a připojte jej k počítači pomocí kabelu USB.

Vyberte **Ladění** > **Start ladění** k nasazení aplikace a spuštění ladění.

Podle pokynů v aplikaci umístěte a vzpomenete kotvu.

V Sadě Visual Studio zastavte aplikaci tak, že vyberete **Zastavit ladění** nebo stisknete Shift **+ F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílení prostorových kotev napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
