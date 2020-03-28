---
title: 'Kurz: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí projektů Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. S DevOps Projects můžete nasadit aplikaci ASP.NET Core do Azure Service Fabric v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 5f14164da5cd89cc7d0578e6b64c39d227734d75
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969476"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí projektů Azure DevOps

Azure DevOps Projects představuje zjednodušené prostředí, kde můžete přenést existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projekty také:
* Automaticky vytvoří prostředky Azure, jako je Azure Service Fabric.
* Vytvoří a nakonfiguruje kanál vydání v Azure DevOps, který nastavuje kanál CI/CD.
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření aplikace ASP.NET Core pomocí devops projects a nasazení do service fabricu
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn do Gitu a automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Vytvoření aplikace ASP.NET Core pomocí devops projects a nasazení do service fabricu

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projekty také vytvoří prostředky Azure, jako je například cluster Service Fabric, v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Vytvořit**.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **možnost .NET**a pak vyberte **Další**.

1. V **části Choose a application framework**vyberte ASP.NET **Core**a pak vyberte **Další**.

1. Vyberte **cluster Prostředků infrastruktury služby**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Chcete-li zobrazit další nastavení konfigurace Azure a identifikovat velikost virtuálního počítače uzlu a operační systém pro cluster Service Fabric, vyberte **změnit**.  
    Toto podokno zobrazuje různé možnosti konfigurace typu a umístění služeb Azure.
 
1. Ukončete konfigurační oblast Azure a pak vyberte **Hotovo**.  
    Po několika minutách je proces dokončen. Ukázková aplikace ASP.NET Core se nastaví v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se cluster Service Fabric, spustí se kanál CI/CD a vaše aplikace se nasadí do Azure. 

    Po dokončení se na webu Azure Portal zobrazí řídicí panel DevOps Projects. Můžete taky přejít na řídicí panel DevOps Projects přímo ze **všech prostředků** na webu Azure Portal. 

    Tento řídicí panel poskytuje přehled o vašem repo kódu Azure DevOps, kanálu CI/CD a clusteru Service Fabric. Můžete nakonfigurovat další možnosti pro váš kanál CI/CD v Azure Repos. Vpravo vyberte **Procházet** a zobrazte spuštěnou aplikaci.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje kanál CI/CD v azure kanálech. Kanál můžete prozkoumat a upravit. Chcete-li se s ním seznámit, postupujte takto:

1. Přejděte na řídicí panel Projektu DevOps.

1. V horní části řídicího panelu Projekty DevOps vyberte **Sestavit kanály**.  
    Karta prohlížeče zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **Stav** a vyberte tři tečky (...).  
    Nabídka zobrazuje několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu. 

1. Pod názvem kanálu buildu vyberte **Historie**.  
    Toto podokno zobrazuje záznam auditu posledních změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**.  
    DevOps Projekty automaticky vytvoří aktivační událost CI a každé potvrzení k repo spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál vydání, který poskytuje CD do Azure. Další informace o kanálu vydání, postupujte takto:

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
    DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste zkoumali dříve vytváří výstup, který se používá pro artefakt. 

1. Na pravé straně ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**.  
    Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Vyberte tři tečky (...) vedle verze a pak vyberte **Otevřít**.  
    Můžete prozkoumat několik nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Potvrzení změn v Gitu a jejich automatické nasazení do Azure 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD provedením jednoduché změny textu.

Nyní jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který automaticky nasazuje vaši nejnovější práci na váš web. Každá změna úložiště Git spustí sestavení a verze nasadí vaše změny do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v repo. Můžete například klonovat úložiště Git ve vašem oblíbeném nástroji nebo rozhraní IDE a potom vysunout změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte**Soubory** **kódu** > a přejděte do repo.

1. Přejděte do adresáře *Zobrazení\Domů,* vyberte tři tečky (...) vedle souboru *Index.cshtml* a vyberte **Upravit**.

1. Proveďte změnu souboru, například přidání textu do jedné z značek div. 

1. Vpravo nahoře vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.  
    Po několika okamžicích se spustí sestavení a potom se spustí verze k nasazení změn. Stav sestavení můžete sledovat na řídicím panelu DevOps Projects nebo v prohlížeči pomocí protokolování Azure DevOps v reálném čase.

1. Po dokončení vydání aktualizujte aplikaci a ověřte změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání fakturačních poplatků vyčištěním prostředků. Když už nejsou potřeba, můžete odstranit cluster Azure Service Fabric a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte funkci **Odstranit** na řídicím panelu Projekty DevOps.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Delete *Delete* funkce zničí data, která je vytvořena projektu v devops projekty v Azure a Azure DevOps a nebude možné načíst. Tento postup použijte až poté, co si pečlivě přečtete výzvy.

1. Na webu Azure Portal přejděte na řídicí panel Projekty DevOps.
1. Vpravo nahoře vyberte **Odstranit**. 
1. V řádku vyberte **Ano,** *chcete-li* prostředky trvale odstranit.

## <a name="next-steps"></a>Další kroky

Volitelně můžete kanál Azure CI/CD přizpůsobit potřebám svého týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření aplikace ASP.NET Core pomocí devops projects a nasazení do service fabricu
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Gitu a jejich automatické nasazení do Azure
> * Vyčištění prostředků

Další informace o service fabric a mikroslužbách najdete v tématu:

> [!div class="nextstepaction"]
> [Použití přístupu založeného na mikroslužbách při vytváření aplikací](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
