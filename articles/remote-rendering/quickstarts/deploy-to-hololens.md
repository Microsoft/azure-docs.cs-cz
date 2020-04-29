---
title: Nasazení ukázky Unity do HoloLens
description: Rychlý Start, který ukazuje, jak získat ukázku Unity na HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679734"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Rychlý Start: nasazení ukázky Unity do HoloLens

V tomto rychlém startu se dozvíte, jak nasadit a spustit ukázkovou aplikaci rychlého startu pro Unity na HoloLens 2.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
>
>* Vytvoření ukázkové aplikace pro rychlé zprovoznění pro HoloLens
>* Nasaďte ukázku do zařízení.
>* Spuštění ukázky na zařízení

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu nasadíme ukázkový projekt z [rychlého startu: vykreslení modelu pomocí Unity](render-model.md).

Ujistěte se, že vaše přihlašovací údaje jsou ve scéně správně uložené a Vy se můžete připojit k relaci z editoru Unity.

## <a name="build-the-sample-project"></a>Sestavení ukázkového projektu

1. Otevřete *soubor > nastavení sestavení*.
1. Změnit *platformu* na **Univerzální platforma Windows**
1. Nastavit *cílové zařízení* na **HoloLens**
1. Nastavit *architekturu* na **ARM64**
1. Nastavení *typu sestavení* na nastavení sestavení **projektu D3D** ![](./media/unity-build-settings.png)
1. Vybrat **přepínač na platformu**
1. Při stisknutí tlačítka **sestavit** (nebo ' sestavit a spustit ') budete požádáni o výběr některé složky, do které má být řešení uloženo.
1. Otevřete vygenerovaný **rychlý Start. sln** pomocí sady Visual Studio
1. Změna konfigurace na **release** a **ARM64**
1. Přepnout režim ladicího programu do konfigurace řešení **vzdáleného počítače** ![](media/unity-deploy-config.png)
1. Sestavení řešení (F7)
1. Pro projekt ' Start ', přejít na *vlastnosti > ladění*
    1. Ujistěte se, že je *verze* konfigurace aktivní.
    1. Nastavit *ladicí program ke spuštění* na **vzdáleném počítači**
    1. Změnit *název počítače* na **IP adresu vašeho HoleLensu**

## <a name="launch-the-sample-project"></a>Spustit vzorový projekt

1. Připojte HoloLens k počítači pomocí kabelu USB.
1. Spusťte ladicí program v aplikaci Visual Studio (F5). Aplikace se automaticky nasadí do zařízení.

Ukázková aplikace by se měla spustit a pak začít novou relaci. Po chvíli je relace připravena a na začátku se zobrazí vzdálený vykreslený model.
Pokud chcete ukázku spustit později později, můžete ji také najít v nabídce Start HoloLens.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu se podíváme na převod vlastního modelu.

> [!div class="nextstepaction"]
> [Rychlý Start: převod modelu pro vykreslování](convert-model.md)
