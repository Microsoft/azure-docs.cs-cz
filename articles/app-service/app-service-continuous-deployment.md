---
title: Průběžné nasazování – Azure App Service | Dokumentace Microsoftu
description: Naučte se povolit průběžné nasazování do služby Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 79f544a9cec24ef84ad9e64e80cabe45dbb6d1d6
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257284"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do služby Azure App Service
V tomto článku se dozvíte, jak nakonfigurovat průběžné nasazování pro [služby Azure App Service](app-service-web-overview.md). App Service umožňuje průběžné nasazování z Bitbucketu, Githubu, a [Azure DevOps služby](https://www.visualstudio.com/team-services/) stažením nejnovější aktualizace z existující úložiště v některé z těchto služeb.

Chcete-li zjistit, jak nakonfigurovat průběžné nasazování ručně v cloudovém úložišti nejsou uvedeny ve na webu Azure portal (například [GitLab](https://gitlab.com/)), naleznete v tématu [nastavení průběžného nasazování pomocí vyžadováno provedení ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publikování úložiště připravené na jednu z podporovaných služeb. Další informace o publikování projektu na tyto služby najdete v tématu [vytvoření úložiště (GitHub)], [vytvoření úložiště (BitBucket)], a [Začínáme se službami Azure DevOps].

## <a name="deploy-continuously-from-github"></a>Průběžné nasazování z Githubu

Pokud chcete povolit průběžné nasazování s Githubem, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Githubu** > **Authorize**. Postupujte podle výzev ověřování. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Stačí jednou autorizovat s Githubem. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu GitHub **změnit účet**.

![](media/app-service-continuous-deployment/github-continue.png)

V **sestavení zprostředkovatele** stránce, zvolte poskytovatele sestavení a klikněte na > **pokračovat**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití serveru sestavení App Service Kudu

V **konfigurovat** vyberte organizace, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-azure-pipelines-preview"></a>Možnost 2: použití kanály Azure (preview)

> [!NOTE]
> Pro službu App Service k vytváření kanálů nezbytné Azure ve vaší organizaci Azure DevOps služby, musí váš účet Azure mít roli **vlastníka** ve vašem předplatném Azure.
>

V **konfigurovat** stránku, **kód** vyberte organizace, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **konfigurovat** stránku, **sestavení** části, nakonfigurovat novou organizaci Azure DevOps služby nebo zadejte existující organizace. Až budete hotovi, klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující služby Azure DevOps organizace, která není uvedená, budete muset [propojení organizace služby Azure DevOps s předplatným Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

V **testovací** zvolte, jestli se má povolit zátěžové testy a pak klikněte na **pokračovat**.

V závislosti na [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/plans/) plánu služby App Service, může se zobrazit také **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má [povolí sloty nasazení](web-sites-staged-publishing.md), pak klikněte na tlačítko **pokračovat**.

### <a name="finish-configuration"></a>Dokončení konfigurace

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace jsou nová potvrzení změn na vybrané úložiště průběžně nasazené do aplikace služby App Service.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Průběžné nasazování z Bitbucketu

Pokud chcete povolit průběžné nasazování pomocí Bitbucketu, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **BitBucket** > **Authorize**. Postupujte podle výzev ověřování. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Stačí jednou autorizovat pomocí Bitbucketu. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu BitBucket **změnit účet**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

V **konfigurovat** vyberte úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace jsou nová potvrzení změn na vybrané úložiště průběžně nasazené do aplikace služby App Service.

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Průběžné nasazování z úložiště Azure (DevOps služby)

Povolit průběžné nasazování se službou z [úložišť Azure](https://docs.microsoft.com/azure/devops/repos/index), přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **úložišť Azure** > **pokračovat**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

V **sestavení zprostředkovatele** stránce, zvolte poskytovatele sestavení a klikněte na > **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující služby Azure DevOps organizace, která není uvedená, budete muset [propojení organizace služby Azure DevOps s předplatným Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití serveru sestavení App Service Kudu

V **konfigurovat** vyberte Azure DevOps servisní organizaci, projekt, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Možnost 2: použití průběžného doručování služeb Azure DevOps

> [!NOTE]
> Pro službu App Service k vytváření kanálů nezbytné Azure ve vaší organizaci Azure DevOps služby, musí váš účet Azure mít roli **vlastníka** ve vašem předplatném Azure.
>

V **konfigurovat** stránku, **kód** vyberte Azure DevOps servisní organizaci, projekt, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **konfigurovat** stránku, **sestavení** nastavte jazyk rozhraní, Azure DevOps služby by měl použít k provedení úlohy buildu pro vybrané úložiště. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **testovací** zvolte, jestli se má povolit zátěžové testy a pak klikněte na **pokračovat**.

V závislosti na [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/plans/) plánu služby App Service, může se zobrazit také **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má [povolí sloty nasazení](web-sites-staged-publishing.md), pak klikněte na tlačítko **pokračovat**. 

### <a name="finish-configuration"></a>Dokončení konfigurace

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace jsou nová potvrzení změn na vybrané úložiště průběžně nasazené do aplikace služby App Service.

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

Průběžné nasazování zakázat, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Githubu** nebo **Azure DevOps služby** nebo **BitBucket**  >  **Odpojit**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Další prostředky

* [Postup při zkoumání běžných problémů s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Způsob používání prostředí PowerShell pro Azure]
* [Dokumentace pro Git]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* [Použití Azure k automatickému generování kanálu CI/CD pro nasazení aplikace ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Způsob používání prostředí PowerShell pro Azure]: /powershell/azureps-cmdlets-docs
[Dokumentace pro Git]: https://git-scm.com/documentation

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Začínáme se službami Azure DevOps]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
