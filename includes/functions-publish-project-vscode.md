---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029276"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure a pak nasadíte svůj kód. 

1. Vyberte ikonu Azure na panelu aktivit a pak v oblasti **Azure: Funkce** zvolte tlačítko **Nasadit do funkce...**

    ![Publikování projektu do Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Na výzvách uveďte následující informace:

    + **Vyberte předplatné**: Zvolte předplatné, které chcete použít. Pokud máte pouze jedno předplatné, nezobrazí se vám to.

    + **Vyberte aplikaci funkce v Azure:** Zvolte `+ Create new Function App` (ne). `Advanced` Tento článek nepodporuje [pokročilý tok publikování](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 
    
    + **Zadejte globálně jedinečný název aplikace funkce**: Zadejte název, který je platný v cestě URL. Název, který zadáte, se ověří, abyste se ujistili, že je jedinečný v Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Vyberte modul runtime**: Zvolte verzi Pythonu, ve které jste běželi místně. Pomocí příkazu `python --version` můžete zkontrolovat svou verzi.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Vyberte runtime**: Zvolte verzi souboru Node.js, na které jste místně běželi. Pomocí příkazu `node --version` můžete zkontrolovat svou verzi.
    ::: zone-end

    + **Vyberte umístění pro nové zdroje**: Chcete-li dosáhnout lepšího výkonu, zvolte [oblast](https://azure.microsoft.com/regions/) ve vašem okolí. 
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure:

    + **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)**: Obsahuje všechny vytvořené prostředky Azure. Název je založen na názvu aplikace funkce.
    + **[Účet úložiště](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**: Standardní účet úložiště se vytvoří s jedinečným názvem, který je založen na názvu aplikace funkce.
    + **[Hostingový plán](../articles/azure-functions/functions-scale.md)**: Plán spotřeby se vytvoří v oblasti Západní USA, aby hostoval vaši aplikaci bez serveru.
    + **Aplikace funkce**: Váš projekt je nasazen a běží v této nové aplikaci funkce.
    + **Application Insights**: Instance, která je připojena k vaší aplikaci funkce, se vytvoří na základě názvu vaší funkce.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 
    
1. Vyberte **Zobrazit výstup** v tomto oznámení zobrazíte výsledky vytváření a nasazení, včetně prostředků Azure, které jste vytvořili. Pokud oznámení zmeškáte, vyberte ikonu zvonku v pravém dolním rohu, abyste ji znovu viděli.

    ![Vytvořit úplné oznámení](media/functions-publish-project-vscode/function-create-notifications.png)
