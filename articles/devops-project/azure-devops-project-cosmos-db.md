---
title: 'Kurz: Nasazení aplikací Node.js využívajících Azure Cosmos DB s projekty Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. S DevOps Projects můžete v několika rychlých krocích nasadit aplikaci Node.js, která je založená na Azure Cosmos DB, do Windows Web Appu.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888892"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Nasazení aplikací Node.js využívajících Azure Cosmos DB s devops projekty

Azure DevOps Projects nabízí zjednodušené prostředí, kde můžete vytvořit kanál průběžné integrace (CI) a průběžného nasazení (CD) do Azure. To lze provést pomocí existujícího kódu a úložiště Git (úložiště) nebo výběrem ukázkové aplikace.

DevOps Projekty také:

* Automaticky vytváří prostředky Azure, jako jsou Azure Cosmos DB, Azure Application Insights, Azure App Service a plány appservice

* Vytvoří a nakonfiguruje kanál vydání CI/CD v Azure DevOps

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace Node.js využívající Azure Cosmos DB pomocí devops Projects
> * Konfigurace Azure DevOps a předplatného Azure
> * Prozkoumejte Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

Potřebujete předplatné Azure, které můžete získat prostřednictvím [Visual Studia Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) zdarma.

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Nasazení aplikace Node.js pomocí projektů DevOps

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projekty také vytvoří prostředky Azure, jako je Azure Cosmos DB, Application Insights, App Service a app service plány, v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Přidat**.

   ![Podokno Projekty DevOps](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Jako runtime vyberte **Node.js** a pak vyberte **Další**. V části **Zvolit architekturu aplikace**vyberte **Express.js**.

1. Povolte oddíl **Přidat databázi** pro **Cosmos DB**a pak vyberte **Další**.

    ![Přidání databáze](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Projects podporuje různé architektury aplikací, jako je **express.js**, **ukázková aplikace Node.js**a **Sail.js**. V tomto kurzu používáme **Express.js**.

1. Vyberte službu Azure, kterou chcete nasadit, a pak vyberte **Další**. Mezi možnosti patří Windows Web App, Služba Azure Kubernetes a Azure Web App pro kontejnery. V tomto kurzu používáme **Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace předplatného Azure DevOps a Azure

1. Zadejte název projektu Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci.

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další nastavení konfigurace Azure nebo určit cenovou úroveň a umístění, vyberte **další nastavení**. Toto podokno zobrazuje různé možnosti konfigurace cenové úrovně a umístění služeb Azure.

1. Ukončete konfigurační oblast Azure a pak vyberte **Hotovo**.

1. Proces skončí po několika minutách. Ukázková aplikace Node.js se nastavuje v úložišti Git ve vaší organizaci Azure DevOps. Pak se vytvoří prostředky Azure Cosmos DB, App Service, App Service a Application Insights, stejně jako kanál CI/CD. Vaše aplikace se pak nasadí do Azure.

   Po dokončení všech těchto procesů se řídicí panel projektu Azure DevOps zobrazí na webu Azure Portal. Můžete taky přejít na řídicí panel DevOps Projects přímo ze **všech prostředků** na webu Azure Portal.

   Tento řídicí panel poskytuje přehled o úložišti kódu Azure DevOps, kanálu CI/CD a databázi Azure Cosmos DB. Další možnosti CI/CD můžete nakonfigurovat v kanálu Azure DevOps. Na pravé straně řídicího panelu vyberte **Azure Cosmos DB** pro zobrazení těchto možností.

## <a name="examine-azure-cosmos-db"></a>Prozkoumejte Azure Cosmos DB

DevOps Projects automaticky konfiguruje Azure Cosmos DB, který můžete prozkoumat a přizpůsobit. Pokud se chcete seznámit s Azure Cosmos DB, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Vpravo vyberte Azure Cosmos DB. Otevře se podokno pro Azure Cosmos DB. Z tohoto zobrazení můžete provádět různé akce, jako je například operace monitorování a prohledávání protokolů.

    ![Podokno Db Azure Cosmos](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Chcete-li se s ním seznámit, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

1. Vyberte hypertextový odkaz v části **Sestavení**. Karta prohlížeče zobrazuje kanál sestavení pro váš nový projekt.

    ![Podokno sestavení](_img/azure-devops-project-cosmos-db/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení. Sestavení provádí různé úlohy, jako je například načítání zdrojového kódu z úložiště Git, vytváření aplikací, spuštění testů částí a publikování výstupů, které se používají pro nasazení.

1. Vyberte **možnost Aktivační události**. DevOps Projekty automaticky vytvoří aktivační událost CI a každé potvrzení k repo spustí nové sestavení. Můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**. V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího a pak v rozevíracím seznamu **Uložit & fronty** vyberte **Uložit.**

1. Pod názvem kanálu buildu vyberte **Historie**. Toto podokno zobrazuje záznam auditu posledních změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

## <a name="examine-the-cd-release-pipeline"></a>Zkontrolujte kanál vydání disku CD-ROM

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál vydání, který poskytuje CD do Azure. Další informace o kanálu vydání, postupujte takto:

1. Přejděte na **Kanály** a vyberte **vydání**.

1. Vyberte **Upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt.

1. Napravo od ikony **Přetažení** vyberte **Aktivační událost nepřetržitého nasazení**. Tento kanál vydání povolil aktivační událost průběžného nasazení, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Aktivační událost můžete zakázat, aby se nasazení spouštěla ručně.

1. Vpravo vyberte část **Zobrazit vydání,** chcete-li zobrazit historii vydání.

1. Vyberte verzi, která zobrazí kanál. Vyberte libovolné prostředí pro kontrolu souhrnu vydání, potvrzení nebo přidružených pracovních položek.

1. Vyberte **Potvrzení**. Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **možnost Zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Potvrzení změn kódu a spuštění kanálu CI/CD

> [!NOTE]
> Následující postup testuje kanál CI/CD provedením jednoduché změny textu.

Teď jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který nasazuje vaši nejnovější práci do služby App Service. Každá změna úložiště Git spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v repo. Můžete například klonovat úložiště Git ve vašem oblíbeném nástroji nebo rozhraní IDE a potom vysunout změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte **Repos** a potom **Soubory**. Tak jdi do svého repo.

1. Repo již obsahuje kód založený na jazyce aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **Application/views/index.pug.**

1. Vyberte **Upravit**a proveďte změnu **řádku číslo 15**. Můžete ji například změnit na "Moje první nasazení ve službě Azure App Service využívající Azure Cosmos DB.".

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.

     Po několika sekundách se sestavení spustí v Azure DevOps a verze se spustí k nasazení změn. Sledujte stav sestavení na řídicím panelu DevOps Projects nebo v prohlížeči pomocí organizace Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte související prostředky, které jste vytvořili, když už je nepotřebujete. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace Node.js využívající Azure Cosmos DB pomocí devops Projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumejte Azure Cosmos DB
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

Další informace a další kroky najdete [v tématu Definování vícestupňového kanálu průběžného nasazení (CD).](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts)


