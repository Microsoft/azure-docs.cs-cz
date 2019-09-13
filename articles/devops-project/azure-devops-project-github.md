---
title: 'Kurz: Vytvoření kanálu CI/CD pro váš stávající kód pomocí Azure DevOps Projects'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. DevOps Projects vám pomůže používat vlastní kód a úložiště GitHub ke spuštění aplikace ve službě Azure podle vašeho výběru v několika rychlých krocích.
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
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898014"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Kurz: Vytvoření kanálu CI/CD pro váš stávající kód pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí DevOps Projects
> * Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.
> * Konfigurace Azure DevOps a předplatné Azure 
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Projděte si kanál Azure Pipelines CI/CD.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup k GitHubu nebo externímu úložišti Git, které obsahuje .NET, Java, PHP, Node, Python nebo statický webový kód.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Azure DevOps Projects taky vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **Nový**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte možnost **Přineste si vlastní kód**a pak vyberte **Další**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.

1. Vyberte buď **GitHub** , nebo externí úložiště Git, a pak vyberte úložiště a větev, která obsahuje vaši aplikaci.

1. Vyberte své webové rozhraní a pak vyberte **Další**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 
    
1. Vyberte cílovou službu a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci.

    a. Do Azure DevOps zadejte název vašeho projektu. 
    
    b. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte Hotovo.

    Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu GitHubu, kanálu CI/CD a vaší aplikaci v Azure. 
    
1. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects automaticky konfiguruje aktivační událost sestavení a vydání CI. Váš kód zůstane ve vašem úložišti GitHubu nebo jiném externím úložišti. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Potvrďte změny v GitHubu a automaticky je nasaďte do Azure. 

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Každá změna úložiště GitHub spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure.

1. Proveďte změnu aplikace a potvrďte změnu v úložišti GitHub.  
    Po chvíli začíná sestavení v Azure Pipelines. Stav sestavení můžete monitorovat na řídicím panelu DevOps Projects nebo ho můžete monitorovat v prohlížeči pomocí vaší organizace Azure DevOps.

1. Po dokončení sestavení aktualizujte svoji aplikaci, aby se ověřily vaše změny.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Projděte si kanál Azure Pipelines CI/CD.

Azure DevOps Projects automaticky konfiguruje kanál CI/CD v Azure Pipelines. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
    Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    Zobrazí se protokol auditu nedávno provedených změn sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
    Azure DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**.  
        V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
    Azure DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání. 
    
1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **rozevíracího** seznamu vyberte možnost **aktivační událost průběžného nasazování**.  
    Tento kanál verze aktivoval trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.  
    Úlohy jsou aktivity, které proces nasazení spustí. V tomto příkladu byl vytvořen úkol pro nasazení do služby Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. 

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. Azure DevOps Projects automaticky konfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. V Azure Portal přejdete na řídicí panel DevOps Projects. 

1. V pravém dolním rohu vyberte odkaz **Application Insights** pro vaši aplikaci.  
    Otevře se podokno **Application Insights** . Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

    ![Podokno Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **časový rozsah**a pak vyberte **poslední hodina**. Pokud chcete filtrovat výsledky, vyberte **aktualizovat**.  
    Nyní můžete zobrazit všechny aktivity za posledních 60 minut. Chcete-li ukončit časový rozsah, vyberte **x**.

1. Vyberte **výstrahy**a pak vyberte **Přidat výstrahu metriky**. 

1. Zadejte název výstrahy.

1. V rozevíracím seznamu **změny zdrojového kódu** vyberte **prostředek App Service.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. V rozevíracím seznamu **metrika** prověřte různé metriky výstrah.  
    Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**. Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Zaškrtněte políčko **oznamování vlastníkům, přispěvatelům a čtenářům e-mailem** .  
    Volitelně můžete provádět další akce, když se zobrazí výstraha spuštěním aplikace logiky Azure.

1. Vyberte **OK** a vytvořte výstrahu.  
    Po chvíli se výstraha zobrazí jako aktivní na řídicím panelu.
    
1. Ukončete oblast **výstrahy** a vraťte se do podokna **Application Insights** .

1. Vyberte možnost **dostupnost**a poté vyberte možnost **Přidat test**. 

1. Zadejte název testu a pak vyberte **vytvořit**.  
    Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace. Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit službu Azure App Service a související prostředky, které jste vytvořili v tomto kurzu. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD v tomto kurzu se automaticky vytvořil kanál sestavení a verze v Azure DevOps Projects. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí DevOps Projects
> * Nakonfigurujte přístup k úložišti GitHub a vyberte rozhraní.
> * Konfigurace Azure DevOps a předplatné Azure 
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Projděte si kanál Azure Pipelines CI/CD.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
