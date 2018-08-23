---
title: Průběžné nasazování do služby Azure App Service | Dokumentace Microsoftu
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
ms.openlocfilehash: 4d3f1c66c6403720bf02c80af1d6833dc3cee3f1
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42060746"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do služby Azure App Service
V tomto článku se dozvíte, jak nakonfigurovat průběžné nasazování pro [služby Azure App Service](app-service-web-overview.md). App Service umožňuje průběžné nasazování z Bitbucketu, Githubu, a [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) stažením nejnovější aktualizace z existující úložiště v některé z těchto služeb.

Chcete-li zjistit, jak nakonfigurovat průběžné nasazování ručně v cloudovém úložišti nejsou uvedeny ve na webu Azure portal (například [GitLab](https://gitlab.com/)), naleznete v tématu [nastavení průběžného nasazování pomocí vyžadováno provedení ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publikování úložiště připravené na jednu z podporovaných služeb. Další informace o publikování projektu v těchto službách najdete v článcích [Vytvoření úložiště (GitHub)], [Vytvoření úložiště (BitBucket)] a [Začínáme se službou VSTS].

## <a name="deploy-continuously-from-github"></a>Průběžné nasazování z Githubu

Pokud chcete povolit průběžné nasazování s Githubem, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Githubu** > **Authorize**. Postupujte podle výzev ověřování. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Stačí jednou autorizovat s Githubem. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu GitHub **změnit účet**.

![](media/app-service-continuous-deployment/github-continue.png)

V **sestavení zprostředkovatele** stránce, zvolte poskytovatele sestavení a klikněte na > **pokračovat**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití serveru sestavení App Service Kudu

V **konfigurovat** vyberte organizace, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Možnost 2: použití průběžné doručování s VSTS

> [!NOTE]
> Pro App Service a vytvořit potřebná sestavení a definice verzí v účtu VSTS, musí váš účet Azure mít roli **vlastníka** ve vašem předplatném Azure.
>

V **konfigurovat** stránku, **kód** vyberte organizace, úložiště a větev, ze kterých chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **konfigurovat** stránku, **sestavení** části, konfigurace nového účtu VSTS nebo zadat existující účet. Až budete hotovi, klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující účet VSTS, která není uvedená, budete muset [propojit účet VSTS se svým předplatným Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

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

## <a name="deploy-continuously-from-vsts"></a>Průběžné nasazování z VSTS

Pokud chcete povolit průběžné nasazování pomocí VSTS, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **VSTS** > **pokračovat**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

V **sestavení zprostředkovatele** stránce, zvolte poskytovatele sestavení a klikněte na > **pokračovat**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Možnost 1: použití serveru sestavení App Service Kudu

V **konfigurovat** vyberte účet VSTS, projekt, úložiště a větev, ze kterého chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Možnost 2: použití průběžné doručování s VSTS

> [!NOTE]
> Pro App Service a vytvořit potřebná sestavení a definice verzí v účtu VSTS, musí váš účet Azure mít roli **vlastníka** ve vašem předplatném Azure.
>

V **konfigurovat** stránku, **kód** vyberte účet VSTS, projekt, úložiště a větev, ze kterého chcete průběžné nasazování. Až budete hotovi, klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující účet VSTS, která není uvedená, budete muset [propojit účet VSTS se svým předplatným Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

V **konfigurovat** stránku, **sestavení** nastavte jazyk rozhraní, VSTS by měl použít k provedení úlohy buildu pro vybrané úložiště. Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **testovací** zvolte, jestli se má povolit zátěžové testy a pak klikněte na **pokračovat**.

V závislosti na [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/plans/) plánu služby App Service, může se zobrazit také **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má [povolí sloty nasazení](web-sites-staged-publishing.md), pak klikněte na tlačítko **pokračovat**. 

### <a name="finish-configuration"></a>Dokončení konfigurace

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Po dokončení konfigurace jsou nová potvrzení změn na vybrané úložiště průběžně nasazené do aplikace služby App Service.

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

Průběžné nasazování zakázat, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Githubu** nebo **VSTS** nebo **BitBucket**  >  **Odpojit**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Další prostředky

* [Postup při zkoumání běžných problémů s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Způsob používání prostředí PowerShell pro Azure]
* [Dokumentace pro Git]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* [Použití Azure k automatickému generování kanálu CI/CD pro nasazení aplikace ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Způsob používání prostředí PowerShell pro Azure]: /powershell/azureps-cmdlets-docs
[Dokumentace pro Git]: http://git-scm.com/documentation

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Začínáme se službou VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
