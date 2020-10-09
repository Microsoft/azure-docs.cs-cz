---
title: 'Rychlý Start: vytvoření kanálu CI/CD pro PHP pomocí Azure DevOps Starter'
description: DevOps Starter usnadňuje začátek práce v Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
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
ms.custom: mvc
ms.openlocfilehash: 99c8bc00beb7b189b200e0b02435262ae34835d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856088"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-starter"></a>Vytvoření kanálu CI/CD pro PHP pomocí Azure DevOps Starter

Azure DevOps Starter prezentuje zjednodušené prostředí, které vytváří prostředky Azure a nastavuje v Azure Pipelines kanál pro průběžnou integraci (CI) a průběžné doručování (CD) pro vaši aplikaci PHP.  

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

 DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit bezplatnou novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se k webu [Microsoft Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter**a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci v PHP. Ukázka v PHP zahrnuje výběr několika architektur aplikace. Výchozí architektura ukázky je Laravel.
        
1. Nechejte výchozí nastavení a pak vyberte **Další**.  

1. Výchozí cíl nasazení je Web App for Containers. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde.  Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

    1. Vyberte název projektu v Azure DevOps. 
    
    1. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte **Hotovo**.  
    
    Po několika minutách se řídicí panel DevOps Starter zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.  
        
2. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Starter automaticky nakonfiguroval aktivační událost sestavení a vydání CI.  Teď jste připraveni při práci na aplikaci v PHP spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

 DevOps Starter vytvoří úložiště Git v Azure Repos nebo GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, proveďte následující kroky:

1. Na levé straně řídicího panelu DevOps Starter vyberte odkaz pro svou hlavní větev. Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonu úložiště, v pravé horní části prohlížeče vyberte **Clone** (Klonovat). Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V několika dalších krocích můžete pomocí webového prohlížeče vytvořit a potvrdit změny kódu přímo do hlavní větve.

1. Na levé straně přejdete do souboru **Resources/views/Welcome. Blade. php** .

1. Vyberte **Upravit**a pak proveďte změnu textu.  Změňte například text v některé ze značek div.

1. Vyberte **Potvrdit**a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter. Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Kontrola kanálu CI/CD

 DevOps Starter automaticky nakonfiguruje úplný kanál CI/CD v Azure Pipelines. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání, postupujte následovně:

1. V horní části řídicího panelu DevOps Starter vyberte **vytvořit kanály**. Tento odkaz otevře kartu prohlížeče a kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte **tři tečky** (...). V nabídce se zobrazí několik možností, jako je například zařazování nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení spouští různé úlohy, jako například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů, které se používají pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na výstižnější, vyberte, **uložte & frontu**a potom vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  V podokně **Historie** se zobrazí záznam o tom, jak vaše poslední změny sestavení provést. Azure Pipelines sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **triggery**. DevOps Starter automaticky vytvořil Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  DevOps Starter vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**. Kanál verze obsahuje kanál, který definuje proces vydání. 

12. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **odkládacího umístění** vyberte **aktivační událost průběžného nasazování**. Tento kanál verze má povolený Trigger CD, který spouští nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Na levé straně vyberte **úlohy**. Úkoly jsou aktivity, které provádí proces nasazení. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle jedné z vašich vydání a pak vyberte  **otevřít**. Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například souhrn verze, související pracovní položky a testy.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružená ke konkrétnímu nasazení. 

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete Azure App Service a další související prostředky odstranit. Použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)