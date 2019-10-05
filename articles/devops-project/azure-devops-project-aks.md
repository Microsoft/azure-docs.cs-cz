---
title: 'Kurz: nasazení aplikací ASP.NET Core do služby Azure Kubernetes pomocí Azure DevOps Projects'
description: Azure DevOps Projects usnadňuje začátek práce s Azure. Pomocí DevOps Projects můžete v několika rychlých krocích nasadit ASP.NET Core aplikaci pomocí služby Azure Kubernetes (AKS).
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969674"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Kurz: nasazení aplikací ASP.NET Core do služby Azure Kubernetes pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projects také:
* Automaticky vytvoří prostředky Azure, jako je Azure Kubernetes Service (AKS).
* Vytvoří a nakonfiguruje kanál verze ve službě Azure DevOps, který nastaví kanál sestavení a vydání pro CI/CD.
* Vytvoří prostředek Azure Application Insights pro monitorování.
* Povolí [Azure monitor pro kontejnery](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) monitorovat výkon pro úlohy kontejneru v clusteru AKS.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Použití DevOps Projects k nasazení ASP.NET Core aplikace do AKS
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Použití DevOps Projects k nasazení ASP.NET Core aplikace do AKS

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects také vytvoří prostředky Azure, jako je cluster AKS, v předplatném Azure podle vašeho výběru.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte Aplikační architekturu**vyberte **ASP.NET Core**.

1. Vyberte **Služba Kubernetes**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Pokud chcete zobrazit další nastavení konfigurace Azure a identifikovat počet uzlů pro cluster AKS, vyberte **změnit**.  
    V tomto podokně se zobrazují různé možnosti konfigurace typu a umístění služeb Azure.
 
1. Ukončete oblast konfigurace Azure a potom vyberte **Hotovo**.  
    Po několika minutách se proces dokončí. Ukázková ASP.NET Core aplikace je nastavená v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se cluster AKS, provedený kanál CI/CD a vaše aplikace se nasadí do Azure. 

    Po dokončení všech těchto stavů se na Azure Portal zobrazí řídicí panel projekt Azure DevOps. Můžete také přejít na DevOps Projects řídicí panel přímo ze **všech prostředků** v Azure Portal. 

    Tento řídicí panel poskytuje přehled o vašem úložišti kódu Azure DevOps, kanálu CI/CD a vašem clusteru AKS. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

## <a name="examine-the-aks-cluster"></a>Prozkoumání clusteru AKS

DevOps Projects automaticky konfiguruje cluster AKS, který můžete prozkoumat a přizpůsobit. Pokud se chcete seznámit s clusterem AKS, udělejte toto:

1. Přejít na řídicí panel DevOps Projects.

1. Na pravé straně vyberte službu AKS.  
    Otevře se podokno pro cluster AKS. Z tohoto zobrazení můžete provádět různé akce, jako je monitorování stavu kontejneru, hledání protokolů a otevírání řídicího panelu Kubernetes.

1. Na pravé straně vyberte **zobrazit řídicí panel Kubernetes**.  
    Případně postupujte podle kroků a otevřete řídicí panel Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Pokud se k němu chcete seznámit, udělejte toto:

1. Přejít na řídicí panel DevOps Projects.

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
    Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem vašeho kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
    DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

## <a name="examine-the-cd-release-pipeline"></a>Kontrola kanálu pro vydání CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje disk CD do Azure. Pokud se chcete dozvědět víc o kanálu vydávání, udělejte toto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
    DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**.  
    Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrďte změny Azure Repos a automaticky je nasaďte do Azure. 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD tím, že provede jednoduchou změnu textu.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Každá změna úložiště Git spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použijte jinou techniku, abyste potvrdili změny v úložišti. Úložiště Git můžete například klonovat v oblíbených nástrojích nebo v integrovaném vývojovém prostředí (IDE) a pak doručovat změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte **kód** > **soubory**a pak přejděte do svého úložiště.

1. V adresáři *Views\Home* vyberte tři tečky (...) vedle souboru *index. cshtml* a pak vyberte **Upravit**.

1. Proveďte v souboru změny, jako je například přidání textu do jedné z značek DIV. 

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte.  
    Po chvíli se Build spustí v Azure DevOps a k nasazení změn se spustí vydání. Monitorujte stav buildu na řídicím panelu DevOps Projects nebo v prohlížeči pomocí vaší organizace Azure DevOps.

1. Až se vydaná verze dokončí, aktualizujte svou aplikaci, aby se ověřily vaše změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání poplatků za účtování pomocí vyčištění prostředků. Pokud už je nepotřebujete, můžete cluster AKS a související prostředky, které jste vytvořili v tomto kurzu, odstranit. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Funkce *odstranění* zničí data vytvořená projektem v DevOps Projects v Azure i ve službě Azure DevOps a nebude možné ji načíst. Tento postup použijte až po pečlivém čtení výzev.

1. V Azure Portal přejdete na řídicí panel DevOps Projects.
2. V pravém horním rohu vyberte **Odstranit**. 
3. Po zobrazení výzvy vyberte **Ano** , pokud chcete prostředky *trvale odstranit* .

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití DevOps Projects k nasazení ASP.NET Core aplikace do AKS
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

Další informace o používání řídicího panelu Kubernetes najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Používání řídicího panelu Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
