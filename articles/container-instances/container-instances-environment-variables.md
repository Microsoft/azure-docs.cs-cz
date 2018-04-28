---
title: Nastavení proměnných prostředí v Azure kontejner instancí
description: Informace o nastavení proměnných prostředí v Azure kontejner instancí
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnných prostředí v vaše instance kontejneru umožňuje poskytovat dynamické konfigurace aplikace nebo skriptu, spusťte kontejner.

Jste aktuálně moct nastavení proměnných prostředí z rozhraní příkazového řádku a prostředí PowerShell. V obou případech použijete příznak na příkazy pro nastavení proměnných prostředí. Nastavení proměnných prostředí v ACI je podobná `--env` argument příkazového řádku k `docker run`. Například pokud použijete microsoft/aci-wordcount kontejneru image můžete upravit chování tak, že zadáte následující proměnné prostředí:

*NumWords*: počet odeslaných do STDOUT slov.

*MinLength*: minimální počet znaků v aplikaci word pro něj pro inventarizaci. Vyšší číslo ignoruje běžná slova jako "z" a "na".

## <a name="azure-cli-example"></a>Příklad Azure CLI

Pokud chcete zobrazit výstup výchozí kontejner, spusťte následující příkaz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Zadáním `NumWords=5` a `MinLength=8` kontejneru proměnných prostředí, by měl zobrazit protokoly kontejneru odlišný výstup.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Jakmile se zobrazí stav kontejneru *ukončeno* (použijte [az kontejneru zobrazit] [ az-container-show] a zkontrolujte jeho stav), zobrazit protokoly na zobrazení výstupu.  Chcete-li zobrazit výstup kontejneru s žádné proměnným prostředí nastaveným – název bude mycontainer1 místo mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Příklad Azure PowerShell

Pokud chcete zobrazit výstup výchozí kontejner, spusťte následující příkaz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Zadáním `NumWords=5` a `MinLength=8` kontejneru proměnných prostředí, by měl zobrazit protokoly kontejneru odlišný výstup.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Jakmile se stav kontejneru *ukončeno* (použijte [Get-AzureRmContainerInstanceLog] [ azure-instance-log] a zkontrolujte jeho stav), zobrazit protokoly výstup zobrazíte. Chcete-li zobrazit kontejneru nastavit protokoly s žádné proměnné prostředí ContainerGroupName být mycontainer1 místo mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Příklad výstupu bez proměnné prostředí

```bash
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

## <a name="example-output-with-environment-variables"></a>Příklad výstupu s proměnné prostředí

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Další postup

Teď, když víte, jak přizpůsobit vstup do vašeho kontejneru, zjistěte, jak se zachovat výstup kontejnery, které dokončit.
> [!div class="nextstepaction"]
> [Připojení sdílenou složku Azure s instancemi Azure kontejneru](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show