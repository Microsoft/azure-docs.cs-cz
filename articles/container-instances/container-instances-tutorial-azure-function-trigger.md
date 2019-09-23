---
title: Kurz – Azure Container Instances triggeru pomocí funkce Azure Functions
description: Vytvoření funkce PowerShellu bez serveru aktivovaného protokolem HTTP pro automatizaci vytváření instancí služby Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 00bd017b0bcff6386e678802c301087819792744
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179978"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Kurz: Vytvoření skupiny kontejnerů pomocí funkce Azure aktivované protokolem HTTP

[Azure Functions](../azure-functions/functions-overview.md) je výpočetní služba bez serveru, která může spouštět skripty nebo kód v reakci na nejrůznější události, jako je požadavek HTTP, časovač nebo zpráva ve frontě Azure Storage.

V tomto kurzu vytvoříte funkci Azure, která přijme požadavek HTTP a aktivuje nasazení [skupiny kontejnerů](container-instances-container-groups.md). V tomto příkladu se dozvíte základy použití Azure Functions k automatickému vytváření prostředků v Azure Container Instances. Upravte nebo rozšíříte příklad složitějších scénářů nebo jiných triggerů událostí. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * K vytvoření základní funkce PowerShellu aktivované protokolem HTTP použijte Visual Studio Code s [rozšířením Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .
> * Povolte v aplikaci Function App identitu a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte a znovu publikujte funkci PowerShellu pro automatizaci nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru aktivovaného protokolem HTTP.

> [!IMPORTANT]
> PowerShell pro Azure Functions je momentálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

Požadavky na instalaci a použití Visual Studio Code s Azure Functions v operačním systému najdete [v tématu Vytvoření první funkce PowerShellu v Azure](../azure-functions/functions-create-first-function-powershell.md#prerequisites) .

Některé kroky v tomto článku používají rozhraní příkazového řádku Azure CLI. K provedení těchto kroků můžete použít Azure Cloud Shell nebo místní instalaci rozhraní příkazového řádku Azure CLI. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Vytvoření základní funkce PowerShellu

Postupujte podle kroků v části [Vytvoření první funkce PowerShellu v Azure](../azure-functions/functions-create-first-function-powershell.md) a vytvořte funkci PowerShell pomocí šablony triggeru http. Použijte výchozí název funkce Azure **HttpTrigger**. Jak je znázorněno v rychlém startu, otestujte funkci místně a publikujte projekt do aplikace Function App v Azure. Tento příklad je základní funkcí spouštěnou protokolem HTTP, která vrací textový řetězec. V pozdějších krocích v tomto článku upravíte funkci pro vytvoření skupiny kontejnerů.

V tomto článku se předpokládá, že publikujete projekt pomocí názvu *myfunctionapp*ve skupině prostředků Azure, která se automaticky jmenuje podle názvu aplikace Function App (také *myfunctionapp*). V pozdějších krocích nahraďte jedinečný název aplikace funkcí a název skupiny prostředků.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Povolení identity spravované v Azure v aplikaci Function App

Teď ve vaší aplikaci Function App povolte [spravovanou identitu](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) přiřazenou systémem. Hostitel PowerShellu, na kterém běží aplikace, se může automaticky ověřit pomocí této identity a povolit funkce pro provádění akcí se službami Azure, ke kterým má identita udělený přístup. V tomto kurzu udělíte oprávnění spravovaných identit k vytváření prostředků ve skupině prostředků aplikace Function App. 

Nejprve pomocí příkazu [AZ Group show][az-group-show] Získejte ID skupiny prostředků aplikace Function App a uložte ji do proměnné prostředí. V tomto příkladu se předpokládá, že spustíte příkaz v prostředí bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Spuštěním [AZ functionapp identity App Assign][az-functionapp-identity-app-assign] přiřaďte k aplikaci Functions místní identitu a přiřaďte jí roli přispěvatele k této skupině prostředků. Tato role umožňuje identitě vytvořit další prostředky, jako jsou například skupiny kontejnerů ve skupině prostředků.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Upravit funkci HttpTrigger

Upravte kód PowerShellu pro funkci **HttpTrigger** , aby se vytvořila skupina kontejnerů. V souboru `run.ps1` pro funkci vyhledejte následující blok kódu. Tento kód zobrazí hodnotu názvu, pokud je jeden předán jako řetězec dotazu v adrese URL funkce:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Nahraďte tento kód následujícím příkladem bloku. Zde platí, že pokud je hodnota Name předána do řetězce dotazu, je použita k pojmenování a vytvoření skupiny kontejnerů pomocí rutiny [New-AzContainerGroup][new-azcontainergroup] . Ujistěte se, že jste nahradili název skupiny prostředků *myfunctionapp* názvem skupiny prostředků vaší aplikace Function App:

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

Tento příklad vytvoří skupinu kontejnerů skládající se z jedné instance kontejneru, která `alpine` spouští image. Kontejner spustí jediný `echo` příkaz a potom ukončí. V reálných příkladech můžete aktivovat vytvoření jedné nebo více skupin kontejnerů pro spuštění úlohy služby Batch.
 
## <a name="test-function-app-locally"></a>Místní test aplikace Function App

Před opětovným publikováním projektu Function App do Azure zajistěte, aby se funkce spouštěla správně místně. Jak je znázorněno v [rychlém startu PowerShellu](../azure-functions/functions-create-first-function-powershell.md), vložte do skriptu PowerShellu místní `Wait-Debugger` zarážku a zavolejte na něj výše. Pokyny k ladění najdete v tématu [ladění powershellu Azure Functions místně](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Opětovné publikování aplikace funkce Azure Functions

Po ověření, že se funkce v místním počítači spustí správně, je čas publikovat projekt znovu do existující aplikace Function App v Azure.

> [!NOTE]
> Nezapomeňte `Wait-Debugger` před publikováním funkcí do Azure odebrat jakákoli volání.

1. V Visual Studio Code otevřete paletu příkazů. Vyhledejte a vyberte `Azure Functions: Deploy to function app...`.
1. Vyberte aktuální pracovní složku pro zip a nasazení.
1. Vyberte předplatné a pak název existující aplikace Function App (*myfunctionapp*). Potvrďte, že chcete přepsat předchozí nasazení.

Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste aktualizovali, vyberte **Zobrazit výstup** v tomto oznámení.

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

Po úspěšném dokončení nasazení získáte adresu URL funkce. Například použijte **Azure: Oblast** funkce v nástroji Visual Studio Code ke zkopírování adresy URL funkce **HttpTrigger** nebo získání adresy URL funkce v [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Adresa URL funkce zahrnuje jedinečný kód a má tvar:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Spustit funkci bez předání názvu

Jako první test spusťte `curl` příkaz a předejte adresu URL funkce bez připojení `name` řetězce dotazu. Ujistěte se, že jste zahrnuli jedinečný kód vaší funkce.

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

Nyní spusťte `curl` příkaz připojením názvu skupiny kontejnerů (*mycontainergroup*) jako řetězce `&name=mycontainergroup`dotazu:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funkce vrátí stavový kód 200 a aktivuje vytvoření skupiny kontejnerů:

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

Ověřte, že se kontejner spustil pomocí příkazu [AZ Container logs][az-container-logs] :

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Ukázkový výstup:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu, můžete spuštěním příkazu [AZ Group Delete][az-group-delete] odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní vytvořený registr kontejneru, spuštěný kontejner i všechny související prostředky.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili funkci Azure, která přebírá požadavek HTTP a aktivuje nasazení skupiny kontejnerů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * K vytvoření základní funkce PowerShellu aktivované protokolem HTTP použijte Visual Studio Code s rozšířením Azure Functions.
> * Povolte v aplikaci Function App identitu a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte kód funkce PowerShellu pro automatizaci nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru aktivovaného protokolem HTTP.

Podrobný příklad, jak spustit a monitorovat kontejnerové úlohy, najdete v blogovém příspěvku bez [serveru řízeném událostmi s prostředím PowerShell Azure functions a Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) a s doprovodnou [ukázkou kódu](https://github.com/anthonychu/functions-powershell-run-aci).

Podrobné pokyny k vytváření funkcí Azure functions a publikování projektů Functions najdete v [dokumentaci k Azure Functions](/azure/azure-functions/) . 

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
