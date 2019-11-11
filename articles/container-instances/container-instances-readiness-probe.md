---
title: Konfigurace sond připravenosti v Azure Container Instances
description: Naučte se konfigurovat sondu, aby kontejnery v Azure Container Instances přijímaly požadavky pouze v případě, že jsou připravené.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 10/17/2019
ms.author: danlep
ms.openlocfilehash: 9cdc8362c377be28a3ed1300b599dc8ebef9e903
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905573"
---
# <a name="configure-readiness-probes"></a>Konfigurace sond připravenosti

U kontejnerových aplikací, které obsluhují provoz, můžete chtít ověřit, že je váš kontejner připravený na zpracování příchozích požadavků. Azure Container Instances podporuje testy připravenosti k zahrnutí konfigurací, aby ke kontejneru za určitých podmínek nezískal přístup. Test připravenosti se chová jako [test připravenosti na Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Aplikace kontejneru může například potřebovat načíst velkou datovou sadu při spuštění a nechcete, aby během této doby přijímala žádosti.

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která zahrnuje test připravenosti, aby kontejner přijímal jenom provoz, když je test úspěšný.

Azure Container Instances podporuje také [sondy živého provozu](container-instances-liveness-probe.md), které můžete nakonfigurovat tak, aby způsobily, že se automaticky restartuje kontejner, který není v pořádku.

## <a name="yaml-configuration"></a>Konfigurace YAML

Jako příklad vytvořte soubor `readiness-probe.yaml` s následujícím fragmentem kódu, který obsahuje test připravenosti. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru, na kterém běží malá webová aplikace. Aplikace je nasazená z veřejné `mcr.microsoft.com/azuredocs/aci-helloworld` image. Tato aplikace kontejneru je také znázorněna v rychlých startech, jako je například [nasazení instance kontejneru v Azure pomocí rozhraní příkazového řádku Azure CLI](container-instances-quickstart.md).

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

### <a name="start-command"></a>Spustit příkaz

Soubor YAML obsahuje spouštěcí příkaz, který má být spuštěn při spuštění kontejneru definovaného vlastností `command`, který přijímá pole řetězců. Tento příkaz simuluje čas, kdy webová aplikace běží, ale kontejner není připravený. Nejprve spustí relaci prostředí a spustí příkaz `node`, který spustí webovou aplikaci. Také spustí příkaz do režimu spánku po 240 sekund, po jehož uplynutí vytvoří soubor s názvem `ready` v adresáři `/tmp`:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Připravenost – příkaz

Tento soubor YAML definuje `readinessProbe`, který podporuje příkaz `exec` připravenosti, který funguje jako kontrolu připravenosti. V tomto příkladu se testuje příkazy připravenosti pro existenci souboru `ready` v adresáři `/tmp`.

Pokud soubor `ready` neexistuje, příkaz připravenosti se ukončí s nenulovou hodnotou; kontejner pokračuje v běhu, ale není k němu mít k dispozici. Po úspěšném ukončení příkazu s ukončovacím kódem 0 je kontejner připravený k otevření. 

Vlastnost `periodSeconds` označuje, že příkaz připravenosti by se měl provádět každých 5 sekund. Test připravenosti se spustí po dobu života skupiny kontejnerů.

## <a name="example-deployment"></a>Příklad nasazení

Spuštěním následujícího příkazu nasaďte skupinu kontejnerů s předchozí konfigurací YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Zobrazit kontroly připravenosti

V tomto příkladu se během prvních 240 sekund příkaz Readiness (připravenost) po kontrole existence souboru `ready` nezdařil. Stavový kód vrátil signál, že kontejner není připravený.

Tyto události se dají zobrazit z Azure Portal nebo pomocí Azure CLI. Například portál zobrazuje události typu `Unhealthy` jsou aktivovány při selhání příkazu Readiness. 

![Událost chybného portálu][portal-unhealthy]

## <a name="verify-container-readiness"></a>Ověřit připravenost kontejneru

Po spuštění kontejneru můžete ověřit, že není na začátku přístupný. Po zřízení Získejte IP adresu skupiny kontejnerů:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Pokus o přístup k webu v době, kdy test připravenosti selhává:

```bash
wget <ipAddress>
```

Výstup ukazuje, že web není přístupný zpočátku:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Po 240 sekundách se příkaz připravenosti úspěšně dokončí a signálem kontejneru je připravený. Když teď spustíte příkaz `wget`, proběhne úspěšně:

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

Až bude kontejner připravený, můžete k webové aplikaci přejít také tak, že přejdete na IP adresu pomocí webového prohlížeče.

> [!NOTE]
> Test připravenosti bude i nadále běžet po dobu životnosti skupiny kontejnerů. Pokud se příkaz Readiness v pozdější době nepovede, kontejner se znovu nezpřístupní. 
> 

## <a name="next-steps"></a>Další kroky

Test připravenosti může být užitečný ve scénářích, které se týkají skupin více kontejnerů, které se skládají z závislých kontejnerů. Další informace o scénářích s více kontejnery najdete v tématu [skupiny kontejnerů v Azure Container Instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
