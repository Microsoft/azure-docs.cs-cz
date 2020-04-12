---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: d8665b4cec3357baee5d6c1b77b5719645575419
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112847"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure a pak nasadíte svůj kód. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 


1. Vyberte ikonu Azure na panelu aktivit a pak v oblasti **Azure: Funkce** zvolte tlačítko **Nasadit do funkce...**

    ![Publikování projektu do Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Na výzvách uveďte následující informace:

    + **Vyberte předplatné**: Zvolte předplatné, které chcete použít. Pokud máte pouze jedno předplatné, nezobrazí se vám to.

    + **Vyberte aplikaci**funkce `+ Create new Function App`v Azure : Zvolte . (Nevybírejte `Advanced` možnost, která není zahrnuta v tomto článku.)
      
    + **Zadejte globálně jedinečný název aplikace funkce**: Zadejte název, který je platný v cestě URL. Název, který zadáte, se ověří, abyste se ujistili, že je jedinečný v Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Vyberte modul runtime**: Zvolte verzi Pythonu, ve které jste běželi místně. Pomocí příkazu `python --version` můžete zkontrolovat svou verzi.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Vyberte runtime**: Zvolte verzi souboru Node.js, na které jste místně běželi. Pomocí příkazu `node --version` můžete zkontrolovat svou verzi.
    ::: zone-end

    + **Vyberte umístění pro nové zdroje**: Chcete-li dosáhnout lepšího výkonu, zvolte [oblast](https://azure.microsoft.com/regions/) ve vašem okolí. 
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure pomocí názvů založených na názvu aplikace funkce:
    
    + Skupina prostředků, která je logickým kontejnerem pro související prostředky.
    + Standardní účet Azure Storage, který udržuje stav a další informace o vašich projektech.
    + Plán spotřeby, který definuje základního hostitele pro aplikaci funkcí bez serveru. 
    + Aplikace funkce, která poskytuje prostředí pro provádění kódu funkce. Aplikace funkce umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení a sdílení prostředků v rámci stejného plánu hostování.
    + Instance Application Insights připojená k aplikaci funkce, která sleduje využití funkce bez serveru.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 
    
1. Vyberte **Zobrazit výstup** v tomto oznámení zobrazíte výsledky vytváření a nasazení, včetně prostředků Azure, které jste vytvořili. Pokud oznámení zmeškáte, vyberte ikonu zvonku v pravém dolním rohu, abyste ji znovu viděli.

    ![Vytvořit úplné oznámení](media/functions-publish-project-vscode/function-create-notifications.png)
