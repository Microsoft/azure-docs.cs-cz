---
title: 'Kurz: Nasazení aplikace v ASP.NET do Azure Functions se službou Azure DevOps Projects'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. S projekty DevOps můžete nasadit aplikace ASP.NET do služby Azure Functions v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6894f9bc6c803e2692afb54d7459adf6b0e6dbd6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828089"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Průběžné nasazování do služby Azure Functions s projekty DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI).

Projekty DevOps také:

* Automaticky vytváří prostředky Azure, třeba Azure Functions

* Vytvoří a nakonfiguruje kanál pro vydávání verzí pro CI/CD v Azure DevOps

V tomto kurzu provedete následující:

> [!div class="checklist"]
>* Projekty DevOps použít k nasazení aplikace ASP.NET do funkce Azure functions
>* Konfigurace Azure DevOps a předplatné Azure
>* Prozkoumejte funkce Azure functions
>* Prozkoumání kanálu CI
>* Prozkoumání kanálu CD
>* Potvrzení změn v Gitu a automaticky nasadit do Azure
>* Vyčištění prostředků

V současné době jsou podporované moduly runtime pro funkce **.NET** a **Node.js**. Používáme. NET runtime pro účely tohoto kurzu k nasazení do služby Azure Functions. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete jej získat bezplatná [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Nasazení aplikace ASP.NET do služby Azure Functions pomocí projekty DevOps

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty DevOps také vytvoří prostředkům Azure, jako jsou IOT hub v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a potom klikněte na tlačítko **přidat**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Vyberte **.NET**a pak vyberte **Další**. V části **zvolte aplikační architekturu**vyberte **ASP.NET** a klikněte na tlačítko **Další**.

1. Vyberte **aplikace Function App** a pak vyberte **Další**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Zadejte název pro váš projekt Azure DevOps.

1. Vytvořte novou organizaci Azure DevOps, nebo vyberte existující organizace.

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další Azure nastavení konfigurace a zjištění cenovou úroveň a umístění, klikněte na další nastavení. V tomto podokně se zobrazí různé možnosti pro konfiguraci cenovou úroveň a umístění služby Azure.

1. Ukončete oblast Azure konfigurace a pak vyberte Hotovo.

1. Po několika minutách po dokončení. Ukázkovou aplikaci v ASP.NET je nastavený v úložišti Git ve vaší organizaci Azure DevOps, aplikace Function App a vytvoření služby Application Insights, spuštění kanálu CI/CD a aplikace je nasazená do Azure.

   Po dokončení všech to řídicího panelu Projekt Azure DevOps se zobrazí na webu Azure Portal. Můžete také přejít na řídicí panel DevOps Projects přímo z **všechny prostředky** na webu Azure Portal.

   Tento řídicí panel poskytuje vhled do vašeho úložiště kódu Azure DevOps, váš kanál CI/CD a vaši funkci Azure functions. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně vyberte **aplikace Function App** zobrazíte.

## <a name="examine-the-function-app"></a>Prozkoumejte aplikace Function App

Projekty DevOps automaticky nakonfiguruje aplikaci function app, což vám umožní zkoumat a přizpůsobit. Chcete-li získat znát aplikace function app, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Na pravé straně vyberte aplikaci funkcí. Pro aplikaci function app se otevře podokno. V tomto zobrazení můžete provádět různé akce, jako jsou operace sledování, prohledávání protokolů.

    ![Function App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Projekty DevOps automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Seznamte se s ním, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

1. Klikněte na hypertextový odkaz v části **sestavení**. Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

    ![Sestavení](_img/azure-devops-project-functions/build.png)

1. Vyberte **Upravit**. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení. Sestavení provádí různé úlohy, jako je načítání zdrojovým kódem z úložiště Git, sestavení aplikace, spouštění testů jednotek a publikování výstupy, které se používají pro nasazení.

1. Vyberte **Triggery**. Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení výstižněji a pak vyberte **Uložit** z **Uložit & frontu** rozevíracího seznamu.

1. Pod názvem kanálu buildu vyberte **Historie**. V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Azure DevOps sledovat všechny změny provedené kanálu sestavení, a to vám umožní porovnat verze.

## <a name="examine-the-cd-release-pipeline"></a>Prozkoumejte CD kanál pro vydávání verzí

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje CD do Azure. Další informace o kanál pro vydávání verzí, postupujte takto:

1. Přejděte **kanály | Verze**.

1. Klikněte na **upravit**.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt.

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**. Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění.

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Klikněte na verzi, čímž se zobrazí kanálu. Klikněte na jakémkoli prostředí ke kontrole vydání **souhrn, potvrzení**přidružené ke **pracovních položek**.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **zobrazit protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

> [!NOTE]
> Následující postup testuje kanálu CI/CD změnou prostého textu.

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práce pro vaši funkci Azure functions. Jednotlivé změny do úložiště Git v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Například můžete klonovat úložiště Git v oblíbených nástrojů nebo integrovaného vývojového prostředí a pak doručit změny do tohoto úložiště.

1. V nabídce Azure DevOps, vyberte **úložišť | Soubory**a potom přejděte do úložiště.

1. Úložiště již obsahuje kód volá **SampleFunctionApp** podle jazyka aplikace, kterou jste zvolili v procesu vytváření. Otevřít **Application/SampleFunctionApp/Function1.cs** souboru.

1. Vyberte **upravit**a pak proveďte změnu **31 číslo řádku** . Například můžete aktualizovat tak **Dobrý den! Vítá vás Azure Functions pomocí projekty DevOps**

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.

1. Otevřít **Application/SampleFunctionApp.Test/Function1TestRunner.cs** souboru. 

1. Vyberte **upravit**a pak proveďte změnu **číslo 21 řádku**. Například můžete aktualizovat tak **Dobrý den! Vítá vás Azure Functions pomocí projektů Azure DevOps**.

     Po chvíli se sestavení začíná v Azure DevOps a provede vydání k nasazení změny. Sledování stavu sestavení v řídícím panelu projekty DevOps, nebo v prohlížeči ve vaší organizaci Azure DevOps.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Projekty DevOps použít k nasazení aplikace ASP.NET Core do funkce Azure functions
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumejte funkce Azure functions
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků

