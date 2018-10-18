---
title: Nasazení aplikace ASP.NET Core do služby Azure Kubernetes Service (AKS) pomocí projektu Azure DevOps | Kurz Azure DevOps Services
description: Díky projektu Azure DevOps můžete v Azure snadno začít pracovat. Projekt Azure DevOps umožňuje snadno a v několika rychlých krocích nasadit aplikaci ASP.NET Core s využitím služby Azure Kubernetes Service (AKS).
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 55ea101b3a03fdb7fc375c4594cab36d4cd79978
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299113"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Kurz: Nasazení aplikace ASP.NET Core do služby Azure Kubernetes Service (AKS) pomocí projektu Azure DevOps

Azure DevOps Project představuje zjednodušené prostředí, kam můžete přenést váš stávající kód a úložiště Git a kde si můžete vybrat jednu z ukázkových aplikací pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Projekt Azure DevOps automaticky vytvoří prostředky Azure, jako je služba AKS, vytvoří a nakonfiguruje kanál verze ve službě Azure DevOps Services zahrnující kanál buildu a verze pro CI/CD a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a AKS
> * Konfigurace služby Azure DevOps Services a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI služby Azure DevOps Services
> * Prozkoumání kanálu CD služby DevOps Services
> * Potvrzení změn do Gitu a automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a AKS

Projekt Azure DevOps vytvoří kanál CI/CD v Azure.  Můžete vytvořit **novou organizaci služby Azure DevOps Services** nebo použít **existující organizaci**.  Azure DevOps Project také vytvoří **prostředky Azure**, jako je cluster AKS, v **předplatném Azure** podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu zvolte ikonu **Vytvořit prostředek** a pak vyhledejte **Projekt DevOps**.  Zvolte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-aks/fullbrowser.png)

1. Vyberte **.NET** a pak zvolte **Další**.

1. V části **Zvolte architekturu aplikace** vyberte **ASP.NET Core** a pak vyberte **Další**.

1. Vyberte **Kubernetes Service** a pak zvolte **Další**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurace služby Azure DevOps Services a předplatného Azure

1. Vytvořte **novou** organizaci služby Azure DevOps nebo zvolte některou **existující** organizaci.  Zvolte **název** projektu.  

1. Vyberte své **předplatné Azure**.

1. Výběrem odkazu **Změnit** zobrazte další nastavení konfigurace Azure a identifikujte **počet uzlů** pro **cluster Kubernetes**.  Tady máte k dispozici různé možnost konfigurace typu a umístění služeb Azure.
 
1. Opusťte oblast konfigurace Azure a zvolte **Hotovo**.

1. Dokončení procesu bude trvat několik minut.  V úložišti Gitu Azure Repos ve vaší organizaci služby Azure DevOps Services se vytvoří ukázková aplikace ASP.NET Core, vytvoří se cluster AKS, spustí se kanál CI/CD a vaše aplikace se nasadí do Azure.  

    Po dokončení se na webu Azure Portal načte **řídicí panel projektu** Azure DevOps.  Na **řídicí panel Azure DevOps Project** můžete přejít také přímo z části **Všechny prostředky** na webu **Azure Portal**.  

    Tento řídicí panel umožňuje zobrazit **úložiště kódu** Azure Repos, **kanál CI/CD služby Azure DevOps Services**a **cluster AKS**.  V kanálu služby Azure DevOps Services můžete konfigurovat další možnosti CI/CD.  Na pravé straně řídicího panelu vyberte **Procházet** a zobrazte vaši spuštěnou aplikaci.

## <a name="examine-the-aks-cluster"></a>Prozkoumání clusteru AKS

Azure DevOps Project automaticky nakonfiguruje cluster AKS.  Cluster můžete prozkoumat a upravit.  Postupujte podle následujících kroků a seznamte se s AKS.

1. Přejděte na **řídicí panel Azure DevOps Project**.

1. Na pravé straně řídicího panelu projektu DevOps vyberte **Služba Kubernetes**.

1. Otevře se okno pro cluster AKS.  V tomto zobrazení můžete provádět různé akce, například **monitorovat stav kontejneru**, **prohledávat protokoly** a otevřít **řídicí panel Kubernetes**.

1. Na pravé straně obrazovky vyberte **Zobrazit řídicí panel Kubernetes**.  Volitelně postupujte podle pokynů k otevření řídicího panelu Kubernetes.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Prozkoumání kanálu CI služby Azure DevOps Services

Projekt Azure DevOps ve vaší organizaci služby Azure DevOps automaticky nakonfiguruje kanál Azure CI/CD.  Kanál můžete prozkoumat a upravit.  Pomocí následujícího postupu se seznamte s kanálem CI/CD služby Azure DevOps Services.

1. Přejděte na **řídicí panel Azure DevOps Project**.

1. V **horní** části **řídicího panelu projektu Azure DevOps** vyberte **Kanály sestavení**.  Tento odkaz otevře na nové záložce prohlížeče kanál buildu Azure DevOps Services pro váš nový projekt.

1. Přesuňte kurzor myši napravo od kanálu buildu vedle pole **Stav**. Vyberte **tři tečky**, které se zobrazí.  Tato akce otevře nabídku, ve které můžete provést několik aktivit, jako je **zařazení nového buildu do fronty**, **pozastavení buildu** a **úprava kanálu buildu**.

1. Vyberte **Upravit**.

1. V tomto zobrazení můžete **prozkoumat různé úlohy**.  Build provádí různé úlohy, jako je načtení zdrojových kódů z úložiště Gitu Azure DevOps Services Repos, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu vyberte **název kanálu buildu**.

1. Změňte **název** kanálu buildu na něco výstižnějšího.  Vyberte **Uložit a zařadit do fronty** a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  Zobrazí se protokol auditu nedávno provedených změn sestavení.  Azure DevOps Services uchovává informace o všech změnách provedených v kanálu buildu a umožňuje porovnávat verze.

1. Vyberte **Triggery**.  Projekt Azure DevOps automaticky vytvořil trigger CI a každé potvrzení v úložišti spustí nový build.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  V závislosti na vašem scénáři můžete určit zásady pro zachování nebo odebrání určitého počtu sestavení.

## <a name="examine-the-azure-devops-services-cd-release-pipeline"></a>Prozkoumání kanálu verze CD služby Azure DevOps Services

Projekt Azure DevOps automaticky vytvoří a nakonfiguruje potřebné kroky pro nasazení z organizace služby Azure DevOps Services do předplatného Azure.  Mezi tyto kroky patří konfigurace připojení ke službě Azure za účelem ověření služby Azure DevOps Services vůči vašemu předplatnému Azure.  Automatizace také vytvoří kanál verze služby Azure DevOps Services, který zajišťuje průběžné nasazování do Azure.  Pomocí níže uvedeného postupu dále prozkoumejte kanál verze služby Azure DevOps Services.

1. Vyberte **Sestavení a vydání** a zvolte **Verze**.  Projekt Azure DevOps vytvořil kanál verze služby Azure DevOps Services pro správu nasazení do Azure.

1. Na levé straně prohlížeče vyberte **tři tečky** vedle kanálu verze a pak zvolte **Upravit**.

1. Kanál verze obsahuje **kanál**, který definuje proces vydání.  V části **Artefakty** vyberte **Zahodit**.  Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Napravo od ikony **Zahodit** vyberte **Trigger průběžného nasazování** (**ikona** blesku).  Tento kanál verze obsahuje povolený trigger průběžného nasazování.  Tento trigger vytvoří nasazení pokaždé, když bude k dispozici nový artefakt sestavení.  Volitelně můžete trigger zakázat, aby pak vaše nasazení vyžadovala ruční spuštění. 

1. Na pravé straně prohlížeče vyberte **Zobrazit verze**.  Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte **tři tečky** vedle některé z vydaných verzí a zvolte **Otevřít**.  Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například **souhrn verze**, **související pracovní položky** a **testy**.

1. Vyberte **Potvrzení**.  Toto zobrazení ukazuje potvrzení kódu související s konkrétním nasazením. Můžete porovnat vydané verze a zobrazit rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  Protokoly obsahují užitečné informace o procesu nasazení.  Můžete je zobrazit během nasazení i po nich.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Potvrzení změn do služby Azure DevOps Services a automatické nasazení do Azure 

 > [!NOTE]
 > Následujícím postupem se provede test kanálu CI/CD prostřednictvím prosté změny textu ve vaší webové aplikaci.

Teď jste připraveni při práci na vaší aplikaci spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.  Každá změna v úložišti Gitu Azure DevOps Services spustí build ve službě Azure DevOps Services a kanál CD nasadí vaše změny do Azure.  K potvrzení změn v úložišti můžete použít následující postup nebo jiné techniky.  Například můžete **naklonovat** úložiště Git do svého oblíbeného nástroje nebo integrovaného vývojového prostředí (IDE) a pak do tohoto úložiště nasdílet změny.

1. V nabídce Azure DevOps Services vyberte **Kód** a pak **Soubory** a přejděte do svého úložiště.
1. Přejděte do adresáře **Views\Home**, vyberte **tři tečky** vedle souboru **Index.cshtml** a pak zvolte **Upravit**.

1. Proveďte změnu souboru například úpravou textu uvnitř některé ze **značek div**.  V pravém horním rohu vyberte **Potvrdit**.  Znovu vyberte **Potvrdit**, aby se změna nasdílela. 

1. Za chvíli se **spustí build ve službě Azure DevOps Services** a pak se provede vydání za účelem nasazení změn.  **Stav buildu** můžete monitorovat na řídicím panelu projektu DevOps nebo v prohlížeči s vaší organizací služby Azure DevOps Services.

1. Po dokončení vydání **aktualizujte aplikaci** v prohlížeči a ověřte, že se zobrazí provedené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 > [!NOTE]
 > Následujícím postupem se trvale odstraní prostředky.  Tuto funkci použijte pouze po důkladném prostudování výzev.

Pokud provádíte testování, můžete vyčistit prostředky a zamezit tak nabíhání poplatků.  Pokud už je nepotřebujete, můžete cluster Azure Kubernetes a související prostředky vytvořené v tomto kurzu odstranit pomocí funkce **Odstranit** na řídicím panelu Azure DevOps Project.  **Buďte opatrní**, protože funkce odstranění zničí všechna data vytvořená projektem Azure DevOps v Azure i ve službě Azure DevOps Services. Po odstranění dat je nebudete moct načíst.

1. Na webu **Azure Portal** přejděte do služby **Azure DevOps Project**.
2. Na **pravé horní** straně řídicího panelu vyberte **Odstranit**.  Po přečtení výzvy vyberte **Ano** a **trvale odstraňte** prostředky.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály.  Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a AKS
> * Konfigurace služby Azure DevOps Services a předplatného Azure 
> * Prozkoumání clusteru AKS
> * Prozkoumání kanálu CI služby Azure DevOps Services
> * Prozkoumání kanálu CD služby DevOps Services
> * Potvrzení změn do Gitu a automatické nasazení do Azure
> * Vyčištění prostředků

Další informace o používání řídicího panelu Kubernetes najdete tady:

> [!div class="nextstepaction"]
> [Používání řídicího panelu Kubernetes](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
