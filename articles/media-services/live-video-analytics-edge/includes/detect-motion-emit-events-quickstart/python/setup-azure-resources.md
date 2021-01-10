---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060566"
---
Tento kurz vyžaduje následující prostředky Azure:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md)

Pro tento rychlý Start doporučujeme, abyste k nasazení požadovaných prostředků ve vašem předplatném Azure použili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . To můžete provést pomocí těchto kroků:

1. Otevřete [Azure Cloud Shell](https://shell.azure.com).
1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Vyberte **vytvořit úložiště** a vytvořte účet úložiště pro informace o cloud Shell relaci. Tento účet úložiště je oddělený od účtu, který vytvoří skript pro použití s vaším účtem Azure Media Services.
1. V rozevírací nabídce na levé straně okna Cloud Shell vyberte **bash** jako své prostředí.

    ![Výběr prostředí](../../../media/quickstarts/env-selector.png)
1. Spusťte následující příkaz.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Po úspěšném dokončení skriptu byste měli vidět všechny požadované prostředky v rámci vašeho předplatného.
1. Po dokončení skriptu vyberte složené závorky a vystavte strukturu složek. V adresáři *~/clouddrive/lva-Sample* se zobrazí několik souborů. V tomto rychlém startu jsou důležité tyto:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env** _ – Tento soubor obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
     _ ***~/clouddrive/lva-sample/appsetting.jsv** _-Visual Studio Code používá tento soubor ke spuštění ukázkového kódu.
     
    Tyto soubory budete potřebovat při nastavení vývojového prostředí v Visual Studio Code v další části. Můžete je teď chtít zkopírovat do místního souboru.
    
    ![Nastavení aplikace](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Pokud narazíte na problémy s prostředky Azure, které se vytvoří, přečtěte si náš *[Průvodce odstraňováním potíží](../../../troubleshoot-how-to.md#common-error-resolutions)** a vyřešte některé běžně zjištěné problémy.