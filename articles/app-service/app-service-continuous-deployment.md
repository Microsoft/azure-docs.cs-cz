---
title: Průběžné nasazování do služby Azure App Service | Microsoft Docs
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
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: e587edeef1cfa080a81f523f63678a645b514c57
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849487"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do služby Azure App Service
V tomto článku se dozvíte, jak nakonfigurovat průběžné nasazování pro [Azure App Service](app-service-web-overview.md). Služby App Service umožňuje průběžné nasazování z BitBucket, Githubu, a [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) přidáváním v nejnovější aktualizace z existující úložiště v jednom z těchto služeb.

Chcete zjistit, jak nakonfigurovat průběžné nasazování ručně z úložiště v cloudu nejsou uvedené na portálu Azure (například [GitLab](https://gitlab.com/)), najdete v části [nastavení průběžné nasazování pomocí ruční kroky](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publikujte úložiště připravené v jedné z podporovaných služeb. Další informace o publikování projektu v těchto službách najdete v článcích [Vytvoření úložiště (GitHub)], [Vytvoření úložiště (BitBucket)] a [Začínáme se službou VSTS].

## <a name="deploy-continuously-from-github"></a>Nepřetržitě nasadit z Githubu

Chcete-li povolit průběžné nasazování pomocí Githubu, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **Githubu** > **Authorize**. Postupujte podle pokynů autorizace. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Potřebujete jenom jednou autorizaci s Githubu. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu GitHub **změnit účet**.

![](media/app-service-continuous-deployment/github-continue.png)

V **poskytovatele sestavení** , vyberte poskytovatele sestavení a klikněte na tlačítko > **pokračovat**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití aplikace služby Kudu sestavení serveru

V **konfigurace** vyberte organizace, úložiště a větve, z nichž chcete nasadit nepřetržitě. Po dokončení klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Možnost 2: pomocí služby VSTS nastavené průběžné doručování

> [!NOTE]
> Pro vytvoření nezbytné sestavení a verze definice ve vašem účtu služby VSTS App Service, musí mít váš účet Azure role **vlastníka** ve vašem předplatném Azure.
>

V **konfigurace** stránky v **kód** vyberte organizace, úložiště a větve, z nichž chcete nasadit nepřetržitě. Po dokončení klikněte na tlačítko **pokračovat**.

V **konfigurace** stránky v **sestavení** část, konfigurace nového účtu služby VSTS nebo zadat účet existujícího. Po dokončení klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující účet služby VSTS, který není uveden, budete muset [propojit účet služby VSTS k předplatnému Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

V **Test** vyberte, zda povolit zátěžových testů a pak klikněte na **pokračovat**.

V závislosti na tom [cenová úroveň](/pricing/details/app-service/plans/) vašeho plánu služby App Service, může se také zobrazit **nasadit do pracovní** stránky. Vyberte, jestli se má [povolit nasazovací sloty](web-sites-staged-publishing.md), pak klikněte na tlačítko **pokračovat**.

### <a name="finish-configuration"></a>Dokončení konfigurace

V **Souhrn** , zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace nové potvrzení v úložišti vybrané jsou nepřetržitě nasazené do aplikace služby App Service.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Nasazení nepřetržitě z BitBucket

Chcete-li povolit průběžné nasazování pomocí BitBucket, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **BitBucket** > **Authorize**. Postupujte podle pokynů autorizace. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Potřebujete jenom jednou autorizaci s BitBucket. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu BitBucket **změnit účet**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

V **konfigurace** vyberte úložiště a větve, z nichž chcete nasadit nepřetržitě. Po dokončení klikněte na tlačítko **pokračovat**.

V **Souhrn** , zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace nové potvrzení v úložišti vybrané jsou nepřetržitě nasazené do aplikace služby App Service.

## <a name="deploy-continuously-from-vsts"></a>Nasazení nepřetržitě ze služby VSTS

Chcete-li povolit průběžné nasazování pomocí služby VSTS, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **služby VSTS** > **pokračovat**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

V **poskytovatele sestavení** , vyberte poskytovatele sestavení a klikněte na tlačítko > **pokračovat**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití aplikace služby Kudu sestavení serveru

V **konfigurace** vyberte účet služby VSTS projektu, úložiště a větve, ze kterého chcete nasadit nepřetržitě. Po dokončení klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Možnost 2: pomocí služby VSTS nastavené průběžné doručování

> [!NOTE]
> Pro vytvoření nezbytné sestavení a verze definice ve vašem účtu služby VSTS App Service, musí mít váš účet Azure role **vlastníka** ve vašem předplatném Azure.
>

V **konfigurace** stránky v **kód** vyberte účet služby VSTS projektu, úložiště a větve, ze kterého chcete nasadit nepřetržitě. Po dokončení klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující účet služby VSTS, který není uveden, budete muset [propojit účet služby VSTS k předplatnému Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

V **konfigurace** stránky v **sestavení** , určete jazykové rozhraní, který by měl služby VSTS použít ke spuštění úlohy sestavení pro vybrané úložiště. Po dokončení klikněte na tlačítko **pokračovat**.

V **Test** vyberte, zda povolit zátěžových testů a pak klikněte na **pokračovat**.

V závislosti na tom [cenová úroveň](/pricing/details/app-service/plans/) vašeho plánu služby App Service, může se také zobrazit **nasadit do pracovní** stránky. Vyberte, jestli se má [povolit nasazovací sloty](web-sites-staged-publishing.md), pak klikněte na tlačítko **pokračovat**. 

### <a name="finish-configuration"></a>Dokončení konfigurace

V **Souhrn** , zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace nové potvrzení v úložišti vybrané jsou nepřetržitě nasazené do aplikace služby App Service.

## <a name="disable-continuous-deployment"></a>Zakázat průběžné nasazování.

Chcete-li zakázat průběžné nasazování, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **Githubu** nebo **služby VSTS** nebo **BitBucket**  >  **Odpojit**.

![](media/app-service-continuous-deployment/disable.png)

## <a name="additional-resources"></a>Další prostředky

* [Postup při zkoumání běžných problémů s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Způsob používání prostředí PowerShell pro Azure]
* [Dokumentace pro Git]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* [Použití Azure automaticky generovat kanálu CI nebo CD pro nasazení aplikace ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Způsob používání prostředí PowerShell pro Azure]: /powershell/azureps-cmdlets-docs
[Dokumentace pro Git]: http://git-scm.com/documentation

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Začínáme se službou VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
