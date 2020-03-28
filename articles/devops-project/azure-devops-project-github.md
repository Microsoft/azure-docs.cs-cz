---
title: 'Kurz: Vytvoření kanálu CI/CD pro váš existující kód pomocí projektů Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. V několika rychlých krocích devops projekty vám pomůže použít vlastní kód a úložiště GitHub ke spuštění aplikace ve službě Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615132"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Kurz: Vytvoření kanálu CI/CD pro váš existující kód pomocí projektů Azure DevOps

Azure DevOps Projects představuje zjednodušený proces pro vytváření průběžné integrace (CI) a průběžné ho doručování (CD) kanálu do Azure. Můžete přenést svůj existující kód a úložiště Git, nebo můžete vybrat ukázkovou aplikaci.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí projektů DevOps
> * Konfigurace přístupu k úložišti GitHub a výběr rozhraní
> * Konfigurace Azure DevOps a předplatného Azure 
> * Potvrzení změn na GitHubu a jejich automatické nasazení do Azure
> * Zkontrolujte kanály Azure Pipelines CI/CD kanálu
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup k githubu nebo externímu úložišti Git, které obsahuje .NET, Java, PHP, Node.js, Python nebo statický webový kód.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Projekty vytvoří kanál CI/CD v azure kanály. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. Azure DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

   ![Nabídka portálu Azure – vytvoření prostředku](_img/azure-devops-project-github/createaresource.png)

3. Vyberte **možnost DevOps** > **DevOps Project**.

   ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/azuredashboard.png)

1. Vyberte **Přenést vlastní kód**a pak vyberte **Další**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurace přístupu k úložišti GitHub a výběr usvého úložiště

1. Vyberte **github** nebo externí úložiště kódu **Git.** V tomto kurzu vyberte **GitHub**. Možná budete muset poprvé ověřit pomocí GitHubu, abyste povolili Azure přístup k úložišti GitHub.

1. Vyberte **úložiště** a **větev**a pak vyberte **Další**.

1. Pokud používáte kontejnery Dockeru, změňte **je aplikace Dockerized** na **ANO**. V tomto kurzu ponechte **políčko NO** a pak vyberte **další**. Další informace o používání kontejnerů Dockeru najeďte na ikonu **i.**

   ![Výběr aplikačního rámce v rozbalovací nabídce](_img/azure-devops-project-github/appframework.png)

1. V rozevíracích nabídkách vyberte **runtime aplikace** a **rozhraní aplikace**a pak vyberte **Další**. Rozhraní aplikace určuje typ cíle nasazení služby Azure, který je k dispozici.

1. Vyberte **službu Azure,** kterou chcete nasadit, a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Zadejte název **aplikace Project**.

1. Vytvořte novou bezplatnou organizaci v **organizaci Azure DevOps nebo** vyberte existující organizaci z rozbalovací nabídky.

1. Vyberte předplatné v **předplatným Azure**a zadejte název do **webové aplikace** nebo použijte výchozí. Vyberte **umístění**a pak vyberte **Hotovo**. Po několika minutách se na webu Azure Portal zobrazí přehled nasazení projektů DevOps.

1. Chcete-li zobrazit řídicí panel Projekty DevOps, vyberte **Možnost Přejít na zdroj.** V pravém horním rohu připněte **Project** na řídicí panel, abyste k němu měli rychlý přístup. Azure DevOps Projects automaticky konfiguruje aktivační událost sestavení a vydání ci. Váš kód zůstane ve vašem úložišti GitHub nebo jiném externím úložišti a ukázková aplikace se nastaví v úložišti v **Azure DevOps Organization**. Azure DevOps Projects spouští sestavení a nasazuje aplikaci do Azure.

   ![Zobrazení řídicího panelu Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Řídicí panel zobrazuje vaše repo kódu, kanál CI/CD a vaši aplikaci v Azure. Vpravo v části Prostředky Azure vyberte **Procházet** a zobrazte spuštěnou aplikaci.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Potvrzení změn na GitHubu a jejich automatické nasazení do Azure

Nyní jste připraveni spolupracovat na aplikaci s týmem. Proces CI/CD automaticky nasazuje vaši nejnovější práci na váš web. Každá změna úložiště GitHub spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure.

1. Na řídicím panelu Projekty DevOps vyberte **Úložiště**. Vaše úložiště GitHub se otevře na nové kartě prohlížeče. **Commit changes**

1. Po několika okamžicích se sestavení spustí v Azure Pipelines. Stav sestavení můžete sledovat na řídicím panelu Projekty DevOps. Můžete také sledovat ve vaší organizaci Azure DevOps výběrem karty **Sestavení kanálů** z řídicího panelu DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Zkontrolujte kanály Azure Pipelines CI/CD kanálu

Azure DevOps Projects automaticky konfiguruje kanál CI/CD v azure kanálech. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte takto:

1. Na řídicím panelu Projekty DevOps vyberte **Build pipelines**.

1. Po otevření stránky **Azure Pipelines** uvidíte historii nejnovějších sestavení a stav pro každé sestavení.

   ![Stránka sestavení Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. V pravém horním rohu stránky **Sestavení** můžete vybrat **Upravit** a změnit aktuální sestavení, **Fronta** pro přidání nového sestavení nebo svislé tlačítko se třemi tečkami (**&#8942;) **a otevřít nabídku s více možnostmi. Vyberte **Upravit**.

1. Sestavení provádí různé úkoly, jako je například načítání zdrojů z repo, obnovení závislostí a publikování výstupů pro nasazení. Vpravo v části **Název**změňte název kanálu sestavení na něco popisnějšího. Vyberte **Uložit & frontu**a pak vyberte **Uložit**. Zadejte komentář a pak znovu vyberte **Uložit.**

   ![Stránka sestavení Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Chcete-li zobrazit záznam auditu posledních změn pro sestavení, vyberte kartu **Historie.**  Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte kartu **Aktivační události.** Projekty Azure DevOps automaticky vytvoří aktivační událost CI s některými výchozími nastaveními. Můžete nastavit aktivační události, jako je **například Povolit průběžnou integraci** ke spuštění sestavení při každém potvrzení změny kódu. Můžete také nastavit aktivační události naplánovat sestavení spustit v určitých časech.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete službu Azure App Service a související prostředky, které jste vytvořili v tomto kurzu, můžete je odstranit. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

Když jste v tomto kurzu nakonfigurovali proces CI/CD, automaticky jste vytvořili kanál sestavení a vydání v projektech Azure DevOps. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu.

Další informace o kanálu CI/CD najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Definování vícestupňového kanálu průběžného nasazení (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Další informace o monitorování aplikací najdete v tématu:
  
 > [!div class="nextstepaction"]
 > [Co je Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
