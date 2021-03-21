---
title: 'Rychlý Start: vytvoření kanálu CI/CD pro Ruby na železnici pomocí Azure DevOps Starter'
description: Azure DevOps Starter usnadňuje začátek práce v Azure. Webovou aplikaci Ruby můžete spustit ve službě Azure v několika rychlých krocích.
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
ms.openlocfilehash: 3da2e116f1f58ca7a5c75da49f64bb8fc046e3ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548491"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-starter"></a>Vytvoření kanálu CI/CD pro Ruby na železnici pomocí Azure DevOps Starter

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro aplikaci Ruby on kolejnice pomocí Azure DevOps Starter. DevOps Starter zjednodušuje počáteční konfiguraci kanálu sestavení a vydání Azure DevOps.

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Azure DevOps Starter vytvoří kanál CI/CD v Azure Repos. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Starter také vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter** a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-app-and-azure-service"></a>Vybrat ukázkovou aplikaci a službu Azure

1. Vyberte ukázkovou aplikaci **Ruby** .

1. Vyberte architekturu aplikace **Ruby on Rails**. Až budete hotovi, vyberte **Další**.

1. Výchozí cíl nasazení je **Web App on Linux**.  Volitelně můžete vybrat **Web App for Containers**. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 
    
1. Vyberte cílovou službu podle vašeho výběru a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Vytvořte novou bezplatnou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte své předplatné a umístění Azure, zadejte název vaší aplikace a potom vyberte **Hotovo**.  
    Po několika minutách se řídicí panel DevOps Starter zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. 
    
    Řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure. Na pravé straně vyberte **Procházet** a zobrazte si spuštěnou aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Potvrďte změny kódu a spusťte CI/CD.

Azure DevOps Starter vytvoří úložiště Git v Azure Pipelines nebo GitHubu. Pokud chcete zobrazit úložiště a udělat v aplikaci změny kódu, udělejte toto:

1. Na řídicím panelu DevOps Starter vlevo vyberte odkaz pro svou hlavní větev. Odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL pro klonování úložiště, vyberte **klonovat** v pravém horním rohu. Úložiště Git můžete klonovat v rámci svého oblíbeného integrovaného vývojového prostředí (IDE). V několika dalších krocích můžete pomocí webového prohlížeče vytvořit a potvrdit změny kódu přímo do hlavní větve.

1. Vlevo otevřete soubor *app/views/Pages/home.html. erb* a pak vyberte **Upravit**.

1. Proveďte změnu souboru. Například upravte nějaký text v rámci jedné z značek DIV.

1. Vyberte **Potvrdit** a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter. Mělo by probíhat sestavení. Změny, které jste provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Projděte si kanál Azure Pipelines CI/CD.

Azure DevOps Starter automaticky nakonfiguruje úplný kanál CI/CD ve vaší organizaci Azure DevOps. Prozkoumejte kanál a podle potřeby ho upravte. Pokud se chcete seznámit s kanály sestavení a vydání Azure DevOps, postupujte takto:

1. Přejít na řídicí panel DevOps Starter.

1. V horní části vyberte **vytvořit kanály**. Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...). V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty** a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**. V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **triggery**.  DevOps Starter automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

1. Vyberte **sestavení a vydání** a pak vyberte **vydané verze**.  DevOps Starter vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**. Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali dříve, vytváří výstup, který se používá pro artefakt. 

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**. Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Na levé straně vyberte **úlohy**. Úlohy jsou aktivity, které proces nasazení provádí. V tomto příkladu byl vytvořen úkol pro nasazení do Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**. Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. 

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit instanci Azure App Service a související prostředky, které jste vytvořili v rámci tohoto rychlého startu. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Další informace o změnách kanálů sestavení a vydání, které vyhovují potřebám vašeho týmu, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](/azure/devops/pipelines/release/define-multistage-release-process)