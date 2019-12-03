---
title: Konfigurace průběžného nasazování
description: Naučte se, jak povolit CI/CD pro Azure App Service z GitHubu, BitBucket, Azure Repos nebo jiných úložišť. Vyberte kanál sestavení, který vyhovuje vašim potřebám.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 692b07c82c329a93d79ad3a87beec5dbe1c595d3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669986"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do Azure App Service

[Azure App Service](overview.md) umožňuje průběžné nasazování z GitHubu, BitBucket a [Azure Repos](https://azure.microsoft.com/services/devops/repos/) úložišť pomocí nejnovějších aktualizací. V tomto článku se dozvíte, jak používat Azure Portal k průběžné nasazování vaší aplikace prostřednictvím služby sestavení Kudu nebo [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Další informace o službách správy zdrojového kódu najdete v tématech [Vytvoření úložiště (GitHub)], [Vytvoření úložiště (Bitbucket)]nebo [vytvoření nového úložiště Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizovat Azure App Service 

Pokud chcete použít Azure Repos, ujistěte se, že je vaše organizace Azure DevOps propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

V případě Bitbucket nebo GitHubu autorizujte Azure App Service pro připojení k úložišti. Pouze jednou je třeba autorizovat se službou správy zdrojového kódu. 

1. V levém navigačním panelu vyberte **App Services** [Azure Portal](https://portal.azure.com) a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .
   
1. Na stránce **centrum nasazení** vyberte **GitHub** nebo **Bitbucket**a potom vyberte **autorizovat**. 
   
   ![Vyberte službu správy zdrojového kódu a potom vyberte autorizovat.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. V případě potřeby se přihlaste ke službě a postupujte podle pokynů pro autorizaci. 

## <a name="enable-continuous-deployment"></a>Povolit průběžné nasazování 

Po autorizaci služby správy zdrojového kódu nakonfigurujte aplikaci pro průběžné nasazování prostřednictvím integrovaného [Kudu App Service sestavovacího serveru](#option-1-use-the-app-service-build-service)nebo prostřednictvím [Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Možnost 1: použití služby App Service Build Service

K průběžnému nasazování z GitHubu, Bitbucket nebo Azure Repos můžete použít vestavěný Kudu App Service Build Server. 

1. V levém navigačním panelu vyberte **App Services** [Azure Portal](https://portal.azure.com) a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .
   
1. Vyberte svého autorizovaného poskytovatele správy zdrojů na stránce **centrum nasazení** a vyberte **pokračovat**. V případě GitHubu nebo Bitbucket můžete také vybrat **změnit účet** a změnit autorizovaný účet. 
   
   > [!NOTE]
   > Pokud chcete použít Azure Repos, ujistěte se, že je vaše organizace Azure DevOps Services propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. Pro GitHub nebo Azure Repos na stránce **poskytovatel sestavení** vyberte **App Service sestavovací služba**a pak vyberte **pokračovat**. Bitbucket vždy používá službu sestavení App Service.
   
   ![Vyberte App Service sestavovací služba a pak vyberte pokračovat.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stránce **Konfigurace** :
   
   - V části GitHub rozbalte rozevírací seznam a vyberte **organizaci**, **úložiště**a **větev** , kterou chcete nasadit průběžně.
     
     > [!NOTE]
     > Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure App Service v GitHubu. Přejděte do úložiště GitHub a přejděte na **nastavení** > **aplikace** > **autorizovaných aplikací OAuth**. Vyberte **Azure App Service**a pak vyberte **udělit**.
     
   - Pro Bitbucket vyberte **tým**BitBucket, **úložiště**a **větev** , které chcete průběžně nasadit.
     
   - V Azure Repos vyberte organizaci, **projekt**, **úložiště**a **větev** **Azure DevOps**, které chcete průběžně nasadit.
     
     > [!NOTE]
     > Pokud vaše organizace Azure DevOps není v seznamu uvedená, ujistěte se, že je propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Vyberte **Pokračovat**.
   
   ![Vyplňte informace o úložišti a pak vyberte pokračovat.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po konfiguraci poskytovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a pak vyberte **Dokončit**.
   
   Nová potvrzení ve vybraném úložišti a větvi se teď do vaší aplikace App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na stránce **centra nasazení** .
   
   ![Sledování potvrzení a nasazení v centru nasazení](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Možnost 2: použití Azure Pipelines 

Pokud má váš účet potřebná oprávnění, můžete nastavit Azure Pipelines pro průběžné nasazování z GitHubu nebo Azure Repos úložišť. Další informace o nasazení prostřednictvím Azure Pipelines najdete v tématu [nasazení webové aplikace do Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Azure App Service vytvořit průběžné doručování Azure Pipelines ve vaší organizaci Azure DevOps: 

- Váš účet Azure musí mít oprávnění k zápisu do Azure Active Directory a vytvoření služby. 
  
- Váš účet Azure musí mít ve svém předplatném Azure roli **vlastníka** .

- Musíte být správce v projektu Azure DevOps, který chcete použít.

Konfigurace Azure Pipelines (Preview):

1. V levém navigačním panelu vyberte **App Services** [Azure Portal](https://portal.azure.com) a pak vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .
   
1. Na stránce **poskytovatel sestavení** vyberte možnost **Azure Pipelines (Preview)** a pak vyberte **pokračovat**. 
   
1. Na stránce **Konfigurace** v části **kód** :
   
   - V části GitHub rozbalte rozevírací seznam a vyberte **organizaci**, **úložiště**a **větev** , kterou chcete nasadit průběžně.
     
     > [!NOTE]
     > Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure App Service v GitHubu. Přejděte do úložiště GitHub a přejděte na **nastavení** > **aplikace** > **autorizovaných aplikací OAuth**. Vyberte **Azure App Service**a pak vyberte **udělit**.
     
   - V případě Azure Repos vyberte **organizaci Azure DevOps**, **projekt**, **úložiště**a **větev** , které chcete nasadit průběžně, nebo nakonfigurujte novou organizaci Azure DevOps.
     
     > [!NOTE]
     > Pokud vaše stávající organizace Azure DevOps není v seznamu uvedená, budete ji muset propojit s vaším předplatným Azure. Další informace najdete v tématu [definice kanálu verze CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Vyberte **Pokračovat**.
   
1. Pro Azure Repos v části **sestavení** určete jazykovou architekturu, kterou má Azure Pipelines použít ke spouštění úloh sestavení a pak vyberte **pokračovat**.
   
1. Na stránce **test** vyberte, zda chcete povolit zátěžové testy, a pak vyberte **pokračovat**.
   
1. V závislosti na [cenové úrovni](https://azure.microsoft.com/pricing/details/app-service/plans/)plánu App Service se může zobrazit stránka **nasazení na přípravu** . Zvolte, jestli se mají [Povolit sloty nasazení](deploy-staging-slots.md), a pak vyberte **pokračovat**.
   
   > [!NOTE]
   > Azure Pipelines nepovoluje průběžné doručování do produkčního slotu. Toto omezení zabrání neúmyslným nasazením do produkčního prostředí. Nastavte průběžné doručování do přípravného slotu, ověřte tyto změny a až budete připraveni, zaměňte sloty.
   
1. Po konfiguraci poskytovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a pak vyberte **Dokončit**.
   
   Nová potvrzení ve vybraném úložišti a větvi se teď do vaší aplikace App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na stránce **centra nasazení** .
   
   ![Sledování potvrzení a nasazení v centru nasazení](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Zakázat průběžné nasazování

Chcete-li zakázat průběžné nasazování, vyberte možnost **Odpojit** v horní části stránky **centra nasazení** vaší aplikace.

![Zakázat průběžné nasazování](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Použití nepodporovaných úložišť

Pro aplikace pro Windows můžete průběžné nasazování nakonfigurovat ručně z cloudového úložiště Git nebo Mecurial, které portál přímo nepodporuje, jako je třeba [GitLab](https://gitlab.com/). Provedete to tak, že vyberete externí pole na stránce **centra nasazení** . Další informace najdete v tématu [Nastavení průběžného nasazování pomocí ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Další zdroje informací:

* [Prozkoumat běžné problémy s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Použití Azure Powershellu](/powershell/azureps-cmdlets-docs)
* [Dokumentace pro Git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Vytvoření nového úložiště Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
