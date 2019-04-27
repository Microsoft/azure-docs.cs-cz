---
title: 'Kurz: Nasazení aplikace v ASP.NET do virtuálních počítačů Azure pomocí projektů Azure DevOps'
description: DevOps Projects umožňuje snadno začít používat Azure a nasazení aplikace v ASP.NET do virtuálních počítačů Azure v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 05643f342d51d99645d3c9204d6e63adcf2a0a73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546447"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace v ASP.NET do virtuálních počítačů Azure pomocí projektů Azure DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI). 

Projekty DevOps také:
* Automaticky vytváří prostředky Azure, jako je například nový Azure virtuální počítač (VM).
* Vytvoří a nakonfiguruje kanál pro vydávání verzí v Azure DevOps, který obsahuje sestavení kanálu pro nepřetržitou Integraci.
* Nastaví kanál pro vydávání verzí pro CD. 
* Vytvoří prostředek služby Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace v ASP.NET pomocí projekty DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Zapsat změny do úložiště Azure a automaticky nasadit do Azure
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Nasazení aplikace v ASP.NET pomocí projekty DevOps

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty DevOps také vytváří prostředky Azure, jako jsou virtuální počítače v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **nový**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte aplikaci Framework**vyberte **ASP.NET**a pak vyberte **Další**.  
    Aplikační platformu, kterou jste zvolili v předchozím kroku, určí typ cíli pro nasazení služby Azure, který je zde k dispozici. 

1. Vyberte virtuální počítač a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizace. 

1. Zadejte název pro váš projekt Azure DevOps. 

1. Vyberte služby vašeho předplatného Azure.  
    Volitelně můžete vybrat **změnu** a pak zadejte další podrobnosti konfigurace, jako je například umístění prostředků Azure.
 
1. Zadejte název virtuálního počítače, uživatelské jméno a heslo pro nový prostředek virtuálního počítače Azure a pak vyberte **provádí**.  
    Po několika minutách bude virtuální počítač Azure připraven. Ukázkovou aplikaci technologie ASP.NET je nastavený v úložišti ve vaší organizaci Azure DevOps, sestavení a vydání je proveden a vaše aplikace bude nasazena k nově vytvořenému virtuálnímu počítači Azure. 

    Po dokončení, řídicího panelu DevOps Projects se zobrazí na webu Azure Portal. Můžete také přejít na řídicí panel z **všechny prostředky** na webu Azure Portal. 

    Řídicí panel poskytuje přehled o úložišti kódu Azure DevOps, váš kanál CI/CD a aplikace běžící v Azure.   

    ![Zobrazení řídicího panelu](_img/azure-devops-project-vms/dashboardnopreview.png)

Projekty DevOps automaticky nakonfiguruje sestavení CI a verze triggeru, který se nasazuje kód změn v úložišti. V Azure DevOps můžete nakonfigurovat další možnosti. Pokud chcete zobrazit spuštěnou aplikaci, vyberte **Procházet**.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI
 
Projekty DevOps automaticky nakonfiguruje kanál CI/CD v kanálech Azure. Kanál můžete prozkoumat a upravit. Seznamte se s kanálem sestavení, postupujte takto:

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
    V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
    Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál CD, který poskytuje CD na virtuálním počítači Azure. Další informace o kanálu Azure DevOps CD, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**a potom vyberte vaše prostředí.  
    Úkoly jsou aktivity, které spustí proces nasazení, a jsou seskupeny ve fázích. Tento kanál pro vydávání verzí dojde ve dvou fázích:
    * První fáze obsahuje úlohu nasazení skupiny prostředků Azure, který provede dvě věci:
      * Nakonfiguruje virtuální počítač pro nasazení
      * Přidá nový virtuální počítač do skupiny nasazení služby Azure DevOps. Skupina nasazení virtuálního počítače v Azure DevOps spravuje logické skupiny nasazení cílových počítačích.
    * Ve druhé fázi vytvoří úlohu spravovat aplikace webové služby IIS webu služby IIS na virtuálním počítači. Nasazení webu se vytvoří druhý úkol nasazení aplikace Web služby IIS.

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete si projít několik nabídek, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zapsat změny do úložiště Azure a automaticky nasadit do Azure 

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web. Jednotlivé změny do úložiště Git v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Změny kódu k zahájení procesu CI/CD a automaticky vaše změny nasadí na web služby IIS na virtuálním počítači Azure.

1. V levém podokně vyberte **kód**a potom přejděte do úložiště.

1. Přejděte na *Views\Home* adresář, vyberte tři tečky (...) vedle položky *Index.cshtml* souboru a pak vyberte **upravit**.

1. Proveďte změnu souboru, např. přidejte nějaký text v rámci jedné značky div. 

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.  
    Po chvíli se sestavení začíná v Azure DevOps a provede vydání k nasazení změny. Sledování stavu sestavení v řídicím panelu projekty DevOps, nebo v prohlížeči ve vaší organizaci Azure DevOps.

1. Po vydání aktualizace aplikace, chcete-li ověřit změny.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. Projekty DevOps automaticky nakonfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects. 

1. V pravém dolním vyberte **Application Insights** odkaz pro vaši aplikaci.  
    **Application Insights** se otevře podokno. Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

    ![V podokně Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **časový rozsah**a pak vyberte **za poslední hodinu**. Chcete-li filtrovat výsledky, vyberte **aktualizace**.  
    Teď si můžete zobrazit všechny aktivity z posledních 60 minut. 
    
1. Chcete-li ukončit časový rozsah, vyberte **x**.

1. Vyberte **výstrahy**a pak vyberte **přidat upozornění metriky**. 

1. Zadejte název pro upozornění.

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

Pokud testujete, vyhnete se účtují poplatky podle údržbě vašich prostředků. Pokud už je nepotřebujete, můžete odstranit virtuální počítač Azure a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu Projekt DevOps. 

> [!IMPORTANT]
> Následující postup se trvale odstraní prostředky. *Odstranit* funkcí zničí data, která se vytvoří v projektu v projektech pro DevOps v Azure i Azure DevOps a nebude možné ho načíst. Pomocí tohoto postupu, až poté, co jste pečlivě přečtěte si zobrazených výzev.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects.
1. V pravém horním rohu, vyberte **odstranit**. 
1. Do příkazového řádku vyberte **Ano** k *trvale odstranit* prostředky.

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace v ASP.NET pomocí projekty DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Zapsat změny do úložiště Azure a automaticky nasadit do Azure
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o kanálu CI/CD, naleznete v tématu:

> [!div class="nextstepaction"]
> [Definování kanálu vícefázové průběžného nasazování (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
