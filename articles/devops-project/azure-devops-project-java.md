---
title: 'Rychlý start: Vytvoření kanálu CI/CD pro Java-Azure DevOps Projects'
description: DevOps Projects usnadňuje začátek práce s Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: a3b57fd7acba4d45b0bd519a21ae74f91291fd77
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873233"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Rychlý start: Nastavení kanálu CI/CD pro aplikaci Java pomocí Azure DevOps Projects

V tomto článku rychlý Start používáte Azure DevOps Projects "zjednodušené prostředí pro nastavení kanálu průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci Java v Azure Pipelines. K vytvoření dalších prostředků Azure můžete použít taky Azure DevOps Projects.  

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

  DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**a pak vyhledejte **DevOps Projects**.  

2. Vyberte **Vytvořit**.

    ![Zahájení konfigurace průběžného doručování](_img/azure-devops-project-java/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci Java.  
Ukázka v Javě zahrnuje výběr několika architektur aplikace.

1. Výchozí ukázková architektura je jarní. Nechejte výchozí nastavení a pak vyberte **Další**.  Web App for Containers je výchozím cílem nasazení. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

2. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

    a. Zvolte název pro váš projekt. 

    b. Vyberte předplatné Azure a umístění, zvolte název pro vaši aplikaci a pak vyberte **provádí**.  
    Po několika minutách se řídicí panel DevOps Projects zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje vhled do vašeho úložiště kódu, kanál CI/CD a vaši aplikaci v Azure.
    

2. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-java/dashboardnopreview.png) 
    
   DevOps Projects automaticky nakonfigurovali aktivační událost sestavení a vydání CI.  Teď jste připraveni při práci na aplikaci v Javě spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte následovně:

1. Na levé straně řídicího panelu DevOps Projects vyberte odkaz pro svou hlavní větev.  
Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonování úložiště, vyberte v pravém horním rohu v prohlížeči možnost **klonovat** .   
    Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně prohlížeče otevřete soubor **Src/Main/WebApp/index.html** .

1. Vyberte **Upravit**a pak proveďte změnu textu.
    Změňte například text v některé ze značek div.

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.   
Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

 V předchozím kroku DevOps Projects automaticky nakonfigurovali úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Při seznámení s kanály sestavení a vydání proveďte následující kroky.

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
Tento odkaz otevře kartu prohlížeče a kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    Tato akce otevře nabídku, kde můžete spustit několik aktivit, jako je například zařazování nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
Sestavení provádí nejrůznější úlohy, jako je načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů, které se používají pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
V **historie** podokně se zobrazí záznam auditovaných nedávné změny pro sestavení.  Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.   
 Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.   
V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
 DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Na levé straně vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
Kanál pro vydávání verzí obsahuje kanál, který definuje procesu vydávání verzí.  
    
12. V části **Artefakty** vyberte **Zahodit**.  
Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
Tento kanál pro vydávání verzí má povoleno CD triggeru, který se spouští nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.   
Úkoly jsou aktivity, které proces nasazení provede. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **zobrazit verze**.  
Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) u jedné vydávání verzí a pak vyberte **otevřít**.  
Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.   
Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s konkrétní nasazení. 

1. Vyberte **Protokoly**.  
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete Azure App Service a další související prostředky odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
