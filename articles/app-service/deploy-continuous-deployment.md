---
title: Konfigurace průběžného nasazování
description: Naučte se, jak povolit CI/CD pro Azure App Service z GitHubu, BitBucket, Azure Repos nebo jiných úložišť. Vyberte kanál sestavení, který vyhovuje vašim potřebám.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 52f0db739cff9614dc4e9f5ef71d582e926fc65a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470264"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Průběžné nasazování do Azure App Service

[Azure App Service](overview.md) umožňuje průběžné nasazování z [GitHubu](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)a [Azure Repos](/azure/devops/repos/git/creatingrepo) úložišť pomocí nejnovějších aktualizací.

> [!NOTE]
> Stránka **centra pro vývoj (Classic)** v Azure Portal, což je staré prostředí pro nasazení, bude zastaralá v březnu 2021. Tato změna nebude mít vliv na žádná existující nastavení nasazení v aplikaci a můžete pokračovat ve správě nasazení aplikace na stránce **centra nasazení** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Konfigurovat zdroj nasazení

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

1. V nabídce vlevo klikněte na nastavení **centra nasazení**  >  . 

1. V části **zdroj** vyberte jednu z možností CI/CD.

    ![Ukazuje, jak zvolit zdroj nasazení v centru nasazení pro Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Pro jednotlivé kroky vyberte kartu, která odpovídá vašemu výběru.

# <a name="github"></a>[GitHub](#tab/github)

4. [Akce GitHubu](#how-the-github-actions-build-provider-works) je výchozím poskytovatelem sestavení. Pokud ho chcete změnit, klikněte na **změnit poskytovatele**  >  **App Service sestavovací služba** (Kudu) > **OK**.

    > [!NOTE]
    > Pokud chcete jako poskytovatele sestavení pro aplikaci App Service použít Azure Pipelines, nekonfigurujte ji v App Service. Místo toho nakonfigurujte CI/CD přímo z Azure Pipelines. Možnost **Azure Pipelines** hned odkazuje na správný směr.

1. Pokud provádíte nasazování z GitHubu poprvé, klikněte na **autorizovat** a postupujte podle výzev k autorizaci. Pokud chcete nasadit z úložiště jiného uživatele, klikněte na **změnit účet**.

1. Po autorizaci účtu Azure pomocí GitHubu vyberte **organizaci**, **úložiště** a **větev** a nakonfigurujte CI/CD pro.

1. Když je zvoleným poskytovatelem sestavení akce GitHubu, můžete vybrat požadovaný soubor pracovního postupu pomocí **zásobníku modulu runtime** a rozevíracích seznamů **verzí** . Azure potvrdí tento soubor pracovního postupu do vybraného úložiště GitHub pro zpracování úloh sestavení a nasazení. Chcete-li zobrazit soubor před uložením změn, klikněte na tlačítko **Náhled souboru**.

    > [!NOTE]
    > App Service zjistí [Nastavení zásobníku jazyka](configure-common.md#configure-language-stack-settings) vaší aplikace a vybere nejvhodnější šablonu pracovního postupu. Pokud zvolíte jinou šablonu, může dojít k nasazení aplikace, která není správně spuštěná. Další informace najdete v tématu [Jak funguje poskytovatel sestavení akcí GitHubu](#how-the-github-actions-build-provider-works).

1. Klikněte na **Uložit**.
   
    Nová potvrzení ve vybraném úložišti a větvi se teď do vaší aplikace App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na kartě **protokoly** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

Integrace BitBucket používá pro automatizaci sestavení službu App Service Build Services (Kudu).

4. Pokud nasazujete z BitBucket poprvé, klikněte na **autorizovat** a postupujte podle výzev pro autorizaci. Pokud chcete nasadit z úložiště jiného uživatele, klikněte na **změnit účet**.

1. Pro Bitbucket vyberte **tým** BitBucket, **úložiště** a **větev** , které chcete průběžně nasadit.

1. Klikněte na **Uložit**.
   
    Nová potvrzení ve vybraném úložišti a větvi se teď do vaší aplikace App Service nasadí průběžně. Potvrzení a nasazení můžete sledovat na kartě **protokoly** .
   
# <a name="local-git"></a>[Místní Git](#tab/local)

Pokud chcete Azure App Service, přečtěte si téma [místní nasazení Gitu](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos jako zdroj nasazení je podpora pro aplikace pro Windows.
>

4. Výchozím poskytovatelem sestavení je služba App Service Build Service (Kudu).

    > [!NOTE]
    > Pokud chcete jako poskytovatele sestavení pro aplikaci App Service použít Azure Pipelines, nekonfigurujte ji v App Service. Místo toho nakonfigurujte CI/CD přímo z Azure Pipelines. Možnost **Azure Pipelines** hned odkazuje na správný směr.

1. Vyberte organizaci, **projekt**, **úložiště** a **větev** **Azure DevOps**, které chcete průběžně nasadit. 

    Pokud vaše organizace DevOps není uvedená, ještě není propojená s vaším předplatným Azure. Další informace najdete v tématu [vytvoření připojení ke službě Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Zakázání průběžného nasazování

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

1. V nabídce vlevo klikněte na nastavení **centrum nasazení**  >    >  **Odpojit**. 

    ![Ukazuje, jak odpojit svou cloudovou složku s vaší aplikací App Service v Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Ve výchozím nastavení se soubor pracovního postupu akcí GitHubu zachová ve vašem úložišti, ale bude i nadále aktivovat nasazení do vaší aplikace. Pokud ho chcete odstranit z úložiště, vyberte **Odstranit soubor pracovního postupu**.

1. Klikněte na **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Jak funguje poskytovatel sestavení akcí GitHubu

Poskytovatel sestavení akcí GitHubu je možnost pro [CI/CD z GitHubu](#configure-deployment-source)a při nastavení CI/CD provede následující kroky:

- Uloží soubor pracovního postupu akce GitHubu do úložiště GitHub, aby zpracovával úlohy sestavení a nasazení App Service.
- Přidá profil publikování aplikace jako tajný klíč GitHubu. Soubor pracovního postupu používá tento tajný klíč k ověření pomocí App Service.
- Zachycuje informace z [pracovních postupů spuštění protokolů](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) a zobrazí je na kartě **protokoly** v **centru nasazení** vaší aplikace.

Poskytovatele sestavení akcí GitHubu můžete přizpůsobit následujícími způsoby:

- Až se soubor pracovního postupu vygeneruje v úložišti GitHubu, přizpůsobte ho. Další informace najdete v tématu [syntaxe pracovního postupu pro akce GitHubu](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Stačí se ujistit, že se pracovní postup nasadí App Service s akcí [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) .
- Pokud je vybraná větev chráněná, můžete si i nadále zobrazit náhled souboru pracovního postupu bez uložení konfigurace a pak ho ručně přidat do úložiště. Tato metoda neposkytuje integraci protokolu s Azure Portal.
- Místo profilu publikování nasaďte pomocí [instančního objektu ve službě](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Ověřování pomocí instančního objektu

Tato volitelná konfigurace nahrazuje výchozí ověřování pomocí profilů publikování ve vygenerovaném souboru pracovního postupu.

1. Vygenerujte instanční objekt pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). V následujícím příkladu nahraďte *\<subscription-id>* , *\<group-name>* a *\<app-name>* vlastními hodnotami:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > V případě zabezpečení udělte minimálnímu požadovanému přístupu k instančnímu objektu. Obor v předchozím příkladu je omezený na konkrétní App Service aplikaci, a ne na celou skupinu prostředků.
    
1. Uložte celý výstup JSON pro další krok, včetně nejvyšší úrovně `{}` .

1. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

1. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného kódu jako `AZURE_CREDENTIALS` .

1. V souboru pracovního postupu vygenerovaném **centrem nasazení** upravte `azure/webapps-deploy` krok s kódem, jako je následující příklad (který je upravený ze souboru pracovního postupu Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Nasazení z jiných úložišť

Pro aplikace pro Windows můžete průběžné nasazování nakonfigurovat ručně z cloudového úložiště Git nebo Mercurial, které portál přímo nepodporuje, jako je třeba [GitLab](https://gitlab.com/). Uděláte to tak, že v rozevíracím seznamu **zdroj** vyberete externí Git. Další informace najdete v tématu [Nastavení průběžného nasazování pomocí ručních kroků](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Další zdroje informací

* [Nasazení z Azure Pipelines do Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Prozkoumat běžné problémy s průběžným nasazováním](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Použití Azure Powershell](/powershell/azure/)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
