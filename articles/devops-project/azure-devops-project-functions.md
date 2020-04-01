---
title: 'Kurz: Nasazení ASP.NET aplikací do funkcí Azure pomocí projektů Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. S DevOps Projects můžete nasadit svou ASP.NET aplikaci do Azure Functions v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971563"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Průběžné nasazování do funkcí Azure pomocí projektů DevOps

Azure DevOps Projects představuje zjednodušené prostředí, kde můžete přenést existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.

DevOps Projekty také:

* Automaticky vytváří prostředky Azure, jako jsou funkce Azure

* Vytvoří a nakonfiguruje kanál vydání v Azure DevOps pro CI/CD

V tomto kurzu provedete následující:

> [!div class="checklist"]
>* Nasazení ASP.NET aplikace do funkce Azure pomocí devops projects
>* Konfigurace Azure DevOps a předplatného Azure
>* Zkontrolujte funkci Azure
>* Prozkoumání kanálu CI
>* Prozkoumání kanálu CD
>* Potvrzení změn v Gitu a jejich automatické nasazení do Azure
>* Vyčištění prostředků

V současné době jsou podporované moduly runtimes pro funkce **.NET** a **Node.js**. Používáme. NET runtime pro tento kurz k nasazení do funkce Azure. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete získat jeden zdarma prostřednictvím [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Nasazení aplikace ASP.NET do funkcí Azure pomocí devops projects

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projekty také vytvoří prostředky Azure, jako je například IoTHub, v předplatném Azure dle vašeho výběru.

1. Přihlášení k [portálu Azure](https://portal.azure.com)

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **Příkaz DevOps Projects**a klepněte na tlačítko **Přidat**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Vyberte **možnost .NET**a pak vyberte **Další**. V části **Zvolit architekturu aplikace**vyberte **ASP.NET** a klepněte na tlačítko **Další**.

1. Vyberte **Aplikace funkce** a pak vyberte **Další**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace předplatného Azure DevOps a Azure

1. Zadejte název projektu Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci.

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další nastavení konfigurace Azure a identifikovat cenovou úroveň a umístění, klikněte na další nastavení. Toto podokno zobrazuje různé možnosti konfigurace cenové úrovně a umístění služeb Azure.

1. Ukončete konfigurační oblast Azure a pak vyberte Hotovo.

1. Po několika minutách je proces dokončen. Ukázková ASP.NET aplikace se nastaví v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se aplikace funkcí a přehledy aplikací, spustí se kanál CI/CD a vaše aplikace se nasadí do Azure.

   Po dokončení se na webu Azure Portal zobrazí řídicí panel Projektu Azure DevOps. Můžete taky přejít na řídicí panel DevOps Projects přímo ze **všech prostředků** na webu Azure Portal.

   Tento řídicí panel poskytuje přehled o úložišti kódu Azure DevOps, kanálu CI/CD a funkci Azure. Další možnosti CI/CD můžete nakonfigurovat v kanálu Azure DevOps. Vpravo vyberte **funkci aplikace,** kterou chcete zobrazit.

## <a name="examine-the-function-app"></a>Zkontrolujte aplikaci funkce

DevOps Projects automaticky konfiguruje aplikaci funkcí, kterou můžete prozkoumat a přizpůsobit. Chcete-li aplikaci funkce poznat, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

    ![Řídicí panel Projektů DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Vpravo vyberte aplikaci funkcí. Otevře se podokno pro aplikaci funkce. Z tohoto zobrazení můžete provádět různé akce, jako je například monitorování operací, vyhledávání protokolů.

    ![Function App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Chcete-li se s ním seznámit, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

1. Klikněte na hypertextový odkaz v části **Sestavení**. Karta prohlížeče zobrazuje kanál sestavení pro váš nový projekt.

    ![Sestavení](_img/azure-devops-project-functions/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení. Sestavení provádí různé úlohy, jako je například načítání zdrojového kódu z úložiště Git, vytváření aplikací, spuštění testů částí a publikování výstupů, které se používají pro nasazení.

1. Vyberte **možnost Aktivační události**. DevOps Projekty automaticky vytvoří aktivační událost CI a každé potvrzení k repo spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**. V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího a pak v rozevíracím seznamu **Uložit & fronty** vyberte **Uložit.**

1. Pod názvem kanálu buildu vyberte **Historie**. Toto podokno zobrazuje záznam auditu posledních změn pro sestavení. Azure DevOps sledovat všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

## <a name="examine-the-cd-release-pipeline"></a>Zkontrolujte kanál vydání disku CD-ROM

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál vydání, který poskytuje CD do Azure. Další informace o kanálu vydání, postupujte takto:

1. Přechod na **kanály | Zprávy**.

1. Klikněte na **Upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt.

1. Na pravé straně ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**. Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění.

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Klikněte na vydání, které zobrazí potrubí. Kliknutím na libovolné prostředí zkontrolujte **souhrn vydání, potvrzení**, přidružené **pracovní položky**.

1. Vyberte **Potvrzení**. Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **možnost Zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

> [!NOTE]
> Následující postup testuje kanál CI/CD provedením jednoduché změny textu.

Teď jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který automaticky nasazuje vaši nejnovější práci do funkce Azure. Každá změna úložiště Git spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v repo. Můžete například klonovat úložiště Git ve vašem oblíbeném nástroji nebo rozhraní IDE a potom vysunout změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte **Repos | soubory**a potom přejděte do repo.

1. Úložiště již obsahuje kód s názvem **SampleFunctionApp** na základě jazyka aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **Application/SampleFunctionApp/Function1.cs.**

1. Vyberte **Upravit**a proveďte změnu **řádku číslo 31** . Například, můžete jej aktualizovat na **Dobrý den, tam! Vítá vás funkce Azure pomocí projektů DevOps**

1. Vpravo nahoře vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.

1. Otevřete soubor **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. Vyberte **Upravit**a proveďte změnu **řádku číslo 21**. Například, můžete jej aktualizovat na **Dobrý den, tam! Vítejte na Azure Funkce pomocí Azure DevOps projekty**.

     Po několika okamžicích se sestavení spustí v Azure DevOps a verze se spustí k nasazení změn. Sledujte stav sestavení na řídicím panelu DevOps Projects nebo v prohlížeči pomocí organizace Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, můžete odstranit, když je již nepotřebujete. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET Core do funkce Azure pomocí devops projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Zkontrolujte funkci Azure
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

