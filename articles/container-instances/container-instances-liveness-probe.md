---
title: Konfigurace liveness sondy v Azure kontejner instancí
description: Naučte se konfigurovat liveness sondy restartu není v pořádku kontejnerů v Azure kontejner instancí
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 76ca4db28d99702532ae656a19f0d54b479a13fe
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249246"
---
# <a name="configure-liveness-probes"></a>Konfigurace liveness sondy

Kontejnerizované aplikace se může spustit pro dlouhou dobu, což vede k porušený stavy, které je nutné být opravit restartováním kontejneru. Azure instancí kontejnerů podporuje liveness sondy na konfigurace, aby vaše kontejneru můžete restartovat, pokud důležité funkce nefunguje. 

Tento článek vysvětluje, jak nasadit kontejner skupinu, která zahrnuje sondu liveness demonstraci na automatické restartování kontejner simulované není v pořádku.

## <a name="yaml-deployment"></a>YAML nasazení

Vytvoření `liveness-probe.yaml` souboru následujícím fragmentem kódu. Tento soubor definuje skupinu kontejneru, která se skládá z kontejner NGNIX, který nakonec se změní na není v pořádku. 

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
  restartPolicy: Never
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Spusťte následující příkaz k nasazení této skupiny kontejner s výše YAML konfigurací:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Spuštění příkazu.

Nasazení definuje počáteční příkaz, který má spustit při prvním spuštění kontejneru spuštěna, definované `command` vlastnost, která přijímá pole řetězců. V tomto příkladu bude spustit relaci bash a vytvořte soubor s názvem `healthy` v rámci `/tmp` adresář předáním tento příkaz:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Bude potom režimu spánku po dobu 30 sekund před odstraněním souboru a potom přešel do režimu spánku 10 minut.

### <a name="liveness-command"></a>Příkaz liveness

Toto nasazení definuje `livenessProbe` který podporuje `exec` liveness příkaz, který funguje jako liveness kontroly. Pokud tento příkaz ukončí s nenulovou hodnotu, bude kontejneru byly ukončeny a restartování signalizace `healthy` soubor nebyl nalezen. Pokud tento příkaz ukončí úspěšně s ukončovacím kódem 0, bude provedena žádná akce.

`periodSeconds` Vlastnost označí liveness příkaz Spustit každých 5 sekund.

## <a name="verify-liveness-output"></a>Zkontrolujte výstup liveness

V rámci prvních 30 sekundách `healthy` existuje soubor vytvořený příkaz ke spuštění. Pokud příkaz liveness vyhledává `healthy` existence souboru stavový kód vrátí nulu, signalizace úspěch, tak bez restartování nastane.

Po 30 sekund `cat /tmp/healthy` se začne být neúspěšné, způsobuje není v pořádku a utracení k událostem. 

Tyto události lze zobrazit z portálu Azure nebo Azure CLI 2.0.

![Portál událostí není v pořádku][portal-unhealthy]

Zobrazením události na portálu Azure, události typu `Unhealthy` aktivuje se při selhání příkazu liveness. Následné událostí bude typu `Killing`, což svědčí o tom odstranění kontejneru, můžete začít restartování. Pokaždé, když k tomu dochází, se zvýší počet restartování kontejneru.

Po dokončení restartování na místě tak, aby prostředky jako veřejné IP adresy a zachovají obsah daného uzlu.

![Čítač portálu restartování][portal-restart]

Pokud test liveness nepřetržitě selže a spustí příliš mnoho restartování, bude vaše kontejneru zadejte exponenciální regrese zpoždění.

## <a name="liveness-probes-and-restart-policies"></a>Sondy liveness a restartování zásady

Zásady restartování mají přednost před aktivovány liveness sondy chování při restartování. Například pokud nastavíte `restartPolicy = Never` *a* sondu liveness skupina kontejneru nebude restartování v případě selhání liveness kontrolu. Skupina kontejneru bude místo toho vyhovovat zásadám skupiny kontejneru restartování z `Never`.

## <a name="next-steps"></a>Další postup

Založený na úlohách scénáře může vyžadovat liveness testu tní povolit automatické restartování, pokud se předběžné funkce nepracuje správně. Další informace o spuštěných kontejnerů založený na úlohách najdete v tématu [spouštět kontejnerizované úlohy v Azure kontejner instancí](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png