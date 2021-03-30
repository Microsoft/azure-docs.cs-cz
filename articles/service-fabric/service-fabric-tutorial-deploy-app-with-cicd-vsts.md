---
title: Nasazení aplikace s využitím CI a Azure Pipelines
description: V tomto kurzu se naučíte, jak nastavit průběžnou integraci a nasazování pro aplikaci Service Fabric pomocí Azure Pipelines.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a26cfaca466e01b154c65b27895f3004f6320e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326333"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Kurz: Nasazení aplikace s CI/CD do clusteru Service Fabric

Tento kurz je čtvrtou částí série a popisuje, jak nastavit průběžnou integraci a nasazování pro aplikaci Azure Service Fabric pomocí Azure Pipelines.  Potřebujete existující aplikaci Service Fabric. Jako příklad se používá aplikace vytvořená v tématu [Sestavení aplikace .NET](service-fabric-tutorial-create-dotnet-app.md).

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření kanálu buildu v Azure Pipelines
> * Vytvoření kanálu verze v Azure Pipelines
> * Automatické nasazení a upgrade aplikace

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření aplikace Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md)
> * [Nasazení aplikace do vzdáleného clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Přidání koncového bodu HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Nakonfigurovat CI/CD s využitím služby Azure Pipelines
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Nainstalujte Visual Studio 2019](https://www.visualstudio.com/) a nainstalujte úlohy vývoje pro vývoj a vývoj pro **Azure** a vývoj **webů** .
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).
* Vytvořte v Azure cluster Service Fabric s Windows, například [podle tohoto kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* Vytvořte [organizaci Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student). To vám umožní vytvořit projekt ve službě Azure DevOps a použít Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové aplikace Voting

Pokud jste nevytvořili hlasovací ukázkovou aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Příprava profilu publikování

Teď, když jste [vytvořili aplikaci](service-fabric-tutorial-create-dotnet-app.md) a [nasadili jste tuto aplikaci do Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), jste připraveni nastavit průběžnou integraci.  Nejprve Připravte profil publikování v rámci aplikace pro použití procesem nasazení, který se provádí v rámci Azure Pipelines.  Profil publikování by měl být nakonfigurovaný tak, aby cílil na cluster, který jste předtím vytvořili.  Spusťte sadu Visual Studio a otevřete existující projekt aplikace Service Fabric.  V **Průzkumníku řešení** klikněte pravým tlačítkem na aplikaci a vyberte **Publikovat...**

Zvolte v rámci projektu aplikace cílový profil, který se použije pro pracovní postup průběžné integrace, například Cloud.  Zadejte koncový bod připojení clusteru.  Zaškrtněte políčko **Upgradovat aplikaci**, aby se vaše aplikace upgradovala pro každé nasazení v Azure DevOps.  Kliknutím na hypertextový odkaz **Uložit** uložte nastavení do profilu publikování a pak kliknutím na **Zrušit** zavřete dialogové okno.

![Nasdílení profilu][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Sdílení řešení sady Visual Studio do nového úložiště Gitu Azure DevOps

Nasdílejte zdrojové soubory své aplikace do projektu v Azure DevOps, abyste mohli generovat buildy.

Pro svůj projekt vytvořte nové místní úložiště Git, a to tak, že  ->  na stavovém řádku v pravém dolním rohu sady Visual Studio vyberete Přidat do správy zdrojového kódu **Git** .

V zobrazení **Nasdílet změny** v **Team Exploreru** vyberte v části **Doručovat do Azure DevOps** tlačítko **Publikovat úložiště Git**.

![Snímek obrazovky okna Team Explorer-synchronizace v aplikaci Visual Studio. Tlačítko publikovat na úložiště Git je v části push do Azure DevOps zvýrazněné.][push-git-repo]

Ověřte svůj e-mail a v rozevíracím seznamu **Doména Azure DevOps** vyberte svůj účet. Zadejte název svého úložiště a vyberte **Publikovat úložiště**.

![Snímek obrazovky s nastavením nabídky pro vložení do Azure DevOps pomocí zvýrazněného tlačítka pro E-mail, účet, název úložiště a publikování][publish-code]

Publikováním úložiště se ve vašem účtu vytvoří nový projekt se stejným názvem jako místní úložiště. Pokud chcete úložiště vytvořit v existujícím projektu, klikněte na **Upřesnit** vedle **názvu úložiště** a vyberte projekt. Svůj kód můžete zobrazit na webu výběrem možnosti **Podívejte se na webu**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurace průběžného doručování s Azure Pipelines

Azure Pipelines kanál sestavení popisuje pracovní postup, který se skládá ze sady kroků sestavení, které jsou spouštěny postupně. Vytvořte kanál buildu, který vytvoří balíček aplikace Service Fabric a další artefakty pro nasazení do clusteru Service Fabric. Přečtěte si další informace o [kanálech buildu Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Kanál verze Azure Pipelines popisuje pracovní postup, který nasadí balíček aplikace do clusteru. Při společném použití provedou kanál buildu a kanál verze celý pracovní postup od zdrojových souborů až po spuštění aplikace v clusteru. Přečtěte si další informace o [Azure Pipelinesch kanálech vydávání verzí](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Vytvoření kanálu buildu

Otevřete webový prohlížeč a přejděte do nového projektu na adrese: [https://&lt;váš_účet&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Vyberte kartu **kanály** a pak **sestavení** a pak klikněte na **Nový kanál**.

![Nový kanál][new-pipeline]

Vyberte **Azure Repos Git** jako zdroj, **hlasovací** týmový projekt, **hlasovací** úložiště a **hlavní** výchozí větev pro ruční a plánovaná sestavení.  Pak klikněte na **pokračovat**.

![Vybrat úložiště][select-repo]

V části **Vybrat šablonu** vyberte šablonu **aplikace Azure Service Fabric** a klikněte na **použít**.

![Výběr šablony sestavení][select-build-template]

Do pole **úlohy** zadejte "hostované VS2017" jako **fond agentů**.

![Výběr úloh][save-and-queue]

V části **Triggery** povolte průběžnou integraci zaškrtnutím políčka **Povolit průběžnou integraci**. Ve **filtrech větví** je **specifikace větve** standardně **hlavní**. Pokud chcete ručně spustit sestavení, vyberte **Uložit a zařadit do fronty**.

![Výběr triggerů][save-and-queue2]

Sestavení se aktivují také pro nasdílení změn nebo vrácení se změnami. Chcete-li zjistit průběh sestavení, přepněte na kartu **sestavení** .  Jakmile ověříte, že se sestavení úspěšně spustí, definujte kanál verze, který nasadí vaši aplikaci do clusteru.

### <a name="create-a-release-pipeline"></a>Vytvoření kanálu verze

Vyberte kartu **kanály** a pak **verze** a potom **+ Nový kanál**.  V části **Vybrat šablonu** vyberte ze seznamu šablonu **Nasazení Azure Service Fabric** a pak klikněte na **Použít**.

![Výběr šablony vydání][select-release-template]

Vyberte **úlohy** -> **prostředí 1** a potom **+ nové** a přidejte nové připojení k clusteru.

![Přidání připojení clusteru][add-cluster-connection]

V zobrazení **Přidat nové připojení Service Fabric** vyberte ověřování **Na základě certifikátů** nebo **Azure Active Directory**.  Zadejte název připojení mysftestcluster a koncový bod clusteru tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (nebo koncový bod clusteru, do kterého nasazujete).

Pro ověřování založené na certifikátech přidejte **kryptografický otisk certifikátu** serveru použitého k vytvoření clusteru.  V části **Klientský certifikát** přidejte kódování Base64 souboru klientského certifikátu. V místním okně nápovědy pro toto pole najdete informace o postupu pro získání reprezentace certifikátu v kódování Base64. Přidejte také **Heslo** pro certifikát.  Pokud nemáte samostatný klientský certifikát, můžete použít certifikát clusteru nebo serveru.

Pokud použijete přihlašovací údaje Azure Active Directory, přidejte **Kryptografický otisk certifikátu serveru** použitého k vytvoření clusteru a do polí **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje, pomocí kterých se chcete připojovat ke clusteru.

Kliknutím na **Přidat** uložte připojení clusteru.

Dále do kanálu přidejte artefakt buildu, aby kanál verze dokázal najít výstup z buildu. Vyberte **Kanál** a pak **Artefakty**->**+ Přidat**.  V oblasti **Zdroj (definice sestavení)** vyberte dříve vytvořený kanál buildu.  Kliknutím na **Přidat** uložte artefakt sestavení.

![Přidání artefaktu][add-artifact]

Povolte trigger průběžného nasazování, aby se po dokončení sestavení automaticky vytvořila vydaná verze. Klikněte na ikonu blesku v artefaktu, povolte trigger a kliknutím na **Uložit** uložte kanál verze.

![Povolení triggeru][enable-trigger]

Výběrem **+ vydaná** verze  ->  **vytvořit vydání**  ->  **vytvořit vytvořte** vydanou verzi ručně. Průběh vydání můžete sledovat na kartě **Vydání**.

Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Potvrzení a nasdílení změn, aktivace vydání

Ověřte fungování kanálu kontinuální integrace tím, že do Azure DevOps vrátíte se změnami nějaký kód.

Při psaní kódu sada Visual Studio automaticky sleduje provedené změny. Potvrďte změny do svého místního úložiště Git tím, že vyberete ikonu probíhajících změn (![Ikona probíhajících změn zobrazuje tužku a číslo.][pending]) na stavovém řádku v pravém dolním rohu.

V zobrazení **Změny** v Team Exploreru přidejte zprávu s popisem vaší aktualizace a potvrďte provedené změny.

![Potvrdit vše][changes]

Vyberte ikonu nepublikovaných změn (![Nepublikované změny][unpublished-changes]) na stavovém řádku nebo zobrazení Synchronizace v Team Exploreru. Vyberte možnost **push** a aktualizujte kód v Azure Pipelines.

![Nasdílení změn][push]

Vložení změn do Azure Pipelines automaticky aktivuje sestavení.  Po úspěšném dokončení kanálu buildu se automaticky vytvoří verze a začne se upgradovat aplikace v clusteru.

Pokud chcete zkontrolovat průběh sestavení, přepněte v **Team Exploreru** v sadě Visual Studio na kartu **Sestavení**.  Jakmile ověříte, že se build úspěšně spouští, definujte kanál verze, který nasadí vaši aplikaci do clusteru.

Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170815.3.

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer spuštěném v okně prohlížeče Verze aplikace "1.0.0.20170815.3" je zvýrazněna.][sfx1]

## <a name="update-the-application"></a>Aktualizace aplikace

Proveďte v aplikaci změny kódu.  Podle předchozích kroků změny uložte a potvrďte.

Jakmile začne upgrade aplikace, můžete průběh upgradu sledovat v Service Fabric Exploreru:

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer Tím se zvýrazní zpráva o stavu upgrade a probíhá upgrade.][sfx2]

Upgrade aplikace může trvat několik minut. Po dokončení upgradu bude aplikace používat další verzi.  V tomto příkladu verzi 1.0.0.20170815.4.

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer spuštěném v okně prohlížeče Je zvýrazněna aktualizovaná verze aplikace "1.0.0.20170815.4".][sfx3]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření kanálu buildu
> * Vytvoření kanálu verze
> * Automatické nasazení a upgrade aplikace

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
