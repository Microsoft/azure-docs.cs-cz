---
title: Nastavení sondy živého provozu na instanci kontejneru
description: Naučte se konfigurovat sondy živého provozu pro restartování poškozených kontejnerů v Azure Container Instances
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934160"
---
# <a name="configure-liveness-probes"></a>Konfigurace testů aktivity

Kontejnerové aplikace mohou běžet po delší dobu, což vede k nefunkčním stavům, které může být nutné opravit restartováním kontejneru. Azure Container Instances podporuje sondy živého provozu, abyste mohli své kontejnery v rámci skupiny kontejnerů nakonfigurovat tak, aby se restartovaly, pokud nefungují kritické funkce. Sonda živého chování se chová jako [test živého Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která zahrnuje test živého provozu, který demonstruje automatické restartování simulovaného kontejneru, který není v pořádku.

Azure Container Instances taky podporuje [testy připravenosti](container-instances-readiness-probe.md), které můžete nakonfigurovat tak, aby se zajistilo, že provoz dosáhne kontejneru jenom v případě, že je připravený.

> [!NOTE]
> V současné době nemůžete použít test živého provozu ve skupině kontejnerů nasazených do virtuální sítě.

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

### <a name="start-command"></a>Spustit – příkaz

Nasazení zahrnuje `command` vlastnost definující spouštěcí příkaz, který se spustí při prvním spuštění kontejneru. Tato vlastnost přijímá pole řetězců. Tento příkaz simuluje kontejner vstupu do stavu není v pořádku.

Nejprve spustí relaci bash a vytvoří soubor s názvem `healthy` v `/tmp` adresáři. Po 30 sekundách se pak před odstraněním souboru dokončí, a pak vstoupí do režimu spánku po dobu 10 minut:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Živý příkaz

Toto nasazení definuje `livenessProbe` , které podporuje příkaz `exec` živého provozu, který funguje jako provozní kontroly. Pokud se tento příkaz ukončí s nenulovou hodnotou, kontejner se ukončí a restartuje, protože se nepovedlo najít signál k `healthy` souboru. Pokud se tento příkaz ukončí úspěšně s ukončovacím kódem 0, není provedena žádná akce.

`periodSeconds` Vlastnost určuje, že se má příkaz ' živý ' spustit každých 5 sekund.

## <a name="verify-liveness-output"></a>Ověřit výstup živého provozu

Během prvních 30 sekund `healthy` soubor vytvořený pomocí spouštěcího příkazu existuje. Když příkaz živá funkce kontroluje existenci `healthy` souboru, vrátí stavový kód 0, signalizaci úspěšné, takže nedojde k restartování.

Po 30 sekundách se příkaz `cat /tmp/healthy` začne zdařit, což způsobí, že dojde k chybám a ukončování událostí.

Tyto události se dají zobrazit z Azure Portal nebo pomocí Azure CLI.

![Událost chybného portálu][portal-unhealthy]

Zobrazením událostí v Azure Portal se události typu `Unhealthy` aktivují při selhání příkazu pro živý provoz. Následná událost je typu `Killing`a označuje odstranění kontejneru, aby bylo možné spustit restart. Počet restartování pro kontejner se zvýší pokaždé, když dojde k této události.

Restarty jsou dokončeny na místě, takže se zachovají prostředky, jako jsou veřejné IP adresy a obsah specifický pro uzel.

![Čítač restartování portálu][portal-restart]

Pokud se test živého provozu nepřetržitě nezdařil a vyvolá příliš mnoho restartování, váš kontejner vstoupí do exponenciálního zpoždění.

## <a name="liveness-probes-and-restart-policies"></a>Provozní sondy a zásady restartování

Zásady restartování nahrazují chování při restartování aktivované sondami živého provozu. Pokud jste například nastavili `restartPolicy = Never` *a a* živý test, skupina kontejnerů se nerestartuje z důvodu neúspěšné kontroly živých. Skupina kontejnerů místo toho dodržuje zásady restartování skupiny kontejnerů `Never`.

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách můžou vyžadovat test živého provozu, aby se povolilo automatické restartování, pokud funkce bez problémů nefunguje správně. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh v Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
