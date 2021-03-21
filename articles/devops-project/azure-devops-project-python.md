---
title: 'Rychlý Start: vytvoření kanálu CI/CD pro Python pomocí Azure DevOps Starter'
description: DevOps Starter usnadňuje začátek práce s Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 99ca60f2610d34d194c50e92501c0126a7c80323
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566545"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>Vytvoření kanálu CI/CD pro Python pomocí Azure DevOps Starter

V tomto rychlém startu použijete zjednodušené prostředí Azure DevOps Starter k nastavení kanálu průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci v Pythonu v Azure Pipelines. K nastavení všeho, co potřebujete pro vývoj, nasazení a monitorování vaší aplikace, můžete použít Azure DevOps Starter. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Účet [Azure DevOps](https://azure.microsoft.com/services/devops/) a organizace.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Starter také vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

1. Do vyhledávacího pole zadejte **DevOps Starter** a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci v Pythonu. Ukázka v Pythonu zahrnuje výběr několika architektur aplikace.

1. Výchozí architektura ukázky je Django. Nechejte výchozí nastavení a pak vyberte **Další**. Výchozí cíl nasazení je Web App for Containers. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

3. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Vytvořte novou organizaci služby Azure DevOps nebo zvolte některou existující organizaci. 

    1. Do Azure DevOps zadejte název vašeho projektu.  

    1. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte **Hotovo**.  
    
     Po několika minutách se počáteční řídicí panel zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.  
    
2. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projects automaticky konfiguruje aktivační událost sestavení a vydání CI. Nyní jste připraveni spolupracovat s týmem v aplikaci Python pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Starter vytvoří úložiště Git v Azure Repos nebo GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte následovně: 

1. Na levé straně řídicího panelu DevOps Starter vyberte odkaz pro svou hlavní větev. Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonu úložiště, v pravé horní části prohlížeče vyberte **Clone** (Klonovat). Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V několika dalších krocích můžete pomocí webového prohlížeče vytvořit a potvrdit změny kódu přímo do hlavní větve.

1. Na levé straně přejdete do souboru **App/Templates/App/index.html** .

1. Vyberte **Upravit** a proveďte změnu nějakého textu. Změňte například text v některé ze značek div.

1. Vyberte **Potvrdit** a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter. Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Kontrola kanálu CI/CD

V předchozím kroku DevOps Starter automaticky nakonfigurovali úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. V horní části řídicího panelu DevOps Starter vyberte **vytvořit kanály**. Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte **tři tečky** (...). V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty** a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**. Zobrazí se protokol auditu nedávno provedených změn sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **triggery**. DevOps Starter automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

1. Vyberte **sestavení a vydání** a pak zvolte **vydané** verze.   
 DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky vedle vašeho kanálu pro vydání a pak vyberte **Upravit**. Kanál vydané verze definuje proces vydání.  
        
12. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **odkládacího umístění** vyberte **aktivační událost průběžného nasazování**. Kanál verze má povolený aktivační událost CD, který spouští nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Na levé straně vyberte **úlohy**. Úkoly jsou aktivity, které provádí proces nasazení. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.  
        
1. Vyberte tři tečky (...) vedle jedné z vašich vydání a pak vyberte **otevřít**. Z tohoto zobrazení je k dispozici několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružená ke konkrétnímu nasazení. 

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a související prostředky můžete odstranit, i když je už nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](/azure/devops/pipelines/release/define-multistage-release-process)