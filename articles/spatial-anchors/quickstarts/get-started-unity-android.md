---
title: Rychlý start – vytvoření aplikace pro Android Unity pomocí Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak k vytváření aplikací pro Android pomocí Unity pomocí prostorových ukotvení.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5647aa20f444d5efd36f03d813ee87ef199cc41
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621868"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Rychlý start: Vytvoření aplikace pro Android Unity s Azure prostorových kotev vztahů

Tento rychlý start popisuje, jak vytvořit aplikaci Android Unity pomocí [prostorových kotvy Azure](../overview.md). Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít ARCore Android aplikace vytvořené pomocí Unity, který můžete uložit a odvolat prostorových ukotvení.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření účtu prostorových kotvy
> * Příprava nastavení buildu Unity
> * Stáhnout a naimportovat sady SDK ARCore pro Unity
> * Konfigurace prostorový kotvy účtu identifikátor a klíč účtu
> * Export projektů Android Studio
> * Nasazení a spuštění na zařízení s Androidem

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Windows nebo macOS počítače s <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> a <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - Pokud používáte Windows, budete také potřebovat <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
  - Pokud spuštěný v systému macOS, zajistit správné fungování Gitu instalovaných pomocí instalace HomeBrew. Jako jeden řádek, terminálu zadejte následující příkaz: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Potom spusťte `brew install git`.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">developer povolené</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore podporující</a> zařízení s Androidem.
- Vaše aplikace musí používat verzi **1.5** sady SDK ARCore pro Unity (podpora ARCore 1.6 + bude možné později).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Nakonfigurujte identifikátor účtu a klíč

V **projektu** podokně přejděte do `Assets/AzureSpatialAnchorsPlugin/Examples` a otevřít `AzureSpatialAnchorsBasicDemo.unity` souboru scény.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Uložte výběrem scény **souboru** -> **Uložit**.

## <a name="export-the-android-studio-project"></a>Export projektů Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Zkontrolujte **Export projektů** zaškrtávacího políčka nemá značku zaškrtnutí. Klikněte na tlačítko **sestavíte a spustíte**. Zobrazí se dotaz, chcete-li uložit vaše `.apk` soubor, můžete vybrat libovolný název.

Postupujte podle pokynů v aplikaci umístit a odvolat ukotvení.

> [!NOTE]
> Při spuštění aplikace, pokud se nezobrazí fotoaparátu/kamery jako pozadí (pro instance, zobrazí se místo toho prázdnou hodnotu, modrá nebo jiných textury) pak budete pravděpodobně muset znovu importovat prostředky v Unity. Zastavte aplikaci. Z hlavní nabídky v Unity, zvolte **prostředků -> všechny opakovaný import**. Spusťte aplikaci znovu.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Kurz: Sdílená složka prostorových kotvy napříč zařízeními](../tutorials/tutorial-share-anchors-across-devices.md)
