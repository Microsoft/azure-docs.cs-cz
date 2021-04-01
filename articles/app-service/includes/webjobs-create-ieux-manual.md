---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102109065"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Vytvoření ručně aktivované webové úlohy

1. Byl získán do [Azure Portal](https://portal.azure.com).
1. Přejít na **App Service** svého <abbr title="Prostředkem aplikace může být webová aplikace, aplikace API nebo mobilní aplikace.">Prostředek aplikace</abbr>.
1. Vyberte **WebJobs**.

    ![Vybrat webové úlohy](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

   ![Stránka WebJob](../media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

    ![Snímek obrazovky zobrazující nastavení, které je potřeba nastavit pro vytvoření ručně aktivované webové úlohy](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Nastavení      | Ukázková hodnota   | 
    | ------------ | ----------------- | 
   | <abbr title="Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než `-` a `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu.">Nahrání souboru</abbr> | ConsoleApp.zip |
    | <abbr title="Mezi typy patří průběžné, aktivované.">Typ</abbr> | Aktivuje | 
    | <abbr title="Typy obsahují naplánované nebo ruční">Aktivační události</a> | Ruční | |

4. Klikněte na **OK**. 

   Nová webová úloha se zobrazí na stránce **WebJobs** .

   ![Seznam WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Ruční webovou úlohu spustíte tak**, že kliknete pravým tlačítkem na jeho název v seznamu a kliknete na **Spustit**.
   
    ![Spustit úlohu WebJob](../media/web-sites-create-web-jobs/runondemand.png)

