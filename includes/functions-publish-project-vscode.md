---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029276"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte. 

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Zadejte následující informace na následujících dotazech:

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Vyberte Function App v Azure**: zvolte `+ Create new Function App` (není `Advanced`). Tento článek nepodporuje [pokročilý tok publikování](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 
    
    + **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný. 
    
    ::: zone pivot="programming-language-python"
    + **Vyberte modul runtime**: Zvolte verzi Pythonu, kterou jste spustili místně. K zkontrolování vaší verze můžete použít příkaz `python --version`.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Vyberte modul runtime**: Zvolte verzi Node. js, kterou jste spustili místně. K zkontrolování vaší verze můžete použít příkaz `node --version`.
    ::: zone-end

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. 
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure:

    + **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** : obsahuje všechny vytvořené prostředky Azure. Název je založen na názvu vaší aplikace Function App.
    + **[Účet úložiště](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : účet úložiště úrovně Standard se vytvoří s jedinečným názvem, který je založený na názvu vaší aplikace Function App.
    + **[Plán hostování](../articles/azure-functions/functions-scale.md)** : v oblasti západní USA se vytvoří plán spotřeby pro hostování aplikace Function bez serveru.
    + **Aplikace Function App**: projekt se nasadí do této nové aplikace Function App a spustí se.
    + **Application Insights**: instance, která je připojená k vaší aplikaci Function App, je vytvořená na základě názvu vaší funkce.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 
    
1. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](media/functions-publish-project-vscode/function-create-notifications.png)
