---
title: Nasazení ukázky Unity do Desktopu
description: Úvodní příručka, která ukazuje, jak dostat ukázku Unity do stolního počítače
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415727"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Úvodní příručka: Nasazení ukázky Unity na plochu

Tento rychlý start popisuje, jak nasadit a spustit ukázkovou aplikaci pro rychlý start pro Unity do stolního počítače.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
>
>* Vytvoření ukázkové aplikace pro rychlý start pro klasickou pracovní plochu
>* Nasazení ukázky do počítače
>* Spuštění ukázky na počítači

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu nasadíme ukázkový projekt z [úvodního startu: Render model s Unity](render-model.md).

Ujistěte se, že vaše přihlašovací údaje jsou uloženy správně se scénou a můžete se připojit k relaci z editoru Unity.

## <a name="disable-virtual-reality-support"></a>Zakázat podporu virtuální reality

Pouze ploché desktopové aplikace jsou v současné době podporovány na ploše, takže podpora VR musí být zakázána.

1. Otevřít *upravit > nastavení projektu...*
1. Vlevo vyberte **Přehrávač.**
1. Vyberte kartu **Nastavení univerzální platformy Windows.**
1. Rozbalte **nastavení XR**.
1. Zakázat **podporu virtuální reality**.
    ![nastavení přehrávače](./media/unity-disable-xr.png)
1. Nad *nastavením XR* **rozbalte položku Nastavení publikování**.
1. V **části Podporované rodiny zařízení**zkontrolujte, zda je zaškrtnuto **políčko Plocha.**

## <a name="build-the-sample-project"></a>Sestavení ukázkového projektu

1. Otevřete *soubor > nastavení sestavení*.
1. Změňte *platformu* na **univerzální platformu Windows**.
1. Nastavte *cílové zařízení* na **PC**.
1. Nastavte *architekturu* na **x86**.
1. Nastavte *typ sestavení* na projekt **D3D**.
  ![Nastavení sestavení](./media/unity-build-settings-pc.png)
1. Vyberte **Přepnout na platformu**.
1. Při stisknutí tlačítka **Build** (nebo 'Build and Run') budete vyzváni k výběru některé složky, kde by mělo být uloženo řešení.
1. Otevřete vygenerovaný **soubor Quickstart.sln** pomocí sady Visual Studio.
1. Změňte konfiguraci na **Release** a **x86**.
1. Přepněte režim ladicího programu na **místní počítač**.
  ![Konfigurace řešení](./media/unity-deploy-config-pc.png)
1. Sestavte řešení (F7).

> [!WARNING]
> Ujistěte se, že jste vybrali **x86**. **x64** není v současné době podporován, viz [omezení platformy](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Spuštění ukázkového projektu

Spusťte ladicí program v sadě Visual Studio (F5). Automaticky nasadí aplikaci do počítače.

Ukázková aplikace by se měla spustit a potom spustit novou relaci. Po chvíli je relace připravena a vzdáleně vykreslený model se zobrazí před vámi.
Pokud chcete spustit ukázku podruhé později, můžete ji také najít z nabídky Start.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu se podíváme na konverzi vlastního modelu.

> [!div class="nextstepaction"]
> [Úvodní příručka: Převod modelu pro vykreslování](convert-model.md)
