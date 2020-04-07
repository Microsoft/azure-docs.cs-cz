---
title: Nasazení ukázky Unity do HoloLens
description: Rychlý start, který ukazuje, jak dostat ukázku Unity na HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679734"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Úvodní příručka: Nasazení ukázky Unity do HoloLens

Tento rychlý start popisuje, jak nasadit a spustit ukázkovou aplikaci pro rychlý start pro Unity na HoloLens 2.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
>
>* Vytvoření ukázkové aplikace pro rychlý start pro HoloLens
>* Nasazení ukázky do zařízení
>* Spuštění ukázky na zařízení

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu nasadíme ukázkový projekt z [úvodního startu: Render model s Unity](render-model.md).

Ujistěte se, že vaše přihlašovací údaje jsou uloženy správně se scénou a můžete se připojit k relaci z editoru Unity.

## <a name="build-the-sample-project"></a>Sestavení ukázkového projektu

1. Otevřete *soubor > nastavení sestavení*.
1. Změna *platformy* na **univerzální platformu Windows**
1. Nastavení *cílového zařízení* na **HoloLens**
1. Nastavit *architekturu* na **ARM64**
1. Nastavit *typ sestavení* na nastavení sestavení projektu ![ **D3D**](./media/unity-build-settings.png)
1. Vybrat **Přepnout na platformu**
1. Při stisknutí tlačítka **Build** (nebo 'Build and Run') budete vyzváni k výběru nějaké složky, kde by mělo být řešení uloženo
1. Otevření generovaného **souboru Quickstart.sln** pomocí sady Visual Studio
1. Změna konfigurace na **Release** a **ARM64**
1. Přepnutí režimu ladicího programu na konfiguraci řešení **vzdáleného počítače** ![](media/unity-deploy-config.png)
1. Sestavení řešení (F7)
1. Projekt "Rychlý start" naleznete v části *Vlastnosti > ladění*
    1. Zkontrolujte, zda je konfigurační *verze aktivní.*
    1. Nastavit *ladicí program na spuštění do* **vzdáleného počítače**
    1. Změna *názvu počítače* na IP adresu **holelens**

## <a name="launch-the-sample-project"></a>Spuštění ukázkového projektu

1. Připojte HoloLens pomocí USB kabelu k počítači.
1. Spusťte ladicí program v sadě Visual Studio (F5). Automaticky nasadí aplikaci do zařízení.

Ukázková aplikace by se měla spustit a potom spustit novou relaci. Po chvíli je relace připravena a vzdáleně vykreslený model se zobrazí před vámi.
Pokud chcete spustit vzorek podruhé později, můžete jej také najít z nabídky Start HoloLens nyní.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu se podíváme na konverzi vlastního modelu.

> [!div class="nextstepaction"]
> [Úvodní příručka: Převod modelu pro vykreslování](convert-model.md)
