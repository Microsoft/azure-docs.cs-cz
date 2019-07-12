---
title: 'Kurz: Nasazení aplikace Node.js využívající službu Azure Cosmos DB pomocí projektů Azure DevOps'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. S projekty DevOps můžete nasadit svou aplikaci Node.js využívající službu Azure Cosmos DB do webové aplikace Windows v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813063"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Nasazení aplikace Node.js využívající služby Azure Cosmos DB s projekty DevOps

Projekty Azure DevOps nabízí moderní prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI).

Projekty DevOps také:

* Vytvoření prostředků Azure automaticky, jako jsou služby Azure Cosmos DB, Application Insights, služby App Service a službu App Service.

* Vytvoří a nakonfiguruje kanál pro vydávání verzí pro CI/CD v Azure DevOps

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Projekty DevOps použít k nasazení aplikace Node.js využívající službu Azure Cosmos DB
> * Konfigurace Azure DevOps a předplatné Azure
> * Prozkoumat Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete jej získat bezplatná [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Nasazení aplikace v Node.js pomocí projekty DevOps

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty DevOps také vytvoří prostředků Azure, jako je Azure Cosmos DB, Application Insights, služby App Service a plán služby App Service v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte v části **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a potom klikněte na tlačítko **přidat**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Vyberte **Node.js** jako modul runtime a pak vyberte **Další**. V části **zvolte aplikační architekturu**vyberte **Express.js**.

1. Povolit v části **přidat databázi** pro **Cosmos DB** a klikněte na **Další**.

    ![Přidat databázi](_img/azure-devops-project-cosmos-db/add-database.png)

    Cosmos DB podporuje různých aplikačních architektur, jako je **Express.js**, **aplikace v Node.js ukázka**, a **Sail.js**. V tomto kurzu se umožní, vezměte v úvahu **Express.js**.

1. Vyberte službu Azure k nasazení aplikace. Máte různé služby jako webové aplikace Windows, služba Kubernetes a Web App for Containers. Pro účely tohoto kurzu budeme používat **webové aplikace Windows**. Klikněte na **Další**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Zadejte název pro váš projekt Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps, nebo vyberte existující organizace.

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další Azure nastavení konfigurace a zjištění cenovou úroveň a umístění, klikněte na další nastavení. V tomto podokně se zobrazí různé možnosti pro konfiguraci cenovou úroveň a umístění služby Azure.

1. Ukončete oblasti konfigurace Azure a pak vyberte **provádí**.

1. Po několika minutách po dokončení. Ukázkovou aplikaci v Node.js je nastavený v úložišti Git ve vaší organizaci Azure DevOps, Azure Cosmos DB, App Service, plán služby App Service a Application Insights se vytvoří, spuštění kanálu CI/CD a aplikace je nasazená do Azure.

   Po dokončení všech to řídicího panelu Projekt Azure DevOps se zobrazí na webu Azure Portal. Můžete také přejít na řídicí panel DevOps Projects přímo z **všechny prostředky** na webu Azure Portal.

   Tento řídicí panel poskytuje přehled do úložišti kódu Azure DevOps svůj kanál CI/CD a ve službě Azure Cosmos DB. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně vyberte **služby Azure Cosmos DB** zobrazíte.

## <a name="examine-the-azure-cosmos-db"></a>Prozkoumat Azure Cosmos DB

Projekty DevOps automaticky nakonfiguruje Cosmos DB, která vám umožní zkoumat a přizpůsobit. Abyste se seznámili se službou Cosmos DB, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Na pravé straně vyberte Cosmos DB. Otevře se podokno pro Cosmos DB. V tomto zobrazení můžete provádět různé akce, jako jsou operace sledování a prohledávání protokolů.

    ![Function App](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Projekty DevOps automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Seznamte se s ním, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

1. Klikněte na hypertextový odkaz v části **sestavení**. Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

    ![Sestavení](_img/azure-devops-project-cosmos-db/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení. Sestavení provádí různé úlohy, jako je načítání zdrojovým kódem z úložiště Git, sestavení aplikace, spouštění testů jednotek a publikování výstupy, které se používají pro nasazení.

1. Vyberte **Triggery**. Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení výstižněji a pak vyberte **Uložit** z **Uložit & frontu** rozevíracího seznamu.

1. Pod názvem kanálu buildu vyberte **Historie**. V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení, a umožňuje porovnání verzí.

## <a name="examine-the-cd-release-pipeline"></a>Prozkoumejte CD kanál pro vydávání verzí

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje CD do Azure. Další informace o kanál pro vydávání verzí, postupujte takto:

1. Přejděte **kanály | Verze**.

1. Klikněte na **upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt.

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**. Tento kanál pro vydávání verzí aktivoval aktivační událost CD, který provede nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění.

1. Na pravé straně vyberte odpovídající část **zobrazit verze** zobrazíte historii verzí.

1. Klikněte na verzi, čímž se zobrazí kanálu. Klikněte na jakémkoli prostředí ke kontrole vydání **souhrn, potvrzení**přidružené ke **pracovních položek**.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

> [!NOTE]
> Následující postup testuje kanálu CI/CD změnou prostého textu.

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na službě Azure App Service. Jednotlivé změny do úložiště Git v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Například můžete klonovat úložiště Git v oblíbených nástrojů nebo integrovaného vývojového prostředí a pak doručit změny do tohoto úložiště.

1. V nabídce Azure DevOps, vyberte **úložišť | Soubory**a potom přejděte do úložiště.

1. Úložiště již obsahuje kód založený na jazyce aplikace, kterou jste zvolili v procesu vytváření. Otevřít **Application/views/index.pug** souboru.

1. Vyberte **upravit**a pak proveďte změnu **číslo 15 řádku** . Například můžete aktualizovat tak **Moje první nasazení do Azure App Service s využitím Azure Cosmos DB**

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.

     Po chvíli se sestavení začíná v Azure DevOps a provede vydání k nasazení změny. Sledování stavu sestavení v řídícím panelu projekty DevOps, nebo v prohlížeči ve vaší organizaci Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Projekty DevOps použít k nasazení aplikace Node.js využívající službu Azure Cosmos DB
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumat Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků
