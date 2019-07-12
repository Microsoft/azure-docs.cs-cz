---
title: Nasazení z místního úložiště Git – Azure App Service
description: Zjistěte, jak povolit místní nasazení z Gitu do služby Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836919"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení z Gitu do služby Azure App Service

Tato příručka ukazuje, jak nasadit aplikaci do [služby Azure App Service](overview.md) z úložiště Git v místním počítači.

## <a name="prerequisites"></a>Požadavky

Postupovat podle kroků v této příručce s postupy:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Nainstalovat Git](https://www.git-scm.com/downloads).
  
- Máte místní úložiště Git s kódem, který chcete nasadit. Pokud chcete stáhnout ukázkové úložiště, spusťte následující příkaz v okně místního terminálu:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Nasazení pomocí Kudu serveru sestavení

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro aplikace s využitím Kudu App Service server sestavení je chcete použít Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Získat adresu URL nasazení

Pokud chcete získat adresu URL pro povolení místního nasazení Gitu pro existující aplikace, spusťte [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) ve službě Cloud Shell. Nahraďte \<název aplikace > a \<skupiny name > s názvy vaší aplikace a jeho skupina prostředků Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Nebo pokud chcete vytvořit novou aplikaci s podporou Git, spusťte [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ve službě Cloud Shell s `--deployment-local-git` parametru. Nahraďte \<název aplikace >, \<název skupiny >, a \<plán name > s názvy pro novou aplikaci Git, jeho skupina prostředků Azure a její plán služby App Service.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Buď příkaz vrátí adresu URL podobnou této: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Pomocí této adresy URL pro nasazení vaší aplikace v dalším kroku.

Nemusíte používat tuto adresu URL na úrovni účtu, můžete také povolit místního Gitu s použitím přihlašovací údaje na úrovni aplikace. Azure App Service automaticky generuje tyto přihlašovací údaje pro každou aplikaci. 

Získání přihlašovacích údajů aplikaci spuštěním následujícího příkazu ve službě Cloud Shell. Nahraďte \<název aplikace > a \<skupiny name > s názvem vaší aplikace a název skupiny prostředků Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Použijte adresu URL, která se vrátí k nasazení své aplikace v dalším kroku.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace

1. Otevřete okně místního terminálu do místního úložiště Git a přidejte vzdálené prostředí Azure. V následujícím příkazu nahraďte \<url > adresa URL nasazení konkrétního uživatele nebo adresa URL konkrétní aplikace, které jste získali v předchozím kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nasdílením změn do Azure pomocí vzdáleného `git push azure master`. 
   
1. V **Git Credential Manageru** okno, zadejte vaše [heslo uživatele nasazení](#configure-a-deployment-user), ne Azure přihlášení pomocí hesla.
   
1. Zkontrolujte výstup. Automatizace specifické pro modul runtime, jako je MSBuild pro technologii ASP.NET, může se zobrazit `npm install` pro Node.js, a `pip install` pro Python. 
   
1. Přejděte do vaší aplikace na webu Azure Portal ověřte, zda je obsah nasazeny.

## <a name="deploy-with-azure-pipelines-builds"></a>Nasazení s využitím Azure kanály sestavení

Pokud váš účet nemá potřebná oprávnění, můžete nastavit kanály Azure (Preview) umožňující místní nasazení přes Git pro vaši aplikaci. 

- Váš účet Azure musí mít oprávnění k zápisu do služby Azure Active Directory a vytvořte službu. 
  
- Musí mít váš účet Azure **vlastníka** role ve vašem předplatném Azure.

- Musíte být správcem v projektu Azure DevOps, které chcete použít.

Pokud chcete povolit místní nasazení přes Git pro vaši aplikaci s kanály Azure (Preview):

1. Přejděte na stránku aplikace služby Azure App Service v [webu Azure portal](https://portal.azure.com)a vyberte **Deployment Center** v levé nabídce.
   
1. Na **Deployment Center** stránce **místního Gitu**a pak vyberte **pokračovat**. 
   
   ![Vyberte místní Git a pak vyberte pokračovat](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na **sestavení zprostředkovatele** stránce **kanály Azure (Preview)** a pak vyberte **pokračovat**. 
   
   ![Vyberte kanály Azure (Preview) a pak vyberte pokračovat.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na **konfigurovat** nakonfigurujte novou organizaci Azure DevOps, nebo zadejte existující organizace a pak vyberte **pokračovat**.
   
   > [!NOTE]
   > Pokud není uvedená vaše stávající organizace Azure DevOps, budete muset propojit se svým předplatným Azure. Další informace najdete v tématu [definovat kanál pro vydávání verzí CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. V závislosti na plánu služby App Service [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/plans/), může se zobrazit **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má [povolí sloty nasazení](deploy-staging-slots.md)a pak vyberte **pokračovat**.
   
1. Na **Souhrn** stránky, zkontrolujte nastavení a pak vyberte **Dokončit**.
   
1. Při zřetězení příkazů Azure je připraveno, zkopírujte adresu URL úložiště Git **Deployment Center** stránku pro použití v dalším kroku. 
   
   ![Zkopírujte adresu URL úložiště Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. V okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. V příkazu nahraďte \<url > s adresou URL úložiště Git, které jste získali v předchozím kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nasdílením změn do Azure pomocí vzdáleného `git push azure master`. 
   
1. Na **Git Credential Manageru** stránce, přihlaste se pomocí své uživatelské jméno visualstudio.com. Jiné metody ověřování, najdete v části [Přehled ověřování služby Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. Jakmile se nasazení dokončí, zobrazit průběh sestavení na `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`a průběh nasazení na `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Přejděte do vaší aplikace na webu Azure Portal ověřte, zda je obsah nasazeny.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Řešení potíží s nasazení

Při publikování pro aplikaci služby App Service v Azure pomocí Gitu, může se zobrazit následující běžné chybové zprávy:

|Message|Příčina|Řešení
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikace nebude funkční ani po.|Spusťte aplikaci na webu Azure Portal. Nasazení z Gitu není k dispozici, když se zastaví webovou aplikaci.|
|`Couldn't resolve host 'hostname'`|Informace o adrese pro "azure" vzdálené je nesprávný.|Použití `git remote -v` příkazu zobrazte výpis všech Vzdálená úložiště, spolu s přidružené adresy URL. Ověřte správnost adresy URL pro "azure" vzdálené. V případě potřeby odebrat a znovu vytvořit toto vzdálené používá správnou adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Jste neurčili, nevložily větve během `git push`, nebo jste nenastavili `push.default` hodnota v `.gitconfig`.|Spustit `git push` znovu, určení hlavní větve: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Pokusili jste se nasdílením změn do větve pouze hlavní na "azure" vzdáleného prostředí.|Spustit `git push` znovu, určení hlavní větve: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Této chybě může dojít, pokud se pokusíte úložiště velké git push přes protokol HTTPS.|Změna konfigurace git v místním počítači lze zviditelnit `postBuffer` větší. Například: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Nasadili jste aplikaci Node.js s _package.json_ soubor, který určuje další požadované moduly.|Zkontrolujte `npm ERR!` chybové zprávy před tuto chybu pro další kontext při selhání. Toto jsou známé příčiny této chyby a odpovídající `npm ERR!` zprávy:<br /><br />**Soubor package.json poškozený**: `npm ERR! Couldn't read dependencies.`<br /><br />**Nativní modul nemá binární distribuce pro Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />or <br />"npm ERR! [modulename@version] předinstalovat: \make || gmake\`|

## <a name="additional-resources"></a>Další zdroje

- [Dokumentace ke službě Project Kudu](https://github.com/projectkudu/kudu/wiki)
- [Průběžné nasazování do služby Azure App Service](deploy-continuous-deployment.md)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
