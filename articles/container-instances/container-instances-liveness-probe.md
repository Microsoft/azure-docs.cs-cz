---
title: Nastavení sondy živosti v instanci kontejneru
description: Zjistěte, jak nakonfigurovat sondy liveness pro restartování kontejnerů není v pořádku v instancích kontejnerů Azure
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934160"
---
# <a name="configure-liveness-probes"></a>Konfigurace testů aktivity

Kontejnerizované aplikace může běžet delší dobu, výsledkem je přerušené stavy, které může být nutné opravit restartováním kontejneru. Azure Container Instances podporuje sondy liveness, takže můžete nakonfigurovat kontejnery v rámci skupiny kontejnerů restartovat, pokud kritické funkce nefunguje. Sonda živosti se chová jako [kubernetesova sonda živosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Tento článek vysvětluje, jak nasadit skupinu kontejnerů, která obsahuje sondu liveness, což ukazuje automatické restartování simulovaného kontejneru není v pořádku.

Azure Container Instances také podporuje [sondy připravenosti](container-instances-readiness-probe.md), které můžete nakonfigurovat tak, aby se zajistilo, že provoz dosáhne kontejneru pouze v případě, že je pro něj připraven.

> [!NOTE]
> V současné době nelze použít sondu liveness ve skupině kontejnerů nasazené do virtuální sítě.

## <a name="yaml-deployment"></a>Nasazení YAML

Vytvořte `liveness-probe.yaml` soubor s následujícím úryvkem. Tento soubor definuje skupinu kontejnerů, která se skládá z kontejneru NGNIX, který se nakonec stane nefunkčním.

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

Spusťte následující příkaz pro nasazení této skupiny kontejnerů s výše uvedenou konfigurací YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Spustit – příkaz

Nasazení obsahuje `command` vlastnost definující počáteční příkaz, který se spustí při prvním spuštění kontejneru. Tato vlastnost přijímá pole řetězců. Tento příkaz simuluje kontejner ucházející do stavu Není v pořádku.

Nejprve spustí bash session a vytvoří `healthy` soubor `/tmp` volaný v adresáři. Potom spí po dobu 30 sekund před odstraněním souboru, pak zadá 10-minutový spánek:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Živost, příkaz

Toto nasazení `livenessProbe` definuje, `exec` který podporuje příkaz živost, který funguje jako kontrola živosti. Pokud tento příkaz ukončí s nenulovou hodnotou, kontejner je ukončena a restartován, signalizace `healthy` souboru nebyl nalezen. Pokud tento příkaz úspěšně ukončí s ukončovací kód 0, je provedena žádná akce.

Vlastnost `periodSeconds` určuje liveness příkaz by měl být spuštěn každých 5 sekund.

## <a name="verify-liveness-output"></a>Ověřit výstup živosti

Během prvních 30 sekund `healthy` existuje soubor vytvořený příkazem start. Když příkaz liveness zkontroluje existenci `healthy` souboru, stavový kód vrátí hodnotu 0, což signalizuje úspěch, takže nedojde k žádnému restartování.

Po 30 sekundách `cat /tmp/healthy` příkaz začne selhat, což způsobuje nezdravé a zabíjení událostí dojít.

Tyto události lze zobrazit z portálu Azure nebo Azure CLI.

![Událost Není v pořádku portálu][portal-unhealthy]

Zobrazením událostí na webu Azure Portal `Unhealthy` se události typu aktivují při selhání příkazu liveness. Následující událost je `Killing`typu , což znamená odstranění kontejneru, takže může začít restartování. Počet restartování pro kontejner se přírůstky pokaždé, když dojde k této události.

Restartování jsou dokončena na místě, takže prostředky, jako jsou veřejné IP adresy a obsah specifický pro uzel jsou zachovány.

![Čítač restartování portálu][portal-restart]

Pokud sonda živosti nepřetržitě selže a aktivuje příliš mnoho restartování, kontejner zadá exponenciální zpoždění back-off.

## <a name="liveness-probes-and-restart-policies"></a>Sondy živosti a zásady restartování

Zásady restartování nahrazují chování restartování vyvolané sondami živosti. Pokud například nastavíte `restartPolicy = Never` sondu a *a* liveness, skupina kontejnerů nebude restartována z důvodu neúspěšné kontroly živosti. Skupina kontejnerů místo toho dodržuje zásady restartování `Never`skupiny kontejnerů .

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách mohou vyžadovat sondu živosti, aby bylo možné povolit automatické restartování, pokud funkce předběžného použití nefunguje správně. Další informace o spouštění kontejnerů založených na úlohách najdete v tématu [Spouštění kontejnerových úloh v instanci kontejnerů Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
