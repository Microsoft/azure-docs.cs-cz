---
title: Vytvoření kanálu CI/CD pro Node. js s Azure DevOps Projects
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 35eebeaa393ff75ada11752aaf9f195efddfa12b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049803"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Rychlý Start: vytvoření kanálu CI/CD v Azure Pipelines pro Node. js s Azure DevOps Projects

V tomto rychlém startu použijete zjednodušené prostředí Azure DevOps Projects k nastavení kanálu průběžné integrace (CI) a průběžného doručování (CD) pro aplikaci Node. js v Azure Pipelines. Pomocí Azure DevOps Projects můžete nastavit vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Účet [Azure DevOps](https://azure.microsoft.com/services/devops/) a organizace.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a v levém podokně vyberte **vytvořit prostředek**. 

   ![Vytvoření prostředku Azure v Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Vyhledejte a vyberte **DevOps Projects**a pak vyberte **vytvořit**.

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci Node. js.  
    Ukázky Node.js zahrnují výběr několika architektur aplikace.

1. Výchozí ukázková architektura je Express. js. Nechejte výchozí nastavení a pak vyberte **Další**.  
    Výchozí cíl nasazení je **Webová aplikace ve Windows**.  Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde.  

2. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 
   
   1. Zadejte název projektu.
      
   1. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte **Hotovo**.  
      Po několika minutách se řídicí panel DevOps Projects zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje vhled do vašeho úložiště kódu, kanál CI/CD a vaši aplikaci v Azure.
   
1. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.
   
   ![Zobrazení řídicího panelu pro kanál CI/CD](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

DevOps Projects automaticky nakonfigurovali aktivační událost sestavení a vydání CI.  Nyní jste připraveni spolupracovat s týmem v aplikaci Node. js pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHubu. Proveďte následující kroky pro zobrazení úložiště a změny kódu v aplikaci.

1. Na levé straně řídicího panelu DevOps Projects vyberte odkaz pro svou hlavní větev.  
Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonování úložiště, vyberte v pravém horním rohu v prohlížeči možnost **klonovat** .   
    Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně prohlížeče otevřete soubor **views/index. pug** .

1. Vyberte **Upravit**a pak proveďte změnu nadpisu H2.  
    Zadejte například příkaz **Začínáme hned s Azure DevOps Projects** nebo udělejte jinou změnu.

1. Vyberte **Potvrdit**a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.   
Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Prozkoumání kanálu Azure CI/CD

V předchozím kroku DevOps Projects automaticky nakonfigurovali úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Při seznámení s kanály sestavení a vydání proveďte následující kroky.

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
Tento odkaz otevře kartu prohlížeče a kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    Tato akce otevře nabídku, kde můžete spustit několik aktivit, jako je například zařazování nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
Sestavení provádí nejrůznější úlohy, jako je načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů, které se používají pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem vašeho kanálu buildu vyberte **Historie**.   
V podokně **Historie** se zobrazí záznam pro audit vašich nedávných změn pro sestavení.  Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.   
 Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.   
V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
 DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Na levé straně vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
Kanál vydané verze definuje proces vydání.

12. V části **Artefakty** vyberte **Zahodit**.  
    Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Vedle ikony **odkládacího umístění** vyberte **aktivační událost průběžného nasazování**.  
Tento kanál pro vydávání verzí má povoleno CD triggeru, který se spouští nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 


1. Na levé straně vyberte **úlohy**.   
Úkoly jsou aktivity, které proces nasazení provede. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.


1. Na pravé straně vyberte **Zobrazit vydané verze**.  
Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) vedle jedné z vašich vydání a pak vyberte **otevřít**.  
Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.   
Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s konkrétní nasazení.

1. Vyberte **Protokoly**.  
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použijte funkci **Odstranit** na řídicím panelu DevOps Projects.


## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
