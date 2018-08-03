---
title: Místní nasazení z Gitu do služby Azure App Service
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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 0f6a0e2fe3aa632137392efe806aaab265eedf10
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435538"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení z Gitu do služby Azure App Service

Tato příručka ukazuje, jak nasadíte tak svůj kód do [služby Azure App Service](app-service-web-overview.md) z úložiště Git v místním počítači.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Postupovat podle kroků v této příručce s postupy:

* [Nainstalovat Git](http://www.git-scm.com/downloads).
* Spravovat místní úložiště Git s kódem, který chcete nasadit.

Použití ukázkové úložiště Pokud chcete postupovat s námi, spusťte následující příkaz v okně místního terminálu:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Nasazení z místního Gitu pomocí Kudu sestavení

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu server sestavení je použití Cloud Shell.

### <a name="create-a-deployment-user"></a>Vytvoření uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Povolení místního Gitu pomocí Kudu

Pokud chcete povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu sestavovací server, spusťte [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) ve službě Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pokud chcete místo toho vytvořte aplikaci s podporou Git, spusťte [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ve službě Cloud Shell s `--deployment-local-git` parametru.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` Příkazu by vám měl dát něco podobného následující výstup:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Nasazení projektu

Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte  _\<url >_ adresou URL vzdáleného úložiště Git, které jste získali z [povolit Git pro vaši aplikaci](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání hesla se ujistěte, že zadáváte heslo, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user), ne heslo, se kterým se přihlašujete na web Azure Portal.

```bash
git push azure master
```

Automatizace specifické pro modul runtime ve výstupu, jako je MSBuild pro technologii ASP.NET, může se zobrazit `npm install` pro Node.js, a `pip install` pro Python. 

Přejděte do své aplikace a zkontrolujte, že obsah nasazení.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Nasazení z místního Gitu k sestavení VSTS

> [!NOTE]
> Pro App Service a vytvořit potřebná sestavení a definice verzí v účtu VSTS, musí váš účet Azure mít roli **vlastníka** ve vašem předplatném Azure.
>

Pokud chcete povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu sestavovací server, přejděte do vaší aplikace v [webu Azure portal](https://portal.azure.com).

V levém navigačním panelu na stránce vaší aplikace, klikněte na tlačítko **Deployment Center** > **místního Gitu** > **pokračovat**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Klikněte na tlačítko **průběžné doručování s VSTS** > **pokračovat**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

V **konfigurovat** stránce, konfigurace nového účtu VSTS nebo zadat existující účet. Až budete hotovi, klikněte na tlačítko **pokračovat**.

> [!NOTE]
> Pokud chcete použít existující účet VSTS, která není uvedená, budete muset [propojit účet VSTS se svým předplatným Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

V **testovací** zvolte, jestli se má povolit zátěžové testy a pak klikněte na **pokračovat**.

V závislosti na [cenovou úroveň](/pricing/details/app-service/plans/) plánu služby App Service, může se zobrazit také **nasazení do přípravného prostředí** stránky. Zvolte, jestli se má povolit sloty nasazení a pak klikněte na **pokračovat**.

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

Trvá několik minut, než účet VSTS, až bude připravená. Až to bude hotové, zkopírujte adresu URL úložiště Git v Centru pro nasazení.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte  _\<url >_ s adresou URL, které jste získali v posledním kroku.

```bash
git remote add vsts <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy ve Správci přihlašovacích údajů Git, přihlaste se pomocí vaší uživatelské visualstudio.com. Další metody ověřování, najdete v části [Přehled ověřování VSTS](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Jakmile se nasazení dokončí, můžete najít průběh sestavení na `https://<vsts_account>.visualstudio.com/<project_name>/_build` a průběh nasazení na `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Přejděte do své aplikace a zkontrolujte, že obsah nasazení.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Řešení potíží s nasazením Kudu

Tady jsou běžné chyby nebo problémy při použití Git k publikování pro aplikaci služby App Service v Azure:

---
**Příznak**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Příčina**: k této chybě může dojít, pokud nebude funkční ani po aplikaci.

**Rozlišení**: Spusťte aplikaci na webu Azure Portal. Nasazení z Gitu není dostupná, když webová aplikace zastavená.

---
**Příznak**: `Couldn't resolve host 'hostname'`

**Příčina**: k této chybě může dojít, pokud bylo nesprávné informace o adrese zadali při vytváření vzdáleného "azure".

**Rozlišení**: použití `git remote -v` příkazu zobrazte výpis všech Vzdálená úložiště, spolu s přidružené adresy URL. Ověřte správnost adresy URL pro "azure" vzdálené. V případě potřeby odebrat a znovu vytvořit toto vzdálené používá správnou adresu URL.

---
**Příznak**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Příčina**: k této chybě může dojít, pokud nechcete zadat větev během `git push`, nebo pokud jste nenastavili `push.default` hodnota v `.gitconfig`.

**Rozlišení**: Spusťte `git push` znovu, určení hlavní větve. Příklad:

```bash
git push azure master
```

---
**Příznak**: `src refspec [branchname] does not match any.`

**Příčina**: této chybě může dojít, pokud se pokusíte vložit pouze hlavní větve na "azure" vzdálené.

**Rozlišení**: Spusťte `git push` znovu, určení hlavní větve. Příklad:

```bash
git push azure master
```

---
**Příznak**: `RPC failed; result=22, HTTP code = 5xx.`

**Příčina**: této chybě může dojít, pokud se pokusíte úložiště velké git push přes protokol HTTPS.

**Rozlišení**: Změna konfigurace git v místním počítači lze zviditelnit postBuffer větší

```bash
git config --global http.postBuffer 524288000
```

---
**Příznak**: `Error - Changes committed to remote repository but your web app not updated.`

**Příčina**: k této chybě může dojít, pokud nasazení aplikace v Node.js s _package.json_ soubor, který určuje další požadované moduly.

**Rozlišení**: další zprávy pomocí "npm ERR!" před tato chyba se mají protokolovat a poskytnete další kontext při selhání. Následující seznam uvádí známé příčiny této chyby a odpovídající "npm ERR!" zpráva:

* **Soubor package.json poškozený**: npm ERR! Nelze číst závislosti.
* **Nativní modul, který nemá binární distribuce pro Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      NEBO
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Další prostředky

* [Dokumentace ke službě Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Průběžné nasazování do služby Azure App Service](app-service-continuous-deployment.md)
* [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
