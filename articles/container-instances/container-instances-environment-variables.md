---
title: Nastavení proměnných prostředí v Azure kontejner instancí
description: Informace o nastavení proměnných prostředí v kontejnerech, které spustíte v Azure kontejner instancí
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/07/2018
ms.author: marsma
ms.openlocfilehash: bc30352f50344031f8356d2be1b800dd035f12ad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830458"
---
# <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnných prostředí v vaše instance kontejneru umožňuje poskytovat dynamické konfigurace aplikace nebo skriptu, spusťte kontejner. Nastavení proměnných prostředí v kontejneru, zadejte je při vytváření instance kontejneru. Můžete nastavit proměnné prostředí, když spustíte kontejner s [rozhraní příkazového řádku Azure](#azure-cli-example), [prostředí Azure PowerShell](#azure-powershell-example)a [portál Azure](#azure-portal-example).

Například pokud spustíte [microsoft/aci-wordcount] [ aci-wordcount] kontejneru bitovou kopii, můžete upravit své chování tak, že zadáte následující proměnné prostředí:

*NumWords*: počet odeslaných do STDOUT slov.

*MinLength*: minimální počet znaků v aplikaci word pro něj pro inventarizaci. Vyšší číslo ignoruje běžná slova jako "z" a "na".

Pokud potřebujete předat tajné klíče jako proměnné prostředí, instancí kontejnerů Azure podporuje [zabezpečení hodnoty](#secure-values) zabezpečení hodnoty pro kontejnery Windows a Linux.

## <a name="azure-cli-example"></a>Příklad Azure CLI

Zobrazit výstup výchozí [microsoft/aci-wordcount] [ aci-wordcount] kontejneru, nejprve spustit s tímto [vytvořit kontejner az] [ az-container-create] příkazu (ne proměnné prostředí zadaný):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Upravit výstup, spusťte kontejner druhý s `--environment-variables` argument přidali, zadání hodnoty *NumWords* a *MinLength* proměnné:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Po obou kontejnery stavu zobrazuje jako *ukončeno* (použijte [az kontejneru zobrazit] [ az-container-show] pro kontrolu stavu), zobrazí jejich protokoly s [az kontejneru protokoly] [ az-container-logs] výstup zobrazíte.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup z kontejnerů zobrazit, jak jste změnit chování skriptu druhý kontejneru nastavením proměnné prostředí.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Příklad Azure PowerShell

Nastavení proměnných prostředí v prostředí PowerShell je podobná rozhraní příkazového řádku, ale používá `-EnvironmentVariable` argument příkazového řádku.

Nejprve spustit [microsoft/aci-wordcount] [ aci-wordcount] kontejner ve výchozím nastavení s tímto [New-AzureRmContainerGroup] [ new-azurermcontainergroup]příkaz:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Nyní spusťte následující [New-AzureRmContainerGroup] [ new-azurermcontainergroup] příkaz. Tato Určuje *NumWords* a *MinLength* proměnné prostředí po naplnění proměnná typu pole `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po obou kontejnery stavu *ukončeno* (použijte [Get-AzureRmContainerInstanceLog] [ azure-instance-log] pro kontrolu stavu), s protokoly pro vyžádání obsahu [ Get-AzureRmContainerInstanceLog] [ azure-instance-log] příkaz.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Výstup pro každý kontejner ukazuje, jak jste změnili skriptu, spusťte kontejner nastavením proměnné prostředí.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Příklad Azure portálu

Nastavení proměnných prostředí, když spustíte kontejner na portálu Azure, zadejte je do **konfigurace** při vytváření kontejneru.

Při nasazení pomocí portálu, jste aktuálně omezená na tři proměnné a je nutné je zadat v tomto formátu: `"variableName":"value"`

Chcete-li zobrazit příklad, spusťte [microsoft/aci-wordcount] [ aci-wordcount] kontejner s *NumWords* a *MinLength* proměnné.

1. V **konfigurace**, nastavte **restartujte zásad** k *při selhání*
2. Zadejte `"NumWords":"5"` první proměnná, vyberte **Ano** pod **přidat další proměnné prostředí**a zadejte `"MinLength":"8"` druhý proměnné. Vyberte **OK** ověřte a pak nasaďte kontejner.

![Portál stránky zobrazující prostředí proměnné povolit tlačítka a textová pole][portal-env-vars-01]

Pokud chcete zobrazit protokoly kontejneru, v části **nastavení** vyberte **kontejnery**, pak **protokoly**. Podobá se výstup uvedené v předchozí rozhraní příkazového řádku a prostředí PowerShell oddíly, že uvidíte, jak byl upraven skript chování proměnné prostředí. Zobrazují se pouze pět slova, každý s minimální délkou osm znaků.

![Portál zobrazuje výstup protokolu kontejneru][portal-env-vars-02]

## <a name="secure-values"></a>Zabezpečené hodnoty
Objekty zabezpečené hodnotami jsou určeny k uložení citlivé údaje, jako jsou hesla nebo klíče pro vaši aplikaci. Použití zabezpečeného hodnoty pro proměnné prostředí je bezpečnější a flexibilnější než včetně v bitové kopii vašeho kontejneru. Další možností je použití tajné svazků, které jsou popsané v [připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md).

Proměnné zabezpečeném prostředí s hodnotami, zabezpečené nebude odhalit zabezpečenou hodnotu ve vlastnostech vaší kontejneru, tak hodnota můžete přistupovat pouze z vašeho kontejneru. Například vlastnosti kontejneru vidět na portálu Azure nebo Azure CLI nezobrazí proměnnou prostředí s hodnotou zabezpečené.

Nastavit proměnnou zabezpečeném prostředí tak, že zadáte `secureValue` vlastnost místo běžné `value` pro typ proměnné. Dvě proměnné definované v následující YAML ukazují dva typy proměnných.

### <a name="yaml-deployment"></a>YAML nasazení

Vytvoření `secure-env.yaml` souboru následujícím fragmentem kódu.

```yaml
apiVersion: 2018-06-01
location: westus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "SECRET"
          "secureValue": "my-secret-value"
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
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

Spusťte následující příkaz k nasazení kontejneru skupiny pomocí YAML.

```azurecli-interactive
az container create --resource-group myRG --name securetest -f secure-env.yaml
```

### <a name="verify-environment-variables"></a>Ověřte proměnné prostředí

Spusťte následující příkaz k dotazu pro proměnné prostředí vašeho kontejneru.

```azurecli-interactive
az container show --resource-group myRG --name securetest --query 'containers[].environmentVariables`
```

Odpověď JSON s podrobnostmi o tento kontejner se zobrazit pouze proměnnou prostředí nezabezpečené a zabezpečené klíč proměnné prostředí.

```json
  "environmentVariables": [
    {
      "name": "NOTSECRET",
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET"
    }
```

Můžete zkontrolovat zabezpečené proměnnou prostředí nastavená `exec` příkaz, který umožňuje spouštění příkazu z spuštěné kontejneru. 

Spusťte následující příkaz pro spuštění relace interaktivní bash s kontejneru.
```azurecli-interactive
az container exec --resource-group myRG --name securetest --exec-command "/bin/bash"
```

V kontejneru, vytiskněte vaše proměnná prostředí pomocí následujícího příkazu bash.
```bash
echo $SECRET
```

## <a name="next-steps"></a>Další postup

Založený na úlohách scénáře, jako je dávkové zpracování velkých datovou sadu s několika kontejnerů, můžete těžit z vlastní proměnné prostředí za běhu. Další informace o spuštěných kontejnerů založený na úlohách najdete v tématu [spouštět kontejnerizované úlohy v Azure kontejner instancí](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
