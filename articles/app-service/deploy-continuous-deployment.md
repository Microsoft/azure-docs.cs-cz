---
title: Konfigurace průběžného nasazování
description: Naučte se, jak povolit CI/CD pro Azure App Service z GitHubu, BitBucket, Azure Repos nebo jiných úložišť. Vyberte kanál sestavení, který vyhovuje vašim potřebám.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: b7730558e2a660b0cf00a5b6962d1e2275dd472c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984402"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do Azure App Service

[Azure App Service](overview.md) umožňuje průběžné nasazování z GitHubu, BitBucket a [Azure Repos](https://azure.microsoft.com/services/devops/repos/) úložišť pomocí nejnovějších aktualizací. V tomto článku se dozvíte, jak používat Azure Portal k průběžné nasazování vaší aplikace prostřednictvím služby sestavení Kudu nebo [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Další informace o službách správy zdrojového kódu najdete v tématech [Vytvoření úložiště (GitHub)], [Vytvoření úložiště (Bitbucket)]nebo [vytvoření nového úložiště Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizovat Azure App Service 

Pokud chcete použít Azure Repos, ujistěte se, že je vaše organizace Azure DevOps propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).

V případě Bitbucket nebo GitHubu autorizujte Azure App Service pro připojení k úložišti. Pouze jednou je třeba autorizovat se službou správy zdrojového kódu. 

1. V [Azure Portal](https://portal.azure.com)vyhledejte  **App Services** a vyberte.

   ![Vyhledejte App Services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Vyberte App Service, kterou chcete nasadit.

   ![Vyberte svou aplikaci.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .
   
1. Na stránce **centrum nasazení** vyberte **GitHub** nebo **Bitbucket**a potom vyberte **autorizovat**. 
   
   ![Vyberte službu správy zdrojového kódu a potom vyberte autorizovat.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. V případě potřeby se přihlaste ke službě a postupujte podle pokynů pro autorizaci. 

## <a name="enable-continuous-deployment"></a>Povolení průběžného nasazování 

Po autorizaci služby správy zdrojového kódu nakonfigurujte aplikaci pro průběžné nasazování prostřednictvím integrovaného [Kudu App Service](#option-1-kudu-app-service) sestavovacího serveru nebo prostřednictvím [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Možnost 1: Kudu App Service

K průběžnému nasazování z GitHubu, Bitbucket nebo Azure Repos můžete použít vestavěný Kudu App Service Build Server. 

1. V [Azure Portal](https://portal.azure.com)vyhledejte **App Services**a pak vyberte App Service, které chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .
   
1. Vyberte svého autorizovaného poskytovatele správy zdrojů na stránce **centrum nasazení** a vyberte **pokračovat**. V případě GitHubu nebo Bitbucket můžete také vybrat **změnit účet** a změnit autorizovaný účet. 
   
   > [!NOTE]
   > Pokud chcete použít Azure Repos, ujistěte se, že je vaše organizace Azure DevOps Services propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
   
1. Pro GitHub nebo Azure Repos na stránce **poskytovatel sestavení** vyberte **App Service sestavovací služba**a pak vyberte **pokračovat**. Bitbucket vždy používá službu sestavení App Service.
   
   ![Vyberte App Service sestavovací služba a pak vyberte pokračovat.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stránce **Konfigurace** :
   
   - V části GitHub rozbalte rozevírací seznam a vyberte **organizaci**, **úložiště**a **větev** , kterou chcete nasadit průběžně.
     
     > [!NOTE]
     > Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure App Service v GitHubu. Přejděte do úložiště GitHub a přejděte do **Nastavení**  >  **aplikace**  >  **autorizované aplikace OAuth**. Vyberte **Azure App Service**a pak vyberte **udělit**. V případě úložišť organizace musíte být vlastníkem organizace, abyste udělili oprávnění.
     
   - Pro Bitbucket vyberte **tým**BitBucket, **úložiště**a **větev** , které chcete průběžně nasadit.
     
   - V Azure Repos vyberte organizaci, **projekt**, **úložiště**a **větev** **Azure DevOps**, které chcete průběžně nasadit.
     
     > [!NOTE]
     > Pokud vaše organizace Azure DevOps není uvedená, ujistěte se, že je propojená s vaším předplatným Azure. Další informace najdete v tématu [Nastavení účtu Azure DevOps Services, aby ho bylo možné nasadit do webové aplikace](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
     
1. Vyberte **Pokračovat**.
   
   ![Vyplňte informace o úložišti a pak vyberte pokračovat.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po konfiguraci poskytovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a pak vyberte **Dokončit**.
   
1. Nová potvrzení ve vybraném úložišti a větvi se teď do vaší aplikace App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na stránce **centra nasazení** .
   
   ![Sledování potvrzení a nasazení v centru nasazení](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Možnost 2: Azure Pipelines 

Pokud má váš účet potřebná oprávnění, můžete nastavit Azure Pipelines pro průběžné nasazování z GitHubu nebo Azure Repos. Další informace o nasazení prostřednictvím Azure Pipelines najdete v tématu [nasazení webové aplikace do Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Požadavky

Aby bylo možné Azure App Service vytvořit průběžné doručování pomocí Azure Pipelines, vaše organizace Azure DevOps by měla mít následující oprávnění: 

- Váš účet Azure musí mít oprávnění k zápisu do Azure Active Directory a vytvoření služby. 
  
- Váš účet Azure musí mít ve svém předplatném Azure roli **vlastníka** .

- Musíte být správce v projektu Azure DevOps, který chcete použít.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. V [Azure Portal](https://portal.azure.com)vyhledejte **App Services**a pak vyberte App Service, které chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .

1. Na stránce **centrum nasazení** vyberte **GitHub** jako poskytovatele správy zdrojového kódu a vyberte **pokračovat**. V případě **GitHubu**můžete vybrat **změnit účet** a změnit autorizovaný účet.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::
   
1. Na stránce **poskytovatel sestavení** vyberte možnost **Azure Pipelines (Preview)** a pak vyberte **pokračovat**.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::
   
1. Na stránce **Konfigurace** v části **kód** vyberte **organizaci**, **úložiště**a **větev** , kterou chcete nasadit nepřetržitě, a vyberte **pokračovat**.
     
     > [!NOTE]
     > Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure App Service v GitHubu. Přejděte do úložiště GitHub a přejděte do **Nastavení**  >  **aplikace**  >  **autorizované aplikace OAuth**. Vyberte **Azure App Service**a pak vyberte **udělit**. V případě úložišť organizace musíte být vlastníkem organizace, abyste udělili oprávnění.
       
    V části **Build (sestavení** ) zadejte organizaci Azure DevOps, Project, Language Framework, kterou Azure Pipelines použít ke spouštění úloh sestavení, a pak vyberte **pokračovat**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::

1. Po konfiguraci poskytovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a pak vyberte **Dokončit**.

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::
   
1. Nová potvrzení ve vybraném úložišti a větvi se teď do App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na stránce **centra nasazení** .
   
   ![Sledování potvrzení a nasazení v centru nasazení](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. V [Azure Portal](https://portal.azure.com)vyhledejte **App Services**a pak vyberte App Service, které chcete nasadit. 
   
1. Na stránce aplikace vyberte v nabídce vlevo možnost **centrum nasazení** .

1. Na stránce **centrum nasazení** vyberte **Azure Repos** jako poskytovatele správy zdrojového kódu a vyberte **pokračovat**.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::

1. Na stránce **poskytovatel sestavení** vyberte možnost **Azure Pipelines (Preview)** a pak vyberte **pokračovat**.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::

1. Na stránce **Konfigurace** v části **kód** vyberte **organizaci**, **úložiště**a **větev** , kterou chcete nasadit nepřetržitě, a vyberte **pokračovat**.

   > [!NOTE]
   > Pokud vaše stávající organizace Azure DevOps není v seznamu uvedená, budete ji muset propojit s vaším předplatným Azure. Další informace najdete v tématu [definice kanálu verze CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   V části **Build (sestavení** ) zadejte organizaci Azure DevOps, Project, Language Framework, kterou Azure Pipelines použít ke spouštění úloh sestavení, a pak vyberte **pokračovat**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::

1. Po konfiguraci poskytovatele sestavení zkontrolujte nastavení na stránce **Souhrn** a pak vyberte **Dokončit**.  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Snímek obrazovky se stránkou centra nasazení App Service.":::

1. Nová potvrzení ve vybraném úložišti a větvi se teď do App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na stránce **centra nasazení** .

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

Chcete-li zakázat průběžné nasazování, vyberte možnost **Odpojit** v horní části stránky **centra nasazení** vaší aplikace.

![Zakázání průběžného nasazování](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Použití nepodporovaných úložišť

Pro aplikace pro Windows můžete průběžné nasazování nakonfigurovat ručně z cloudového úložiště Git nebo Mercurial, které portál přímo nepodporuje, jako je třeba [GitLab](https://gitlab.com/). Provedete to tak, že vyberete externí pole na stránce **centra nasazení** . Další informace najdete v tématu [Nastavení průběžného nasazování pomocí ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Další zdroje

* [Prozkoumat běžné problémy s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Použití Azure Powershell](/powershell/azure/)
* [Dokumentace k Gitu](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Vytvoření úložiště (GitHub)]: https://help.github.com/articles/create-a-repo
[Vytvoření úložiště (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Vytvoření nového úložiště Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo