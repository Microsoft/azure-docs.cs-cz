---
title: Kurz – aktivace skupiny kontejnerů podle funkce Azure
description: Vytvoření funkce PowerShellu spouštěné protokolem HTTP bez serveru pro automatizaci vytváření instancí kontejnerů Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331021"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Kurz: Vytvoření skupiny kontejnerů pomocí funkce Azure spouštěné protokolem HTTP

[Azure Functions](../azure-functions/functions-overview.md) je výpočetní služba bez serveru, která může spouštět skripty nebo kód v reakci na různé události, jako je například požadavek HTTP, časovač nebo zpráva ve frontě Azure Storage.

V tomto kurzu vytvoříte funkci Azure, která přebírá požadavek HTTP a aktivuje nasazení [skupiny kontejnerů](container-instances-container-groups.md). Tento příklad ukazuje základy použití funkce Azure automaticky vytvářet prostředky v Azure Container Instances. Upravte nebo rozšiřte příklad pro složitější scénáře nebo jiné aktivační události. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Pomocí kódu Visual Studia s [rozšířením Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vytvořte základní funkci PowerShellu spouštěnou http.
> * Povolte identitu v aplikaci funkce a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte a znovu publikujte funkci Prostředí PowerShell pro automatizaci nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru spouštěné protokolem HTTP.

> [!IMPORTANT]
> PowerShell pro funkce Azure je momentálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

Požadavky na instalaci a používání kódu Visual Studia s funkcemi Azure v osu najdete v tématu [Vytvoření první funkce v Azure.](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment)

Některé kroky v tomto článku používají azure cli. K dokončení těchto kroků můžete použít Azure Cloud Shell nebo místní instalaci vázacích příkazových zprogramu Azure. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Vytvoření základní funkce Prostředí PowerShell

Postupujte podle kroků v [části Vytvoření první funkce PowerShellu v Azure](../azure-functions/functions-create-first-function-powershell.md) a vytvořte funkci PowerShellu pomocí šablony HTTP Trigger. Použijte výchozí název funkce Azure **HttpTrigger**. Jak je znázorněno na úvodním startu, otestujte funkci místně a publikujte projekt do aplikace funkce v Azure. Tento příklad je základní funkce spouštěná protokolem HTTP, která vrací textový řetězec. V pozdějších krocích v tomto článku upravit funkci k vytvoření skupiny kontejnerů.

Tento článek předpokládá, že publikujete projekt pomocí názvu *myfunctionapp*ve skupině prostředků Azure automaticky pojmenované podle názvu aplikace funkce (také *myfunctionapp).* V pozdějších krocích nahraďte název aplikace jedinečné funkce a název skupiny prostředků.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Povolení identity spravované Azure v aplikaci funkcí

Teď povolte systémem přiřazenou [spravovanou identitu](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) ve vaší funkční aplikaci. Hostitel Prostředí PowerShell, který aplikaci spouště, se může automaticky ověřovat pomocí této identity, což umožňuje funkcím přijímat akce ve službách Azure, ke kterým byla identita udělena. V tomto kurzu udělíte oprávnění spravované identity k vytvoření prostředků ve skupině prostředků aplikace funkce. 

Nejprve použijte příkaz [az group show][az-group-show] k získání ID skupiny prostředků aplikace funkce a uložte jej do proměnné prostředí. Tento příklad předpokládá spuštění příkazu v prostředí Bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Spusťte [aplikaci az functionapp identity přiřadit][az-functionapp-identity-app-assign] přiřadit místní identitu do aplikace funkce a přiřadit roli přispěvatele do skupiny prostředků. Tato role umožňuje identity vytvořit další prostředky, jako jsou skupiny kontejnerů ve skupině prostředků.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Změnit funkci HttpTrigger

Upravte kód prostředí PowerShell pro funkci **HttpTrigger** a vytvořte skupinu kontejnerů. V `run.ps1` souboru pro funkci najděte následující blok kódu. Tento kód zobrazí hodnotu názvu, pokud je předána jako řetězec dotazu v adrese URL funkce:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Nahraďte tento kód následujícím ukázkovým blokem. Zde pokud je v řetězci dotazu předána hodnota názvu, slouží k pojmenování a vytvoření skupiny kontejnerů pomocí rutiny [New-AzContainerGroup.][new-azcontainergroup] Nezapomeňte nahradit název skupiny prostředků *myfunctionapp* názvem skupiny prostředků pro vaši aplikaci funkce:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Tento příklad vytvoří skupinu kontejnerů skládající se `alpine` z jedné instance kontejneru, která spouštějí bitovou kopii. Kontejner spustí jeden `echo` příkaz a potom ukončí. V příkladu reálného světa můžete aktivovat vytvoření jedné nebo více skupin kontejnerů pro spuštění dávkové úlohy.
 
## <a name="test-function-app-locally"></a>Testovací aplikace funkce místně

Ujistěte se, že funkce běží správně místně před znovupublikování projektu aplikace funkce do Azure. Jak je znázorněno na [rychlém startu prostředí PowerShell](../azure-functions/functions-create-first-function-powershell.md), vložte místní zarážku do skriptu prostředí PowerShell a `Wait-Debugger` nad něj volání. Pokyny k ladění najdete v tématu [ladění powershellových funkcí Azure místně](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Opětovné publikování aplikace funkcí Azure

Po ověření, že funkce běží správně v místním počítači, je čas znovu publikovat projekt do existující aplikace funkce v Azure.

> [!NOTE]
> Nezapomeňte odebrat všechna `Wait-Debugger` volání před publikováním funkcí do Azure.

1. V kódu sady Visual Studio otevřete paletu příkazů. Vyhledejte a `Azure Functions: Deploy to function app...`vyberte .
1. Vyberte aktuální pracovní složku, kterou chcete zip ovat a nasadit.
1. Vyberte předplatné a pak název existující aplikace funkce *(myfunctionapp).* Potvrďte, že chcete přepsat předchozí nasazení.

Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Vyberte **Zobrazit výstup** v tomto oznámení zobrazíte výsledky vytváření a nasazení, včetně prostředků Azure, které jste aktualizovali.

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

Po úspěšném dokončení nasazení získáte adresu URL funkce. Můžete například použít oblast **Azure: Funkce** v kódu Visual Studia ke zkopírování adresy URL funkce **HttpTrigger** nebo získat adresu URL funkce na [webu Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Adresa URL funkce obsahuje jedinečný kód a je ve formě:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Spustit funkci bez předání názvu

Jako první test spusťte `curl` příkaz a předavěte adresu URL funkce bez připojení řetězce dotazu. `name` Nezapomeňte uvést jedinečný kód funkce.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Funkce vrátí stavový kód 400 a text `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Spustit funkci a předat název skupiny kontejnerů

Nyní spusťte `curl` příkaz připojením názvu skupiny kontejnerů *(mycontainergroup)* jako řetězce `&name=mycontainergroup`dotazu :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funkce vrátí stavový kód 200 a spustí vytvoření skupiny kontejnerů:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Ověřte, zda byl kontejner spuštěn pomocí příkazu [protokoly kontejneru az:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Ukázkový výstup:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu, můžete spustit příkaz [odstranění skupiny az][az-group-delete] a odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní vytvořený registr kontejneru, spuštěný kontejner i všechny související prostředky.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili funkci Azure, která přebírá požadavek HTTP a aktivuje nasazení skupiny kontejnerů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Pomocí kódu Visual Studia s rozšířením Azure Functions vytvořte základní funkci PowerShellu spouštěnou http.
> * Povolte identitu v aplikaci funkce a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte kód funkce Prostředí PowerShell a automatizujte nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru spouštěné protokolem HTTP.

Podrobný příklad spuštění a monitorování kontejnerizované úlohy najdete v příspěvku blogu [kontejnery bez serveru řízené událostmi s funkcemi PowerShell Azure a instancemi kontejnerů Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) a s doprovodnou [ukázkou kódu](https://github.com/anthonychu/functions-powershell-run-aci).

Podrobné pokyny k vytváření funkcí Azure a publikování projektu funkcí najdete v [dokumentaci k funkcím Azure.](/azure/azure-functions/) 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
