---
title: 'Kurz: nasazení aplikací ASP.NET pro Azure Functions s využitím Azure DevOps Projects'
description: Azure DevOps Projects usnadňuje začátek práce s Azure. Pomocí DevOps Projects můžete nasadit aplikaci ASP.NET, která Azure Functions v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971563"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Průběžné nasazování do Azure Functions s využitím DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.

DevOps Projects také:

* Automaticky vytvoří prostředky Azure, například Azure Functions

* Vytvoří a nakonfiguruje kanál verze ve službě Azure DevOps pro CI/CD.

V tomto kurzu provedete následující:

> [!div class="checklist"]
>* Použití DevOps Projects k nasazení aplikace v ASP.NET do funkce Azure Functions
>* Konfigurace Azure DevOps a předplatného Azure
>* Kontrola funkce Azure Functions
>* Prozkoumání kanálu CI
>* Prozkoumání kanálu CD
>* Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
>* Vyčištění prostředků

V současné době jsou podporované běhové moduly pro funkce **.NET** a **Node. js**. Používáme. .NET runtime pro tento kurz nasazení na Azure Functions. 

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete si ji stáhnout [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Použití DevOps Projects k nasazení aplikace v ASP.NET pro Azure Functions

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects také vytvoří prostředky Azure, jako je například IoTHub, v předplatném Azure podle vašeho výběru.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak klikněte na **Přidat**.

   ![Projekty DevOps](_img/azure-devops-project-functions/devops-project.png)

1. Vyberte **.NET**a pak vyberte **Další**. V části **zvolte Aplikační architekturu**vyberte **ASP.NET** a klikněte na **Další**.

1. Vyberte **Function App** a pak vyberte **Další**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace předplatného Azure DevOps a Azure

1. Zadejte název projektu Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci.

1. Vyberte své předplatné Azure.

1. Pokud chcete zobrazit další nastavení konfigurace Azure a identifikovat cenovou úroveň a umístění, klikněte na další nastavení. V tomto podokně se zobrazují různé možnosti konfigurace cenové úrovně a umístění služeb Azure.

1. Ukončete oblast konfigurace Azure a potom vyberte Hotovo.

1. Po několika minutách se proces dokončí. Ukázková aplikace ASP.NET je nastavená v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se Function App a Application Insights se vytvoří kanál CI/CD a vaše aplikace se nasadí do Azure.

   Po dokončení všech těchto stavů se na Azure Portal zobrazí řídicí panel projekt Azure DevOps. Můžete také přejít na DevOps Projects řídicí panel přímo ze **všech prostředků** v Azure Portal.

   Tento řídicí panel poskytuje přehled o vašem úložišti kódu Azure DevOps, kanálu CI/CD a funkci Azure Functions. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně vyberte **Function App** k zobrazení.

## <a name="examine-the-function-app"></a>Prověřte Function App

DevOps Projects automaticky konfiguruje aplikaci funkcí, kterou můžete prozkoumat a přizpůsobit. Pokud se chcete dozvědět, jak aplikace Function App potřebujete, udělejte toto:

1. Přejít na řídicí panel DevOps Projects.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Na pravé straně vyberte aplikace Function App. Otevře se podokno aplikace Function App. Z tohoto zobrazení můžete provádět různé akce, jako je monitorování operací, hledání protokolů.

    ![Function App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Pokud se k němu chcete seznámit, udělejte toto:

1. Přejít na řídicí panel DevOps Projects.

1. Klikněte na hypertextový odkaz v části **sestavení**. Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

    ![Build](_img/azure-devops-project-functions/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je například načítání zdrojového kódu z úložiště Git, sestavování aplikace, spuštění testů jednotek a publikování výstupů, které se používají pro nasazení.

1. Vyberte **Triggery**. DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení na výstižnější a pak v rozevíracím seznamu **uložit & fronty** vyberte **Uložit** .

1. Pod názvem vašeho kanálu buildu vyberte **Historie**. V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

## <a name="examine-the-cd-release-pipeline"></a>Kontrola kanálu pro vydání CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje disk CD do Azure. Pokud se chcete dozvědět víc o kanálu vydávání, udělejte toto:

1. Přejděte na **kanály | Verze**.

1. Klikněte na **Upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt.

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**. Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Klikněte na verzi, která zobrazí kanál. Kliknutím na libovolné prostředí zkontrolujete souhrn vydaných verzí **, potvrzení změn**a přidružené **pracovní položky**.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

> [!NOTE]
> Následující postup testuje kanál CI/CD tím, že provede jednoduchou změnu textu.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci do funkce Azure Functions. Každá změna úložiště Git spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použijte jinou techniku, abyste potvrdili změny v úložišti. Úložiště Git můžete například klonovat v oblíbených nástrojích nebo v integrovaném vývojovém prostředí (IDE) a pak doručovat změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte možnost **úložišť | Soubory**a pak přejít na své úložiště.

1. Úložiště již obsahuje kód s názvem **SampleFunctionApp** na základě jazyka aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **Application/SampleFunctionApp/function1. cs** .

1. Vyberte **Upravit**a pak proveďte změnu na **řádek číslo 31** . Můžete ho například aktualizovat na **Hello! Vítá vás Azure Functions používání DevOps Projects**

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte.

1. Otevřete soubor **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Vyberte **Upravit**a pak proveďte změnu na **řádek číslo 21**. Můžete ho například aktualizovat na **Hello! Vítá vás Azure Functions používání Azure DevOps Projects**.

     Po chvíli se Build spustí v Azure DevOps a k nasazení změn se spustí vydání. Monitorujte stav buildu na řídicím panelu DevOps Projects nebo v prohlížeči pomocí vaší organizace Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, můžete odstranit, když už je nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití DevOps Projects k nasazení ASP.NET Core aplikace do služby Azure Functions
> * Konfigurace Azure DevOps a předplatného Azure 
> * Kontrola funkce Azure Functions
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

