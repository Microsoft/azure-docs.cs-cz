---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744864"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Vytvoření průběžné úlohy WebJob

1. Byl získán do [Azure Portal](https://portal.azure.com).
1. Přejít na **App Service** svého <abbr title="Prostředkem aplikace může být webová aplikace, aplikace API nebo mobilní aplikace.">Prostředek aplikace</abbr>.
1. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Snímek obrazovky, který zobrazuje nastavení přidání úlohy WebJob, které je třeba nakonfigurovat.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | 
   | ------------ | ----------------- | 
   | <abbr title="Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než `-` a `_` .">Název</abbr> | myContinuousWebJob | 
   | <abbr title=" Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu.">Nahrání souboru</abbr> | ConsoleApp.zip |
   | <abbr title="Mezi typy patří průběžné, aktivované.">Typ</abbr> | Průběžný | 
   | <abbr title="K dispozici pouze pro nepřetržité webové úlohy. Určuje, zda se program nebo skript spouští na všech instancích nebo pouze v jedné instanci. Možnost spuštění na více instancích se nevztahuje na cenové úrovně Free nebo Shared.">Měřítko</abbr> | Více instancí | 

1. Klikněte na **OK**.

    Nová webová úloha se zobrazí na stránce **WebJobs** .

    ![Seznam WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Chcete-li ukončit nebo restartovat** průběžnou úlohu WebJob, klikněte pravým tlačítkem myši na webovou úlohu v seznamu a klikněte na tlačítko **zastavit** nebo **Spustit**.

   ![Zastavení průběžné úlohy WebJob](../media/web-sites-create-web-jobs/continuousstop.png)
