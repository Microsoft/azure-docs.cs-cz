---
title: 'Rychlý start: Vytvoření kanálu CI/CD pro Node.js s projekty Azure DevOps'
description: DevOps Projects umožňuje snadno začít používat Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
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
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: dadc394e9c9ddb152923095de10b8f727310d3f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554800"
---
#  <a name="create-a-cicd-pipeline-for--nodejs-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro Node.js s projekty Azure DevOps 

Projekty Azure DevOps představuje zjednodušené prostředí, která vytváří prostředky Azure a nastavuje kontinuální integrace (CI) a kanál průběžného doručování (CD) pro aplikace v Node.js v Azure kanály.  

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**a poté vyhledejte **DevOps Projects**. 

    ![Zahájení konfigurace průběžného doručování](_img/azure-devops-project-nodejs/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci Node.js.  
    Ukázky Node.js zahrnují výběr několika architektur aplikace.

1. Výchozí ukázky rozhraní je Express.js. Ponechte výchozí nastavení a pak vyberte **Další**.  
    Výchozí cíl nasazení je **Webová aplikace ve Windows**.  Rozhraní framework aplikace, který jste zvolili dříve, určí typ cíle nasazení služby Azure k dispozici tady.  

2. Ponechat výchozí služby a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo zvolte existující organizace. 

    a. Zvolte název pro váš projekt. 

    b. Vyberte předplatné Azure a umístění, zvolte název pro vaši aplikaci a pak vyberte **provádí**.  
    Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázková aplikace je nastavena v úložišti ve vaší organizaci Azure DevOps, sestavení je spuštěno a vaše aplikace bude nasazena do Azure. Tento řídicí panel poskytuje vhled do vašeho úložiště kódu, kanál CI/CD a vaši aplikaci v Azure.
     
3. Vyberte **Procházet** k běžící aplikaci zobrazit.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-nodejs/dashboardnopreview.png) 
    
Projekty DevOps automaticky nakonfiguruje položky konfigurace sestavení a vydaná verze triggeru.  Teď jste připraveni spolupracovat s týmem ve službě aplikace v Node.js s CI/CD proces, který automaticky nasadí nejnovější práci na svých webech.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

Projekty DevOps vytvoří úložiště Git v úložišti Azure nebo z Githubu. Proveďte následující kroky k zobrazení úložiště a proveďte změny kódu aplikace.

1. Na levé straně řídicího panelu DevOps Projects vyberte odkaz pro hlavní větev.  
Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Chcete-li zobrazit adresu URL klonování úložiště, vyberte **klonování** nahoře přímo z prohlížeče.   
    Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně prohlížeče, přejděte **views/index.pug** souboru.

1. Vyberte **upravit**a potom změňte nadpis h2.  
    Zadejte například **Get started right away with the Azure DevOps Project** (Začínáme rovnou se službou Azure DevOps Project) nebo proveďte nějakou jinou změnu.

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.   
Teď byste měli vidět probíhající sestavení. Změny, které jste právě provedli jsou automaticky vytvořené a nasazené prostřednictvím kanálu CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Prozkoumání kanálu Azure CI/CD

Projekty DevOps v předchozím kroku, automaticky nakonfiguruje úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Proveďte následující kroky a seznamte se s sestavení a vydávání kanálů.

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
Tento odkaz otevře na kartě prohlížeče a kanál sestavení pro nový projekt.

1. Přejděte **stav** pole a pak vyberte tři tečky (...).  
    Tato akce otevře nabídku, kde můžete začít několika aktivity, například zařazování nového sestavení, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
Sestavení provádí různé úlohy, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupy, které se používají pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
V **historie** podokně se zobrazí záznam auditovaných nedávné změny pro sestavení.  Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.   
 Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.   
V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
 Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Na levé straně, vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
Kanál pro vydávání verzí definuje procesu vydávání verzí.

12. V části **Artefakty** vyberte **Zahodit**.  
    Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
Tento kanál pro vydávání verzí má povoleno CD triggeru, který se spouští nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 


1. Na levé straně vyberte **úlohy**.   
Úkoly jsou aktivity, které proces nasazení provede. V tomto příkladu byl úkol vytvořen k nasazení do služby Azure App Service.


1. Na pravé straně vyberte **zobrazit verze**.  
Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) u jedné vydávání verzí a pak vyberte **otevřít**.  
Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.   
Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s konkrétní nasazení.

1. Vyberte **Protokoly**.  
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.


## <a name="next-steps"></a>Další postup

Pokud jste nakonfigurovali procesu CI/CD, sestavení a kanály pro vydávání se automaticky vytvořily. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
