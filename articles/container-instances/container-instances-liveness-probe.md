---
title: Nastavení sondy živého provozu na instanci kontejneru
description: Naučte se konfigurovat sondy živého provozu pro restartování poškozených kontejnerů v Azure Container Instances
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 96d98d18a3f0ac666fb2c057216f7844b176d177
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481677"
---
# <a name="configure-liveness-probes"></a>Konfigurace testů aktivity

Kontejnerové aplikace mohou běžet po delší dobu, což vede k nefunkčním stavům, které může být nutné opravit restartováním kontejneru. Azure Container Instances podporuje sondy živého provozu, abyste mohli své kontejnery v rámci skupiny kontejnerů nakonfigurovat tak, aby se restartovaly, pokud nefungují kritické funkce. Sonda živého chování se chová jako [test živého Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která zahrnuje test živého provozu, který demonstruje automatické restartování simulovaného kontejneru, který není v pořádku.

Azure Container Instances taky podporuje [testy připravenosti](container-instances-readiness-probe.md), které můžete nakonfigurovat tak, aby se zajistilo, že provoz dosáhne kontejneru jenom v případě, že je připravený.

## <a name="yaml-deployment"></a>Nasazení YAML

Vytvořte `liveness-probe.yaml` soubor s následujícím fragmentem kódu. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru NGNIX, který se nakonec stává stavem není v pořádku.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Spuštěním následujícího příkazu Nasaďte tuto skupinu kontejnerů s výše uvedenou konfigurací YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Spustit příkaz

Nasazení definuje počáteční příkaz, který se spustí při prvním spuštění kontejneru definovaného vlastností `command`, který přijímá pole řetězců. V tomto příkladu spustí relaci bash a vytvoří soubor s názvem `healthy` v rámci `/tmp` adresáře předáním tohoto příkazu:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Po 30 sekundách se pak tento soubor před odstraněním dokončí a pak přejde do režimu spánku po dobu 10 minut.

### <a name="liveness-command"></a>Živý příkaz

Toto nasazení definuje `livenessProbe`, který podporuje příkaz `exec` živý, který funguje jako aktivní kontrolu. Pokud se tento příkaz ukončí s nenulovou hodnotou, kontejner se ukončí a restartuje a signalizace `healthy` soubor se nepovedlo najít. Pokud se tento příkaz ukončí úspěšně s ukončovacím kódem 0, nebude provedena žádná akce.

Vlastnost `periodSeconds` určuje, že se má příkaz pro živý provoz provádět každých 5 sekund.

## <a name="verify-liveness-output"></a>Ověřit výstup živého provozu

Během prvních 30 sekund existuje `healthy` soubor vytvořený pomocí spouštěcího příkazu. Když příkaz pro živou kontrolu existence souboru `healthy`, vrátí stavový kód nulu a signalizaci úspěšné, takže nedojde k restartování.

Po 30 sekundách se `cat /tmp/healthy` začnou zdařit, což způsobí, že dojde k chybám, které způsobují události v pořádku a usmrcování.

Tyto události se dají zobrazit z Azure Portal nebo pomocí Azure CLI.

![Událost chybného portálu][portal-unhealthy]

Zobrazením událostí v Azure Portal se události typu `Unhealthy` aktivují při selhání příkazu živého provozu. Následná událost bude typu `Killing`a signalizuje odstranění kontejneru, aby bylo možné začít restart. Počet restartování pro kontejner se zvýší pokaždé, když dojde k této události.

Restarty jsou dokončeny na místě, takže se zachovají prostředky jako veřejné IP adresy a obsah specifický pro uzel.

![Čítač restartování portálu][portal-restart]

Pokud se test živého provozu nepřetržitě nezdaří a aktivuje se příliš mnoho restartování, váš kontejner vstoupí v případě exponenciálního zpoždění zpět.

## <a name="liveness-probes-and-restart-policies"></a>Provozní sondy a zásady restartování

Zásady restartování nahrazují chování při restartování aktivované sondami živého provozu. Pokud například nastavíte `restartPolicy = Never` *a* sondu živého provozu, skupina kontejnerů se nerestartuje z důvodu neúspěšné kontroly živých. Skupina kontejnerů místo toho bude vyhovovat zásadám restartování `Never`skupiny kontejnerů.

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách můžou vyžadovat test živého provozu, aby se povolilo automatické restartování, pokud funkce bez problémů nefunguje správně. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh v Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
