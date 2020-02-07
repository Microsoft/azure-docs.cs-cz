---
title: 'Rychlý Start: vytvoření kanálu CI/CD pro Python s Azure DevOps Projects'
description: DevOps Projects usnadňuje Začínáme s Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
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
ms.openlocfilehash: b912dfe3fb6461a925977192a6631ecac1357d35
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049816"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro Python s Azure DevOps Projects

V tomto rychlém startu použijete zjednodušené prostředí Azure DevOps Projects k nastavení kanálu průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci v Pythonu v Azure Pipelines. Pomocí Azure DevOps Projects můžete nastavit vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Účet [Azure DevOps](https://azure.microsoft.com/services/devops/) a organizace.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a v levém podokně vyberte **vytvořit prostředek**. 

   ![Vytvoření prostředku Azure v Azure Portal](_img/azure-devops-project-python/fullbrowser.png)

1. Vyhledejte a vyberte **DevOps Projects**a pak vyberte **vytvořit**.

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci Python. Ukázka v Pythonu zahrnuje výběr několika architektur aplikace.

1. Výchozí ukázková architektura je Django. Nechejte výchozí nastavení a pak vyberte **Další**.    
Web App for Containers je výchozím cílem nasazení. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

3. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

    a. Do Azure DevOps zadejte název vašeho projektu.  

    b. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte **Hotovo**.  
     Po několika minutách se řídicí panel projektu zobrazuje v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.  
    
2. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projects automaticky konfiguruje aktivační událost sestavení a vydání CI. Nyní jste připraveni spolupracovat s týmem v aplikaci Python pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

 DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte následovně: 

1. Na levé straně řídicího panelu DevOps Projects vyberte odkaz pro svou hlavní větev.  
        Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonu úložiště, v pravé horní části prohlížeče vyberte **Clone** (Klonovat).   
Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE).  V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně přejdete do souboru **App/Templates/app/index.html** .

1. Vyberte **Upravit** a proveďte změnu nějakého textu. Změňte například text v některé ze značek div.

1. Vyberte **Potvrdit**a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.   
    Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

V předchozím kroku DevOps Projects automaticky nakonfigurovali úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte **tři tečky** (...).  
        V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
        Sestavení provádí různé úkoly, jako je načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem vašeho kanálu buildu vyberte **Historie**.  
        Zobrazí se protokol auditu nedávno provedených změn sestavení.  Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
         DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  
        V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak zvolte **vydané**verze.   
 DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
Kanál vydané verze definuje proces vydání.  
        
12. V části **Artefakty** vyberte **Zahodit**.   
Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **odkládacího umístění** vyberte **aktivační událost průběžného nasazování**.  
        Kanál verze má povolený aktivační událost CD, který spouští nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.   
Úkoly jsou aktivity, které proces nasazení provede. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.  
        
1. Vyberte tři tečky (...) vedle jedné z vašich vydání a pak vyberte **otevřít**.  
        Z tohoto zobrazení je k dispozici několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**. 
        Toto zobrazení ukazuje potvrzení kódu, která jsou přidružená ke konkrétnímu nasazení. 

1. Vyberte **Protokoly**.   
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a související prostředky můžete odstranit, i když je už nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
