---
title: Místní nasazení z Gitu do služby Azure App Service
description: Zjistěte, jak povolit místní nasazení Git do služby Azure App Service.
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
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 842cd6f67a04bec0ed06282bdeeea8b8a51c0667
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Místní nasazení z Gitu do služby Azure App Service

Tento postup Průvodce vám ukáže postup nasazení kódu do [Azure App Service](app-service-web-overview.md) z úložiště Git v místním počítači.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li postupujte podle kroků v této příručce postupy:

* [Nainstalovat Git](http://www.git-scm.com/downloads).
* Udržujte místní úložiště Git se kód, který chcete nasadit.

Použití ukázkové úložiště se podle nich zorientujete, spusťte následující příkaz v místním okně terminálu:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Příprava úložiště

Ujistěte se, že kořenového adresáře úložiště má správné soubory v projektu.

| Modul runtime | Soubory kořenového adresáře |
|-|-|
| ASP.NET (jenom Windows) | _*.sln_, _*.csproj_, nebo _default.aspx_ |
| Jádro ASP.NET | _*.sln_ nebo _*.csproj_ |
| PHP | _index.php_ |
| Ruby (pouze Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, nebo _package.json_ pomocí spuštění skriptu |
| Python (jenom Windows) | _\*.PY_, _requirements.txt_, nebo _souboru runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, nebo  _iisstart.htm_ |
| Webové úlohy | _\<job_name > / run. \<rozšíření >_ pod _aplikace\_Data/úlohy/průběžné_ (pro nepřetržité webové úlohy) nebo _aplikace\_dat, úlohy nebo aktivaci_ (pro aktivaci Webové úlohy). Další informace najdete v tématu [Kudu WebJobs dokumentace](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funkce | V tématu [průběžné nasazování pro Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Chcete-li přizpůsobit vaše nasazení, můžete zahrnout _.deployment_ soubor v kořenovém adresáři úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Nezapomeňte `git commit` všechny změny, které chcete nasadit.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Povolit Git pro vaši aplikaci.

Pokud chcete povolit nasazení Git pro existující aplikace služby App Service, spusťte [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) v prostředí cloudu.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Chcete-li místo toho vytvořte aplikaci povoleno Git, spusťte [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) v prostředí cloudu s `--deployment-local-git` parametr.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` Příkaz měl dát něco podobného jako následující výstup:

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

## <a name="deploy-your-project"></a>Nasazení projektu

Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte  _\<adresa url >_ s adresou URL vzdálené Git, které jste získali z [povolit Git pro vaši aplikaci](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání hesla se ujistěte, že zadáváte heslo, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user), ne heslo, se kterým se přihlašujete na web Azure Portal.

```bash
git push azure master
```

Mohou se zobrazit specifické pro modul runtime automatizace ve výstupu, jako je například MSBuild pro technologii ASP.NET, `npm install` pro Node.js, a `pip install` pro jazyk Python. 

Po dokončení nasazení aplikace na portálu Azure nyní měli záznam vaše `git push` v **možnosti nasazení** stránky.

![](./media/app-service-deploy-local-git/deployment_history.png)

Přejděte do své aplikace a ověřte, zda je obsah nasazeny.

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou běžné chyby nebo problémy při použití Git pro publikování do aplikace služby App Service v Azure:

---
**Příznaky**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Příčina**: k této chybě může dojít, pokud aplikace není spuštěná.

**Řešení**: Spusťte aplikaci na portálu Azure. Nasazení Git není dostupná, když webové aplikace je zastavena.

---
**Příznaky**: `Couldn't resolve host 'hostname'`

**Příčina**: k této chybě může dojít, pokud bylo nesprávné informace o adrese zadali při vytváření vzdálené služby azure.

**Řešení**: použití `git remote -v` příkazu zobrazíte všechny dálkové ovladače, společně s přidružené adresy URL. Ověřte správnost adresy URL pro "azure" vzdálené. V případě potřeby odeberte a znovu tento vzdálené pomocí správnou adresu URL.

---
**Příznaky**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Příčina**: k této chybě může dojít, pokud nezadáte větev během `git push`, nebo pokud jste nenastavili `push.default` hodnotu `.gitconfig`.

**Řešení**: Spusťte `git push` znovu, zadání hlavní větve. Příklad:

```bash
git push azure master
```

---
**Příznaky**: `src refspec [branchname] does not match any.`

**Příčina**: k této chybě může dojít, pokud se pokusíte nabízená větev pouze hlavní na "azure" vzdálené.

**Řešení**: Spusťte `git push` znovu, zadání hlavní větve. Příklad:

```bash
git push azure master
```

---
**Příznaky**: `RPC failed; result=22, HTTP code = 5xx.`

**Příčina**: k této chybě může dojít, pokud se pokusíte úložiště velké git push přes protokol HTTPS.

**Řešení**: Změna konfigurace git v místním počítači, chcete-li postBuffer zvětšit

```bash
git config --global http.postBuffer 524288000
```

---
**Příznaky**: `Error - Changes committed to remote repository but your web app not updated.`

**Příčina**: k této chybě může dojít, pokud nasadíte aplikaci Node.js s _package.json_ soubor, který určuje další požadované moduly.

**Řešení**: další zprávy s 'npm ERR!. před tato chyba se mají protokolovat a může poskytnout další kontext při selhání. Následující seznam uvádí známé příčiny této chyby a odpovídající 'npm ERR!. zpráva:

* **Soubor package.json chybná**: npm ERR! Nepodařilo se přečíst závislosti.
* **Nativní modul, který nemá binární distribuce pro systém Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      NEBO
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Další prostředky

* [Dokumentace Kudu projektu](https://github.com/projectkudu/kudu/wiki)
* [Průběžné nasazování do Azure App Service](app-service-continuous-deployment.md)
* [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Ukázka: Vytvoření webové aplikace a nasazení kódu z místního úložiště Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
