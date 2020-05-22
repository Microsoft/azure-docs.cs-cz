---
title: Nasazení ukázky Unity do Desktopu
description: Rychlý Start, který ukazuje, jak získat ukázku Unity do stolního počítače
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 59b235a32ae9363cc5fc804385867e140be502e2
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758992"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Rychlý Start: nasazení ukázky Unity do desktopu

V tomto rychlém startu se dozvíte, jak nasadit a spustit ukázkovou aplikaci rychlého startu pro Unity do stolního počítače.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
>
>* Vytvoření ukázkové aplikace pro rychlé zprovoznění pro Desktop
>* Nasazení ukázky do počítače
>* Spuštění ukázky na počítači

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu nasadíme ukázkový projekt z [rychlého startu: vykreslení modelu pomocí Unity](render-model.md).

Ujistěte se, že vaše přihlašovací údaje jsou ve scéně správně uložené a Vy se můžete připojit k relaci z editoru Unity.

## <a name="disable-virtual-reality-support"></a>Zakázat podporu virtuální reality

Na ploše se aktuálně podporují jenom ploché aplikace klasické pracovní plochy, takže je potřeba, aby byla vypnutá podpora VR.

1. Otevřít *úpravy > nastavení projektu...*
1. Na levé straně vyberte **Player** .
1. Vyberte kartu **nastavení Univerzální platforma Windows** .
1. Rozbalte **Nastavení XR**.
1. Zakáže **virtuální realitu**, která je podporovaná.
    ![nastavení přehrávače](./media/unity-disable-xr.png)
1. Nad *nastavením XR*rozbalte položku **Nastavení publikování**.
1. V **podporovaných rodinách zařízení**se ujistěte, že je zaškrtnuté políčko **plocha** .

## <a name="build-the-sample-project"></a>Sestavení ukázkového projektu

1. Otevřete *soubor > nastavení sestavení*.
1. Změnit *platformu* na **Univerzální platforma Windows** (v tomto případě se podporuje i**počítač Standalone** ).
1. Nastavte *cílové zařízení* na **počítač**.
1. Nastavte *architekturu* na **x86**.
1. Nastavte *typ sestavení* na **projekt D3D**.
  ![Nastavení sestavení](./media/unity-build-settings-pc.png)
1. Vyberte **přepínač přepnout na platformu**.
1. Při stisknutí tlačítka **sestavit** (nebo ' sestavit a spustit ') se zobrazí výzva, abyste vybrali složku, do které má být řešení uloženo.
1. Otevřete vygenerovaný **rychlý Start. sln** pomocí sady Visual Studio.
1. Změňte konfiguraci na **release** a **x86**.
1. Přepněte režim ladicího programu na **místní počítač**.
  ![Konfigurace řešení](./media/unity-deploy-config-pc.png)
1. Sestavte řešení (F7).

> [!WARNING]
> Ujistěte se, že jste vybrali možnost **x86**. **platforma x64** není momentálně podporovaná, viz [omezení platformy](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Spustit vzorový projekt

Spusťte ladicí program v aplikaci Visual Studio (F5). Aplikace se automaticky nasadí do počítače.

Ukázková aplikace by se měla spustit a pak začít novou relaci. Po chvíli je relace připravena a na začátku se zobrazí vzdálený vykreslený model.
Pokud chcete ukázku spustit později, můžete ji také najít v nabídce Start.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu se podíváme na převod vlastního modelu.

> [!div class="nextstepaction"]
> [Rychlý Start: převod modelu pro vykreslování](convert-model.md)
