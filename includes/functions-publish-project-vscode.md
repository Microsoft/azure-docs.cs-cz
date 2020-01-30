---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842117"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte. 

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Zadejte následující informace na následujících dotazech:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----- |
    | Výběr předplatného | Vaše předplatné | Zobrazuje se, když máte více předplatných. |
    | Výběr Function App v Azure | + Vytvořit nové Function App | Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. |
    | Zadejte globálně jedinečný název aplikace Function App. | Jedinečný název | Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Vyberte umístění pro nové prostředky. | Region (Oblast) | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----- |
    | Výběr předplatného | Vaše předplatné | Zobrazuje se, když máte více předplatných. |
    | Výběr Function App v Azure | + Vytvořit nové Function App | Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. |
    | Zadejte globálně jedinečný název aplikace Function App. | Jedinečný název | Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Vyberte modul runtime. | Vaše verze | Vyberte jazykovou verzi, kterou jste spustili místně. |
    | Vyberte umístění pro nové prostředky. | Region (Oblast) | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. | 

    ::: zone-end

    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure:

    + **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** : obsahuje všechny vytvořené prostředky Azure. Název je založen na názvu vaší aplikace Function App.
    + **[Účet úložiště](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : účet úložiště úrovně Standard se vytvoří s jedinečným názvem, který je založený na názvu vaší aplikace Function App.
    + **[Plán hostování](../articles/azure-functions/functions-scale.md)** : v oblasti západní USA se vytvoří plán spotřeby pro hostování aplikace Function bez serveru.
    + **Aplikace Function App**: projekt se nasadí do této nové aplikace Function App a spustí se.
    + **[Application Insights]()** : instance, která je připojená k vaší aplikaci Function App, je vytvořená na základě názvu vaší funkce.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 
    
1. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení.

    ![Vytvořit kompletní oznámení](media/functions-publish-project-vscode/function-create-notifications.png)

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem myši (Windows) nebo Ctrl + Click (MacOS) na **HttpExample**a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
