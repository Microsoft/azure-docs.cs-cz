---
title: Vytvoření kanálu CI/CD pro stávající kód pomocí služby Azure DevOps Project | Kurz VSTS
description: DevOps Project usnadňuje začátek práce v Azure. Pomůže vám v několika rychlých krocích použít vlastní kód a úložiště GitHub ke spuštění aplikace v libovolné službě Azure.
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
ms.openlocfilehash: 8c92b45cd3949e56515286c963b035e3c449835b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967347"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Vytvoření kanálu CI/CD pro stávající kód pomocí služby Azure DevOps Project

Azure DevOps Project představuje zjednodušené prostředí, kam můžete přenést váš stávající kód a úložiště Git a kde si můžete vybrat jednu z ukázkových aplikací pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps
> * Konfigurace přístupu k úložišti GitHub a výběr architektury
> * Konfigurace VSTS a předplatného Azure 
> * Potvrzení změn na GitHubu a automatické nasazení do Azure
> * Prozkoumání kanálu CI/CD VSTS
> * Konfigurace monitorování pomocí Azure Application Insights

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Přístup k úložišti GitHub nebo externímu úložišti Git obsahujícímu webový kód v .NET, Javě, PHP, Node.js nebo Pythonu nebo kód statického webu.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Project vytvoří kanál CI/CD ve VSTS.  Můžete vytvořit **nový účet VSTS** nebo použít **existující účet**.  Azure DevOps Project také vytvoří **prostředky Azure** v **předplatném Azure** podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu zvolte ikonu **+ Nový** a pak vyhledejte **Projekt DevOps**.  Zvolte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **Použít vlastní kód**.  Jakmile budete hotovi, zvolte **Další**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Konfigurace přístupu k úložišti GitHub a výběr architektury

1. Vyberte **GitHub**.  Volitelně můžete zvolit **externí úložiště Git**.  Zvolte vaše **úložiště** a **větev** obsahující vaši aplikaci.

1. Vyberte vaši **webovou architekturu**.  Jakmile budete hotovi, zvolte **Další**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. Architektura aplikace, kterou jste zvolili v předchozích krocích, určuje typ cíle nasazení služby Azure, který je zde k dispozici.  Vyberte libovolnou **cílovou službu**.  Jakmile budete hotovi, zvolte **Další**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurace VSTS a předplatného Azure 

1. Vytvořte **nový** účet VSTS nebo zvolte **existující** účet.  Zvolte **název** pro váš projekt VSTS.  Vyberte vaše **předplatné Azure**, **umístění** a zvolte **název** pro vaši aplikaci.  Jakmile budete hotovi, zvolte **Hotovo**.

    ![Zadání informací o VSTS](_img/azure-devops-project-github/vstsazureinfo.png)

1. Během několika minut se na webu Azure Portal načte **řídicí panel projektu**.  Ukázková aplikace se nastaví v úložišti ve vašem účtu VSTS, spustí se sestavení a vaše aplikace se nasadí do Azure.  Tento řídicí panel poskytuje přehled o vašem **úložišti kódu** na GitHubu, **kanálu CI/CD VSTS** a vaší **aplikaci v Azure**.  Na pravé straně řídicího panelu vyberte **Procházet** a zobrazte vaši spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Projekt Azure DevOps automaticky nakonfiguruje trigger CI pro sestavení a vydání.  Váš kód zůstane ve vašem úložišti GitHub nebo jiném externím úložišti.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na GitHubu a automatické nasazení do Azure 

Teď jste připraveni při práci na vaší aplikaci spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.  Každá změna v úložišti GitHub spustí sestavení ve VSTS a definice nástroje Release Management pro VSTS provede nasazení do Azure.

1.  Proveďte změnu vaší aplikace a **potvrďte** změnu ve vašem úložišti GitHub.
2.  Za chvíli se ve VSTS spustí sestavení.  Stav sestavení můžete monitorovat na řídicím panelu projektu DevOps nebo ve vašem účtu VSTS v prohlížeči.
3.  Po dokončení sestavení **aktualizujte aplikaci** v prohlížeči a ověřte, že se zobrazí provedené změny.

## <a name="examine-the-vsts-cicd-pipeline"></a>Prozkoumání kanálu CI/CD VSTS

Projekt Azure DevOps ve vašem účtu VSTS automaticky nakonfiguroval úplný kanál CI/CD VSTS.  Prozkoumejte kanál a podle potřeby ho upravte.  Postupujte podle následujících kroků a seznamte se s definicemi sestavení a verze VSTS.

1. V **horní** části řídicího panelu projektu Azure DevOps vyberte **Kanály sestavení**.  Tento odkaz na nové kartě prohlížeče otevře definici sestavení VSTS pro váš nový projekt.

1. Přesuňte kurzor myši napravo od definice sestavení vedle pole **Stav**. Vyberte **tři tečky**, které se zobrazí.  Tato akce otevře nabídku, ze které můžete spustit několik aktivit, jako je zařazení nového sestavení do fronty, pozastavení sestavení a úprava definice sestavení.

1. Vyberte **Upravit**.

1. V tomto zobrazení můžete **prozkoumat různé úlohy** pro vaši definici sestavení.  Sestavení provádí různé úlohy, jako je načtení zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části definice sestavení vyberte **název definice sestavení**.

1. Změňte **název** vaší definice sestavení na něco výstižnějšího.  Vyberte **Uložit a zařadit do fronty** a pak vyberte **Uložit**.

1. Pod názvem vaší definice sestavení vyberte **Historie**.  Zobrazí se protokol auditu nedávno provedených změn sestavení.  VSTS uchovává informace o všech změnách definice sestavení a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  Projekt Azure DevOps automaticky vytvořil trigger CI a každé potvrzení v úložišti spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  V závislosti na vašem scénáři můžete určit zásady pro zachování nebo odebrání určitého počtu sestavení.

1. Vyberte **Sestavení a vydání** a zvolte **Verze**.  Projekt Azure DevOps vytvořil definici verze VSTS pro správu nasazení do Azure.

1. Na levé straně prohlížeče vyberte **tři tečky** vedle vaší definice verze a pak zvolte **Upravit**.

1. Definice verze obsahuje **kanál**, který definuje proces vydání.  V části **Artefakty** vyberte **Zahodit**.  Definice sestavení, kterou jste zkoumali v předchozích krocích, vygeneruje výstup, který se použije pro artefakt. 

1. Napravo od ikony **Zahodit** vyberte **Trigger průběžného nasazování**.  Tato definice verze má povolený trigger CD, který spustí nasazení pokaždé, když bude k dispozici nový artefakt sestavení.  Volitelně můžete trigger zakázat, aby vaše nasazení vyžadovala ruční spuštění. 

1. Na levé straně prohlížeče vyberte **Úlohy**.  Úlohy jsou aktivity, které se provádí ve vašem procesu nasazení.  V tomto příkladu se vytvořila úloha pro nasazení do služby **Azure App Service**.

1. Na pravé straně prohlížeče vyberte **Zobrazit verze**.  Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte **tři tečky** vedle některé z vydaných verzí a zvolte **Otevřít**.  Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například souhrn verze, související pracovní položky a testy.

1. Vyberte **Potvrzení**.  Toto zobrazení ukazuje potvrzení kódu související s konkrétním nasazením. 

1. Vyberte **Protokoly**.  Protokoly obsahují užitečné informace o procesu nasazení.  Můžete je zobrazit během nasazení i po nich.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace.  Projekt Azure DevOps automaticky nakonfiguroval prostředek Application Insights pro vaši aplikaci.  Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. Na webu Azure Portal přejděte na řídicí panel **Azure DevOps Project**.  V pravé dolní části řídicího panelu zvolte odkaz **Application Insights** pro vaši aplikaci.

1. Na webu Azure Portal se otevře okno **Application Insights**.  Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **Časový rozsah** a pak zvolte **Poslední hodina**.  Výběrem možnosti **Aktualizovat** vyfiltrujte výsledky.  Teď se zobrazí všechny aktivity za posledních 60 minut.  Výběrem **x** zavřete časový rozsah.

1. Vyberte **Upozornění** a pak **+ Přidat upozornění na metriku**.  

1. Zadejte **Název** upozornění.

1. V rozevírací nabídce vyberte **Změna zdroje při**.  Zvolte váš **prostředek App Service**.

1. Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**.  Vyberte rozevírací nabídku **Metrika** a prozkoumejte různé metriky upozornění.  Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Zaškrtněte políčko u možnosti **Oznámit přes vlastníky, přispěvatele a čtenáře e-mailů**.  Volitelně můžete při aktivaci upozornění provádět další akce prostřednictvím spuštění aplikace logiky Azure.

1. Zvolte **Ok** a vytvořte upozornění.  Za chvíli se na řídicím panelu zobrazí aktivní upozornění.  **Opusťte** oblast Upozornění a vraťte se do **okna Application Insights**.

1. Vyberte **Dostupnost** a pak **+ Přidat test**. 

1. Zadejte **Název testu** a pak zvolte **Vytvořit**.  Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace.  Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete službu Azure App Service a související prostředky vytvořené v tomto rychlém startu odstranit pomocí funkce **Odstranit** na řídicím panelu Azure DevOps Project.

## <a name="next-steps"></a>Další kroky

Když jste v tomto kurzu nakonfigurovali proces CI/CD, ve vašem projektu VSTS se automaticky vytvořily definice sestavení a verze. Tyto definice sestavení a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps
> * Konfigurace přístupu k úložišti GitHub a výběr architektury
> * Konfigurace VSTS a předplatného Azure 
> * Potvrzení změn na GitHubu a automatické nasazení do Azure
> * Prozkoumání kanálu CI/CD VSTS
> * Konfigurace monitorování pomocí Azure Application Insights

Další informace o kanálu VSTS najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
