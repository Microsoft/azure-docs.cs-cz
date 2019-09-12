---
title: 'Rychlý start: Vytvoření kanálu CI/CD pro programovací jazyk na cestách pomocí Azure DevOps Projects'
description: DevOps Projects usnadňuje začátek práce s Azure. Pomocí několika rychlých kroků vám pomůže spustit webovou aplikaci v jazyce API ve službě Azure.
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
ms.openlocfilehash: f7429a6de05a301b579354d722ad354b78ce6cbf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899674"
---
# <a name="create-a-cicd-pipeline-for-go-by-using-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro přechod pomocí Azure DevOps Projects

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro vaši aplikaci v cestách pomocí Azure DevOps Projects. DevOps Projects zjednodušuje počáteční konfiguraci kanálu sestavení a vydání Azure DevOps.

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Project**a pak vyberte **vytvořit**.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Vybrat ukázkovou aplikaci a službu Azure

1. Vyberte ukázkovou aplikaci **Přejít** a pak vyberte **Další**.  
    
1. Výchozí architektura je **Aplikace Simple Go**. Vyberte **Další**.  
    Aplikační rozhraní, které jste si zvolili dříve, určuje typ cílů nasazení služby Azure, které jsou k dispozici pro nasazení. 
    
1. Ponechte výchozí službu Azure a vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci bezplatné Azure DevOps nebo zvolte existující organizace. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte Hotovo.  
    Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. 
    
    Řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure. Na pravé straně vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Potvrďte změny kódu a spusťte CI/CD.

DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHubu. Pokud chcete zobrazit úložiště a udělat v aplikaci změny kódu, udělejte toto:

1. Na DevOps Projects vlevo vyberte odkaz pro svou hlavní větev.  
    Odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL pro klonování úložiště, vyberte **klonovat** v pravém horním rohu.  
    Úložiště Git můžete klonovat v rámci svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Vlevo klikněte na soubor *views/index.html* a pak vyberte **Upravit**.

1. Proveďte změnu souboru. Například upravte nějaký text v rámci jedné z značek DIV.

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.  
    Mělo by probíhat sestavení. Provedené změny se automaticky vytvořené a nasazené prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

DevOps Projects automaticky konfiguruje plný kanál CI/CD v Azure Repos. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání Azure DevOps, postupujte takto:

1. Přejít na řídicí panel DevOps Projects.

1. V horní části vyberte **vytvořit kanály**.  
    Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
    DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
    DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali dříve, vytváří výstup, který se používá pro artefakt. 

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**.  
    Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.  
    Úlohy jsou aktivity, které proces nasazení provádí. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. 

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit instanci Azure App Service a související prostředky, které jste vytvořili v rámci tohoto rychlého startu. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další postup

Další informace o změnách kanálů sestavení a vydání, které vyhovují potřebám vašeho týmu, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
