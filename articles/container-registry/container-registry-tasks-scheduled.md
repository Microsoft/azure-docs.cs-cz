---
title: Plánování úloh Azure Container Registry
description: Nastavení časovačů ke spuštění úlohy Azure Container Registry podle daného plánu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509700"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Spustit úlohu ACR podle daného plánu

V tomto článku se dozvíte, jak spouštět [ACR úloh](container-registry-tasks-overview.md) podle plánu. Naplánování úlohy zařiďte si jeden nebo více *aktivačních časovačích*. 

Plánování úloh je užitečné pro scénáře, jako jsou následující:

* Spuštění úlohy kontejneru pro operace plánované údržby. Například spusťte kontejnerizované aplikace odebrat nepotřebné imagí z registru.
* Spusťte sadu testů pro bitovou kopii produkčního prostředí během pracovní doby v rámci monitorování živého webu.

Pro spuštění příkladů v tomto článku můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete použít ho místně, verze 2.0.68 nebo později se vyžaduje. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Informace o plánování úkolu

* **Aktivační událost s výraz cron** -využívá trigger časovače pro úlohu *výraz cron*. Výraz je řetězec s pěti pole zadáte minutu, hodinu, den, měsíc a den v týdnu pro aktivaci úlohy. Frekvence maximálně jednou za minutu jsou podporovány. 

  Například výraz `"0 12 * * Mon-Fri"` spustí úlohu v poledne UTC na každý den v týdnu. Zobrazit [podrobnosti](#cron-expressions) dále v tomto článku.
* **Více aktivačních časovačích** – přidání více časovače úkolu je povoleno, tak dlouho, dokud se liší plány. 
    * Více aktivačních časovačích zadejte při vytváření úkolu, nebo je přidat později.
    * Volitelně pojmenuje aktivační události pro snadnější správu, nebo úlohy ACR poskytne výchozí názvy aktivační událost.
    * Pokud časovače plány překrývají najednou, úlohy ACR aktivuje úlohu v naplánovaném čase pro každý časovače. 
* **Jiné triggery úloh** – v rámci úlohy aktivované pomocí časovače můžete také povolit triggery na základě [zdrojový kód potvrzení](container-registry-tutorial-build-task.md) nebo [základní aktualizací bitové kopie](container-registry-tutorial-base-image-update.md). Stejně jako ostatní služby ACR úlohy, můžete také [ručně aktivovat][az-acr-task-run] naplánované úlohy.

## <a name="create-a-task-with-a-timer-trigger"></a>Vytvoří úkol s triggerem timer

Když vytvoříte úlohu s [az acr úloha vytvoření][az-acr-task-create] příkazu, můžete volitelně přidat aktivační událost časovače. Přidat `--schedule` parametr a předáte výraz cronu časovač. 

Jako jednoduchý příklad, zadáním následujícího příkazu aktivačních událostí spuštěna `hello-world` image z Docker Hubu každý den ve 21:00 UTC. Úloha se spustí bez kontext zdrojového kódu.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Spustit [az acr úkolu zobrazit][az-acr-task-show] příkazu, jestli je nakonfigurovaná trigger časovače. Ve výchozím nastavení je také povolena aktivační události update základní image.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Aktivuje úlohu ručně s [az acr úlohy][az-acr-task-run] zajistit, že to je správně nastavený:

```azurecli
az acr task run --name mytask --registry myregistry
```

Pokud se kontejner spustí úspěšně, výstup je podobný následujícímu:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Po naplánovaném čase, spusťte [seznamu – spuštěním az acr úlohy][az-acr-task-list-runs] příkazu ověřte, že časovač spustil úlohu podle očekávání:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Po úspěšné časovač výstup je podobný následujícímu:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Spravovat aktivačních časovačích

Použití [az acr úloh časovače][az-acr-task-timer] příkazy pro správu aktivace časovače pro úlohu služby ACR.

### <a name="add-or-update-a-timer-trigger"></a>Přidat nebo aktualizovat trigger časovače

Po vytvoření úkolu pomocí volitelně přidat aktivační událost časovače [přidání az acr úloh časovače][az-acr-task-timer-add] příkazu. Následující příklad přidá název triggeru časovače *časovač2* k *mytask* jste předtím vytvořili. Tento časovač aktivuje úlohu každý den v 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aktualizace plánu aktivační událost existující nebo změnit její stav pomocí [az acr úloha časovače aktualizace][az-acr-task-timer-update] příkazu. Například aktivační událost s názvem aktualizovat *časovač2* k aktivaci úlohy v 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Seznam aktivačních časovačích

[Az acr úloh časovače][az-acr-task-timer-list] příkaz zobrazí aktivačních časovačích nastavení pro úlohu:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Příklad výstupu:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Odebrat aktivační událost časovače 

Použití [az acr úloh časovače odebrat][az-acr-task-timer-remove] příkazu odeberte trigger časovače z úlohy. Následující příklad odebere *časovač2* aktivaci *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Výrazů cron

Používá ACR úkoly [NCronTab](https://github.com/atifaziz/NCrontab) knihovny interpretace výrazů cron. Podporované výrazy v úlohách ACR mít pět povinných polí oddělené symbolem mezery:

`{minute} {hour} {day} {month} {day-of-week}`

Časové pásmo použita pomocí výrazů cron je koordinovaný univerzální čas (UTC). Hodiny se ve 24hodinovém formátu.

> [!NOTE]
> Úlohy služby ACR se nepodporuje `{second}` nebo `{year}` pole ve výrazech cron. Pokud zkopírujete výraz cronu používá v jiném systému, je potřeba odebrat těchto polí, pokud jsou použity.

Každé pole může mít jednu z následujících typů hodnot:

|Type  |Příklad:  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>"5 * * * *"</nobr>|každou hodinu na 5 minut po hodině|
|Všechny hodnoty (`*`)|<nobr>"* 5 * * *"</nobr>|každou minutu hodinu od 5:00 UTC (60 za den)|
|Rozsah (`-` operátor)|<nobr>"0 1-3 * * *"</nobr>|3krát za den v 1:00 2:00 a 3:00 UTC|  
|Sadu hodnot (`,` operátor)|<nobr>"20,30,40 * * * *"</nobr>|3krát za hodinu za 20 minut, 30 minut a 40 minut po hodině|
|Hodnota intervalu (`/` operátor)|<nobr>"*/10 * * * *"</nobr>|6krát za hodinu za 10 minut, 20 minut a tak dále, po hodině

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Příklady procesu cron

|Příklad:|Při aktivaci  |
|---------|---------|
|`"*/5 * * * *"`|každých pět minut|
|`"0 * * * *"`|jednou v horní části každou hodinu|
|`"0 */2 * * *"`|každé dvě hodiny|
|`"0 9-17 * * *"`|každou hodinu od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|v 9:30 UTC každý den|
|`"30 9 * * 1-5"`|v 9:30 UTC každý den v týdnu|
|`"30 9 * Jan Mon"`|v 9:30 UTC každé pondělí v lednu|


## <a name="next-steps"></a>Další postup

Pro příklady úloh, aktivovaného potvrzení zdrojový kód nebo základní image aktualizace, přečtěte si [sérii úkolů ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli