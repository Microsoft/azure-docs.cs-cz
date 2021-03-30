---
title: Vytvoření první trvalé funkce v Azure Functions pomocí prostředí PowerShell
description: Vytvořte a publikujte funkci trvalosti Azure v PowerShellu pomocí Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317255"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Vytvoření první trvalé funkce v PowerShellu

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

V tomto článku se naučíte, jak používat rozšíření Visual Studio Code Azure Functions k místnímu vytvoření a otestování trvalé funkce "Hello World".  Tato funkce provede orchestraci a zřetězení volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Spuštění trvalé funkce v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Instalace rozšíření [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code

* Ujistěte se, že máte nejnovější verzi [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions vyžaduje účet služby Azure Storage. Potřebujete předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. 

1. V Visual Studio Code stisknutím klávesy F1 (nebo Ctrl/Cmd + Shift + P) otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create New Project...` .

    ![Funkce Create](media/quickstart-js-vscode/functions-create-project.png)

1. Zvolte prázdné umístění složky pro váš projekt a zvolte **možnost vybrat**.

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro projekt Function App. | PowerShell | Vytvořte místní projekt PowerShell Functions. |
    | Vybrat verzi | Azure Functions v3 | Tato možnost se zobrazí jenom v případě, že už nejsou nainstalované základní nástroje. V takovém případě se při prvním spuštění aplikace nainstalují základní nástroje. |
    | Vyberte šablonu pro funkci prvního projektu. | Prozatím přeskočte | |
    | Vyberte, jak se má projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře VS Code ve vybrané složce. |

V případě potřeby Visual Studio Code nainstaluje Azure Functions Core Tools. Zároveň vytvoří projekt Function App ve složce. Tento projekt obsahuje [host.js](../functions-host-json.md) a [local.settings.jsv](../functions-run-local.md#local-settings-file) konfiguračních souborech.

V kořenové složce je také vytvořena package.jsv souboru.

### <a name="configure-function-app-to-use-powershell-7"></a>Konfigurace aplikace Function App pro použití prostředí PowerShell 7

Otevřete *local.settings.jsv* souboru a potvrďte, že nastavení s názvem `FUNCTIONS_WORKER_RUNTIME_VERSION` je nastaveno na `~7` . Pokud chybí nebo je nastavená na jinou hodnotu, aktualizujte obsah souboru.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Vytvoření funkcí

Základní aplikace Durable Functions obsahuje tři funkce:

* *Funkce Orchestrator* – popisuje pracovní postup, který orchestruje jiné funkce.
* *Funkce aktivity* – volána funkcí Orchestrator, provede práci a případně vrátí hodnotu.
* *Klientská funkce* – běžná funkce Azure, která spouští funkci Orchestrator. V tomto příkladu se používá funkce aktivované protokolem HTTP.

### <a name="orchestrator-function"></a>Funkce Orchestrator

Použijete šablonu k vytvoření trvalého kódu funkce v projektu.

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Durable Functions Orchestrator (Preview) | Vytvoření orchestrace Durable Functions |
    | Zadejte název funkce. | HelloOrchestrator | Název trvalé funkce |

Přidali jste nástroj Orchestrator pro koordinaci funkcí aktivity. Otevřete *HelloOrchestrator/run.ps1* pro zobrazení funkce Orchestrator. Každé volání `Invoke-ActivityFunction` rutiny vyvolá funkci aktivity s názvem `Hello` .

Dále přidáte `Hello` funkci odkazované aktivity.

### <a name="activity-function"></a>Activity – funkce

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Aktivita Durable Functions (Preview) | Vytvoření funkce aktivity |
    | Zadejte název funkce. | Hello (Nazdar) | Název funkce aktivity |

Přidali jste `Hello` funkci aktivity, která je vyvolána nástrojem Orchestrator. Otevřete *Hello/run.ps1* a podívejte se, že se pojmenuje jako vstup a vrátí pozdrav. Funkce Activity je místo, kde provedete akce, jako je například vytvoření databázového volání nebo provedení výpočtu.

Nakonec přidáte funkci aktivovanou protokolem HTTP, která spouští orchestraci.

### <a name="client-function-http-starter"></a>Klientská funkce (HTTP Starter)

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Durable Functions HTTP Starter (Preview) | Vytvoření funkce HTTP Starter |
    | Zadejte název funkce. | HttpStart | Název funkce aktivity |
    | Úroveň autorizace | Anonymní | Pro účely ukázky povolte funkci, která se má volat bez ověřování. |

Přidali jste funkci aktivovanou protokolem HTTP, která spouští orchestraci. Otevřete *HttpStart/run.ps1* , abyste viděli, že používá `Start-NewOrchestration` rutinu k zahájení nové orchestrace. Pak pomocí `New-OrchestrationCheckStatusResponse` rutiny vrátí odpověď HTTP obsahující adresy URL, které lze použít k monitorování a správě nové orchestrace.

Nyní máte aplikaci Durable Functions, která se dá spustit místně a nasadit do Azure.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. Při prvním spuštění aplikace Function App z Visual Studio Code budete vyzváni k instalaci těchto nástrojů.

1. Chcete-li otestovat funkci, nastavte zarážku v `Hello` kódu funkce Activity (*Hello/run.ps1*). Stisknutím klávesy F5 nebo výběrem `Debug: Start Debugging` z palety příkazů spusťte projekt Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v tématu [diagnostika Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Durable Functions vyžaduje, aby se spustil účet Azure Storage. Když VS Code zobrazí výzvu k výběru účtu úložiště, zvolte **Vybrat účet úložiště**.

    ![Vytvoření účtu úložiště](media/quickstart-js-vscode/functions-select-storage.png)

1. Podle pokynů zadejte následující informace, které vám pomůžou vytvořit nový účet úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název vašeho předplatného* | Vyberte své předplatné Azure. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který se má vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, která se má vytvořit |
    | Výběr umístění | *věřitel* | Vyberte oblast, kterou chcete zavřít. |

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/quickstart-js-vscode/functions-f5.png)

1. Pomocí prohlížeče nebo nástroje, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/), odešlete požadavek HTTP POST na koncový bod adresy URL. Nahradí poslední segment názvem funkce Orchestrator ( `HelloOrchestrator` ). Adresa URL by měla být podobná `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Odpověď je počáteční výsledek z funkce HTTP, která vám umožní zjistit, že trvalá orchestrace byla úspěšně spuštěna. Není ještě konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím si vydejte dotaz na stav orchestrace.

1. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek. Případně můžete i nadále používat metodu post pro vydání žádosti o získání.

   Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která ukazuje, že se instance dokončila, a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá takto: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Pokud chcete zastavit ladění, stiskněte **SHIFT + F5** v vs Code.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 


1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte složku**: Zvolte složku z pracovního prostoru nebo přejděte k nějakému, který obsahuje vaši aplikaci Function App. Tato funkce se nezobrazuje, pokud už máte spuštěnou platnou aplikaci Function App.

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Výběr aplikace funkcí v Azure:** Zvolte `+ Create new Function App`. (Nevybírejte `Advanced` možnost, která není pokrytá v tomto článku.)
      
    + **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný. 

    + **Vyberte modul runtime**: Zvolte verzi prostředí PowerShell, kterou jste spustili místně. `pwsh -version`K zkontrolování vaší verze můžete použít příkaz.

        > [!NOTE]
        > Rozšíření Azure Functions VS Code možná zatím nepodporuje PowerShell 7. Pokud není prostředí PowerShell 7 k dispozici jako možnost, vyberte nyní PowerShell 6. x a po vytvoření aplikace Function App [aktualizujte verzi ručně](#update-function-app-ps7) .

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. 
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure s použitím názvů na základě názvu vaší aplikace Function App:
    
    + Skupina prostředků, což je logický kontejner pro související prostředky.
    + Účet Standard Azure Storage, který uchovává stav a další informace o vašich projektech.
    + Plán spotřeby, který definuje základního hostitele pro aplikaci s funkcí bez serveru. 
    + Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků v rámci stejného plánu hostování.
    + Instance Application Insights připojená k aplikaci Function App, která sleduje využití funkce bez serveru.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení.

1. <a name="update-function-app-ps7"></a>Pokud jste při vytváření aplikace Function App nedokázali vybrat *prostředí PowerShell 7* , otevřete paletu příkazů stisknutím klávesy F1 (nebo stisknutím kombinace kláves Ctrl/Cmd + Shift + P). V paletě příkazů vyhledejte a vyberte `Azure Functions: Upload Local Settings...` . Podle pokynů vyberte aplikaci funkcí, kterou jste vytvořili. Pokud se zobrazí výzva k přepsání stávajících nastavení, vyberte *Ne všem*.
    
1. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci spuštěnou protokolem HTTP, by měla být v tomto formátu: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření a publikování aplikace s funkcí PowerShellu pro trvalou činnost.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)
