---
title: Rychlý start – vytvoření aplikace HoloLens s Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak sestavit aplikaci HoloLens pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 80dcdd666c1067f2fc9415a663f26b82d1335d5f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "67135266"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Rychlý start: Vytvoření aplikace HoloLens s Azure prostorových ukotvení v C++/WinRT a DirectX

Tento rychlý start popisuje, jak vytvořit aplikaci pomocí HoloLens [prostorových kotvy Azure](../overview.md) v C++/WinRT a rozhraní DirectX. Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci HoloLens, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Nasazení a spuštění na zařízení HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Počítače s Windows s <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> nainstalované s **vývoj pro univerzální platformu Windows** pracovního vytížení a **Windows 10 SDK (10.0.17763.0 nebo novější)** komponenty, a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
- [ C++WinRT rozšíření aplikace Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) pro Visual Studio musí nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Zařízení HoloLens s [vývojářský režim](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) povolena. Tento článek vyžaduje zařízení HoloLens se [Windows 10. října 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označované také jako RS5). Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete **nastavení** aplikaci, přejděte na **aktualizace a zabezpečení**, vyberte **vyhledat aktualizace** tlačítko.
- Aplikaci musíte nastavit **spatialPerception** schopností ve svém manifestu AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otevřete ukázkový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otevřít `HoloLens\DirectX\SampleHoloLens.sln` v sadě Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

Dalším krokem je konfigurace aplikace pro používání identifikátor účtu a klíč účtu. Jste zkopírovali do textového editoru, když [nastavení zdroje prostorových kotvy](#create-a-spatial-anchors-resource).

Otevřít `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Vyhledejte `SpatialAnchorsAccountKey` pole a nahraďte `Set me` klíčem účtu.

Vyhledejte `SpatialAnchorsAccountId` pole a nahraďte `Set me` s identifikátor účtu.

## <a name="deploy-the-app-to-your-hololens"></a>Nasazení aplikace na vašich HoloLens

Změnit **konfigurace řešení** k **vydání**, změňte **platforma řešení** k **x86**a vyberte **zařízení**  z možnosti cíl nasazení.

Pokud v HoloLens 2, použijte **ARM** jako **platforma řešení**, namísto **x86**.

![Visual Studio Configuration](./media/get-started-hololens/visual-studio-configuration.png)

Zapne zařízení HoloLens, přihlaste a připojte ho k počítači pomocí kabelu USB.

Vyberte **ladění** > **spustit ladění** k nasazení vaší aplikace a spusťte ladění.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

V sadě Visual Studio, zastavte aplikaci tak, že vyberete **Zastavit ladění** nebo stiskněte **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
