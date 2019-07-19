---
title: Konfigurace sond živého provozu v Azure Container Instances
description: Naučte se konfigurovat sondy živého provozu pro restartování poškozených kontejnerů v Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325870"
---
# <a name="configure-liveness-probes"></a>Konfigurace testů aktivity

Kontejnerové aplikace mohou běžet po delší dobu, což vede k nefunkčním stavům, které mohou být potřeba opravit restartováním kontejneru. Azure Container Instances podporuje testy živého provozu pro zahrnutí konfigurací, aby se Váš kontejner mohl restartovat, pokud nefungují kritické funkce.

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která zahrnuje test živého provozu, který demonstruje automatické restartování simulovaného kontejneru, který není v pořádku.

## <a name="yaml-deployment"></a>Nasazení YAML

`liveness-probe.yaml` Vytvořte soubor s následujícím fragmentem kódu. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru NGNIX, který se nakonec stává stavem není v pořádku.

```yaml
apiVersion: 2018-06-01
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

Nasazení definuje spouštěcí příkaz, který má být spuštěn při prvním spuštění kontejneru definovaného `command` vlastností, která přijímá pole řetězců. V tomto příkladu spustí relaci bash a v `healthy` `/tmp` adresáři vytvoří soubor s názvem, který projdete tímto příkazem:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Po 30 sekundách se pak tento soubor před odstraněním dokončí a pak vstoupí do režimu spánku až 10 minut.

### <a name="liveness-command"></a>Živý příkaz

Toto nasazení definuje `livenessProbe` , které `exec` podporuje příkaz živého provozu, který funguje jako aktivní kontrolu. Pokud se tento příkaz ukončí s nenulovou hodnotou, kontejner se ukončí a restartuje, protože se nepovedlo `healthy` najít signál souboru. Pokud se tento příkaz ukončí úspěšně s ukončovacím kódem 0, nebude provedena žádná akce.

`periodSeconds` Vlastnost určuje, že se má příkaz ' živý ' spustit každých 5 sekund.

## <a name="verify-liveness-output"></a>Ověřit výstup živého provozu

Během prvních 30 sekund `healthy` soubor vytvořený pomocí spouštěcího příkazu existuje. Když příkaz živá funkce kontroluje `healthy` existenci souboru, vrátí stavový kód nulu, signalizaci úspěch, takže nedojde k restartování.

Po 30 sekundách se začne `cat /tmp/healthy` zdařit selhání, což způsobí, že dojde k chybám a ukončování událostí.

Tyto události se dají zobrazit z Azure Portal nebo pomocí Azure CLI.

![Událost chybného portálu][portal-unhealthy]

Zobrazením událostí v Azure Portal se události typu `Unhealthy` aktivují při selhání příkazu pro živý provoz. Následná událost bude typu `Killing`a označuje odstranění kontejneru, aby bylo možné spustit restart. Počet restartování kontejneru se zvýší pokaždé, když k tomuto dojde.

Restarty jsou dokončeny na místě, takže se zachovají prostředky jako veřejné IP adresy a obsah specifický pro uzel.

![Čítač restartování portálu][portal-restart]

Pokud se test živého provozu nepřetržitě nezdaří a aktivuje se příliš mnoho restartování, váš kontejner vstoupí v případě exponenciálního zpoždění zpět.

## <a name="liveness-probes-and-restart-policies"></a>Provozní sondy a zásady restartování

Zásady restartování nahrazují chování při restartování aktivované sondami živého provozu. Pokud jste například nastavili `restartPolicy = Never` *a a* živý test, skupina kontejnerů se v případě neúspěšné kontroly živého chodu restartuje. Skupina kontejnerů místo toho bude vyhovovat zásadám `Never`restartování skupiny kontejnerů.

## <a name="next-steps"></a>Další postup

Scénáře založené na úlohách můžou vyžadovat test živého provozu, aby se povolilo automatické restartování, pokud funkce bez problémů nefunguje správně. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh v Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
