---
title: Nasazení ukázky Unity do HoloLens
description: Průvodce Rychlý start, který ukazuje, jak získat ukázku Unity na zařízení HoloLens.
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 3eec935d0a25f9510cd9a2f6e00b7ac22756e697
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88796795"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Rychlý start: Nasazení ukázky Unity do HoloLens

V tomto průvodci Rychlý start se dozvíte, jak na zařízení HoloLens 2 nasadit a spustit ukázkovou aplikaci Unity pro rychlé zprovoznění.

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
>
>* Sestavit ukázkovou aplikaci pro rychlé zprovoznění pro HoloLens
>* Nasadit ukázku do zařízení
>* Spustit ukázku v zařízení

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu nasadíme ukázkový projekt z článku [Rychlý start: Vykreslení modelu s využitím Unity](render-model.md).

Ujistěte se, že máte řádně uložené přihlašovací údaje do scény a že se můžete z editoru Unity připojit k relaci.

## <a name="build-the-sample-project"></a>Sestavení ukázkového projektu

1. Otevřete nabídku *File (Soubor) -> Build Settings (Nastavení sestavení)* .
1. U položky *Platform* (Platforma) změňte nastavení na hodnotu **Universal Windows Platform** (Univerzální platforma Windows).
1. U položky *Target Device* (Cílové zařízení) nastavte **HoloLens**.
1. U položky *Architecture* (Architektura) nastavte **ARM64**.
1. U položky *Build Type* (Typ sestavení) nastavte **D3D Project** (Projekt D3D).\
    ![Nastavení sestavení](./media/unity-build-settings.png)
1. Vyberte **Switch to Platform** (Přepnout na platformu).
1. Když stisknete položku **Build** (Sestavit) nebo Build And Run (Sestavit a spustit), zobrazí se výzva k výběru složky, do které se má řešení uložit.
1. Otevřete vygenerovaný soubor **Quickstart.sln** ve Visual Studiu.
1. Změňte konfiguraci na možnosti **Vydaná verze** a **ARM64**.
1. Přepněte režim ladicího programu na **Vzdálený počítač**.\
    ![Konfigurace řešení](media/unity-deploy-config.png)
1. Sestavení řešení
1. U projektu Rychlý start přejděte na položky *Vlastnosti > Ladění*.
    1. Ujistěte se, že je aktivní konfigurace *Vydaná verze*.
    1. U položky *Spustit ladicí program* vyberte **Vzdálený počítač**.
    1. U položky *Název počítače* vyberte **IP adresu zařízení HoloLens**.

## <a name="launch-the-sample-project"></a>Spuštění ukázkového projektu

1. Připojte HoloLens k počítači pomocí kabelu USB.
1. Ve Visual Studiu spusťte ladicí program (F5). Ten automaticky nasadí aplikaci do zařízení.

Ukázková aplikace by se měla spustit a zahájit novou relaci. Po chvíli bude relace připravená a před vámi se zobrazí vzdáleně vykreslený model.
Pokud budete chtít ukázku později spustit podruhé, už ji najdete v nabídce Start zařízení HoloLens.

## <a name="next-steps"></a>Další kroky

V následujícím rychlém startu se podíváme na převod vlastního modelu.

> [!div class="nextstepaction"]
> [Rychlý start: Převod modelu pro vykreslování](convert-model.md)
