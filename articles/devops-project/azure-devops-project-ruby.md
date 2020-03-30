---
title: 'Úvodní příručka: Vytvoření kanálu CI/CD pro Ruby on Rails pomocí projektů Azure DevOps'
description: Azure DevOps Projects usnadňuje schůdky v Azure. Webovou aplikaci Ruby můžete spustit ve službě Azure v několika rychlých krocích.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 5cb47cdd76d1de284c4dc6dbdfbfedd0095653ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "70899572"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro Ruby on Rails pomocí projektů Azure DevOps

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro vaši aplikaci Ruby on Rails pomocí projektů Azure DevOps. DevOps Projekty zjednodušuje počáteční konfiguraci Azure DevOps sestavení a uvolnění kanálu.

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Projekty vytvoří kanál CI/CD v Azure Repos. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Vytvořit**.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci **Ruby.**

1. Vyberte architekturu aplikace **Ruby on Rails**. Až budete hotovi, vyberte **Další**.

1. Výchozí cíl nasazení je **Web App on Linux**.  
    Volitelně můžete vybrat **Web App pro kontejnery**. Rozhraní aplikace, které jste zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 
    
1. Vyberte cílovou službu podle svého výběru a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Vytvořte novou bezplatnou organizaci Azure DevOps nebo zvolte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte předplatné a umístění Azure, zadejte název aplikace a pak vyberte **Hotovo**.  
    Po několika minutách se na webu Azure Portal zobrazí řídicí panel DevOps Projects. Ukázková aplikace se nastaví v repo ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. 
    
    Řídicí panel poskytuje přehled o vašem kódu, kanálu CI/CD a vaší aplikaci v Azure. Vpravo vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

Azure DevOps Projects vytvoří úložiště Git v Azure Pipelines nebo GitHubu. Pokud chcete zobrazit repo a provést změny kódu v aplikaci, postupujte takto:

1. Na řídicím panelu Projekty DevOps vyberte vlevo odkaz pro hlavní větev.  
    Odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Chcete-li zobrazit adresu URL klonování repo, vyberte **klonovat** v pravém horním rohu.  
    Úložiště Git můžete naklonovat ve svém oblíbeném IDE. V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Vlevo přejděte do souboru *app/views/pages/home.html.erb* a pak vyberte **Upravit**.

1. Proveďte změnu souboru. Můžete například upravit nějaký text v jedné z značek div.

1. Vyberte **Potvrdit**a uložte změny.

1. V prohlížeči přejděte na řídicí panel Projekty DevOps.  
    Sestavení by mělo být v průběhu. Provedené změny jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Zkontrolujte kanály Azure Pipelines CI/CD kanálu

Azure DevOps Projects automaticky nakonfiguruje úplný kanál CI/CD ve vaší organizaci Azure DevOps. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání Azure DevOps, postupujte takto:

1. Přejděte na řídicí panel Projekty devops.

1. Nahoře vyberte **Sestavení kanálů**.  
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

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
    DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste zkoumali dříve vytváří výstup, který se používá pro artefakt. 

1. Na pravé straně ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**.  
    Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

1. Vlevo vyberte **Úkoly**.  
    Úkoly jsou aktivity, které proces nasazení provádí. V tomto příkladu byla vytvořena úloha pro nasazení do služby Azure App Service.

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Vyberte tři tečky (...) vedle verze a pak vyberte **Otevřít**.  
    Můžete prozkoumat několik nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. 

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nejsou potřeba, můžete odstranit instanci služby Azure App Service a související prostředky, které jste vytvořili v tomto rychlém startu. Chcete-li tak učinit, použijte funkci **Odstranit** na řídicím panelu Projekty DevOps.

## <a name="next-steps"></a>Další kroky

Další informace o úpravách kanálů sestavení a vydání tak, aby vyhovovaly potřebám vašeho týmu, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Definování vícestupňového kanálu průběžného nasazení (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
