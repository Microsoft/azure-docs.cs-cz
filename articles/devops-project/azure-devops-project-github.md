---
title: 'Kurz: Vytvoření kanálu CI/CD pro váš stávající kód pomocí projektů Azure DevOps'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. DevOps Projects umožňuje použít vlastní kód a úložiště GitHub se vzorovými a spustit aplikaci ve službě Azure podle vašeho výběru v několika rychlých krocích.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555268"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Kurz: Vytvoření kanálu CI/CD pro váš stávající kód pomocí projektů Azure DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI).

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí projektů DevOps
> * Konfigurace přístupu k úložišti GitHub a zvolte architekturu.
> * Konfigurace Azure DevOps a předplatné Azure 
> * Potvrzení změn na Githubu a automaticky nasadit do Azure
> * Prozkoumejte kanálu Azure kanály CI/CD
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup ke Githubu nebo externí úložiště Git, který obsahuje .NET, Java, PHP, Node, Python nebo statický webový kód.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Projekty Azure DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty Azure DevOps také vytváří prostředky Azure v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **nový**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **přineste si vlastní kód**a pak vyberte **Další**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Konfigurace přístupu k úložišti GitHub a zvolte architekturu.

1. Vyberte buď **Githubu** nebo na externí úložiště Git a potom vyberte úložiště a větev, která obsahuje vaši aplikaci.

1. Vyberte vaše webové rozhraní a pak vyberte **Další**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    Rozhraní framework aplikace, který jste zvolili dříve, určí typ cíli pro nasazení služby Azure, který je zde k dispozici. 
    
1. Vyberte cílovou službu a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizace.

    a. Zadejte název pro váš projekt Azure DevOps. 
    
    b. Vyberte předplatné Azure a umístění, zadejte název pro vaši aplikaci a pak vyberte **provádí**.

    Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázková aplikace je nastavena v úložišti ve vaší organizaci Azure DevOps, sestavení je spuštěno a vaše aplikace bude nasazena do Azure. Tento řídicí panel poskytuje přehled o svém úložišti Githubu kódu, kanál CI/CD a vaši aplikaci v Azure. 
    
1. Vyberte **Procházet** k běžící aplikaci zobrazit.

    ![Zobrazení řídicího panelu DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Projekty Azure DevOps se automaticky nakonfiguruje položky konfigurace sestavení a vydaná verze triggeru. Váš kód zůstane ve svém úložišti Githubu nebo jiné externí úložiště. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Potvrzení změn na Githubu a automaticky nasadit do Azure 

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web. Jednotlivé změny do úložiště Githubu v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure.

1. Proveďte změnu aplikace a poté potvrďte změny do úložiště GitHub.  
    Po chvíli se spustí sestavení v kanálech Azure. Můžete monitorovat stav sestavení v řídicím panelu projekty DevOps, nebo ji můžete sledovat v prohlížeči ve vaší organizaci Azure DevOps.

1. Po dokončení sestavení, aktualizujte aplikaci ověřit změny.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Prozkoumejte kanálu Azure kanály CI/CD

Projekty Azure DevOps v kanálech Azure automaticky nakonfiguruje kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Seznamte se s kanály sestavení a vydaných verzí, postupujte takto:

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
    Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

1. Přejděte **stav** pole a pak vyberte tři tečky (...).  
    Nabídka obsahuje několik možností, jako je například přidávání nové sestavení, přerušení sestavení a úpravy kanálu sestavení do fronty.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úlohy, jako je načítání zdroje z Gitu výstupy úložiště, obnovení závislostí a publikování použít pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    Zobrazí se protokol auditu nedávno provedených změn sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
    Projekty Azure DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**.  
        V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty Azure DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání. 
    
1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál verze aktivoval trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.  
    Úkoly jsou aktivity, které spustí proces nasazení. V tomto příkladu byl úkol vytvořen k nasazení do služby Azure App service.

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. 

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. Projekty Azure DevOps se automaticky nakonfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects. 

1. V pravém dolním vyberte **Application Insights** odkaz pro vaši aplikaci.  
    **Application Insights** se otevře podokno. Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

    ![V podokně Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **časový rozsah**a pak vyberte **za poslední hodinu**. Chcete-li filtrovat výsledky, vyberte **aktualizace**.  
    Teď si můžete zobrazit všechny aktivity z posledních 60 minut. Chcete-li ukončit časový rozsah, vyberte **x**.

1. Vyberte **výstrahy**a pak vyberte **přidat upozornění metriky**. 

1. Zadejte název pro upozornění.

1. V **zdroj Alter na** rozevíracího seznamu vyberte vaše **prostředek služby App Service.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. V **metrika** rozevíracího seznamu, prozkoumejte různé metriky upozornění.  
    Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**. Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Vyberte **upozornění prostřednictvím e-mailu vlastníci, přispěvatelé a čtenáři** zaškrtávací políčko.  
    Volitelně můžete provádět další akce, při upozornění se zobrazí při spuštění aplikace logiky Azure.

1. Vyberte **OK** k vytvoření upozornění.  
    Po chvíli se upozornění se zobrazí jako aktivní na řídicím panelu.
    
1. Ukončení **výstrahy** oblasti a vraťte se zpět do **Application Insights** podokně.

1. Vyberte **dostupnosti**a pak vyberte **testu přidat**. 

1. Zadejte název testu a pak vyberte **vytvořit**.  
    Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace. Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit služby Azure App service a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup

Při konfiguraci procesu CI/CD v tomto kurzu kanál sestavení a vydané verzi se automaticky vytvořily v Azure DevOps Projects. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření kanálu CI/CD pomocí projektů DevOps
> * Konfigurace přístupu k úložišti GitHub a zvolte architekturu.
> * Konfigurace Azure DevOps a předplatné Azure 
> * Potvrzení změn na Githubu a automaticky nasadit do Azure
> * Prozkoumejte kanálu Azure kanály CI/CD
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o kanálu CI/CD, naleznete v tématu:

> [!div class="nextstepaction"]
> [Definování kanálu vícefázové průběžného nasazování (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
