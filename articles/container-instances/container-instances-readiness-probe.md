---
title: Nastavení testu připravenosti na instanci kontejneru
description: Naučte se konfigurovat sondu, aby kontejnery v Azure Container Instances přijímaly požadavky pouze v případě, že jsou připravené.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169659"
---
# <a name="configure-readiness-probes"></a>Konfigurace testů připravenosti

U kontejnerových aplikací, které obsluhují provoz, můžete chtít ověřit, že je váš kontejner připravený na zpracování příchozích požadavků. Azure Container Instances podporuje testy připravenosti k zahrnutí konfigurací, aby ke kontejneru za určitých podmínek nezískal přístup. Test připravenosti se chová jako [test připravenosti na Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Aplikace kontejneru může například potřebovat načíst velkou datovou sadu při spuštění a nechcete, aby během této doby přijímala žádosti.

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která zahrnuje test připravenosti, aby kontejner přijímal jenom provoz, když je test úspěšný.

Azure Container Instances podporuje také [sondy živého provozu](container-instances-liveness-probe.md), které můžete nakonfigurovat tak, aby způsobily, že se automaticky restartuje kontejner, který není v pořádku.

> [!NOTE]
> V současné době nemůžete použít test připravenosti ve skupině kontejnerů nasazených do virtuální sítě.

## <a name="yaml-configuration"></a>Konfigurace YAML

Jako příklad vytvořte `readiness-probe.yaml` soubor s následujícím fragmentem kódu, který obsahuje test připravenosti. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru, na kterém běží malá webová aplikace. Aplikace se nasadí z veřejné `mcr.microsoft.com/azuredocs/aci-helloworld` image. Tato kontejnerová aplikace je také znázorněna v [části nasazení instance kontejneru v Azure pomocí rozhraní příkazového řádku Azure](container-instances-quickstart.md) a dalších rychlých startů.

```yaml
apiVersion: 2019-12-01
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

Nasazení zahrnuje `command` vlastnost definující spouštěcí příkaz, který se spustí při prvním spuštění kontejneru. Tato vlastnost přijímá pole řetězců. Tento příkaz simuluje čas, kdy webová aplikace běží, ale kontejner není připravený. 

Nejdřív spustí relaci prostředí a spustí `node` příkaz pro spuštění webové aplikace. Také spustí příkaz do režimu spánku po 240 sekund, po jehož uplynutí vytvoří soubor nazvaný `ready` v `/tmp` adresáři:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Připravenost – příkaz

Tento soubor YAML definuje `readinessProbe` , který podporuje `exec` příkaz připravenosti, který funguje jako kontrolu připravenosti. Tento příklad testuje příkazy připravenosti pro existenci `ready` souboru v `/tmp` adresáři.

Pokud `ready` soubor neexistuje, příkaz připravenosti se ukončí s nenulovou hodnotou. kontejner pokračuje v běhu, ale není k němu možné mít k dispozici. Po úspěšném ukončení příkazu s ukončovacím kódem 0 je kontejner připravený k otevření. 

Tato `periodSeconds` vlastnost určuje, že příkaz připravenosti by se měl spouštět každých 5 sekund. Test připravenosti se spustí po dobu života skupiny kontejnerů.

## <a name="example-deployment"></a>Příklad nasazení

Spuštěním následujícího příkazu nasaďte skupinu kontejnerů s předchozí konfigurací YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Zobrazit kontroly připravenosti

V tomto příkladu se během prvních 240 sekund příkaz Readiness (připravenost) po kontrole `ready` existence souboru nezdařil. Stavový kód vrátil signál, že kontejner není připravený.

Tyto události se dají zobrazit z Azure Portal nebo pomocí Azure CLI. Například portál zobrazuje události typu `Unhealthy` , které jsou aktivovány při selhání příkazu připravenosti. 

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

Po 240 sekundách se příkaz připravenosti úspěšně dokončí a signálem kontejneru je připravený. Nyní při spuštění `wget` příkazu proběhne úspěšně:

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
