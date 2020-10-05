---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí rozhraní DirectX'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí prostorových ukotvení.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e6d728647bb654ee2645bb1e3eb5968dcab337a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87810340"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Rychlý Start: Vytvoření aplikace HoloLens pomocí prostorových kotev Azure v C++/WinRT a DirectX

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí [prostorových kotev Azure](../overview.md) v C++/WinRT a DirectX. Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít aplikaci HoloLens, která může uložit a odvolat prostorovou kotvu.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotev
> * Konfigurace identifikátoru účtu prostorových kotev a klíče účtu
> * Nasazení a spuštění na zařízení HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:
- Počítač s Windows se sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nainstalovaný s úlohou **vývoje Univerzální platforma Windows** a komponentou **Windows 10 SDK (10.0.18362.0 nebo novější)** . Musíte taky nainstalovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a> a <a href="https://git-lfs.github.com/">Git LFS</a>.
- [Rozšíření Visual Studio/WinRT v jazyce C++](https://aka.ms/cppwinrt/vsix) pro Visual Studio by se mělo nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Zařízení HoloLens s povoleným [vývojářským režimem](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . Tento článek vyžaduje zařízení HoloLens s [Windows 10 říjen 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označuje se také jako RS5). Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení**a pak vyberte tlačítko **Vyhledat aktualizace** .
- Vaše aplikace musí v manifestu AppX nastavit funkci **spatialPerception** .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřete `HoloLens\DirectX\SampleHoloLens.sln` v aplikaci Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurace identifikátoru a klíče účtu

Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

Otevřete `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte ho `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte ho `Set me` identifikátorem účtu.

Vyhledejte `SpatialAnchorsAccountDomain` pole a nahraďte ho `Set me` doménou účtu.

## <a name="deploy-the-app-to-your-hololens"></a>Nasazení aplikace do HoloLens

Změňte **konfiguraci řešení** na **release**, změňte **platformu řešení** na **x86**a vyberte **zařízení** z možností cíle nasazení.

Pokud používáte HoloLens 2, používejte jako platformu pro **řešení** **ARM64** místo **x86**.

![Konfigurace sady Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Zapněte zařízení HoloLens, přihlaste se a připojte ho k počítači pomocí kabelu USB.

Vyberte **ladit**  >  **Spustit ladění** pro nasazení aplikace a spuštění ladění.

Podle pokynů v aplikaci založte a odvoláte kotvu.

V aplikaci Visual Studio zastavte aplikaci tak, že vyberete **Zastavit ladění** nebo stisknete **SHIFT + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: sdílení prostorových ukotvení napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
