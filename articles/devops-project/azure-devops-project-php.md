---
title: 'Rychlý start: Vytvoření kanálu CI/CD pro PHP pomocí Azure DevOps Projects'
description: DevOps Projects usnadňuje začátek práce s Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
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
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899582"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro PHP pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, které vytvoří prostředky Azure a nastaví pro aplikaci PHP kanál pro průběžnou integraci (CI) a průběžné doručování (CD) v Azure Pipelines.  

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

 DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit bezplatnou novou organizaci Azure DevOps nebo použít stávající organizaci. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte ikonu **vytvořit prostředek** a pak vyhledejte **DevOps Projects**.  

3. Vyberte **Vytvořit**.

    ![Spouští se konfigurace průběžného doručování.](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci PHP.  
        Ukázky PHP zahrnují výběr několika aplikačních architektur. Výchozí ukázková architektura je Laravel. 
        
2. Nechejte výchozí nastavení a pak vyberte **Další**.  

1. Web App for Containers je výchozím cílem nasazení.  
    Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde.  Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

    a. Vyberte název projektu v Azure DevOps. 
    
    b. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte Hotovo.   
        Po několika minutách se řídicí panel DevOps Projects zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.  
        
2. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Projects automaticky nakonfigurovali aktivační událost sestavení a vydání CI.  Teď jste připraveni při práci na aplikaci v PHP spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

 DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, proveďte následující kroky:

1. Na levé straně řídicího panelu DevOps Projects vyberte odkaz pro svou hlavní větev.   
    Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonu úložiště, v pravé horní části prohlížeče vyberte **Clone** (Klonovat).   
    Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V několika dalších krocích můžete pomocí webového prohlížeče vytvořit a potvrdit změny kódu přímo do hlavní větve.

1. Na levé straně přejdete do souboru **Resources/views/Welcome. Blade. php** .

1. Vyberte **Upravit**a pak proveďte změnu textu.  Změňte například text v některé ze značek div.

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.  
Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

 DevOps Projects automaticky konfiguruje plný kanál CI/CD v Azure Pipelines. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
    Tento odkaz otevře kartu prohlížeče a kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte **tři tečky** (...).  
    V nabídce se zobrazí několik možností, jako je například zařazování nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení spouští různé úlohy, jako například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů, které se používají pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název kanálu sestavení na výstižnější, vyberte, **uložte & frontu**a potom vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
    V podokně **Historie** se zobrazí záznam o tom, jak vaše poslední změny sestavení provést. Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
      Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.   
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
     DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál pro vydávání verzí obsahuje kanál, který definuje procesu vydávání verzí. 

12. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.   
    Tento kanál verze má povolený Trigger CD, který spouští nasazení pokaždé, když je k dispozici nový artefakt sestavení.  Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.  
        Úkoly jsou aktivity, které proces nasazení provede.  V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle jedné z vašich vydání a pak vyberte **otevřít**.  
        Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například souhrn verze, související pracovní položky a testy.

1. Vyberte **Potvrzení**.  
        Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s konkrétní nasazení. 

1. Vyberte **Protokoly**.  
        Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete Azure App Service a další související prostředky odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
