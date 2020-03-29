---
title: Nastavit sondu připravenosti na instanci kontejneru
description: Zjistěte, jak nakonfigurovat sondu, která zajistí, že kontejnery v instanci azure kontejneru přijímají požadavky, pouze když jsou připravené
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935682"
---
# <a name="configure-readiness-probes"></a>Konfigurace testů připravenosti

U kontejnerizovaných aplikací, které obsluhují provoz, můžete ověřit, zda je váš kontejner připraven ke zpracování příchozích požadavků. Azure Container Instances podporuje sondy připravenosti zahrnout konfigurace tak, aby váš kontejner nelze přistupovat za určitých podmínek. Sonda připravenosti se chová jako [sonda připravenosti Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Aplikace kontejneru může například potřebovat načíst velkou sadu dat během spuštění a nechcete, aby během této doby přijímali požadavky.

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která obsahuje sondu připravenosti, takže kontejner přijímá pouze provoz, když sonda uspěje.

Azure Container Instances také podporuje [sondy liveness](container-instances-liveness-probe.md), které můžete nakonfigurovat tak, aby způsobily automatické restartování kontejneru není v pořádku.

> [!NOTE]
> V současné době nelze použít sondu připravenosti ve skupině kontejnerů nasazené do virtuální sítě.

## <a name="yaml-configuration"></a>Konfigurace YAML

Jako příklad vytvořte `readiness-probe.yaml` soubor s následujícím výstřižkem, který obsahuje sondu připravenosti. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru s malou webovou aplikací. Aplikace se nasadí `mcr.microsoft.com/azuredocs/aci-helloworld` z veřejné image. Tato kontejnerizovaná aplikace je taky demonstrovaná v [instanci nasazení kontejneru v Azure pomocí Azure CLI](container-instances-quickstart.md) a dalších rychlých startů.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Spustit – příkaz

Nasazení obsahuje `command` vlastnost definující počáteční příkaz, který se spustí při prvním spuštění kontejneru. Tato vlastnost přijímá pole řetězců. Tento příkaz simuluje čas, kdy se webová aplikace spustí, ale kontejner není připraven. 

Nejprve spustí relaci prostředí a `node` spustí příkaz ke spuštění webové aplikace. Spustí také příkaz do režimu spánku po dobu 240 `ready` sekund, po kterém vytvoří soubor volaný v adresáři: `/tmp`

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Připravenost, příkaz

Tento soubor YAML `readinessProbe` definuje, `exec` který podporuje příkaz připravenosti, který funguje jako kontrola připravenosti. Tento příklad připravenosti příkaz testy `ready` pro existenci `/tmp` souboru v adresáři.

Pokud `ready` soubor neexistuje, příkaz připravenosti se ukončí s nenulovou hodnotou. kontejner pokračuje v běhu, ale nelze získat přístup. Když příkaz úspěšně ukončí s ukončovací kód 0, kontejner je připraven k přístupu. 

Vlastnost `periodSeconds` určuje připravenost příkaz by měl spustit každých 5 sekund. Sonda připravenosti běží po dobu životnosti skupiny kontejnerů.

## <a name="example-deployment"></a>Příklad nasazení

Spusťte následující příkaz pro nasazení skupiny kontejnerů s předchozí konfigurací YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Zobrazit kontroly připravenosti

V tomto příkladu během prvních 240 sekund příkaz připravenosti `ready` selže při kontrole existence souboru. Stavový kód vrátil signály, že kontejner není připraven.

Tyto události lze zobrazit z portálu Azure nebo Azure CLI. Portál například zobrazuje události `Unhealthy` typu jsou spuštěny při selhání příkazu připravenosti. 

![Událost Není v pořádku portálu][portal-unhealthy]

## <a name="verify-container-readiness"></a>Ověření připravenosti kontejneru

Po spuštění kontejneru můžete ověřit, že není zpočátku přístupný. Po zřizování získáte IP adresu skupiny kontejnerů:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Pokuste se o přístup k webu, zatímco sonda připravenosti selže:

```bash
wget <ipAddress>
```

Výstup ukazuje, že web není zpočátku přístupný:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Po 240 sekundách se příkaz připravenosti uspěje a signalizuje, že kontejner je připraven. Nyní při spuštění `wget` příkazu, uspěje:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Když je kontejner připraven, můžete také přistupovat k webové aplikaci procházením IP adresy pomocí webového prohlížeče.

> [!NOTE]
> Sonda připravenosti nadále spuštěna po dobu životnosti skupiny kontejnerů. Pokud příkaz připravenosti selže později, kontejner se opět stane nepřístupným. 
> 

## <a name="next-steps"></a>Další kroky

Sonda připravenosti může být užitečná ve scénářích zahrnujících skupiny s více kontejnery, které se skládají ze závislých kontejnerů. Další informace o scénářích s více kontejnery najdete [v tématu Skupiny kontejnerů v instancích kontejnerů Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
