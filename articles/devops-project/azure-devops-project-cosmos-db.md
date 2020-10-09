---
title: 'Kurz: nasazení aplikací Node.js využívajících Azure Cosmos DB s Azure DevOps Starter'
description: Azure DevOps Starter usnadňuje začátek práce v Azure. S DevOps Starter můžete nasadit aplikaci Node.js, která se používá Azure Cosmos DB do webové aplikace Windows v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: de98d9db515e4ed88759efa7f22dddcca06151c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854677"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Nasazení aplikací Node.js využívajících Azure Cosmos DB s DevOps Starter

Azure DevOps Starter nabízí zjednodušené prostředí, ve kterém můžete vytvořit kanál průběžné integrace (CI) a průběžného nasazování (CD) do Azure. Můžete to provést pomocí stávajícího kódu a úložiště Git (úložiště) nebo výběrem ukázkové aplikace.

DevOps Starter také:

* Automaticky vytvoří prostředky Azure, jako jsou Azure Cosmos DB, Azure Application Insights, Azure App Service a plány App Service.

* Vytvoří a nakonfiguruje kanál verze CI/CD ve službě Azure DevOps.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Použití DevOps Starter k nasazení aplikace Node.js s využitím Azure Cosmos DB
> * Konfigurace Azure DevOps a předplatného Azure
> * Kontrola Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

Potřebujete předplatné Azure, které můžete získat zdarma prostřednictvím [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) .

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Použití DevOps Starter k nasazení aplikace Node.js

DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Starter také vytvoří prostředky Azure, jako jsou Azure Cosmos DB, Application Insights, App Service a plány App Service, v předplatném Azure dle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter**a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Jako modul runtime vyberte **Node.js** a pak vyberte **Další**. V části **zvolte Aplikační architekturu**vyberte **Express.js**.

1. Povolte část **Přidat databázi** pro **Cosmos DB**a pak vyberte **Další**.

    ![Přidání databáze](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter podporuje různé aplikační architektury, jako jsou **Express.js**, **ukázkové Node.js aplikace**a **Sail.js**. V tomto kurzu používáme **Express.js**.

1. Vyberte službu Azure pro nasazení aplikace a pak vyberte **Další**. Mezi tyto možnosti patří webová aplikace Windows, služba Azure Kubernetes a Azure Web App for Containers. V tomto kurzu používáme **webovou aplikaci Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace předplatného Azure DevOps a Azure

1. Zadejte název projektu Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci.

1. Vyberte své předplatné Azure.

1. Pokud chcete zobrazit další nastavení konfigurace Azure nebo určit cenovou úroveň a umístění, vyberte **Další nastavení**. V tomto podokně se zobrazují různé možnosti konfigurace cenové úrovně a umístění služeb Azure.

1. Ukončete oblast konfigurace Azure a potom vyberte **Hotovo**.

1. Proces se dokončí po několika minutách. Ukázková Node.js aplikace se nastavuje v úložišti Git ve vaší organizaci Azure DevOps. Pak se vytvoří Azure Cosmos DB, App Service, App Service plán a prostředky Application Insights a také kanál CI/CD. Vaše aplikace se pak nasadí do Azure.

   Po dokončení všech těchto procesů se v Azure Portal zobrazí řídicí panel Azure DevOps Starter. Můžete také přejít na řídicí panel DevOps Starter přímo ze **všech prostředků** v Azure Portal.

   Tento řídicí panel poskytuje přehled o vašem úložišti kódu Azure DevOps, kanálu CI/CD a vaší Azure Cosmos DB databázi. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně řídicího panelu vyberte **Azure Cosmos DB** pro zobrazení těchto možností.

## <a name="examine-azure-cosmos-db"></a>Kontrola Azure Cosmos DB

DevOps Starter automaticky nakonfiguruje Azure Cosmos DB, které můžete prozkoumat a přizpůsobit. Pokud se chcete seznámit s Azure Cosmos DB, udělejte toto:

1. Přejít na řídicí panel DevOps Starter.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Na pravé straně vyberte Azure Cosmos DB. Otevře se podokno pro Azure Cosmos DB. Z tohoto zobrazení můžete provádět různé akce, jako jsou operace monitorování a hledání protokolů.

    ![Azure Cosmos DB podokno](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Starter automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Pokud se k němu chcete seznámit, udělejte toto:

1. Přejít na řídicí panel DevOps Starter.

1. Vyberte hypertextový odkaz v části **sestavení**. Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

    ![Podokno sestavení](_img/azure-devops-project-cosmos-db/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je například načítání zdrojového kódu z úložiště Git, sestavování aplikace, spuštění testů jednotek a publikování výstupů, které se používají pro nasazení.

1. Vyberte **triggery**. DevOps Starter automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Můžete vybrat zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název vašeho kanálu sestavení na výstižnější a pak v rozevíracím seznamu **uložit & fronty** vyberte **Uložit** .

1. Pod názvem kanálu buildu vyberte **Historie**. V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

## <a name="examine-the-cd-release-pipeline"></a>Kontrola kanálu pro vydání CD

DevOps Starter automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje disk CD do Azure. Pokud se chcete dozvědět víc o kanálu vydávání, udělejte toto:

1. Přejít na **kanály** a vybrat **vydané verze**.

1. Vyberte **Upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt.

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**. Tento kanál vydaných verzí povolil Trigger průběžného nasazování, který provádí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Trigger můžete zakázat, aby se vaše nasazení provádělo ručně.

1. Na pravé straně vyberte části **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte verzi, ve které se bude kanál zobrazovat. Vyberte libovolné prostředí pro kontrolu souhrnu vydaných verzí, potvrzení změn nebo přidružených pracovních položek.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Potvrzení změn kódu a spuštění kanálu CI/CD

> [!NOTE]
> Následující postup testuje kanál CI/CD tím, že provede jednoduchou změnu textu.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který nasadí vaši poslední práci do vašeho App Service. Každá změna úložiště Git spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použijte jinou techniku, abyste potvrdili změny v úložišti. Úložiště Git můžete například klonovat v oblíbených nástrojích nebo v integrovaném vývojovém prostředí (IDE) a pak doručovat změny do tohoto úložiště.

1. V nabídce Azure **DevOps vyberte úložiště** a pak **soubory**. Pak přejdete do svého úložiště.

1. Úložiště již obsahuje kód na základě jazyka aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **Application/views/index. pug** .

1. Vyberte **Upravit**a pak proveďte změnu na **řádek číslo 15**. Můžete ho například změnit na "Moje první nasazení, které má Azure App Service využívá Azure Cosmos DB."

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte.

     Po několika sekundách se Build spustí v Azure DevOps a k nasazení změn se spustí vydání. Monitorujte stav buildu na řídicím panelu Starter DevOps nebo v prohlížeči pomocí vaší organizace Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte související prostředky, které jste vytvořili, když už je nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití DevOps Starter k nasazení aplikace Node.js s využitím Azure Cosmos DB
> * Konfigurace Azure DevOps a předplatného Azure 
> * Kontrola Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

Další informace a další kroky najdete v tématu [definice kanálu pro průběžné nasazování (CD) s více fázemi](/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) .