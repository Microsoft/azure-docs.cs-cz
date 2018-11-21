---
title: 'Kurz: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí projektů Azure DevOps'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. S projekty DevOps můžete nasadit aplikaci ASP.NET Core do Azure Service Fabric v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2bba5d54c2b6298c2dd8059d47e5975ad3f176c8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264756"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí projektů Azure DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI). 

Projekty DevOps také:
* Automaticky vytváří prostředky Azure, jako je Azure Service Fabric.
* Vytvoří a nakonfiguruje kanál pro vydávání verzí v Azure DevOps, který nastaví kolekci kanálu CI/CD.
* Vytvoří prostředek služby Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření aplikace ASP.NET Core a nasaďte ji do Service Fabric pomocí projekty DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn do Gitu a automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabricc"></a>Použijte k vytvoření aplikace ASP.NET Core a nasaďte ji do služby Fabricc projekty DevOps

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty DevOps prostředků Azure, jako je cluster Service Fabric také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte aplikační architekturu**vyberte **ASP.NET Core**a pak vyberte **Další**.

1. Vyberte **Service Fabric Cluster**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Vytvořte novou organizaci Azure DevOps, nebo vyberte existující organizace. 

1. Zadejte název pro váš projekt Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další Azure nastavení konfigurace a k identifikaci operačního systému pro cluster Service Fabric a velikosti virtuálního počítače uzlu, vyberte **změnu**.  
    V tomto podokně se zobrazí různé možnosti pro konfiguraci typu a umístění služby Azure.
 
1. Ukončete oblasti konfigurace Azure a pak vyberte **provádí**.  
    Po několika minutách po dokončení. Ukázkové aplikace v ASP.NET Core je nastavený v úložišti Git ve vaší organizaci Azure DevOps, vytvoření clusteru Service Fabric, je proveden kanálu CI/CD a aplikace je nasazená do Azure. 

    Po dokončení všech to řídicího panelu DevOps Projects se zobrazí na webu Azure Portal. Můžete také přejít na řídicí panel DevOps Projects přímo z **všechny prostředky** na webu Azure Portal. 

    Tento řídicí panel poskytuje přehled o Azure DevOps úložiště kódu, váš kanál CI/CD a vašeho clusteru Service Fabric. Konfigurace dalších možností pro svůj kanál CI/CD v úložišti Azure. Na pravé straně vyberte **Procházet** zobrazíte běžící aplikaci.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Projekty DevOps automaticky nakonfiguruje kanál CI/CD v kanálech Azure. Kanál můžete prozkoumat a upravit. Seznamte se s ním, postupujte takto:

1. Přejdete na řídicí panel DevOps Projectss.

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
    Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

1. Přejděte **stav** pole a pak vyberte tři tečky (...).  
    Nabídka obsahuje několik možností, jako je například přidávání nové sestavení, přerušení sestavení a úpravy kanálu sestavení do fronty.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úlohy, jako je načítání zdroje z Gitu výstupy úložiště, obnovení závislostí a publikování použít pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení. 

1. Pod názvem kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
    Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje CD do Azure. Další informace o kanál pro vydávání verzí, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat dříve vytvoří výstup, který se používá pro artefakt. 

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete si projít několik nabídek, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Potvrzení změn v Gitu a automaticky nasadit do Azure 

 > [!NOTE]
 > Následující postup testuje kanálu CI/CD změnou prostého textu.

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web. Jednotlivé změny do úložiště Git spustí sestavení a vydání nasadí změny do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Například můžete klonovat úložiště Git v oblíbených nástrojů nebo integrovaného vývojového prostředí a pak doručit změny do tohoto úložiště.

1. V nabídce Azure DevOps, vyberte **kód** > **soubory**a potom přejděte do úložiště.

1. Přejděte na *Views\Home* adresář, vyberte tři tečky (...) vedle položky *Index.cshtml* souboru a pak vyberte **upravit**.

1. Proveďte změnu souboru, např. přidejte nějaký text v rámci jedné značky div. 

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.  
    Po chvíli se spustí sestavení a poté spustí vydání k nasazení změny. Můžete monitorovat stav sestavení na řídicím panelu projekty DevOps nebo v prohlížeči s Azure DevOps protokolování v reálném čase.

1. Po dokončení vydání aktualizujte své aplikace a zkontrolujte provedené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, vyhnete se účtují poplatky podle údržbě vašich prostředků. Pokud už je nepotřebujete, můžete odstranit cluster Azure Service Fabric a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu projekty DevOps.

> [!IMPORTANT]
> Následující postup se trvale odstraní prostředky. *Odstranit* funkcí zničí data, která se vytvoří v projektu v projektech pro DevOps v Azure i Azure DevOps a nebude možné ho načíst. Pomocí tohoto postupu, až poté, co jste pečlivě přečtěte si zobrazených výzev.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects.
1. V pravém horním rohu, vyberte **odstranit**. 
1. Do příkazového řádku vyberte **Ano** k *trvale odstranit* prostředky.

## <a name="next-steps"></a>Další postup

Volitelně můžete kanál Azure CI/CD přizpůsobit potřebám svého týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření aplikace ASP.NET Core a nasaďte ji do Service Fabric pomocí projekty DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a automaticky nasadit do Azure
> * Vyčištění prostředků

Další informace o Service Fabric a mikroslužeb, naleznete v tématu:

> [!div class="nextstepaction"]
> [Použití přístupu založeného na mikroslužbách při vytváření aplikací](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
