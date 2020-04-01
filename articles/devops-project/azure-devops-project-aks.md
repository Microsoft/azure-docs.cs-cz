---
title: 'Kurz: Nasazení aplikací ASP.NET Core do služby Azure Kubernetes pomocí projektů Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. S DevOps Projects můžete nasadit svou aplikaci ASP.NET Core pomocí služby Azure Kubernetes Service (AKS) v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969674"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Kurz: Nasazení aplikací ASP.NET Core do služby Azure Kubernetes pomocí projektů Azure DevOps

Azure DevOps Projects představuje zjednodušené prostředí, kde můžete přenést existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projekty také:
* Automaticky vytváří prostředky Azure, jako je například Služba Azure Kubernetes (AKS).
* Vytvoří a nakonfiguruje kanál vydání v Azure DevOps, který nastavuje kanál sestavení a vydání pro CI/CD.
* Vytvoří prostředek Azure Application Insights pro monitorování.
* Umožňuje [Azure Monitor pro kontejnery](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) ke sledování výkonu pro úlohy kontejnerů v clusteru AKS

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET Core do AKS pomocí devops projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Nasazení aplikace ASP.NET Core do AKS pomocí devops projects

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projekty také vytvoří prostředky Azure, jako je například cluster AKS, v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Vytvořit**.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **možnost .NET**a pak vyberte **Další**.

1. V části **Zvolit aplikační architekturu**vyberte **ASP.NET Core**.

1. Vyberte **Kubernetes service**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další nastavení konfigurace Azure a identifikovat počet uzlů pro cluster AKS, vyberte **změnit**.  
    Toto podokno zobrazuje různé možnosti konfigurace typu a umístění služeb Azure.
 
1. Ukončete konfigurační oblast Azure a pak vyberte **Hotovo**.  
    Po několika minutách je proces dokončen. Ukázka ASP.NET aplikace Core se nastaví v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se cluster AKS, spustí se kanál CI/CD a vaše aplikace se nasadí do Azure. 

    Po dokončení se na webu Azure Portal zobrazí řídicí panel Projektu Azure DevOps. Můžete taky přejít na řídicí panel DevOps Projects přímo ze **všech prostředků** na webu Azure Portal. 

    Tento řídicí panel poskytuje přehled o úložišti kódu Azure DevOps, kanálu CI/CD a clusteru AKS. Další možnosti CI/CD můžete nakonfigurovat v kanálu Azure DevOps. Vpravo vyberte **Procházet** a zobrazte spuštěnou aplikaci.

## <a name="examine-the-aks-cluster"></a>Prozkoumání clusteru AKS

DevOps Projects automaticky konfiguruje cluster AKS, který můžete prozkoumat a přizpůsobit. Pokud se chcete seznámit s clusterem AKS, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

1. Vpravo vyberte službu AKS.  
    Otevře se podokno clusteru AKS. Z tohoto zobrazení můžete provádět různé akce, jako je například sledování stavu kontejneru, hledání protokolů a otevření řídicího panelu Kubernetes.

1. Vpravo vyberte **Zobrazit řídicí panel Kubernetes**.  
    Případně podle pokynů otevřete řídicí panel Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Chcete-li se s ním seznámit, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

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

## <a name="examine-the-cd-release-pipeline"></a>Zkontrolujte kanál vydání disku CD-ROM

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál vydání, který poskytuje CD do Azure. Další informace o kanálu vydání, postupujte takto:

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
    DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Na pravé straně ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**.  
    Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Vyberte tři tečky (...) vedle verze a pak vyberte **Otevřít**.  
    Můžete prozkoumat několik nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD provedením jednoduché změny textu.

Nyní jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který automaticky nasazuje vaši nejnovější práci na váš web. Každá změna úložiště Git spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v repo. Můžete například klonovat úložiště Git ve vašem oblíbeném nástroji nebo rozhraní IDE a potom vysunout změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte**Soubory** **kódu** > a přejděte do repo.

1. Přejděte do adresáře *Zobrazení\Domů,* vyberte tři tečky (...) vedle souboru *Index.cshtml* a vyberte **Upravit**.

1. Proveďte změnu souboru, například přidání textu do jedné z značek div. 

1. Vpravo nahoře vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.  
    Po několika okamžicích se sestavení spustí v Azure DevOps a verze se spustí k nasazení změn. Sledujte stav sestavení na řídicím panelu DevOps Projects nebo v prohlížeči pomocí organizace Azure DevOps.

1. Po dokončení vydání aktualizujte aplikaci a ověřte změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání fakturačních poplatků vyčištěním prostředků. Pokud již nejsou potřeba, můžete odstranit cluster AKS a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte funkci **Odstranit** na řídicím panelu Projekty DevOps.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Delete *Delete* funkce zničí data, která je vytvořena projektu v devops projekty v Azure a Azure DevOps a nebude možné načíst. Tento postup použijte až poté, co si pečlivě přečtete výzvy.

1. Na webu Azure Portal přejděte na řídicí panel Projekty DevOps.
2. Vpravo nahoře vyberte **Odstranit**. 
3. V řádku vyberte **Ano,** *chcete-li* prostředky trvale odstranit.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET Core do AKS pomocí devops projects
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

Další informace o používání řídicího panelu Kubernetes najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Použití řídicího panelu Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
