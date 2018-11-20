---
title: 'Kurz: Nasazení aplikace ASP.NET Core do Azure Kubernetes Service (AKS) pomocí projektů Azure DevOps'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. S projekty DevOps můžete nasadit aplikaci ASP.NET Core s Azure Kubernetes Service (AKS) v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6e2b53e51d7da117a7f690cb676d0ec096bcb1cd
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165542"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace ASP.NET Core do Azure Kubernetes Service (AKS) pomocí projektů Azure DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI). 

Projekty DevOps také:
* Automaticky vytváří prostředky Azure, jako je Azure Kubernetes Service (AKS).
* Vytvoří a nakonfiguruje kanál pro vydávání verzí v Azure DevOps, který nastaví kolekci sestavení a vydaná verze kanálu pro CI/CD.
* Vytvoří prostředek služby Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Projekty DevOps použít k nasazení aplikace ASP.NET Core do AKS
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Projekty DevOps použít k nasazení aplikace ASP.NET Core do AKS

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Prostředky Azure, jako je AKS cluster DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte aplikační architekturu**vyberte **ASP.NET Core**.

1. Vyberte **služby Kubernetes**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Vytvořte novou organizaci Azure DevOps, nebo vyberte existující organizace. 

1. Zadejte název pro váš projekt Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Zobrazit další Azure nastavení konfigurace a chcete-li určit počet uzlů clusteru AKS, vyberte **změnu**.  
    V tomto podokně se zobrazí různé možnosti pro konfiguraci typu a umístění služby Azure.
 
1. Ukončete oblasti konfigurace Azure a pak vyberte **provádí**.  
    Po několika minutách po dokončení. Ukázková aplikace ASP.NET Core je nastavený v úložišti Git ve vaší organizaci Azure DevOps, se vytvoří AKS cluster, spuštění kanálu CI/CD a aplikace je nasazená do Azure. 

    Po dokončení všech to řídicího panelu Projekt Azure DevOps se zobrazí na webu Azure Portal. Můžete také přejít na řídicí panel DevOps Projects přímo z **všechny prostředky** na webu Azure Portal. 

    Tento řídicí panel poskytuje vhled do vašeho úložiště kódu Azure DevOps, váš kanál CI/CD a clusteru AKS. V kanálu Azure DevOps můžete nakonfigurovat další možnosti CI/CD. Na pravé straně vyberte **Procházet** zobrazíte běžící aplikaci.

## <a name="examine-the-aks-cluster"></a>Prozkoumání clusteru AKS

Projekty DevOps automaticky nakonfiguruje cluster AKS, což vám umožní zkoumat a přizpůsobit. Seznamte se s clusterem AKS, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

1. Na pravé straně vyberte službě AKS.  
    Pro AKS cluster se otevře podokno. V tomto zobrazení můžete provádět různé akce, jako je monitorování stavu kontejneru, prohledávání protokolů a otevřete řídicí panel Kubernetes.

1. Na pravé straně vyberte **řídicí panel Kubernetes zobrazení**.  
    Volitelně můžete postupovat podle kroků otevřete řídicí panel Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Projekty DevOps automaticky nakonfiguruje kanál CI/CD ve vaší organizaci Azure DevOps. Kanál můžete prozkoumat a upravit. Seznamte se s ním, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

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

## <a name="examine-the-cd-release-pipeline"></a>Prozkoumejte CD kanál pro vydávání verzí

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje CD do Azure. Další informace o kanál pro vydávání verzí, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt. 

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete si projít několik nabídek, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zapsat změny do úložiště Azure a automaticky nasadit do Azure 

 > [!NOTE]
 > Následující postup testuje kanálu CI/CD změnou prostého textu.

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web. Jednotlivé změny do úložiště Git v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Například můžete klonovat úložiště Git v oblíbených nástrojů nebo integrovaného vývojového prostředí a pak doručit změny do tohoto úložiště.

1. V nabídce Azure DevOps, vyberte **kód** > **soubory**a potom přejděte do úložiště.

1. Přejděte na *Views\Home* adresář, vyberte tři tečky (...) vedle položky *Index.cshtml* souboru a pak vyberte **upravit**.

1. Proveďte změnu souboru, např. přidejte nějaký text v rámci jedné značky div. 

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.  
    Po chvíli se sestavení začíná v Azure DevOps a provede vydání k nasazení změny. Sledování stavu sestavení v řídícím panelu projekty DevOps, nebo v prohlížeči ve vaší organizaci Azure DevOps.

1. Po dokončení vydání aktualizujte své aplikace a zkontrolujte provedené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, vyhnete se účtují poplatky podle údržbě vašich prostředků. Pokud už je nepotřebujete, můžete odstranit AKS cluster a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu projekty DevOps.

> [!IMPORTANT]
> Následující postup se trvale odstraní prostředky. *Odstranit* funkcí zničí data, která se vytvoří v projektu v projektech pro DevOps v Azure i Azure DevOps a nebude možné ho načíst. Pomocí tohoto postupu, až poté, co jste pečlivě přečtěte si zobrazených výzev.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects.
2. V pravém horním rohu, vyberte **odstranit**. 
3. Do příkazového řádku vyberte **Ano** k *trvale odstranit* prostředky.

## <a name="next-steps"></a>Další postup

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Projekty DevOps použít k nasazení aplikace ASP.NET Core do AKS
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků

Další informace o použití řídicího panelu Kubernetes najdete v tématu:

> [!div class="nextstepaction"]
> [Používání řídicího panelu Kubernetes](https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard)
