---
title: Nasazení aplikace typu kontejner pomocí CI/CD
description: V tomto kurzu se naučíte, jak nastavit průběžnou integraci a nasazování pro aplikaci Azure Service Fabric Container pomocí sady Visual Studio Azure DevOps.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018847"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Kurz: Nasazení aplikace typu kontejner s CI/CD do clusteru Service Fabric

Tento kurz je druhou částí série a popisuje, jak nastavit průběžnou integraci a nasazování pro aplikaci Azure Service Fabric Container pomocí sady Visual Studio a Azure DevOps.  Je zapotřebí existující aplikace Service Fabric; jako příklad se používá aplikace vytvořená v článku [Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](service-fabric-host-app-in-a-container.md).

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření definice sestavení v aplikaci Visual Studio Team Explorer
> * Vytvoření definice verze v aplikaci Visual Studio Team Explorer
> * Automatické nasazení a upgrade aplikace

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Máte cluster v Azure. Případně ho můžete [vytvořit pomocí tohoto kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* [Nasadili jste do něj kontejnerizovanou aplikaci](service-fabric-host-app-in-a-container.md).

## <a name="prepare-a-publish-profile"></a>Příprava profilu publikování

Teď, když jste [nasadili aplikaci typu kontejner](service-fabric-host-app-in-a-container.md), jste připraveni nastavit kontinuální integraci.  Nejprve ve své aplikaci připravte profil publikování použitý procesem nasazení, který se provádí v rámci Azure DevOps.  Profil publikování by měl být nakonfigurovaný tak, aby cílil na cluster, který jste předtím vytvořili.  Spusťte sadu Visual Studio a otevřete existující projekt aplikace Service Fabric.  V **Průzkumníku řešení** klikněte pravým tlačítkem na aplikaci a vyberte **Publikovat...**

Zvolte v rámci projektu aplikace cílový profil, který se použije pro pracovní postup průběžné integrace, například Cloud.  Zadejte koncový bod připojení clusteru.  Zaškrtněte políčko **Upgradovat aplikaci**, aby se vaše aplikace upgradovala pro každé nasazení v Azure DevOps.  Kliknutím na hypertextový odkaz **Uložit** uložte nastavení do profilu publikování a pak kliknutím na **Zrušit** zavřete dialogové okno.

![Nasdílení profilu][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Sdílení řešení sady Visual Studio do nového úložiště Gitu Azure DevOps

Sdílejte zdrojové soubory aplikace do týmového projektu v Azure DevOps, abyste mohli generovat sestavení.

Pro svůj projekt vytvořte nové místní úložiště Git, a to tak, že  ->  na stavovém řádku v pravém dolním rohu sady Visual Studio vyberete Přidat do správy zdrojového kódu **Git** .

V zobrazení **Nasdílet změny** v **Team Exploreru** vyberte v části **Doručovat do Azure DevOps** tlačítko **Publikovat úložiště Git**.

![Snímek obrazovky okna Team Explorer-synchronizace v aplikaci Visual Studio. V části push do Azure DevOps se zvýrazní tlačítko publikovat na úložiště Git.][push-git-repo]

Ověřte svůj e-mail a v rozevíracím seznamu **účet** vyberte svou organizaci. Je možné, že budete muset nastavit organizaci, pokud ji ještě nemáte. Zadejte název svého úložiště a vyberte **Publikovat úložiště**.

![Snímek obrazovky okna push do Azure DevOps. Zvýrazní se nastavení pro E-mail, účet, název úložiště a tlačítko publikovat úložiště.][publish-code]

Publikováním úložiště se ve vašem účtu vytvoří nový týmový projekt se stejným názvem jako místní úložiště. Pokud chcete úložiště vytvořit v existujícím týmovém projektu, klikněte na **Upřesnit** vedle **názvu úložiště** a vyberte týmový projekt. Svůj kód můžete zobrazit na webu výběrem možnosti **Podívejte se na webu**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurace průběžného doručování s Azure Pipelines

Definice sestavení Azure DevOps popisuje pracovní postup, který se skládá ze sady kroků sestavení, které jsou spouštěny sekvenčně. Vytvořte definici sestavení, která vytvoří balíček aplikace Service Fabric a další artefakty pro nasazení do clusteru Service Fabric. Přečtěte si další informace o [definicích sestavení](https://www.visualstudio.com/docs/build/define/create)Azure DevOps. 

Definice vydané verze Azure DevOps popisuje pracovní postup, který nasadí balíček aplikace do clusteru. Při společném použití definice sestavení a definice verze provedou celý pracovní postup od zdrojových souborů až po spuštění aplikace v clusteru. Přečtěte si další informace o [definicích verzí](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)Azure DevOps.

### <a name="create-a-build-definition"></a>Vytvoření definice sestavení

Otevřete nový týmový projekt tak, že přejdete na https://dev.azure.com webový prohlížeč a vyberete svou organizaci a potom nový projekt. 

Na levém panelu vyberte možnost **kanály** a pak klikněte na **Nový kanál**.

>[!NOTE]
>Pokud se šablona definice sestavení nezobrazí, ujistěte se, že je vypnutá funkce **Nové prostředí pro tvorbu kanálů YAML**. Tato funkce se konfiguruje v části **Funkce Preview** vašeho účtu DevOps.

![Nový kanál][new-pipeline]

Vyberte **Azure Repos Git** jako zdroj, název týmového projektu, úložiště projektu a **hlavní** výchozí větev nebo ruční a plánované sestavení.  Pak klikněte na **pokračovat**.

V části **Vybrat šablonu** vyberte šablonu **Aplikace Azure Service Fabric s podporou Dockeru** a klikněte na **Použít**.

![Výběr šablony sestavení][select-build-template]

V části **Úlohy** vyberte jako **Fond agentů** možnost **Hostované VS2017**.

![Výběr úloh][task-agent-pool]

Klikněte na **Označit image**.

V části **Typ registru kontejneru** vyberte **Azure Container Registry**. Vyberte nějaké **předplatné Azure** a klikněte na tlačítko **Autorizovat**. Vyberte **Azure Container Registry**.

![Výběr možnosti Označit image v Dockeru][select-tag-images]

Klikněte na **Nabídnout image**.

V části **Typ registru kontejneru** vyberte **Azure Container Registry**. Vyberte nějaké **předplatné Azure** a klikněte na tlačítko **Autorizovat**. Vyberte **Azure Container Registry**.

![Výběr možnosti Nabídnout image v Dockeru][select-push-images]

Na kartě **aktivační události** povolte kontinuální integraci zaškrtnutím **Povolit průběžnou integraci**. V části **Filtry větví** klikněte na **+ Přidat** a do pole **Specifikace větve** se vyplní výchozí hodnota **master**.

Kliknutím na **Uložit a zařadit do fronty** v dialogu **Uložit kanál buildu a zařadit do fronty** ručně spusťte build.

![Výběr triggerů][save-and-queue]

Sestavení se aktivují také pro nasdílení změn nebo vrácení se změnami. Chcete-li zjistit průběh sestavení, přepněte na kartu **sestavení** .  Jakmile ověříte, že se sestavení úspěšně spustí, definujte definici verze, která nasadí vaši aplikaci do clusteru.

### <a name="create-a-release-definition"></a>Vytvoření definice verze

Na levém panelu vyberte možnost **kanály** a pak **verze** a potom **+ Nový kanál**.  V části **Vybrat šablonu** vyberte ze seznamu šablonu **Nasazení Azure Service Fabric** a pak klikněte na **Použít**.

![Výběr šablony vydání][select-release-template]

Vyberte **Úlohy**, dále **Prostředí 1** a pak kliknutím na **+ Nový** přidejte nové připojení clusteru.

![Přidání připojení clusteru][add-cluster-connection]

V zobrazení **Přidat nové připojení Service Fabric** vyberte ověřování **Na základě certifikátů** nebo **Azure Active Directory**.  Zadejte název připojení mysftestcluster a koncový bod clusteru tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (nebo koncový bod clusteru, do kterého nasazujete).

Pokud použijete ověřování na základě certifikátů, přidejte **Kryptografický otisk certifikátu serveru** použitého k vytvoření clusteru.  V části **Klientský certifikát** přidejte kódování Base64 souboru klientského certifikátu. V místním okně nápovědy pro toto pole najdete informace o postupu pro získání reprezentace certifikátu v kódování Base64. Přidejte také **Heslo** pro certifikát.  Pokud nemáte samostatný klientský certifikát, můžete použít certifikát clusteru nebo serveru.

Pokud použijete přihlašovací údaje Azure Active Directory, přidejte **Kryptografický otisk certifikátu serveru** použitého k vytvoření clusteru a do polí **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje, pomocí kterých se chcete připojovat ke clusteru.

Kliknutím na **Přidat** uložte připojení clusteru.



V části Fáze agenta klikněte na **Nasazení aplikace Service Fabric**.
Klikněte na **Nastavení Dockeru** a potom na **Konfigurovat nastavení Dockeru**. V seznamu **Zdroj přihlašovacích údajů registru** vyberte **Připojení ke službě Azure Resource Manager**. Pak vyberte své **předplatné Azure**.

![Agent kanálu verze][release-pipeline-agent]

Dále do kanálu přidejte artefakt sestavení, aby definice verze dokázala najít výstup sestavení. Vyberte **Kanál** a pak **Artefakty**->**+ Přidat**.  V části **Zdroj (definice sestavení)** vyberte definici sestavení, kterou jste předtím vytvořili.  Kliknutím na **Přidat** uložte artefakt sestavení.

![Přidání artefaktu][add-artifact]

Povolte trigger průběžného nasazování, aby se po dokončení sestavení automaticky vytvořila vydaná verze. Klikněte na ikonu blesku v artefaktu, povolte trigger a kliknutím na **Uložit** uložte definici verze.

![Povolení triggeru][enable-trigger]

Výběrem **+ vydaná** verze  ->  **vytvořit vydání**  ->  **vytvořit vytvořte** vydanou verzi ručně. Průběh vydání můžete sledovat na kartě **Vydání**.

Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Potvrzení a nasdílení změn, aktivace vydání

Ověřte fungování kanálu kontinuální integrace tím, že do Azure DevOps vrátíte se změnami nějaký kód.

Při psaní kódu sada Visual Studio automaticky sleduje provedené změny. Potvrďte změny do svého místního úložiště Git tím, že vyberete ikonu probíhajících změn (![Ikona probíhajících změn zobrazuje tužku a číslo.][pending]) na stavovém řádku v pravém dolním rohu.

V zobrazení **Změny** v Team Exploreru přidejte zprávu s popisem vaší aktualizace a potvrďte provedené změny.

![Potvrdit vše][changes]

Vyberte ikonu nepublikovaných změn (![Nepublikované změny][unpublished-changes]) na stavovém řádku nebo zobrazení Synchronizace v Team Exploreru. Výběrem možnosti **Nasdílet změny** aktualizujte kód v Azure DevOps.

![Nasdílení změn][push]

Nasdílením změn do Azure DevOps se automaticky aktivuje build.  Po úspěšném dokončení definice sestavení se automaticky vytvoří vydaná verze a začne se upgradovat aplikace v clusteru.

Pokud chcete zkontrolovat průběh sestavení, přepněte v **Team Exploreru** v sadě Visual Studio na kartu **Sestavení**.  Jakmile ověříte, že se sestavení úspěšně provádí, nadefinujte definici verze, která nasadí vaši aplikaci do clusteru.

Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170815.3.

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer Na kartě základy se zvýrazní verze aplikace "1.0.0.20170815.3".][sfx1]

## <a name="update-the-application"></a>Aktualizace aplikace

Proveďte v aplikaci změny kódu.  Podle předchozích kroků změny uložte a potvrďte.

Jakmile začne upgrade aplikace, můžete průběh upgradu sledovat v Service Fabric Exploreru:

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer Zpráva "probíhá upgrade" je zvýrazněna a stav aplikace je "upgrade".][sfx2]

Upgrade aplikace může trvat několik minut. Po dokončení upgradu bude aplikace používat další verzi.  V tomto příkladu verzi 1.0.0.20170815.4.

![Snímek obrazovky hlasovací aplikace v Service Fabric Explorer Na kartě základy je zvýrazněna aktualizovaná verze aplikace "1.0.0.20170815.4".][sfx3]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření definice sestavení
> * Vytvoření definice verze
> * Automatické nasazení a upgrade aplikace

V další části kurzu se dozvíte, jak nastavit [monitorování vašeho kontejneru](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
