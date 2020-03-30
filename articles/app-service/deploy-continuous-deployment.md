---
title: Konfigurace průběžného nasazování
description: Zjistěte, jak povolit CI/CD na Azure App Service z GitHub, BitBucket, Azure Repos nebo jiných úložištích. Vyberte kanál sestavení, který vyhovuje vašim potřebám.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266036"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazení do služby Azure App Service

[Azure App Service](overview.md) umožňuje průběžné nasazení z úložišť GitHub, BitBucket a [Azure Repos](https://azure.microsoft.com/services/devops/repos/) vytažením nejnovějších aktualizací. Tento článek ukazuje, jak používat portál Azure k průběžnému nasazování aplikace prostřednictvím služby sestavení Kudu nebo [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Další informace o službách správy zdrojového kódu najdete [v tématu Vytvoření úložiště (GitHub),] [Vytvoření úložiště (BitBucket)]nebo Vytvoření [nového úložiště Git (Azure Repos).]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizace služby Azure App Service 

Pokud chcete používat Azure Repos, ujistěte se, že vaše organizace Azure DevOps je propojená s vaším předplatným Azure. Další informace najdete [v tématu Nastavení účtu služby Azure DevOps Services, aby mohl nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Pro Bitbucket nebo GitHub autorizujte Azure App Service pro připojení k úložišti. Je třeba provést autorizaci se službou správy zdrojového kódu pouze jednou. 

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**. 

   ![Vyhledejte služby aplikací.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Vyberte webovou aplikaci, kterou chcete nasadit.

   ![Vyberte aplikaci.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na stránce aplikace vyberte V levé nabídce **Centrum nasazení.**
   
1. Na stránce **Centrum nasazení** vyberte **GitHub** nebo **Bitbucket**a pak vyberte **Autorizovat**. 
   
   ![Vyberte službu správy zdrojového kódu a pak vyberte Autorizovat.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. V případě potřeby se přihlaste ke službě a postupujte podle pokynů k autorizaci. 

## <a name="enable-continuous-deployment"></a>Povolení průběžného nasazování 

Po autorizaci služby správy zdrojového kódu nakonfigurujte aplikaci pro průběžné nasazení prostřednictvím integrovaného [serveru sestavení služby Kudu App Service](#option-1-use-the-app-service-build-service)nebo prostřednictvím Azure [Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Možnost 1: Použití služby sestavení služby App Service

Integrovaný server sestavení služby Kudu App Service můžete použít k nepřetržitému nasazení z GitHubu, Bitbucketu nebo Azure Repos. 

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Služby aplikací**a vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte V levé nabídce **Centrum nasazení.**
   
1. Na stránce Centrum pro nasazení vyberte autorizovaného poskytovatele správy **zdrojového kódu** a vyberte **Pokračovat**. Pro GitHub nebo Bitbucket můžete také vybrat **Změnit účet** a změnit autorizovaný účet. 
   
   > [!NOTE]
   > Pokud chcete používat Azure Repos, ujistěte se, že vaše organizace Azure DevOps Services je propojená s vaším předplatným Azure. Další informace najdete [v tématu Nastavení účtu služby Azure DevOps Services, aby mohl nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. Pro GitHub nebo Azure Repos, na stránce **zprostředkovatele sestavení,** vyberte **Služba sestavení služby App Service**a pak vyberte **Pokračovat**. Bitbucket vždy používá službu sestavení služby App Service.
   
   ![Vyberte Služba sestavení služby App Service a pak vyberte Pokračovat.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stránce **Konfigurovat:**
   
   - Pro GitHub rozbalte a vyberte **organizace**, **úložiště**a **větve,** které chcete nasadit nepřetržitě.
     
     > [!NOTE]
     > Pokud žádné úložiště nevidíte, možná budete muset autorizovat službu Azure App Service na GitHubu. Přejděte do úložiště GitHub a přejděte do **nastavení** > **aplikací** > **Autorizovaných Aplikací OAuth**. Vyberte **Azure App Service**a pak vyberte **Udělit**. Pro repozitáře organizace musíte být vlastníkem organizace udělit oprávnění.
     
   - V bitbucketu vyberte **Bitbucket Team**, **Repository**a **Branch,** které chcete nasadit nepřetržitě.
     
   - Pro Azure Repos vyberte **Azure DevOps Organization**, **Project**, **Repository**a **Branch,** které chcete nasadit nepřetržitě.
     
     > [!NOTE]
     > Pokud vaše organizace Azure DevOps není v seznamu, ujistěte se, že je propojená s vaším předplatným Azure. Další informace najdete [v tématu Nastavení účtu služby Azure DevOps Services, aby mohl nasadit do webové aplikace](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)..
     
1. Vyberte **Pokračovat**.
   
   ![Vyplňte informace o úložišti a pak vyberte Pokračovat.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po konfiguraci zprostředkovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a vyberte **možnost Dokončit**.
   
   Nové revize ve vybraném úložišti a větvi se teď neustále nasazují do aplikace App Service. Potvrzení a nasazení můžete sledovat na stránce **Centrum nasazení.**
   
   ![Sledování potvrzení a nasazení v Centru nasazení](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Možnost 2: Použití azure kanálů 

Pokud má váš účet potřebná oprávnění, můžete nastavit Azure Pipelines tak, aby se neustále nasazovaly z úložišť GitHub nebo Azure Repos. Další informace o nasazení prostřednictvím Azure Pipelines najdete [v tématu Nasazení webové aplikace do Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Pro Azure App Service k vytvoření průběžného doručování Azure Pipelines ve vaší organizaci Azure DevOps: 

- Váš účet Azure musí mít oprávnění k zápisu do Služby Azure Active Directory a k vytvoření služby. 
  
- Váš účet Azure musí mít roli **vlastníka** ve vašem předplatném Azure.

- Musíte být správcev projektu Azure DevOps, který chcete použít.

Konfigurace Azure Pipelines (preview):

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Služby aplikací**a vyberte webovou aplikaci, kterou chcete nasadit. 
   
1. Na stránce aplikace vyberte V levé nabídce **Centrum nasazení.**
   
1. Na stránce **Zprostředkovatel sestavení** vyberte **Azure Pipelines (Preview)** a pak vyberte **Pokračovat**. 
   
1. Na stránce **Konfigurovat** v části **Kód:**
   
   - Pro GitHub rozbalte a vyberte **organizace**, **úložiště**a **větve,** které chcete nasadit nepřetržitě.
     
     > [!NOTE]
     > Pokud žádné úložiště nevidíte, možná budete muset autorizovat službu Azure App Service na GitHubu. Přejděte do úložiště GitHub a přejděte do **nastavení** > **aplikací** > **Autorizovaných Aplikací OAuth**. Vyberte **Azure App Service**a pak vyberte **Udělit**. Pro repozitáře organizace musíte být vlastníkem organizace udělit oprávnění.
     
   - Pro Azure Repos vyberte **Organizaci Azure DevOps**, **Project**, **Repository**a **Branch,** které chcete nasadit nepřetržitě, nebo nakonfigurujte novou organizaci Azure DevOps.
     
     > [!NOTE]
     > Pokud vaše stávající organizace Azure DevOps není v seznamu, možná ji budete muset propojit s předplatným Azure. Další informace naleznete v [tématu Definování kanálu vydání disku CD- d.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)
     
1. Vyberte **Pokračovat**.
   
1. Pro Azure Repos, v části **Sestavení,** zadejte jazykový rámec, který by měl y azure kanály použít ke spuštění úloh sestavení a pak vyberte **Pokračovat**.
   
1. Na stránce **Test** zvolte, zda chcete povolit zátěžové testy, a pak vyberte **Pokračovat**.
   
1. V závislosti na [cenové úrovni](https://azure.microsoft.com/pricing/details/app-service/plans/)plánu služby App Service se může zobrazit stránka **Nasazení do pracovní** fáze. Zvolte, zda chcete [povolit sloty pro nasazení](deploy-staging-slots.md), a pak vyberte **Pokračovat**.
   
   > [!NOTE]
   > Azure Pipelines neumožňuje průběžné doručování do produkčního slotu. Toto omezení zabraňuje náhodnému nasazení do produkčního prostředí. Nastavte průběžné doručování do pracovního slotu, ověřte změny tam a pak swap sloty, když jste připraveni.
   
1. Po konfiguraci zprostředkovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a vyberte **možnost Dokončit**.
   
   Nové revize ve vybraném úložišti a větvi se teď neustále nasazují do aplikace App Service. Potvrzení a nasazení můžete sledovat na stránce **Centrum nasazení.**
   
   ![Sledování potvrzení a nasazení v Centru nasazení](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

Pokud chcete nepřetržité nasazování zakázat, vyberte **Odpojit** v horní části stránky **Centra nasazení** aplikace.

![Zakázání průběžného nasazování](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Použití nepodporovaných repos

Pro aplikace pro Windows můžete ručně nakonfigurovat průběžné nasazení z cloudového úložiště Git nebo Mercurial, které portál přímo nepodporuje, jako je [GitLab](https://gitlab.com/). Můžete to udělat výběrem externí pole na stránce **Centrum nasazení.** Další informace naleznete v [tématu Nastavení průběžného nasazování pomocí ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Další zdroje

* [Prozkoumejte běžné problémy s nepřetržitým nasazením](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Použití Azure PowerShellu](/powershell/azureps-cmdlets-docs)
* [Dokumentace gitu](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Vytvoření nového úložiště Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
