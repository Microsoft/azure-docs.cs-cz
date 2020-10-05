---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: f4e58f4f510450db13ae13d3beecba4d55e766bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91408536"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.


1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Zadejte následující informace na následujících dotazech:

    - **Vyberte složku**: Zvolte složku z pracovního prostoru nebo přejděte k nějakému, který obsahuje vaši aplikaci Function App. Tato funkce se nezobrazuje, pokud už máte spuštěnou platnou aplikaci Function App.

    - **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    - **Vyberte Function App v Azure**: zvolte `- Create new Function App` . (Nevybírejte `Advanced` možnost, která není pokrytá v tomto článku.)
      
    - **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný.
    
    ::: zone pivot="programming-language-python"
    - **Vyberte modul runtime**: Zvolte verzi Pythonu, kterou jste spustili místně. `python --version`K zkontrolování vaší verze můžete použít příkaz.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    - **Vyberte modul runtime**: zvolte verzi Node.js, na které jste pracovali místně. `node --version`K zkontrolování vaší verze můžete použít příkaz.
    ::: zone-end

    - **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. 
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure s použitím názvů na základě názvu vaší aplikace Function App:
    
    - Skupina prostředků, což je logický kontejner pro související prostředky.
    - Účet Standard Azure Storage, který uchovává stav a další informace o vašich projektech.
    - Plán spotřeby, který definuje základního hostitele pro aplikaci s funkcí bez serveru. 
    - Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků v rámci stejného plánu hostování.
    - Instance Application Insights připojená k aplikaci Function App, která sleduje využití funkce bez serveru.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 
    
1. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](media/functions-publish-project-vscode/function-create-notifications.png)
