---
title: Rychlý Start – vytvoření aplikace pro HoloLens pomocí prostorových kotev Azure | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94cae186cee099618772f53d2b820e12f20cad64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562399"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Rychlý start: Vytvoření aplikace HoloLens pomocí prostorových kotev Azure v C++/WinRT a DirectX

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí [prostorových kotev Azure](../overview.md) v C++/WinRT a DirectX. Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít aplikaci HoloLens, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:
- Počítač s Windows se sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nainstalovaný s úlohou **vývoje Univerzální platforma Windows** a komponentou **Windows 10 SDK (10.0.18362.0 nebo novější)** a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
- [/WinRT Visual Studio Extension (VSIX) pro Visual Studio by se mělo nainstalovat z Visual Studio Marketplace. C++](https://aka.ms/cppwinrt/vsix) [](https://marketplace.visualstudio.com/)
- Zařízení HoloLens s povoleným [vývojářským režimem](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . Tento článek vyžaduje zařízení HoloLens s [Windows 10 říjen 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označuje se také jako RS5). Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení**a pak vyberte tlačítko **Vyhledat aktualizace** .
- Vaše aplikace musí v manifestu AppX nastavit funkci **spatialPerception** .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřete `HoloLens\DirectX\SampleHoloLens.sln` v aplikaci Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při nastavování [prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

Otevřít `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Vyhledejte pole a nahraďte `Set me` ho klíčem účtu. `SpatialAnchorsAccountKey`

Vyhledejte pole a nahraďte `Set me` ho identifikátorem účtu. `SpatialAnchorsAccountId`

## <a name="deploy-the-app-to-your-hololens"></a>Nasazení aplikace do HoloLens

Změňte **konfiguraci řešení** na **release**, změňte **platformu řešení** na **x86**a vyberte **zařízení** z možností cíle nasazení.

Pokud používáte HoloLens 2, používejte **ARM** jako **platformu řešení**místo **x86**.

![Konfigurace sady Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste se a připojte ho k počítači pomocí kabelu USB.

Vyberte **ladit** > **Spustit ladění** pro nasazení aplikace a spuštění ladění.

Podle pokynů v aplikaci založte a odvoláte kotvu.

V aplikaci Visual Studio zastavte aplikaci tak, že vyberete **Zastavit ladění** nebo stisknete **SHIFT + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílet prostorové kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
