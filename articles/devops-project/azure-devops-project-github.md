---
title: 'Kurz: vytvoření kanálu CI/CD pro váš stávající kód pomocí Azure DevOps Starter'
description: Azure DevOps Starter usnadňuje začátek práce v Azure. V několika rychlých krocích vám DevOps Projects pomůže používat vlastní kód a úložiště GitHub ke spuštění aplikace ve službě Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566596"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Vytvoření kanálu CI/CD pro úložiště GitHub pomocí Azure DevOps Starter

Azure DevOps Starter prezentuje zjednodušený proces vytváření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. Můžete převést existující kód a úložiště Git, nebo můžete vybrat ukázkovou aplikaci.

V této lekci:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí DevOps Starter
> * Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.
> * Konfigurace Azure DevOps a předplatného Azure 
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Projděte si kanál Azure Pipelines CI/CD.
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup k GitHubu nebo externímu úložišti Git, které obsahuje .NET, Java, PHP, Node.js, Python nebo statický webový kód.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Azure DevOps Starter také vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter** a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Vyberte možnost **Přineste si vlastní kód** a pak vyberte **Další**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurace přístupu k úložišti GitHub a výběr architektury

1. Vyberte buď **GitHub** , nebo externí úložiště kódu **Git** . Pro tento kurz vyberte **GitHub**. K tomu, abyste mohli Azure získat přístup k úložišti GitHubu, se možná budete muset nejdřív ověřit pomocí GitHubu.

1. Vyberte **úložiště** a **větev** a pak vyberte **Další**.

1. Pokud používáte kontejnery Docker, změňte **je aplikace Dockerized** na **Ano**. Pro tento kurz nechejte **žádné** vybrané a pak vyberte **Další**. Další informace o používání kontejnerů Docker naleznete po najetí myší na ikonu **i** .

   ![Výběr aplikační architektury v rozevírací nabídce](_img/azure-devops-project-github/appframework.png)

1. V rozevíracích nabídkách vyberte **modul runtime aplikace** a **aplikační rozhraní** a pak vyberte **Další**. Rozhraní Application Framework určuje typ cíle nasazení služby Azure, který je k dispozici.

1. Vyberte **službu Azure** pro nasazení aplikace a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Zadejte název **projektu**.

1. Vytvořte si novou bezplatnou organizaci v **organizaci Azure DevOps** nebo z rozevírací nabídky vyberte existující organizaci.

1. V **předplatném Azure** vyberte své předplatné a buď zadejte název do **webové aplikace** , nebo použijte výchozí nastavení. Vyberte **umístění** a potom vyberte **Hotovo**. Po několika minutách se v Azure Portal zobrazí přehled nasazení DevOps Starter.

1. Vyberte **Přejít k prostředku** a zobrazte řídicí panel DevOps Starter. V pravém horním rohu připněte **projekt** na řídicí panel pro rychlý přístup. Azure DevOps Starter automaticky nakonfiguruje aktivační událost sestavení a vydání CI. Váš kód zůstává v úložišti GitHubu nebo v jiném externím úložišti a ukázková aplikace je nastavená v úložišti v **organizaci Azure DevOps**. Azure DevOps Starter spustí Build a nasadí aplikaci do Azure.

   ![Zobrazení řídicího panelu Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Řídicí panel zobrazuje vaše úložiště kódu, kanál CI/CD a vaši aplikaci v Azure. Na pravé straně v části prostředky Azure vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.

Nyní jste připraveni spolupracovat na své aplikaci s týmem. Proces CI/CD automaticky nasadí vaši nejnovější práci na web. Každá změna úložiště GitHub spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure.

1. Na řídicím panelu DevOps Starter vyberte **úložiště**. Vaše úložiště GitHub se otevře na nové kartě prohlížeče. Proveďte změnu aplikace a pak vyberte **Potvrdit změny**.

1. Po chvíli začíná sestavení v Azure Pipelines. Stav sestavení můžete monitorovat na řídicím panelu Start DevOps. Můžete ho také monitorovat ve vaší organizaci Azure DevOps, a to tak, že na řídicím panelu DevOps Starter vyberete kartu **kanály sestavení** .

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Projděte si kanál Azure Pipelines CI/CD.

Azure DevOps Starter automaticky nakonfiguruje kanál CI/CD v Azure Pipelines. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. Na řídicím panelu DevOps Starter vyberte **vytvořit kanály**.

1. Po otevření stránky **Azure Pipelines** uvidíte historii nejnovějších sestavení a stavu každého sestavení.

   ![Stránka sestavení Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. V pravém horním rohu stránky **sestavení** můžete vybrat **Upravit** pro změnu aktuálního sestavení, **zařazení do fronty** pro přidání nového sestavení nebo svislé tlačítko se třemi tečkami (**&#8942;**) k otevření nabídky s dalšími možnostmi. Vyberte **Upravit**.

1. Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště, obnovení závislostí a publikování výstupů pro nasazení. Napravo v části **název** změňte název kanálu sestavení na výstižnější. Vyberte **uložit & frontu** a potom vyberte **Uložit**. Zadejte komentář a pak znovu vyberte **Uložit** .

   ![Stránka buildů Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Chcete-li zobrazit záznam pro audit vašich nedávných změn pro sestavení, vyberte kartu **Historie** .  Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte kartu **triggery** . Azure DevOps Projects automaticky vytvoří Trigger ci s některými výchozími nastaveními. Můžete nastavit triggery, jako je například **Povolit průběžnou integraci** pro spuštění sestavení při každém potvrzení změny kódu. Můžete také nastavit triggery k naplánování spuštění sestavení v určitých časech.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete Azure App Service a související prostředky, které jste vytvořili v tomto kurzu, můžete je odstranit. Použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD v tomto kurzu jste automaticky vytvořili kanál sestavení a vydání v Azure DevOps Projects. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu.

Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](/azure/devops/pipelines/release/define-multistage-release-process)

Další informace o monitorování aplikací najdete v těchto tématech:
  
 > [!div class="nextstepaction"]
 > [Co je Azure monitor?](../azure-monitor/overview.md)