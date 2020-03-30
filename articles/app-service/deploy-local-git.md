---
title: Nasazení z místního úložiště Git
description: Přečtěte si, jak povolit místní nasazení Gitu do služby Azure App Service. Jedním z nejjednodušších způsobů nasazení kódu z místního počítače.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152988"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení Gitu do služby Azure App Service

Tento návod vám ukáže, jak nasadit aplikaci do [služby Azure App Service](overview.md) z úložiště Git v místním počítači.

## <a name="prerequisites"></a>Požadavky

Postupujte podle pokynů v tomto návodu:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Nainstalujte Git](https://www.git-scm.com/downloads).
  
- Mějte místní úložiště Git s kódem, který chcete nasadit. Chcete-li stáhnout ukázkové úložiště, spusťte v místním okně terminálu následující příkaz:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Nasazení pomocí serveru sestavení Kudu

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro vaši aplikaci se serverem sestavení Služby aplikací Kudu, je použití Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Získání adresy URL nasazení

Chcete-li získat adresu URL, která umožní [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) místní nasazení Gitu pro existující aplikaci, spusťte v prostředí Cloud Shell. Nahraďte \<> \<názvu aplikace a> názvů skupin názvy vaší aplikace a její skupiny prostředků Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Pokud používáte linuxapp-service-plan, musíte přidat tento parametr: --runtime python|3.7


Nebo chcete-li vytvořit novou aplikaci [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) s podporou Gitu, spusťte v cloudovém prostředí s parametrem. `--deployment-local-git` Nahraďte \<> \<název aplikace,> \<názvy skupin a> názvy plánu názvy nové aplikace Git, její skupiny prostředků Azure a plánu služby Azure App Service.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Buď příkaz vrátí adresu `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL, například: . Tuto adresu URL použijte k nasazení aplikace v dalším kroku.

Namísto použití této adresy URL na úrovni účtu můžete také povolit místní Git pomocí přihlašovacích údajů na úrovni aplikace. Služba Azure App Service automaticky generuje tato pověření pro každou aplikaci. 

Získejte přihlašovací údaje aplikace spuštěním následujícího příkazu v prostředí Cloud Shell. Nahraďte \<název \<aplikace> a> názvu skupiny názvem aplikace názvem aplikace a názvem skupiny prostředků Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Použijte adresu URL, která se vrátí k nasazení aplikace v dalším kroku.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace

1. Otevřete okno místního terminálu do místního úložiště Git a přidejte vzdálený Azure. V následujícím příkazu \<nahraďte url> adresou URL adresou URL pomocí adresy URL specifické pro uživatele nasazení nebo adresy URL specifické pro aplikaci, kterou jste získali z předchozího kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Přejde mejda na vzdálenější azure s `git push azure master`. 
   
1. V okně **Správce přihlašovacích údajů Git** zadejte heslo uživatele [nasazení](#configure-a-deployment-user), ne heslo pro přihlášení do Azure.
   
1. Zkontrolujte výstup. Může se zobrazit automatizace specifická pro modul runtime, například MSBuild pro ASP.NET, `npm install` pro Node.js a `pip install` pro Python. 
   
1. Přejděte do své aplikace na webu Azure Portal a ověřte, že obsah je nasazený.

## <a name="deploy-with-azure-pipelines-builds"></a>Nasazení pomocí sestavení Azure Pipelines

Pokud má váš účet potřebná oprávnění, můžete nastavit Azure Pipelines (Preview) tak, aby povolily místní nasazení Gitu pro vaši aplikaci. 

- Váš účet Azure musí mít oprávnění k zápisu do Služby Azure Active Directory a k vytvoření služby. 
  
- Váš účet Azure musí mít roli **vlastníka** ve vašem předplatném Azure.

- Musíte být správcev projektu Azure DevOps, který chcete použít.

Povolení místního nasazení Gitu pro vaši aplikaci pomocí Azure Pipelines (Preview):

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**. 

1. Vyberte aplikaci Azure App Service a v levé nabídce vyberte **Centrum nasazení.**
   
1. Na stránce **Centrum nasazení** vyberte **Místní Git**a pak vyberte **Pokračovat**. 
   
   ![Vyberte Místní Git a pak vyberte Pokračovat.](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na stránce **Zprostředkovatel sestavení** vyberte **Azure Pipelines (Preview)** a pak vyberte **Pokračovat**. 
   
   ![Vyberte Azure Pipelines (Preview) a pak vyberte Pokračovat.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na stránce **Konfigurace** nakonfigurujte novou organizaci Azure DevOps nebo určete existující organizaci a pak vyberte **Pokračovat**.
   
   > [!NOTE]
   > Pokud vaše stávající organizace Azure DevOps není v seznamu, možná ji budete muset propojit s předplatným Azure. Další informace naleznete v [tématu Definování kanálu vydání disku CD- d.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)
   
1. V závislosti na [cenové úrovni](https://azure.microsoft.com/pricing/details/app-service/plans/)plánu služby App Service se může zobrazit stránka **Nasazení do pracovní** fáze. Zvolte, zda chcete [povolit sloty pro nasazení](deploy-staging-slots.md), a pak vyberte **Pokračovat**.
   
1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Dokončit**.
   
1. Až bude Azure Pipeline připravený, zkopírujte adresu URL úložiště Git ze stránky **Centrum nasazení,** abyste ji použili v dalším kroku. 
   
   ![Kopírování adresy URL úložiště Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. V místním okně terminálu přidejte do místního úložiště Git dálkové ovládání Azure. V příkazu \<nahraďte url> URL úložiště Git, které jste získali z předchozího kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Přejde mejda na vzdálenější azure s `git push azure master`. 
   
1. Na stránce **Správce přihlašovacích údajů Git** se přihlaste pomocí svého uživatelského jména visualstudio.com. Další metody ověřování najdete v [tématu Přehled ověřování služby Azure DevOps](/vsts/git/auth-overview?view=vsts)Services .
   
1. Po dokončení nasazení zobrazte průběh `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`sestavení na adrese `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`a průběh nasazení na .
   
1. Přejděte do své aplikace na webu Azure Portal a ověřte, že obsah je nasazený.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Poradce při potížích s nasazením

Při publikování v aplikaci App Service v Azure se mohou zobrazit následující běžné chybové zprávy:

|Zpráva|Příčina|Řešení
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikace není v provozu.|Spusťte aplikaci na webu Azure Portal. Nasazení Gitu není dostupné, když je webová aplikace zastavená.|
|`Couldn't resolve host 'hostname'`|Informace o adrese pro vzdálené "azure" je nesprávná.|Pomocí `git remote -v` příkazu můžete vypsat všechny vzdálené ovladače spolu s přidruženou adresou URL. Ověřte, zda je adresa URL vzdáleného zařízení azure správná. V případě potřeby odeberte a znovu vytvořte tento dálkový ovladač pomocí správné adresy URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Větev jste nezadali vprůběhu `git push`nebo jste `push.default` nenastavili hodnotu v . `.gitconfig`|Spustit `git push` znovu a zadat hlavní `git push azure master`větev: .|
|`src refspec [branchname] does not match any.`|Pokusili jste se zatlačit na jinou větev než hlavní na vzdáleném "azure".|Spustit `git push` znovu a zadat hlavní `git push azure master`větev: .|
|`RPC failed; result=22, HTTP code = 5xx.`|K této chybě může dojít, pokud se pokusíte tlačit velké úložiště git přes protokol HTTPS.|Změňte konfiguraci git v místním `postBuffer` počítači, aby se větší. Například: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Nasadíte aplikaci Node.js se souborem _package.json,_ který určuje další požadované moduly.|Zkontrolujte `npm ERR!` chybové zprávy před touto chybou pro další kontext na selhání. Následují známé příčiny této chyby a `npm ERR!` odpovídající zprávy:<br /><br />**Poškozený soubor package.json**:`npm ERR! Couldn't read dependencies.`<br /><br />**Nativní modul nemá binární distribuci pro Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />– nebo – <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Další zdroje

- [Dokumentace projektu Kudu](https://github.com/projectkudu/kudu/wiki)
- [Průběžné nasazení do služby Azure App Service](deploy-continuous-deployment.md)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
