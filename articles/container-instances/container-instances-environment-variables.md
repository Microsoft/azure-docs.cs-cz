---
title: Nastavení proměnných prostředí v instanci kontejneru
description: Naučte se, jak nastavit proměnné prostředí v kontejnerech, které spouštíte v Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169761"
---
# <a name="set-environment-variables-in-container-instances"></a>Nastavení proměnných prostředí v instancích kontejnerů

Když v instancích kontejnerů nastavíte proměnné prostředí, můžete dynamicky konfigurovat aplikaci nebo skript, které kontejner spustí. To je podobné jako `--env` argument příkazového řádku pro `docker run` . 

Chcete-li nastavit proměnné prostředí v kontejneru, určete je při vytváření instance kontejneru. Tento článek ukazuje příklady nastavení proměnných prostředí při spuštění kontejneru pomocí [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)a [Azure Portal](#azure-portal-example). 

Pokud například spustíte image kontejneru Microsoft [ACI-WORDCOUNT][aci-wordcount] , můžete změnit její chování zadáním následujících proměnných prostředí:

*NUMWORDS*: počet slov odeslaných do STDOUT.

*MinLength*: minimální počet znaků ve slovech, které se mají spočítat. Vyšší číslo ignoruje běžná slova jako "z" a ".".

Pokud potřebujete předat tajné klíče jako proměnné prostředí, Azure Container Instances podporuje [zabezpečené hodnoty](#secure-values) pro kontejnery Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Příklad rozhraní příkazového řádku Azure

Chcete-li zobrazit výchozí výstup kontejneru [ACI-WORDCOUNT][aci-wordcount] , spusťte jej nejprve pomocí příkazu [AZ Container Create][az-container-create] (nejsou zadány žádné proměnné prostředí):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Chcete-li změnit výstup, spusťte druhý kontejner s `--environment-variables` přidaným argumentem a určete hodnoty proměnných *NUMWORDS* a *minLength* . (V tomto příkladu se předpokládá, že rozhraní příkazového řádku spouštíte v prostředí bash nebo v Azure Cloud Shell. Při použití příkazového řádku systému Windows zadejte proměnné s dvojitými uvozovkami, například `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Jakmile se stav obou kontejnerů zobrazuje jako *ukončeno* (ke kontrole stavu použijte příkaz [AZ Container show][az-container-show] ), zobrazte jeho protokoly pomocí příkazu [AZ Container logs][az-container-logs] , aby se zobrazil výstup.

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

**mujkontejner2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Příklad Azure PowerShell

Nastavení proměnných prostředí v PowerShellu se podobá rozhraní CLI, ale používá `-EnvironmentVariable` argument příkazového řádku.

Nejdřív spusťte kontejner [ACI-WORDCOUNT][aci-wordcount] ve své výchozí konfiguraci pomocí tohoto příkazu [New-AzContainerGroup][new-Azcontainergroup] :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Nyní spusťte následující příkaz [New-AzContainerGroup][new-Azcontainergroup] . Tato hodnota určuje proměnné prostředí *NUMWORDS* a *minLength* po naplnění proměnné pole `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po *ukončení* obou kontejnerů (ke kontrole stavu použijte příkaz [Get-AzContainerInstanceLog][azure-instance-log] ), pomocí příkazu [Get-AzContainerInstanceLog][azure-instance-log] načtěte jejich protokoly.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Výstup každého kontejneru ukazuje, jak jste změnili skript spouštěný kontejnerem nastavením proměnných prostředí.

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

## <a name="azure-portal-example"></a>Příklad Azure Portal

Chcete-li nastavit proměnné prostředí při spuštění kontejneru v Azure Portal, určete je na stránce **Upřesnit** při vytváření kontejneru.

1. Na stránce **Upřesnit** nastavte **zásady restartování** na při *selhání* .
2. V části **proměnné prostředí** zadejte `NumWords` s hodnotou `5` pro první proměnnou a zadejte `MinLength` s hodnotou `8` pro druhou proměnnou. 
1. Vyberte **zkontrolovat + vytvořit** a ověřte a nasaďte kontejner.

![Stránka portálu zobrazující tlačítko Povolit proměnnou prostředí a textová pole][portal-env-vars-01]

Chcete-li zobrazit protokoly kontejneru, v části **Nastavení** vyberte **kontejnery** a pak **protokoly**. Podobně jako výstup zobrazený v předchozích oddílech CLI a PowerShellu vidíte, jak bylo chování skriptu upraveno pomocí proměnných prostředí. Zobrazí se pouze pět slov, z nichž každá má minimální délku osm znaků.

![Portál znázorňující výstup protokolu kontejneru][portal-env-vars-02]

## <a name="secure-values"></a>Zabezpečené hodnoty

Objekty s bezpečnými hodnotami jsou určeny pro ukládání citlivých informací, jako jsou hesla nebo klíče pro vaši aplikaci. Použití zabezpečených hodnot pro proměnné prostředí je bezpečnější a flexibilnější než zahrnutí do image kontejneru. Další možností je použít tajné svazky popsané v tématu [připojení tajného svazku v Azure Container Instances](container-instances-volume-secret.md).

Proměnné prostředí se zabezpečenými hodnotami nejsou viditelné ve vlastnostech kontejneru – jejich hodnoty lze použít pouze v rámci kontejneru. Například vlastnosti kontejneru zobrazené v Azure Portal nebo v rozhraní příkazového řádku Azure zobrazují pouze název zabezpečené proměnné, nikoli její hodnotu.

Nastavte proměnnou zabezpečeného prostředí zadáním `secureValue` vlastnosti namísto regulárního `value` typu proměnné. Dvě proměnné definované v následujícím YAML ukazují dva typy proměnných.

### <a name="yaml-deployment"></a>Nasazení YAML

Vytvořte `secure-env.yaml` soubor s následujícím fragmentem kódu.

```yaml
apiVersion: 2019-12-01
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

Spuštěním následujícího příkazu nasaďte skupinu kontejnerů pomocí YAML (podle potřeby upravte název skupiny prostředků):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Ověřit proměnné prostředí

Spuštěním příkazu [AZ Container show zobrazíte][az-container-show] dotaz na proměnné prostředí kontejneru:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpověď JSON zobrazuje klíč a hodnotu nezabezpečeného prostředí, ale pouze název proměnné zabezpečeného prostředí:

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

Pomocí příkazu [AZ Container exec][az-container-exec] , který umožňuje provádění příkazu ve spuštěném kontejneru, můžete ověřit, zda byla nastavena proměnná zabezpečeného prostředí. Spuštěním následujícího příkazu spusťte interaktivní relaci bash v kontejneru:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otevření interaktivního prostředí v rámci kontejneru můžete získat přístup k `SECRET` hodnotě proměnné:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, například dávkové zpracování velké datové sady s několika kontejnery, můžou využívat vlastní proměnné prostředí za běhu. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh pomocí zásad restartování](container-instances-restart-policy.md).

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
