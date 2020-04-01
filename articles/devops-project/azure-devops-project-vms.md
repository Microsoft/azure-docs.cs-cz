---
title: 'Kurz: Nasazení aplikace ASP.NET do virtuálních počítačů Azure pomocí projektů Azure DevOps'
description: DevOps Projects usnadňuje zahájení v Azure a nasazení ASP.NET aplikace do virtuálních počítačů Azure v několika rychlých krocích.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969560"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace ASP.NET do virtuálních počítačů Azure pomocí projektů Azure DevOps

Azure DevOps Projects představuje zjednodušené prostředí, kde můžete přenést existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projekty také:
* Automaticky vytváří prostředky Azure, jako je například nový virtuální počítač Azure (VM).
* Vytvoří a nakonfiguruje kanál vydání v Azure DevOps, který zahrnuje kanál sestavení pro CI.
* Nastaví kanál vydání pro disk CD. 
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET pomocí devops projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Nasazení aplikace ASP.NET pomocí devops projects

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure, jako jsou virtuální počítače v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Nový**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Vytvořit**.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **možnost .NET**a pak vyberte **Další**.

1. V části **Choose a application Framework**vyberte **ASP.NET**a pak vyberte **Další**.  
    Rozhraní aplikace, které jste zvolili v předchozím kroku, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

1. Vyberte virtuální počítač a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte předplacené služby Azure.  
    Volitelně můžete vybrat **změnit** a zadejte další podrobnosti konfigurace, jako je například umístění prostředků Azure.
 
1. Zadejte název virtuálního počítače, uživatelské jméno a heslo pro nový prostředek virtuálního počítače Azure a pak vyberte **Hotovo**.  
    Po několika minutách bude virtuální počítač Azure připravený. Ukázková ASP.NET aplikace se nastaví v repo v organizaci Azure DevOps, spustí se sestavení a vydání a vaše aplikace se nasadí do nově vytvořeného virtuálního počítače Azure. 

    Po dokončení se na webu Azure Portal zobrazí řídicí panel DevOps Projects. Na řídicí panel můžete taky přejít přímo ze **všech prostředků** na webu Azure Portal. 

    Řídicí panel poskytuje přehled o vašem repo kódu Azure DevOps, kanálu CI/CD a spuštěné aplikaci v Azure.   

    ![Zobrazení řídicího panelu](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects automaticky konfiguruje ci sestavení a aktivační událost vydání, která nasazuje změny kódu do vašeho repo. V Azure DevOps můžete nakonfigurovat další možnosti. Chcete-li zobrazit spuštěnou aplikaci, vyberte **procházet**.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI
 
DevOps Projekty automaticky nakonfiguroval kanál CI/CD v Azure Pipelines. Kanál můžete prozkoumat a upravit. Chcete-li se seznámit s kanálem sestavení, postupujte takto:

1. V horní části řídicího panelu Projekty DevOps vyberte **Sestavit kanály**.  
    Karta prohlížeče zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **Stav** a vyberte tři tečky (...).  
    Nabídka zobrazuje několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího, vyberte **Uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    Toto podokno zobrazuje záznam auditu posledních změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**.  
    DevOps Projekty automaticky vytvoří aktivační událost CI a každé potvrzení k repo spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál CD, který poskytuje CD do virtuálního počítače Azure. Další informace o kanálu disku CD Azure DevOps postupujte takto:

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
    DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**.  
    Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

1. Vlevo vyberte **Úkoly**a pak vyberte prostředí.  
    Úkoly jsou aktivity, které proces nasazení provádí a jsou seskupeny ve fázích. K tomuto kanálu vydání dochází ve dvou fázích:
    * První fáze obsahuje úlohu nasazení skupiny prostředků Azure, která provádí dvě věci:
      * Konfiguruje virtuální počítače pro nasazení
      * Přidá nový virtuální počítač do skupiny nasazení Azure DevOps. Skupina nasazení virtuálních počítačů v Azure DevOps spravuje logické skupiny počítačů cílů nasazení.
    * Ve druhé fázi vytvoří úloha správy webové aplikace služby IIS web služby IIS na virtuálním počítači. K nasazení webu se vytvoří druhá úloha nasazení webové aplikace služby IIS.

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Vyberte tři tečky (...) vedle verze a pak vyberte **Otevřít**.  
    Můžete prozkoumat několik nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure 

Nyní jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který automaticky nasazuje vaši nejnovější práci na váš web. Každá změna úložiště Git spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v repo. Změny kódu zahájí proces CI/CD a automaticky nasadí vaše změny na web služby IIS na virtuálním počítači Azure.

1. V levém podokně vyberte **Kód**a přejděte do repo.

1. Přejděte do adresáře *Zobrazení\Domů,* vyberte tři tečky (...) vedle souboru *Index.cshtml* a vyberte **Upravit**.

1. Proveďte změnu souboru, například přidání textu do jedné z značek div. 

1. Vpravo nahoře vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.  
    Po několika okamžicích se sestavení spustí v Azure DevOps a verze se spustí k nasazení změn. Sledujte stav sestavení na řídicím panelu DevOps Projects nebo v prohlížeči pomocí organizace Azure DevOps.

1. Po dokončení vydání aktualizujte aplikaci a ověřte změny.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. DevOps Projects automaticky konfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. Na webu Azure Portal přejděte na řídicí panel Projekty DevOps. 

1. Vpravo dole vyberte odkaz **Přehledy aplikací** pro vaši aplikaci.  
    Otevře se podokno **Přehledy aplikací.** Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

    ![Podokno Přehledy aplikací](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **Časový rozsah**a pak vyberte **Poslední hodina**. Chcete-li výsledky filtrovat, vyberte **možnost Aktualizovat**.  
    Nyní můžete zobrazit všechny aktivity za posledních 60 minut. 
    
1. Chcete-li časový rozsah ukončit, vyberte **x**.

1. Vyberte **Výstrahy**a pak **vyberte Přidat upozornění na metriku**. 

1. Zadejte název výstrahy.

1. V rozevíracím seznamu **Metrika** zkontrolujte různé metriky výstrah.  
    Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**. Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Zaškrtněte políčko **Upozornit prostřednictvím vlastníků e-mailů, přispěvatelů a čtenářů.**  
    Volitelně můžete provést další akce při zobrazení výstrahy spuštěním aplikace logiky Azure.

1. Chcete-li výstrahu vytvořit, vyberte **ok.**  
    Po chvíli se výstraha na řídicím panelu zobrazí jako aktivní. 

1. Ukončete oblast **Výstrahy** a vraťte se do podokna **Přehledy aplikací.**

1. Vyberte **Dostupnost**a pak vyberte **Přidat test**. 

1. Zadejte název testu a pak vyberte **Vytvořit**.  
    Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace. Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání fakturačních poplatků vyčištěním prostředků. Když už nejsou potřeba, můžete odstranit virtuální počítač Azure a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte funkci **Odstranit** na řídicím panelu projektu DevOps. 

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Delete *Delete* funkce zničí data, která je vytvořena projektu v devops projekty v Azure a Azure DevOps a nebude možné načíst. Tento postup použijte až poté, co si pečlivě přečtete výzvy.

1. Na webu Azure Portal přejděte na řídicí panel Projekty DevOps.
1. Vpravo nahoře vyberte **Odstranit**. 
1. V řádku vyberte **Ano,** *chcete-li* prostředky trvale odstranit.

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET pomocí devops projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Definování vícestupňového kanálu průběžného nasazení (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
