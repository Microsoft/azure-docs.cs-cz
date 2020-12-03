---
title: Nasazení z místního úložiště Git
description: Naučte se, jak povolit místní nasazení Git Azure App Service. Jedním z nejjednodušších způsobů, jak nasadit kód z místního počítače.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 84e257111e8da0546cf104e0cc5d3ac95a9294ba
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558670"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení Gitu pro Azure App Service

V této příručce se dozvíte, jak nasadit aplikaci pro [Azure App Service](overview.md) z úložiště Git na místním počítači.

## <a name="prerequisites"></a>Předpoklady

Postup je popsaný v tomto návodu:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Nainstalujte Git](https://www.git-scm.com/downloads).
  
- Mít místní úložiště Git s kódem, který chcete nasadit. Chcete-li stáhnout ukázkové úložiště, spusťte následující příkaz v místním okně terminálu:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-with-kudu-build-server"></a>Nasazení s Kudu Build serverem

Nejjednodušší způsob, jak povolit místní nasazení Git pro vaši aplikaci pomocí serveru Kudu App Service Build Server, je použití Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Získat adresu URL nasazení

Pokud chcete získat adresu URL pro povolení místního nasazení Git pro existující aplikaci, spusťte příkaz [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) v Cloud Shell. Nahraďte \<app-name> a \<group-name> názvem vaší aplikace a její skupiny prostředků Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Pokud používáte plán služby aplikace pro Linux, je nutné přidat tento parametr:--runtime Python | 3.7


Nebo pokud chcete vytvořit novou aplikaci s povoleným Git, spusťte [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) v Cloud shell s `--deployment-local-git` parametrem. Nahraďte \<app-name> , \<group-name> a \<plan-name> názvem nové aplikace pro Git, její skupinou prostředků Azure a jejím Azure App Servicem plánem.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Buď příkaz vrátí adresu URL jako: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Pomocí této adresy URL můžete aplikaci nasadit v dalším kroku.

Místo použití této adresy URL na úrovni účtu můžete povolit místní Git taky pomocí přihlašovacích údajů na úrovni aplikace. Azure App Service automaticky generuje tato pověření pro každou aplikaci. 

Přihlašovací údaje aplikace získáte spuštěním následujícího příkazu v Cloud Shell. Nahraďte \<app-name> a názvem \<group-name> vaší aplikace a názvem skupiny prostředků Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Použijte adresu URL, která se vrátí k nasazení aplikace v dalším kroku.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace

1. Otevřete místní okno terminálu pro místní úložiště Git a přidejte vzdálené úložiště Azure. V následujícím příkazu nahraďte adresu \<url> URL specifickou pro konkrétního uživatele nebo adresu URL specifickou pro konkrétní aplikaci, kterou jste získali v předchozím kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nahrajte službu Azure Remote pomocí služby `git push azure main` . 
   
1. V okně **Správce přihlašovacích údajů Git** zadejte [heslo uživatele nasazení](#configure-a-deployment-user), ne heslo pro přihlášení k Azure.
   
1. Zkontrolujte výstup. Může se zobrazit automatizace specifická pro modul runtime, jako je MSBuild pro ASP.NET, `npm install` pro Node.js a `pip install` pro Python. 
   
1. Přejděte do aplikace v Azure Portal a ověřte, že je obsah nasazený.

## <a name="deploy-with-azure-pipelines-builds"></a>Nasazení pomocí Azure Pipelines sestavení

Pokud má váš účet potřebná oprávnění, můžete nastavit Azure Pipelines (Preview), abyste povolili místní nasazení Git pro vaši aplikaci. 

- Váš účet Azure musí mít oprávnění k zápisu do Azure Active Directory a vytvoření služby. 
  
- Váš účet Azure musí mít ve svém předplatném Azure roli **vlastníka** .

- Musíte být správce v projektu Azure DevOps, který chcete použít.

Povolení místního nasazení Git pro vaši aplikaci pomocí Azure Pipelines (Preview):

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**. 

1. Vyberte svou aplikaci Azure App Service a v levé nabídce vyberte **centrum nasazení** .
   
1. Na stránce **centrum nasazení** vyberte **místní Git** a pak vyberte **pokračovat**. 
   
   ![Vyberte místní Git a pak vyberte pokračovat.](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na stránce **poskytovatel sestavení** vyberte možnost **Azure Pipelines (Preview)** a pak vyberte **pokračovat**. 
   
   ![Vyberte Azure Pipelines (Preview) a pak vyberte pokračovat.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na stránce **Konfigurace** nakonfigurujte novou organizaci Azure DevOps nebo zadejte existující organizaci a pak vyberte **pokračovat**.
   
   > [!NOTE]
   > Pokud vaše stávající organizace Azure DevOps není v seznamu uvedená, budete ji muset propojit s vaším předplatným Azure. Další informace najdete v tématu [definice kanálu verze CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. V závislosti na [cenové úrovni](https://azure.microsoft.com/pricing/details/app-service/plans/)plánu App Service se může zobrazit stránka **nasazení na přípravu** . Zvolte, jestli se mají [Povolit sloty nasazení](deploy-staging-slots.md), a pak vyberte **pokračovat**.
   
1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Dokončit**.
   
1. Až bude kanál Azure připravený, zkopírujte adresu URL úložiště Git ze stránky **centra nasazení** , která se použije v dalším kroku. 
   
   ![Zkopírování adresy URL úložiště Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. V místním okně terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. V příkazu nahraďte \<url> adresu URL úložiště Git, které jste získali z předchozího kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nahrajte službu Azure Remote pomocí služby `git push azure main` . 
   
1. Na stránce **Správce přihlašovacích údajů Git** se přihlaste pomocí uživatelského jména VisualStudio.com. Další metody ověřování najdete v tématu [Přehled ověřování Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. Po dokončení nasazení si prohlédněte průběh sestavení v `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` a průběh nasazení v `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` .
   
1. Přejděte do aplikace v Azure Portal a ověřte, že je obsah nasazený.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Řešení potíží s nasazením

Když použijete Git k publikování App Service aplikace v Azure, může se zobrazit následující běžné chybové zprávy:

|Zpráva|Příčina|Řešení
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikace není v provozu.|Spusťte aplikaci v Azure Portal. Nasazení Git není po zastavení webové aplikace dostupné.|
|`Couldn't resolve host 'hostname'`|Informace o adrese pro vzdálené úložiště Azure jsou nesprávné.|Pomocí `git remote -v` příkazu můžete vypsat všechny vzdálené části společně s přidruženou adresou URL. Ověřte, jestli je adresa URL vzdáleného webu Azure správná. V případě potřeby tento vzdálený příkaz odeberte a znovu vytvořte pomocí správné adresy URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nezadali jste větev během `git push` nebo jste nenastavili `push.default` hodnotu v `.gitconfig` .|Spusťte `git push` znovu a určete hlavní větev: `git push azure main` .|
|`src refspec [branchname] does not match any.`|Pokusili jste se odeslat do jiné jiné větve než na vzdáleném Azure.|Spusťte `git push` znovu a určete hlavní větev: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|K této chybě může dojít, pokud se pokusíte odeslat velké úložiště Git přes HTTPS.|Změňte konfiguraci Gitu na místním počítači, aby byla `postBuffer` větší. Příklad: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Nasadili jste aplikaci Node.js s _package.jsv_ souboru, který určuje další požadované moduly.|`npm ERR!`Před touto chybou zkontrolujte chybové zprávy pro další kontext chyby. Níže jsou uvedené známé příčiny této chyby a odpovídající `npm ERR!` zprávy:<br /><br />**Nesprávně vytvořený package.jsv souboru**: `npm ERR! Couldn't read dependencies.`<br /><br />**Nativní modul nemá pro Windows binární distribuci**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />nebo <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Další zdroje informací

- [Dokumentace k Project Kudu](https://github.com/projectkudu/kudu/wiki)
- [Průběžné nasazování do Azure App Service](deploy-continuous-deployment.md)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
