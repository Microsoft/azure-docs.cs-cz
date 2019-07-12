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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836838"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do služby Azure App Service

[Azure App Service](overview.md) umožňuje průběžné nasazování z Githubu, BitBucket, a [úložišť Azure](https://azure.microsoft.com/services/devops/repos/) úložišť stažením nejnovější aktualizace. Tento článek ukazuje, jak pomocí webu Azure portal průběžné nasazování vaší aplikace prostřednictvím služby build service Kudu nebo [kanály Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Další informace o služba source control services najdete v tématu [vytvoření úložiště (GitHub)], [vytvoření úložiště (BitBucket)], nebo [vytvoření nového úložiště Git (úložiště Azure)].

Jak ručně nakonfigurovat průběžné nasazování z cloudové úložiště, kterou portál nepodporuje přímo, jako například [GitLab](https://gitlab.com/), naleznete v tématu [nastavit průběžné nasazování pomocí vyžadováno provedení ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Povolit službě Azure App Service 

Pro použití úložiště Azure, ujistěte se, že vaše organizace Azure DevOps je propojený s předplatným Azure. Další informace najdete v tématu [nastavení účtu služby Azure DevOps, můžete nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Pro GitHub nebo Bitbucket Povolit službě Azure App Service pro připojení k úložišti. Stačí jednou autorizovat s službu správy zdrojových kódů. 

1. Vyberte **App Services** v [webu Azure portal](https://portal.azure.com) levé navigaci a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte **Deployment Center** v levé nabídce.
   
1. Na **Deployment Center** stránce **Githubu** nebo **Bitbucket**a pak vyberte **Authorize**. 
   
   ![Vyberte službu správy zdrojových kódů a potom vyberte Authorize.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Přihlaste se ke službě v případě potřeby a postupujte podle zobrazených výzev ověřování. 

## <a name="enable-continuous-deployment"></a>Povolit průběžné nasazování 

Jakmile povolíte službu správy zdrojových kódů, nakonfigurujte svou aplikaci pro průběžné nasazování prostřednictvím integrovaných [Kudu App Service buildovací server](#option-1-use-the-app-service-build-service), nebo prostřednictvím [kanály Azure](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Option 1: Používají sestavovací službu App Service

Můžete použít integrované služby App Service Kudu buildovací server pro průběžné nasazování z Githubu, Bitbucket nebo úložiště Azure. 

1. Vyberte **App Services** v [webu Azure portal](https://portal.azure.com) levé navigaci a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte **Deployment Center** v levé nabídce.
   
1. Vyberte váš poskytovatel správy zdrojů autorizované na **Deployment Center** stránku a vybrat **pokračovat**. Pro GitHub nebo Bitbucket můžete také vybrat **změnit účet** změnit oprávnění účtu. 
   
   > [!NOTE]
   > Pro použití úložiště Azure, ujistěte se, že vaše organizace služby Azure DevOps je propojený s předplatným Azure. Další informace najdete v tématu [nastavení účtu služby Azure DevOps, můžete nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. Pro GitHub nebo úložiště Azure na **sestavení zprostředkovatele** stránce **vytvořit službu App Service**a pak vyberte **pokračovat**. Bitbucket vždy používá sestavovací služba App Service.
   
   ![Vyberte sestavovací služba App Service a potom vyberte pokračovat.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na **konfigurovat** stránky:
   
   - Pro GitHub, rozevírací seznam a vyberte **organizace**, **úložiště**, a **větev** chcete průběžné nasazování.
     
     > [!NOTE]
     > Pokud nevidíte žádné úložiště, budete muset autorizovat službu Azure App Service na webu GitHub. Přejděte do vašeho úložiště GitHub a přejděte na **nastavení** > **aplikací** > **autorizované aplikace OAuth**. Vyberte **služby Azure App Service**a pak vyberte **udělení**.
     
   - Bitbucket, vyberte Bitbucketu **týmu**, **úložiště**, a **větev** chcete průběžné nasazování.
     
   - Pro úložiště Azure, vyberte **organizace Azure DevOps**, **projektu**, **úložiště**, a **větev** chcete průběžné nasazování.
     
     > [!NOTE]
     > Pokud není uvedená vaše organizace Azure DevOps, ujistěte se, že je propojený s předplatným Azure. Další informace najdete v tématu [nastavení účtu služby Azure DevOps, můžete nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. Vyberte **pokračovat**.
   
   ![Vyplňte informace o úložišti a pak vyberte pokračovat.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po dokončení konfigurace poskytovatele sestavení, zkontrolujte nastavení na **Souhrn** stránce a pak vyberte **Dokončit**.
   
   Nová potvrzení změn vybrané úložiště a větev teď průběžné nasazování do vaší aplikace služby App Service. Potvrzení a nasazení můžete sledovat na **Deployment Center** stránky.
   
   ![Sledování, potvrzení a nasazení v Centru pro nasazení](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Option 2: Použití Azure Pipelines 

Pokud váš účet nemá potřebná oprávnění, můžete nastavit kanály Azure k průběžné nasazování z úložiště GitHub nebo úložiště Azure. Další informace o nasazení prostřednictvím kanálů Azure najdete v tématu [nasazení webové aplikace do Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Pro Azure App Service a vytvářet kanály Azure průběžné doručování ve vaší organizaci Azure DevOps: 

- Váš účet Azure musí mít oprávnění k zápisu do služby Azure Active Directory a vytvořte službu. 
  
- Musí mít váš účet Azure **vlastníka** role ve vašem předplatném Azure.

- Musíte být správcem v projektu Azure DevOps, které chcete použít.

Konfigurace kanálů Azure (Preview):

1. Vyberte **App Services** v [webu Azure portal](https://portal.azure.com) levé navigaci a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte **Deployment Center** v levé nabídce.
   
1. Na **sestavení zprostředkovatele** stránce **kanály Azure (Preview)** a pak vyberte **pokračovat**. 
   
1. Na **konfigurovat** stránku, **kód** části:
   
   - Pro GitHub, rozevírací seznam a vyberte **organizace**, **úložiště**, a **větev** chcete průběžné nasazování.
     
     > [!NOTE]
     > Pokud nevidíte žádné úložiště, budete muset autorizovat službu Azure App Service na webu GitHub. Přejděte do vašeho úložiště GitHub a přejděte na **nastavení** > **aplikací** > **autorizované aplikace OAuth**. Vyberte **služby Azure App Service**a pak vyberte **udělení**.
     
   - Pro úložiště Azure, vyberte **organizace Azure DevOps**, **projektu**, **úložiště**, a **větev** chcete nasadit průběžně nebo nakonfigurujte novou organizaci Azure DevOps.
     
     > [!NOTE]
     > Pokud není uvedená vaše stávající organizace Azure DevOps, budete muset propojit se svým předplatným Azure. Další informace najdete v tématu [definovat kanál pro vydávání verzí CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Vyberte **pokračovat**.
   
1. Pro úložiště Azure v **sestavení** nastavte jazyk rozhraní, kanály Azure byste měli používat ke spouštění úloh sestavení a pak vyberte **pokračovat**.
   
1. Na **testovací** zvolte, jestli se má povolit zátěžové testy a pak vyberte **pokračovat**.
   
1. V závislosti na plánu služby App Service [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/plans/), může se zobrazit **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má [povolí sloty nasazení](deploy-staging-slots.md)a pak vyberte **pokračovat**.
   
   > [!NOTE]
   > Kanály Azure neumožňuje průběžného doručování na produkční slot. Smyslem tohoto omezení náhodných nasazení do produkčního prostředí. Nastavit průběžné doručování do přípravného slotu a ověřte změny tam pak Prohodit sloty, až budete připravení.
   
1. Po dokončení konfigurace poskytovatele sestavení, zkontrolujte nastavení na **Souhrn** stránce a pak vyberte **Dokončit**.
   
   Nová potvrzení změn vybrané úložiště a větev teď průběžné nasazování do vaší aplikace služby App Service. Potvrzení a nasazení můžete sledovat na **Deployment Center** stránky.
   
   ![Sledování, potvrzení a nasazení v Centru pro nasazení](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

Chcete-li zakázat průběžné nasazování, vyberte **odpojit** v horní části vaší aplikace **Centrum nasazení** stránky.

![Zakázání průběžného nasazování](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Další zdroje

* [Zkoumání běžných problémů s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Použití Azure Powershellu](/powershell/azureps-cmdlets-docs)
* [Dokumentace pro Git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Vytvoření nového úložiště Git (úložiště Azure)]: /azure/devops/repos/git/creatingrepo
