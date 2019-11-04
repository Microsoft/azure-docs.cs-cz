---
title: 'Kurz: vytvoření kanálu CI/CD pro váš stávající kód pomocí Azure DevOps Projects'
description: Azure DevOps Projects usnadňuje začátek práce s Azure. DevOps Projects vám pomůže používat vlastní kód a úložiště GitHub ke spuštění aplikace ve službě Azure podle vašeho výběru v několika rychlých krocích.
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
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481112"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Kurz: vytvoření kanálu CI/CD pro váš stávající kód pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí DevOps Projects
> * Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.
> * Konfigurace Azure DevOps a předplatného Azure 
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Projděte si kanál Azure Pipelines CI/CD.
> * Vyčištění prostředků

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup k GitHubu nebo externímu úložišti Git, které obsahuje .NET, Java, PHP, Node, Python nebo statický webový kód.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Azure DevOps Projects taky vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. V nabídce Azure Portal vyberte **vytvořit prostředek**.

   ![Nabídka Azure Portal – vytvoření prostředku](_img/azure-devops-project-github/createaresource.png)

3. Vyberte **DevOps > projekt DevOps**.

   ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Vyberte možnost **Přineste si vlastní kód**a pak vyberte **Další**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.

1. Vyberte buď **GitHub** , nebo externí úložiště kódu **Git** . V tomto kurzu vyberte **GitHub**. Pokud chcete, aby Azure měl přístup k úložišti GitHubu, možná budete muset nejdřív ověřit pomocí GitHubu.

2. Dokončete výběrem **úložiště** a **větve**, vyberte **Další**.

3. Pokud používáte změnu kontejnerů Docker, **Dockerized se aplikace** na **Ano**, pro tento kurz **nezaškrtnete** políčko **Další**. Další informace o používání kontejnerů Docker najdete najetím myší na ikonu **i** .

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. V rozevíracích seznamech vyberte **modul runtime aplikace** a **rozhraní**a pak vyberte **Další**. Aplikační rozhraní, které vyberete, určuje typ cíle nasazení služby Azure, který je k dispozici.

5. Vyberte **službu Azure** , abyste mohli aplikaci nasadit, a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Zadejte **název projektu**.

2. Vytvořte novou bezplatnou **organizaci Azure DevOps** nebo z rozevíracího seznamu vyberte existující organizaci.

3. Vyberte své **předplatné Azure**, zadejte název **webové aplikace** nebo výchozí nastavení. Vyberte **umístění**a potom vyberte **Hotovo**. Po několika minutách se v Azure Portal zobrazí přehled nasazení projektu DevOps.

4. Vyberte **Přejít k prostředku** a zobrazte řídicí panel projektu DevOps. V pravém horním rohu připněte **projekt** na řídicí panel pro rychlý přístup. Azure DevOps Projects automaticky konfiguruje aktivační událost sestavení a vydání CI. Váš kód zůstane ve vašem úložišti GitHubu nebo jiném externím úložišti. Ukázková aplikace se nastavuje v úložišti ve vaší **organizaci Azure DevOps**. Spustí se sestavení a vaše aplikace se nasadí do Azure.

   ![Zobrazení řídicího panelu DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. Řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure. Napravo v části prostředky Azure vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Každá změna úložiště GitHub spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure.

1. Z řídicího panelu projektu DevOps vyberte **úložiště**. Vaše úložiště GitHub se otevře na nové kartě prohlížeče. proveďte u své aplikace změnu a klikněte na **Potvrdit změny**.

2. Po chvíli začíná sestavení v Azure Pipelines. Stav sestavení můžete monitorovat na řídicím panelu DevOps Projects nebo ho monitorovat v organizaci Azure DevOps tak, že na řídicím panelu projekt vyberete kartu **kanály sestavení** .

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Projděte si kanál Azure Pipelines CI/CD.

Azure DevOps Projects automaticky konfiguruje kanál CI/CD v Azure Pipelines. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. Z řídicího panelu DevOps Projects vyberte **vytvořit kanály**.

2. Po otevření stránky **Azure Pipelines** uvidíte historii nejnovějších sestavení a stavu každého sestavení.

   ![Sestavení kanálu Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. V pravém horním rohu stránky **sestavení** se zobrazí možnosti pro **úpravu** aktuálního sestavení, **zařazení** do fronty pro nové sestavení a tři tečky ( **&#8942;** ) pro otevření nabídky s dalšími možnostmi, vyberte **Upravit**.

4. Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště, obnovování závislostí a publikování výstupů pro nasazení. Napravo pod položkou **název**změňte název kanálu sestavení na výstižnější. Vyberte **uložit & frontu**, pak **Uložit**, ponechte komentář a pak vyberte **Uložit** znovu.

   ![Stránka buildů Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Pokud se chcete podívat na záznam pro audit vašich nedávných změn pro sestavení, vyberte kartu **Historie** . Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

6. Vyberte kartu **triggery** . projekt Azure DevOps automaticky vytvoří Trigger ci s některými výchozími nastaveními. Triggery, jako je například **Enable Continuous Integration** , lze nastavit tak, aby pokaždé, když byla změna kódu potvrzena, nebo aby se sestavení spouštěla v určitých časech.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit službu Azure App Service a související prostředky, které jste vytvořili v tomto kurzu. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD v tomto kurzu se automaticky vytvořil kanál sestavení a verze v Azure DevOps Projects. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Naučili jste se tyto postupy:

> [!div class="checklist"]
>  * Vytvoření kanálu CI/CD pomocí DevOps Projects
> * Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.
> * Konfigurace Azure DevOps a předplatného Azure
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Projděte si kanál Azure Pipelines CI/CD.
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Další informace o monitorování aplikací najdete v těchto tématech:
  
 > [!div class="nextstepaction"]
 > [Co je Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
