---
title: 'Úvodní příručka: Vytvoření kanálu CI/CD pro rozhraní .NET s azure devops projects'
description: Azure DevOps Projects usnadňuje schůdky v Azure. Pomůže vám v několika rychlých krocích spustit aplikaci .NET v libovolné službě Azure.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
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
ms.openlocfilehash: 7d2ccdfa1fe553d0795a82856dd255f4a54138bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "70898029"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro rozhraní .NET s azure devops projects

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro vaše .NET jádro nebo ASP.NET aplikace s DevOps projekty. DevOps Projects zjednodušuje počáteční konfiguraci kanálu sestavení a vydání v Azure Pipelines.

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projekty vytvoří kanál CI/CD v Azure DevOps. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte na levém **navigačním panelu** vytvořit ikonu zdroje a vyhledejte **položku DevOps Projects**.  

3.  Vyberte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci **.NET**. Ukázky .NET zahrnují výběr open source architektury ASP.NET nebo multiplatformní architektury .NET Core.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Tato ukázka představuje aplikaci ASP.NET Core MVC. Vyberte rozhraní **.NET Core** aplikace a pak vyberte **Další**.    
    
3. Vyberte **Windows Web App** jako cíl nasazení a pak vyberte **Další**. Volitelně můžete pro nasazení zvolit další služby Azure. Rozhraní aplikace, které jste zvolili dříve, určuje typ cíle nasazení služby Azure je k dispozici zde.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Zadejte **název projektu**.

2. Vytvořte novou bezplatnou **organizaci Azure DevOps nebo** zvolte existující organizaci z rozevíracího přehledu.

3. Vyberte **předplatné Azure**, zadejte název webové **aplikace** nebo převezměte výchozí a pak vyberte **Hotovo**. Po několika minutách se na webu Azure Portal zobrazí přehled nasazení projektů DevOps. 

4. Chcete-li zobrazit řídicí panel projektu DevOps Project, vyberte **možnost Přejít na zdroj.** V pravém horním rohu připněte **Project** na řídicí panel, abyste k němu měli rychlý přístup. Ukázková aplikace se nastaví v repo ve vaší **organizaci Azure DevOps**. Sestavení se spustí a vaše aplikace se nasadí do Azure.

5. Řídicí panel poskytuje přehled o vašem kódu, kanálu CI/CD a vaší aplikaci v Azure. Vpravo v části Prostředky Azure vyberte **Procházet** a zobrazte spuštěnou aplikaci.

   ![Zobrazení řídicího panelu](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvořili úložiště Git v Azure Repos nebo GitHub. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte takto:

1. Na levé straně řídicího panelu Projekty DevOps vyberte odkaz pro **hlavní** větev. Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

2. V následujících několika krocích můžete pomocí webového prohlížeče provádět a porušovat změny kódu přímo do **hlavní** větve. Můžete také klonovat úložiště Git ve svém oblíbeném IDE výběrem **Klonování** v pravém horním rohu stránky úložiště. 

3. Vlevo přejděte do struktury souborů aplikace **application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Vyberte **Upravit**a proveďte změnu nadpisu h2. Zadejte například **Začít hned s Azure DevOps Projects** nebo proveďte nějakou jinou změnu.

      ![Úpravy kódu](_img/azure-devops-project-aspnet-core/codechange.png)

5. Vyberte **Potvrdit**, zanechat komentář a znovu vyberte **Potvrdit.**

6. V prohlížeči přejděte na řídicí panel projektu Azure DevOps Project.  Teď by se mělo zobrazit probíhající sestavení. Provedené změny jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Zkontrolujte kanál CI/CD

V předchozím kroku Azure DevOps Projekty automaticky nakonfiguroval úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Postupujte podle následujících kroků, abyste se seznámili s kanály sestavení a vydání Azure DevOps.

1. V horní části řídicího panelu Projekty DevOps vyberte **Sestavit kanály**.  
Tento odkaz otevře kartu prohlížeče a Azure DevOps sestavení kanálu pro váš nový projekt.

1. Vyberte tři tečky (...).  Tato akce otevře nabídku, kde můžete spustit několik aktivit, jako je například řazení do fronty nové sestavení, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

    ![Kanál buildu](_img/azure-devops-project-aspnet-core/builddef.png)

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení.  
 Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího, vyberte **Uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
V podokně **Historie** se zobrazí záznam auditu posledních změn pro sestavení.  Azure Pipelines sleduje všechny změny, které jsou provedeny v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**.  
DevOps Projekty automaticky vytvořili aktivační událost CI a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  
V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1.  Vlevo vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
Kanál verze obsahuje kanál, který definuje proces vydání.  

1. V části **Artefakty** vyberte **Zahodit**.  Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Vedle ikony **Přetažení** vyberte **aktivační událost průběžného nasazení**.  
Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění.  

1. Vlevo vyberte **Úkoly**.   
Úkoly jsou aktivity, které provádí proces nasazení. V tomto příkladu byla vytvořena úloha pro nasazení do služby Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydání**. Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) vedle jedné z vašich verzí a pak vyberte **Otevřít**.  
Existuje několik nabídek k prozkoumání, jako je například souhrn vydání, přidružené pracovní položky a testy.


1. Vyberte **Potvrzení**.   
Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k určitému nasazení. 

1. Vyberte **Protokoly**.  
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už je nepotřebujete. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

Další informace o úpravě kanálu buildu a verze tak, aby splňovaly požadavky vašeho týmu, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
