---
title: 'Kurz: nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí Azure DevOps Projects'
description: Azure DevOps Projects usnadňuje začátek práce s Azure. Pomocí DevOps Projects můžete v několika rychlých krocích nasadit ASP.NET Core aplikaci do Azure Service Fabric.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 5f14164da5cd89cc7d0578e6b64c39d227734d75
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969476"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Kurz: nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projects také:
* Automaticky vytvoří prostředky Azure, jako je například Azure Service Fabric.
* Vytvoří a nakonfiguruje kanál verze ve službě Azure DevOps, který nastaví kanál CI/CD.
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Pomocí DevOps Projects vytvořit aplikaci ASP.NET Core a nasadit ji do Service Fabric
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn do Gitu a automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Pomocí DevOps Projects vytvořit aplikaci ASP.NET Core a nasadit ji do Service Fabric

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects také vytvoří prostředky Azure, jako je Cluster Service Fabric, v předplatném Azure podle vašeho výběru.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte Aplikační architekturu**vyberte **ASP.NET Core**a pak vyberte **Další**.

1. Vyberte možnost **Cluster Service Fabric**a pak vyberte **Další**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné Azure.

1. Pokud chcete zobrazit další nastavení konfigurace Azure a identifikovat velikost virtuálního počítače uzlu a operační systém pro cluster Service Fabric, vyberte **změnit**.  
    V tomto podokně se zobrazují různé možnosti konfigurace typu a umístění služeb Azure.
 
1. Ukončete oblast konfigurace Azure a potom vyberte **Hotovo**.  
    Po několika minutách se proces dokončí. Ukázková ASP.NET Core aplikace je nastavená v úložišti Git ve vaší organizaci Azure DevOps, vytvoří se cluster pro Service Fabric, provedený kanál CI/CD a vaše aplikace se nasadí do Azure. 

    Po dokončení všech těchto DevOps Projects se řídicí panel zobrazí v Azure Portal. Můžete také přejít na DevOps Projects řídicí panel přímo ze **všech prostředků** v Azure Portal. 

    Tento řídicí panel poskytuje přehled o vašem úložišti kódu Azure DevOps, kanálu CI/CD a vašem Service Fabricovém clusteru. Můžete nakonfigurovat další možnosti pro kanál CI/CD v Azure Repos. Na pravé straně vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje kanál CI/CD v Azure Pipelines. Kanál můžete prozkoumat a upravit. Pokud se k němu chcete seznámit, udělejte toto:

1. Přejít na řídicí panel projektu DevOps.

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
    Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení. 

1. Pod názvem vašeho kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
    DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje disk CD do Azure. Pokud se chcete dozvědět víc o kanálu vydávání, udělejte toto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
    DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali dříve, vytváří výstup, který se používá pro artefakt. 

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**.  
    Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Potvrďte změny v Gitu a automaticky je nasaďte do Azure. 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD tím, že provede jednoduchou změnu textu.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Při každé změně úložiště Git se spustí sestavení a vydaná verze nasadí vaše změny do Azure. Postupujte podle pokynů v této části, nebo použijte jinou techniku, abyste potvrdili změny v úložišti. Úložiště Git můžete například klonovat v oblíbených nástrojích nebo v integrovaném vývojovém prostředí (IDE) a pak doručovat změny do tohoto úložiště.

1. V nabídce Azure DevOps vyberte **kód** > **soubory**a pak přejděte do svého úložiště.

1. V adresáři *Views\Home* vyberte tři tečky (...) vedle souboru *index. cshtml* a pak vyberte **Upravit**.

1. Proveďte v souboru změny, jako je například přidání textu do jedné z značek DIV. 

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte.  
    Po chvíli se spustí sestavení a následně se spustí vydání pro nasazení změn. Stav sestavení můžete monitorovat na řídicím panelu DevOps Projects nebo v prohlížeči pomocí protokolování v reálném čase v Azure DevOps.

1. Až se vydaná verze dokončí, aktualizujte svou aplikaci, aby se ověřily vaše změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání poplatků za účtování pomocí vyčištění prostředků. Pokud už je nepotřebujete, můžete cluster Azure Service Fabric a související prostředky, které jste vytvořili v tomto kurzu, odstranit. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Funkce *odstranění* zničí data vytvořená projektem v DevOps Projects v Azure i ve službě Azure DevOps a nebude možné ji načíst. Tento postup použijte až po pečlivém čtení výzev.

1. V Azure Portal přejdete na řídicí panel DevOps Projects.
1. V pravém horním rohu vyberte **Odstranit**. 
1. Po zobrazení výzvy vyberte **Ano** , pokud chcete prostředky *trvale odstranit* .

## <a name="next-steps"></a>Další kroky

Volitelně můžete kanál Azure CI/CD přizpůsobit potřebám svého týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Pomocí DevOps Projects vytvořit aplikaci ASP.NET Core a nasadit ji do Service Fabric
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny v Gitu a automaticky je nasaďte do Azure.
> * Vyčištění prostředků

Další informace o Service Fabric a mikroslužbách najdete v tématech:

> [!div class="nextstepaction"]
> [Použití přístupu založeného na mikroslužbách při vytváření aplikací](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
