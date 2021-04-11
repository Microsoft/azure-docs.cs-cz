---
title: Nasazení z místního úložiště Git
description: Naučte se, jak povolit místní nasazení Git Azure App Service. Jedním z nejjednodušších způsobů, jak nasadit kód z místního počítače.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: faf3afc60c8517509199e6a306f511a15b32358c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732835"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení Gitu pro Azure App Service

V této příručce se dozvíte, jak nasadit aplikaci pro [Azure App Service](overview.md) z úložiště Git na místním počítači.

## <a name="prerequisites"></a>Požadavky

Postup je popsaný v tomto návodu:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Nainstalujte Git](https://www.git-scm.com/downloads).

- Mít místní úložiště Git s kódem, který chcete nasadit. Chcete-li stáhnout ukázkové úložiště, spusťte následující příkaz v místním okně terminálu:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

Viz téma [Konfigurace pověření nasazení pro Azure App Service](deploy-configure-credentials.md). Můžete použít buď pověření uživatelského rozsahu nebo pověření pro obor aplikace.

## <a name="create-a-git-enabled-app"></a>Vytvoření aplikace s povoleným Git

Pokud už máte aplikaci App Service a chcete pro ni nakonfigurovat místní nasazení Git, přečtěte si téma [Konfigurace existující aplikace](#configure-an-existing-app) .

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte [`az webapp create`](/cli/azure/webapp#az_webapp_create) s `--deployment-local-git` možností. Například:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Výstup obsahuje adresu URL, jako je: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Pomocí této adresy URL můžete aplikaci nasadit v dalším kroku.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte rutinu [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) z kořenového adresáře úložiště Git. Například:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Když tuto rutinu spustíte z adresáře, který je úložištěm Git, automaticky se pro vaši aplikaci App Service pro vás vytvoří Vzdálená aplikace Git s názvem `azure` .

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Na portálu musíte nejdřív vytvořit aplikaci a pak pro ni nakonfigurovat nasazení. Viz [Konfigurace existující aplikace](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Konfigurace existující aplikace

Pokud jste ještě aplikaci nevytvořili, přečtěte si místo toho možnost [vytvořit aplikaci s povoleným Git](#create-a-git-enabled-app) .

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte příkaz [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Například:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Výstup obsahuje adresu URL, jako je: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Pomocí této adresy URL můžete aplikaci nasadit v dalším kroku.

> [!TIP]
> Tato adresa URL obsahuje uživatelské jméno nasazení v oboru uživatele. Pokud chcete, můžete místo toho [použít přihlašovací údaje rozsahu aplikace](deploy-configure-credentials.md#appscope) . 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Nastavte `scmType` aplikaci spuštěním rutiny [set-AzResource](/powershell/module/az.resources/set-azresource) .

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku správy vaší aplikace.

1. V nabídce vlevo vyberte nastavení **centra nasazení**  >  . Vyberte **místní Git** ve **zdroji** a pak klikněte na **Uložit**.

    ![Ukazuje, jak povolit místní nasazení Git pro App Service v Azure Portal](./media/deploy-local-git/enable-portal.png)

1. V části místní Git zkopírujte **git clone identifikátor URI** pro pozdější verzi. Tento identifikátor URI neobsahuje žádné přihlašovací údaje.

-----

## <a name="deploy-the-web-app"></a>Nasazení webové aplikace

1. V místním okně terminálu změňte adresář do kořenového adresáře úložiště Git a přidejte vzdálené úložiště Git pomocí adresy URL, kterou jste získali z vaší aplikace. Pokud vaše zvolená metoda neposkytuje adresu URL, použijte `https://<app-name>.scm.azurewebsites.net/<app-name>.git` s názvem vaší aplikace v `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Pokud jste [v PowerShellu vytvořili aplikaci s povoleným Git pomocí rutiny New-AzWebApp](#create-a-git-enabled-app), je vzdálená podpora už vytvořená za vás.
   
1. Nahrajte službu Azure Remote pomocí služby `git push azure master` . 
   
1. V okně **Správce přihlašovacích údajů Git** zadejte svoje [přihlašovací údaje do oboru uživatelů nebo aplikací](#configure-a-deployment-user), nikoli přihlašovací údaje pro přihlášení k Azure.

    Pokud vaše Vzdálená adresa URL Git již obsahuje uživatelské jméno a heslo, nezobrazí se vám žádné výzvy. 
   
1. Zkontrolujte výstup. Může se zobrazit automatizace specifická pro modul runtime, jako je MSBuild pro ASP.NET, `npm install` pro Node.js a `pip install` pro Python. 
   
1. Přejděte do aplikace v Azure Portal a ověřte, že je obsah nasazený.

## <a name="troubleshoot-deployment"></a>Řešení potíží s nasazením

Když použijete Git k publikování App Service aplikace v Azure, může se zobrazit následující běžné chybové zprávy:

|Zpráva|Příčina|Řešení
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikace není v provozu.|Spusťte aplikaci v Azure Portal. Nasazení Git není po zastavení webové aplikace dostupné.|
|`Couldn't resolve host 'hostname'`|Informace o adrese pro vzdálené úložiště Azure jsou nesprávné.|Pomocí `git remote -v` příkazu můžete vypsat všechny vzdálené části společně s přidruženou adresou URL. Ověřte, jestli je adresa URL vzdáleného webu Azure správná. V případě potřeby tento vzdálený příkaz odeberte a znovu vytvořte pomocí správné adresy URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nezadali jste větev během `git push` nebo jste nenastavili `push.default` hodnotu v `.gitconfig` .|Spusťte `git push` znovu a určete hlavní větev: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Vložili jste místní větev, která neodpovídá větvi nasazení aplikace v Azure.|Ověřte, zda je aktuální větev `master` . Chcete-li změnit výchozí větev, použijte `DEPLOYMENT_BRANCH` nastavení aplikace.|
|`src refspec [branchname] does not match any.`|Pokusili jste se odeslat do jiné jiné větve než na vzdáleném Azure.|Spusťte `git push` znovu a určete hlavní větev: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|K této chybě může dojít, pokud se pokusíte odeslat velké úložiště Git přes HTTPS.|Změňte konfiguraci Gitu na místním počítači, aby byla `postBuffer` větší. Příklad: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Nasadili jste aplikaci Node.js s _package.jsv_ souboru, který určuje další požadované moduly.|`npm ERR!`Před touto chybou zkontrolujte chybové zprávy pro další kontext chyby. Níže jsou uvedené známé příčiny této chyby a odpovídající `npm ERR!` zprávy:<br /><br />**Nesprávně vytvořený package.jsv souboru**: `npm ERR! Couldn't read dependencies.`<br /><br />**Nativní modul nemá pro Windows binární distribuci**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />nebo <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Další zdroje informací

- [App Service Server sestavení (dokumentace k Project Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Průběžné nasazování do Azure App Service](deploy-continuous-deployment.md)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
