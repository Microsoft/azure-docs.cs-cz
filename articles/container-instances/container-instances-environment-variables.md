---
title: Nastavení proměnných prostředí v instanci kontejneru
description: Zjistěte, jak nastavit proměnné prostředí v kontejnerech, které spouštěte v azure container instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247225"
---
# <a name="set-environment-variables-in-container-instances"></a>Nastavení proměnných prostředí v instancích kontejneru

Když v instancích kontejnerů nastavíte proměnné prostředí, můžete dynamicky konfigurovat aplikaci nebo skript, které kontejner spustí. To je podobné `--env` argumentu příkazového řádku . `docker run` 

Chcete-li nastavit proměnné prostředí v kontejneru, zadejte je při vytváření instance kontejneru. Tento článek ukazuje příklady nastavení proměnných prostředí při spuštění kontejneru s [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)a [portál Azure](#azure-portal-example). 

Pokud například spustíte bitovou kopii kontejneru [Microsoft aci-wordcount,][aci-wordcount] můžete upravit jeho chování zadáním následujících proměnných prostředí:

*NumWords*: Počet slov odeslaných stdout.

*MinLength*: Minimální počet znaků ve slově, které mají být započítány. Vyšší číslo ignoruje běžná slova jako "z" a "the".

Pokud potřebujete předat tajné klíče jako proměnné prostředí, instance kontejneru Azure podporuje [zabezpečené hodnoty](#secure-values) pro kontejnery Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Příklad příkazového příkazového příkazu k Azure

Chcete-li zobrazit výchozí výstup kontejneru [aci-wordcount,][aci-wordcount] spusťte jej nejprve pomocí tohoto [příkazu az kontejner u vytvoření][az-container-create] (žádné proměnné prostředí zadány):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Chcete-li upravit výstup, spusťte druhý kontejner s přidaným argumentem `--environment-variables` a zadejte hodnoty pro proměnné *NumWords* a *MinLength.* (Tento příklad předpokládá, že používáte příkazového příkazové konto v prostředí Bash nebo Azure Cloud Shell. Pokud používáte příkazový řádek systému Windows, zadejte `--environment-variables "NumWords"="5" "MinLength"="8"`proměnné s dvojitými uvozovkami, například .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Jakmile se stav obou kontejnerů zobrazí jako *Ukončeno* (použijte [az kontejner show][az-container-show] ke kontrole stavu), zobrazte jejich protokoly s [protokoly kontejneru az,][az-container-logs] abyste viděli výstup.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup kontejnerů ukazuje, jak jste upravili chování skriptu druhého kontejneru nastavením proměnných prostředí.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Příklad Prostředí Azure PowerShell

Nastavení proměnných prostředí v prostředí PowerShell je podobné `-EnvironmentVariable` cli, ale používá argument příkazového řádku.

Nejprve spusťte kontejner [aci-wordcount][aci-wordcount] ve výchozí konfiguraci pomocí tohoto [příkazu New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Nyní spusťte následující příkaz [New-AzContainerGroup.][new-Azcontainergroup] Tento je specifikován *numwords* a *minlength* proměnné prostředí po `envVars`vyplnění proměnné pole, :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Jakmile je stav obou kontejnerů *ukončen* (ke kontrole stavu použijte [Get-AzContainerInstanceLog),][azure-instance-log] vyžádejte jejich protokoly pomocí příkazu [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Výstup pro každý kontejner ukazuje, jak jste změnili skript spuštěný kontejnerem nastavením proměnných prostředí.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Příklad portálu Azure

Pokud chcete nastavit proměnné prostředí při spuštění kontejneru na webu Azure Portal, zadejte je na stránce **Upřesnit** při vytváření kontejneru.

1. Na stránce **Upřesnit** nastavte **zásadu Restartování** *na možnost Selhání*
2. V části **Proměnné** `NumWords` prostředí zadejte `5` hodnotu pro první `MinLength` proměnnou `8` a zadejte s hodnotou pro druhou proměnnou. 
1. Vyberte **Revize + vytvořit** k ověření a nasadit kontejner.

![Stránka portálu s proměnnou prostředí Povolit tlačítka a textová pole][portal-env-vars-01]

Chcete-li zobrazit protokoly kontejneru, vyberte v části **Nastavení** **kontejnery**a potom **protokoly**. Podobně jako výstup zobrazený v předchozích oddílech CLI a PowerShell uvidíte, jak bylo chování skriptu změněno proměnnými prostředí. Zobrazí se pouze pět slov, z nichž každé má minimální délku osmi znaků.

![Portál zobrazující výstup protokolu kontejneru][portal-env-vars-02]

## <a name="secure-values"></a>Zabezpečené hodnoty

Objekty se zabezpečenými hodnotami jsou určeny k uložení citlivých informací, jako jsou hesla nebo klíče pro vaši aplikaci. Použití zabezpečených hodnot pro proměnné prostředí je bezpečnější a flexibilnější než jejich zahrnutí do image kontejneru. Další možností je použití tajných svazků popsaných v [poli Připojení tajného svazku v azure container instances](container-instances-volume-secret.md).

Proměnné prostředí se zabezpečenými hodnotami nejsou viditelné ve vlastnostech kontejneru – k jejich hodnotám lze přistupovat pouze z kontejneru. Například vlastnosti kontejneru zobrazené na webu Azure Portal nebo Azure CLI zobrazí pouze název zabezpečené proměnné, nikoli její hodnotu.

Nastavte proměnnou zabezpečeného prostředí `secureValue` zadáním vlastnosti namísto běžné `value` pro typ proměnné. Dvě proměnné definované v následujícím YAML ukazují dva typy proměnných.

### <a name="yaml-deployment"></a>Nasazení YAML

Vytvořte `secure-env.yaml` soubor s následujícím úryvkem.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Spusťte následující příkaz pro nasazení skupiny kontejnerů pomocí yaml (podle potřeby upravte název skupiny prostředků):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Ověření proměnných prostředí

Spusťte příkaz [az container show][az-container-show] pro dotazování proměnných prostředí kontejneru:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpověď JSON zobrazuje klíč a hodnotu nezabezpečené proměnné prostředí, ale pouze název proměnné zabezpečeného prostředí:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Pomocí příkazu [az container exec,][az-container-exec] který umožňuje provádění příkazu v běžícím kontejneru, můžete ověřit, zda byla nastavena proměnná zabezpečeného prostředí. Spuštěním interaktivní relace bash v kontejneru spusťte následující příkaz:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otevření interaktivního prostředí v kontejneru máte `SECRET` přístup k hodnotě proměnné:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, jako je například dávkové zpracování velké datové sady s několika kontejnery, mohou využívat proměnné vlastního prostředí za běhu. Další informace o spouštění kontejnerů založených na úlohách naleznete v tématu [Spuštění kontejnerizovaných úloh se zásadami restartování](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
