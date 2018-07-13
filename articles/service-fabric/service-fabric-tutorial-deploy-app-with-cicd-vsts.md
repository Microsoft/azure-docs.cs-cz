---
title: Nasazení aplikace Service Fabric s využitím průběžné integrace (Team Services) v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nastavit průběžnou integraci a nasazování pro aplikaci Service Fabric pomocí Visual Studio Team Services.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f3cc4f518278cca915e40bd691c6a7674219916e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109388"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Kurz: Nasazení aplikace s CI/CD do clusteru Service Fabric

V tomto kurzu, který je čtvrtou částí série, se dozvíte, jak nastavit průběžnou integraci a nasazování pro aplikaci Azure Service Fabric pomocí Visual Studio Team Services.  Potřebujete existující aplikaci Service Fabric. Jako příklad se používá aplikace vytvořená v tématu [Sestavení aplikace .NET](service-fabric-tutorial-create-dotnet-app.md).

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření definice sestavení v Team Services
> * Vytvoření definice verze v Team Services
> * Automatické nasazení a upgrade aplikace

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Nasadit aplikaci do vzdáleného clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Přidat koncový bod HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Nakonfigurovat CI/CD pomocí Visual Studio Team Services
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).
* Vytvořte v Azure cluster Service Fabric s Windows, například [podle tohoto kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* Vytvořte [účet Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové aplikace Voting

Pokud jste nesestavili ukázkovou aplikaci Voting v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Příprava profilu publikování

Teď, když jste [vytvořili aplikaci](service-fabric-tutorial-create-dotnet-app.md) a [nasadili jste tuto aplikaci do Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), jste připraveni nastavit průběžnou integraci.  Nejprve v rámci své aplikace připravte profil publikování, který použije proces nasazení, který se provádí v rámci Team Services.  Profil publikování by měl být nakonfigurovaný tak, aby cílil na cluster, který jste předtím vytvořili.  Spusťte sadu Visual Studio a otevřete existující projekt aplikace Service Fabric.  V **Průzkumníku řešení** klikněte pravým tlačítkem na aplikaci a vyberte **Publikovat...**

Zvolte v rámci projektu aplikace cílový profil, který se použije pro pracovní postup průběžné integrace, například Cloud.  Zadejte koncový bod připojení clusteru.  Zaškrtněte políčko **Upgradovat aplikaci**, aby se vaše aplikace upgradovala pro každé nasazení v Team Services.  Kliknutím na hypertextový odkaz **Uložit** uložte nastavení do profilu publikování a pak kliknutím na **Zrušit** zavřete dialogové okno.

![Nasdílení profilu][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Sdílení řešení sady Visual Studio do nového úložiště Team Services Git

Zdrojové soubory své aplikace můžete sdílet do týmového projektu v Team Services, abyste mohli generovat sestavení.

Vytvořte pro svůj projekt nové místní úložiště Git tím, že na stavovém řádku v pravém dolním rohu sady Visual Studio vyberete **Přidat do správy zdrojového kódu** -> **Git**.

V zobrazení **Nasdílení změn** v **Team Exploreru** vyberte v části **Nasdílet do Visual Studio Team Services** tlačítko **Publikovat úložiště Git**.

![Nasdílení úložiště Git][push-git-repo]

Ověřte svůj e-mail a v rozevíracím seznamu **Doména Team Services** vyberte svůj účet. Zadejte název svého úložiště a vyberte **Publikovat úložiště**.

![Nasdílení úložiště Git][publish-code]

Publikováním úložiště se ve vašem účtu vytvoří nový týmový projekt se stejným názvem jako místní úložiště. Pokud chcete úložiště vytvořit v existujícím týmovém projektu, klikněte na **Upřesnit** vedle názvu **úložiště** a vyberte týmový projekt. Svůj kód můžete zobrazit na webu výběrem možnosti **Podívejte se na webu**.

## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurace průběžného doručování s VSTS

Definice sestavení Team Services popisuje pracovní postup, který se skládá z řady postupně prováděných kroků sestavení. Vytvořte definici sestavení, která vytvoří balíček aplikace Service Fabric a další artefakty pro nasazení do clusteru Service Fabric. Další informace o [definicích sestavení Team Services](https://www.visualstudio.com/docs/build/define/create). 

Definice verze Team Services popisuje pracovní postup, který nasadí balíček aplikace do clusteru. Při společném použití definice sestavení a definice verze provedou celý pracovní postup od zdrojových souborů až po spuštění aplikace v clusteru. Další informace o [definicích verzí](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) Team Services.

### <a name="create-a-build-definition"></a>Vytvoření definice sestavení

Otevřete webový prohlížeč a přejděte do nového týmového projektu na adrese: [https://&lt;váš_účet&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Vyberte kartu **Sestavení a vydání**, pak **Sestavení** a nakonec **+ Nová definice**.  V části **Vybrat šablonu** vyberte šablonu **Aplikace Azure Service Fabric** a klikněte na **Použít**.

![Výběr šablony sestavení][select-build-template]

V části **Úlohy** zadejte do pole **Fronta agenta** Hosted VS2017.

![Výběr úloh][save-and-queue]

V části **Triggery** nastavte **Stav triggeru** a povolte tak průběžnou integraci.  Pokud chcete ručně spustit sestavení, vyberte **Uložit a zařadit do fronty**.

![Výběr triggerů][save-and-queue2]

Sestavení se aktivují také pro nasdílení změn nebo vrácení se změnami. Pokud chcete zkontrolovat průběh sestavení, přepněte na kartu **Sestavení**.  Jakmile ověříte, že se sestavení úspěšně provádí, nadefinujte definici verze, která nasadí vaši aplikaci do clusteru.

### <a name="create-a-release-definition"></a>Vytvoření definice verze

Vyberte kartu **Sestavení a vydání**, pak **Vydání** a nakonec **+ Nová definice**.  V části **Vybrat šablonu** vyberte ze seznamu šablonu **Nasazení Azure Service Fabric** a pak klikněte na **Použít**.

![Výběr šablony vydání][select-release-template]

Vyberte **Úlohy**->**Prostředí 1** a pak kliknutím na **+ Nové** přidejte nové připojení clusteru.

![Přidání připojení clusteru][add-cluster-connection]

V zobrazení **Přidat nové připojení Service Fabric** vyberte ověřování **Na základě certifikátů** nebo **Azure Active Directory**.  Zadejte název připojení mysftestcluster a koncový bod clusteru tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (nebo koncový bod clusteru, do kterého nasazujete).

Pokud použijete ověřování na základě certifikátů, přidejte **Kryptografický otisk certifikátu serveru** použitého k vytvoření clusteru.  V části **Klientský certifikát** přidejte kódování Base64 souboru klientského certifikátu. V místním okně nápovědy pro toto pole najdete informace o postupu pro získání reprezentace certifikátu v kódování Base64. Přidejte také **Heslo** pro certifikát.  Pokud nemáte samostatný klientský certifikát, můžete použít certifikát clusteru nebo serveru.

Pokud použijete přihlašovací údaje Azure Active Directory, přidejte **Kryptografický otisk certifikátu serveru** použitého k vytvoření clusteru a do polí **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje, pomocí kterých se chcete připojovat ke clusteru.

Kliknutím na **Přidat** uložte připojení clusteru.

Dále do kanálu přidejte artefakt sestavení, aby definice verze dokázala najít výstup sestavení. Vyberte **Kanál** a pak **Artefakty**->**+ Přidat**.  V části **Zdroj (definice sestavení)** vyberte definici sestavení, kterou jste předtím vytvořili.  Kliknutím na **Přidat** uložte artefakt sestavení.

![Přidání artefaktu][add-artifact]

Povolte trigger průběžného nasazování, aby se po dokončení sestavení automaticky vytvořila vydaná verze. Klikněte na ikonu blesku v artefaktu, povolte trigger a kliknutím na **Uložit** uložte definici verze.

![Povolení triggeru][enable-trigger]

Výběrem **+ Vydání** -> **Vytvořit vydání** -> **Vytvořit** ručně vytvořte vydání.  Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Potvrzení a nasdílení změn, aktivace vydání

Ověřte fungování kanálu průběžné integrace tím, že do Team Services vrátíte se změnami nějaký kód.

Při psaní kódu sada Visual Studio automaticky sleduje provedené změny. Potvrďte změny do svého místního úložiště Git tím, že vyberete ikonu probíhajících změn (![Čekající na vyřízení][pending]) na stavovém řádku v pravém dolním rohu.

V zobrazení **Změny** v Team Exploreru přidejte zprávu s popisem vaší aktualizace a potvrďte provedené změny.

![Potvrdit vše][changes]

Vyberte ikonu nepublikovaných změn (![Nepublikované změny][unpublished-changes]) na stavovém řádku nebo zobrazení Synchronizace v Team Exploreru. Vyberte **Nasdílet změny** a aktualizujte svůj kód v Team Services nebo v sadě TFS.

![Nasdílení změn][push]

Nasdílením změn do Team Services se automaticky aktivuje sestavení.  Po úspěšném dokončení definice sestavení se automaticky vytvoří vydaná verze a začne se upgradovat aplikace v clusteru.

Pokud chcete zkontrolovat průběh sestavení, přepněte v **Team Exploreru** v sadě Visual Studio na kartu **Sestavení**.  Jakmile ověříte, že se sestavení úspěšně provádí, nadefinujte definici verze, která nasadí vaši aplikaci do clusteru.

Ověřte, že sestavení proběhlo úspěšně a aplikace je spuštěná v clusteru.  Otevřete webový prohlížeč a přejděte na adresu [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Poznamenejte si verzi aplikace, v tomto příkladu je to 1.0.0.20170815.3.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aktualizace aplikace

Proveďte v aplikaci změny kódu.  Podle předchozích kroků změny uložte a potvrďte.

Jakmile začne upgrade aplikace, můžete průběh upgradu sledovat v Service Fabric Exploreru:

![Service Fabric Explorer][sfx2]

Upgrade aplikace může trvat několik minut. Po dokončení upgradu bude aplikace používat další verzi.  V tomto příkladu verzi 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření definice sestavení
> * Vytvoření definice verze
> * Automatické nasazení a upgrade aplikace

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
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
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
