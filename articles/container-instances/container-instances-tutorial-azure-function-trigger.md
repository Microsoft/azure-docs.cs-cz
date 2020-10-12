---
title: Kurz – spuštění skupiny kontejnerů podle funkce Azure Functions
description: Vytvoření funkce PowerShellu bez serveru aktivovaného protokolem HTTP pro automatizaci vytváření instancí služby Azure Container Instances
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ec4b2273f6be6ea4aabed2b660e0b7553f861d0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072039"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Kurz: použití funkce Azure aktivované protokolem HTTP k vytvoření skupiny kontejnerů

[Azure Functions](../azure-functions/functions-overview.md) je výpočetní služba bez serveru, která může spouštět skripty nebo kód v reakci na nejrůznější události, jako je požadavek HTTP, časovač nebo zpráva ve frontě Azure Storage.

V tomto kurzu vytvoříte funkci Azure, která přijme požadavek HTTP a aktivuje nasazení [skupiny kontejnerů](container-instances-container-groups.md). V tomto příkladu se dozvíte základy použití Azure Functions k automatickému vytváření prostředků v Azure Container Instances. Upravte nebo rozšíříte příklad složitějších scénářů nebo jiných triggerů událostí. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * K vytvoření základní funkce PowerShellu aktivované protokolem HTTP použijte Visual Studio Code s [rozšířením Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .
> * Povolte v aplikaci Function App identitu a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte a znovu publikujte funkci PowerShellu pro automatizaci nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru aktivovaného protokolem HTTP.

## <a name="prerequisites"></a>Požadavky

V tématu [Vytvoření první funkce v Azure použijte Visual Studio Code](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell#configure-your-environment) pro požadavky na instalaci a použití Visual Studio Code s rozšířením Azure Functions v operačním systému.

Další kroky v tomto článku se používají Azure PowerShell. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure PowerShell][azure-powershell-install] a [přihlášení do Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Vytvoření základní funkce PowerShellu

Postupujte podle kroků v části [Vytvoření první funkce PowerShellu v Azure](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell) a vytvořte funkci PowerShell pomocí šablony triggeru http. Použijte výchozí název funkce Azure **HttpTrigger**. Jak je znázorněno v rychlém startu, otestujte funkci místně a publikujte projekt do aplikace Function App v Azure. Tento příklad je základní funkcí spouštěnou protokolem HTTP, která vrací textový řetězec. V pozdějších krocích v tomto článku upravíte funkci pro vytvoření skupiny kontejnerů.

V tomto článku se předpokládá, že publikujete projekt pomocí názvu *myfunctionapp*ve skupině prostředků Azure, která se automaticky jmenuje podle názvu aplikace Function App (také *myfunctionapp*). V pozdějších krocích nahraďte jedinečný název aplikace funkcí a název skupiny prostředků.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Povolení identity spravované v Azure v aplikaci Function App

V následujících příkazech je ve vaší aplikaci Function App povolená [spravovaná identita](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) přiřazená systémem. Hostitel PowerShellu, na kterém běží aplikace, se může automaticky ověřit v Azure pomocí této identity a povolit funkce pro provádění akcí se službami Azure, ke kterým má identita udělený přístup. V tomto kurzu udělíte oprávnění spravovaných identit k vytváření prostředků ve skupině prostředků aplikace Function App. 

[Přidejte](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) do aplikace Function App identitu:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Přiřaďte identitu role přispěvatele v oboru pro skupinu prostředků:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Upravit funkci HttpTrigger

Upravte kód PowerShellu pro funkci **HttpTrigger** , aby se vytvořila skupina kontejnerů. V souboru `run.ps1` pro funkci vyhledejte následující blok kódu. Tento kód zobrazí hodnotu názvu, pokud je jeden předán jako řetězec dotazu v adrese URL funkce:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Nahraďte tento kód následujícím příkladem bloku. Zde platí, že pokud je hodnota Name předána do řetězce dotazu, je použita k pojmenování a vytvoření skupiny kontejnerů pomocí rutiny [New-AzContainerGroup][new-azcontainergroup] . Ujistěte se, že jste nahradili název skupiny prostředků *myfunctionapp* názvem skupiny prostředků vaší aplikace Function App:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Tento příklad vytvoří skupinu kontejnerů skládající se z jedné instance kontejneru, která spouští `alpine` image. Kontejner spustí jediný `echo` příkaz a potom ukončí. V reálných příkladech můžete aktivovat vytvoření jedné nebo více skupin kontejnerů pro spuštění úlohy služby Batch.
 
## <a name="test-function-app-locally"></a>Místní test aplikace Function App

Před opětovným publikováním projektu Function App do Azure zajistěte, aby se funkce spouštěla místně. Při místním spuštění funkce nevytváří prostředky Azure. Můžete ale testovat tok funkcí s a bez předávání hodnoty názvu do řetězce dotazu. Chcete-li ladit funkci, přečtěte si téma [ladění powershellu Azure Functions místně](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Opětovné publikování aplikace funkce Azure Functions

Po ověření, že se funkce spustí místně, znovu publikujte projekt do existující aplikace Function App v Azure.

1. V Visual Studio Code otevřete paletu příkazů. Vyhledejte a vyberte `Azure Functions: Deploy to Function App...` .
1. Vyberte aktuální pracovní složku pro zip a nasazení.
1. Vyberte předplatné a pak název existující aplikace Function App (*myfunctionapp*). Potvrďte, že chcete přepsat předchozí nasazení.

Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste aktualizovali, vyberte **Zobrazit výstup** v tomto oznámení.

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

Po úspěšném dokončení nasazení získáte adresu URL funkce. Například můžete použít oblast **Azure: Functions** v Visual Studio Code ke ZKOPÍROVÁNÍ adresy URL funkce **HttpTrigger** nebo získat adresu URL funkce v [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Adresa URL funkce má formu:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Spustit funkci bez předání názvu

Jako první test spusťte `curl` příkaz a předejte adresu URL funkce bez připojení `name` řetězce dotazu. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

Funkce vrátí stavový kód 200 a text `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` :

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Spustit funkci a předat název skupiny kontejnerů

Nyní spusťte `curl` příkaz a přidejte název skupiny kontejnerů (*mycontainergroup*) jako řetězec dotazu `?name=mycontainergroup` :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

Funkce vrátí stavový kód 200 a aktivuje vytvoření skupiny kontejnerů:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Pomocí příkazu [Get-AzContainerInstanceLog][get-azcontainerinstancelog] ověřte, že byl kontejner spuštěný:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Ukázkový výstup:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu, můžete spuštěním příkazu [az Group Delete] [az-Group-Delete] odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní aplikaci funkcí, kterou jste vytvořili, a také spuštěný kontejner a všechny související prostředky.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili funkci Azure, která přebírá požadavek HTTP a aktivuje nasazení skupiny kontejnerů. Naučili jste se:

> [!div class="checklist"]
> * K vytvoření základní funkce PowerShellu aktivované protokolem HTTP použijte Visual Studio Code s rozšířením Azure Functions.
> * Povolte v aplikaci Function App identitu a udělte jí oprávnění k vytváření prostředků Azure.
> * Upravte kód funkce PowerShellu pro automatizaci nasazení skupiny kontejnerů s jedním kontejnerem.
> * Ověřte nasazení kontejneru aktivovaného protokolem HTTP.

Podrobný příklad, jak spustit a monitorovat kontejnerové úlohy, najdete v blogovém příspěvku bez [serveru řízeném událostmi s prostředím PowerShell Azure functions a Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) a s doprovodnou [ukázkou kódu](https://github.com/anthonychu/functions-powershell-run-aci).

Podrobné pokyny k vytváření funkcí Azure functions a publikování projektů Functions najdete v [dokumentaci k Azure Functions](../azure-functions/index.yml) . 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
