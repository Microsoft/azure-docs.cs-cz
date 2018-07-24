---
title: Konfigurace sond aktivity ve službě Azure Container Instances
description: Další informace o konfiguraci sondy aktivity restartovat není v pořádku, kontejnery ve službě Azure Container Instances
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 1582f0d7ec688bc72cc9d1aa6ae0ddb0a6ad3a17
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213067"
---
# <a name="configure-liveness-probes"></a>Konfigurace testů aktivity

Kontejnerizované aplikace může spustit dlouhou dobu, což vede k porušení stavy, které může být zapotřebí opravit restartováním kontejneru. Služba Azure Container Instances podporuje aktivity testy zahrnují konfigurace tak, aby kontejneru můžete restartovat, pokud důležité funkce nefunguje.

Tento článek vysvětluje, jak nasadit skupiny kontejnerů, která obsahuje test aktivity, předvede automatické restartování kontejneru s Simulovaná není v pořádku.

## <a name="yaml-deployment"></a>Nasazení YAML

Vytvoření `liveness-probe.yaml` souboru následujícím fragmentem kódu. Tento soubor definuje skupinu kontejnerů, který se skládá z NGNIX kontejneru, který nakonec nebude v pořádku.

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

Spuštěním následujícího příkazu nasaďte tuto skupinu kontejnerů s konfiguraci uvedené výš YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Spustit – příkaz

Definuje počáteční příkaz ke spuštění, při prvním spuštění kontejneru spuštěná, definované nasazení `command` vlastnost, která přijímá pole řetězců. V tomto příkladu se spustit relaci prostředí bash a vytvořte soubor s názvem `healthy` v rámci `/tmp` adresáře předáním tohoto příkazu:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Potom se přejít do režimu spánku po dobu 30 sekund před odstraněním souboru a pak přejde do režimu spánku 10 minut.

### <a name="liveness-command"></a>Příkaz aktivity

Definuje toto nasazení `livenessProbe` která podporuje `exec` aktivity příkaz, který funguje jako kontrola aktivity. Pokud tento příkaz se ukončí s nenulovou hodnotu, kontejneru budou ukončeny a restartování, signalizace `healthy` soubor nebyl nalezen. Pokud se příkaz úspěšně ukončí s ukončovacím kódem 0, bude provedena žádná akce.

`periodSeconds` Vlastnost určuje, aktivity příkazu by se měl spustit každých 5 sekund.

## <a name="verify-liveness-output"></a>Ověření výstupu aktivity

Během prvních 30 sekund `healthy` existuje soubor vytvořený pomocí příkazu start. Když příkaz aktivity vyhledá `healthy` souboru existenci stavový kód vrátí nulu na začátku, signalizace úspěch, tak žádné restartování dochází.

Po 30 sekundách `cat /tmp/healthy` se začnou být neúspěšné, způsobí není v pořádku a utracení k událostem.

Tyto události můžete zobrazit z webu Azure portal nebo rozhraní příkazového řádku Azure.

![Událost portálu není v pořádku][portal-unhealthy]

Zobrazením událostí na webu Azure Portal, události typu `Unhealthy` aktivuje se při selhání příkazu aktivity. Následné event bude typu `Killing`, značící odstranění kontejneru mohli začít restartování. Počet restartování pro kontejner se zvýší pokaždé, když k tomu dochází.

Po dokončení se restartuje místní tak zdroje, jako jsou veřejné IP adresy a zachová obsah daného uzlu.

![Restartování portálu čítače][portal-restart]

Pokud test aktivity průběžně se nezdaří a aktivuje příliš mnoho restartování, zadá svůj kontejner exponenciální regrese zpoždění.

## <a name="liveness-probes-and-restart-policies"></a>Aktivity sondy a zásady restartování

Zásady restartování mají přednost před chování při restartování aktivuje sondy aktivity. Pokud nastavíte například `restartPolicy = Never` *a* test aktivity, skupina kontejnerů restartování v případě zaškrtnutí neúspěšné aktivity. Skupina kontejnerů bude místo toho dodržovat zásady restartování skupiny kontejnerů `Never`.

## <a name="next-steps"></a>Další postup

Scénáře založené na úlohách můžou vyžadovat test aktivity, která aktivuje automatické restartování v případě, že předběžné funkce nepracuje správně. Další informace o spuštěné kontejnery založené na úlohách najdete v tématu [spouštění kontejnerizovaných úloh ve službě Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
