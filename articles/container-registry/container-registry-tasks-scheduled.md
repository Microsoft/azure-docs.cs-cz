---
title: Kurz – naplánování úlohy ACR
description: V tomto kurzu se dozvíte, jak spustit úlohu registru kontejneru Azure podle definovaného plánu nastavením jedné nebo více aktivačních událostí časovače.
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402875"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Spuštění úlohy ACR podle definovaného plánu

Tento kurz ukazuje, jak spustit [úlohu ACR](container-registry-tasks-overview.md) podle plánu. Naplánujte úlohu nastavením jedné nebo více *aktivačních událostí časovače*. Časovač aktivační události lze použít samostatně, nebo v kombinaci s jinými aktivačních událostí úlohy.

V tomto kurzu se dozvíte o plánování úkolů a:

> [!div class="checklist"]
> * Vytvoření úlohy se spouštěčem časovače
> * Správa aktivačních událostí časovače

Plánování úkolu je užitečné pro následující scénáře:

* Spusťte úlohu kontejneru pro operace naplánované údržby. Spusťte například kontejnerizovanou aplikaci a odeberte z registru nepotřebné obrázky.
* Spusťte sadu testů produkční bitové kopie během pracovního dne jako součást monitorování živého webu.

Můžete použít Azure Cloud Shell nebo místní instalace Azure CLI ke spuštění příklady v tomto článku. Pokud jej chcete používat místně, je vyžadována verze 2.0.68 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Plánování úkolu

* **Trigger s cron expression** - Aktivační událost časovače pro úlohu používá *výraz cron*. Výraz je řetězec s pěti poli určujícími minutu, hodinu, den, měsíc a den v týdnu pro spuštění úkolu. Frekvence až jednou za minutu jsou podporovány.

  Například výraz `"0 12 * * Mon-Fri"` spustí úlohu v poledne UTC každý den v týdnu. Podrobnosti [naleznete](#cron-expressions) dále v tomto článku.
* **Více aktivačních událostí časovače** - Přidání více časovačů k úloze je povoleno, pokud se plány liší.
    * Při vytváření úkolu zadejte více aktivačních událostí časovače nebo je přidejte později.
    * Volitelně pojmenujte aktivační události pro snadnější správu nebo úlohy ACR budou poskytovat výchozí názvy aktivačních událostí.
    * Pokud se časové plány překrývají současně, úlohy ACR spustí úlohu v naplánovaný čas pro každý časovač.
* **Další aktivační události úlohy** – V úlohě spouštěné časovačem můžete také povolit aktivační události na základě [potvrzení zdrojového kódu](container-registry-tutorial-build-task.md) nebo [aktualizací základní bitové kopie](container-registry-tutorial-base-image-update.md). Stejně jako ostatní úkoly ACR můžete také [ručně spustit][az-acr-task-run] naplánovanou úlohu.

## <a name="create-a-task-with-a-timer-trigger"></a>Vytvoření úlohy se spouštěčem časovače

Když vytvoříte úkol s příkazem [az acr task create,][az-acr-task-create] můžete volitelně přidat aktivační událost časovače. Přidejte `--schedule` parametr a předavěte výraz cron pro časovač.

Jako jednoduchý příklad následující příkaz aktivuje `hello-world` spuštění bitové kopie z Docker Hub každý den v 21:00 UTC. Úloha je spuštěna bez kontextu zdrojového kódu.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Spusťte příkaz [az acr task show,][az-acr-task-show] abyste zjistili, že je nakonfigurována aktivační událost časovače. Ve výchozím nastavení je povolena také aktivační událost aktualizace základní bitové kopie.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Spusťte úlohu ručně s [az acr úlohou,][az-acr-task-run] abyste zajistili, že je správně nastavena:

```azurecli
az acr task run --name mytask --registry myregistry
```

Pokud kontejner úspěšně běží, výstup je podobný následujícímu:

```output
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

Po naplánovaném čase spusťte příkaz [az acr seznam úloh aos,][az-acr-task-list-runs] abyste ověřili, že časovač spustil úlohu podle očekávání:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Když je časovač úspěšný, výstup je podobný následujícímu:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Správa aktivačních událostí časovače

Pomocí příkazů [časovače úloh az acr][az-acr-task-timer] můžete spravovat aktivační události časovače pro úlohu ACR.

### <a name="add-or-update-a-timer-trigger"></a>Přidání nebo aktualizace aktivační události časovače

Po vytvoření úkolu, volitelně přidat časovač aktivační událost pomocí [az acr task timer přidat][az-acr-task-timer-add] příkaz. Následující příklad přidá časovač aktivační události *časovač2* *mytask* vytvořil dříve. Tento časovač spustí úlohu každý den v 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aktualizujte plán existující aktivační události nebo změňte její stav pomocí příkazu [aktualizace časovače úloh az acr.][az-acr-task-timer-update] Například aktualizujte aktivační událost s názvem *timer2,* abyste aktivovali úlohu v čase 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Aktivační události časovače seznamu

Příkaz [az acr task timer zobrazuje][az-acr-task-timer-list] aktivační události časovače nastavené pro úlohu:

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

### <a name="remove-a-timer-trigger"></a>Odebrání aktivační události časovače

Pomocí příkazu [az acr task timer remove][az-acr-task-timer-remove] odeberte aktivační událost časovače z úlohy. Následující příklad odebere aktivační událost *timer2* z *mého úkolu*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron výrazy

Úlohy ACR používají knihovnu [NCronTab](https://github.com/atifaziz/NCrontab) k interpretaci výrazů cron. Podporované výrazy v acr úkoly mají pět požadovaných polí oddělených prázdné místo:

`{minute} {hour} {day} {month} {day-of-week}`

Časové pásmo používané s výrazy cron je koordinovaný světový čas (UTC). Hodiny jsou ve 24hodinovém formátu.

> [!NOTE]
> ACR Úkoly `{second}` nepodporuje `{year}` pole nebo ve výrazech cron. Pokud zkopírujete výraz cron používaný v jiném systému, nezapomeňte tato pole odebrat, pokud jsou použita.

Každé pole může mít jeden z následujících typů hodnot:

|Typ  |Příklad  |Při spuštění  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>`"5 * * * *"`</nobr>|každou hodinu po 5 minutách po hodině|
|Všechny hodnoty`*`( )|<nobr>`"* 5 * * *"`</nobr>|každou minutu hodiny začínající 5:00 UTC (60 x denně)|
|Rozsah (operátor)`-`|<nobr>`"0 1-3 * * *"`</nobr>|3x denně, v 1:00, 2:00 a 3:00 UTC|
|Sada hodnot (operátor)`,`|<nobr>`"20,30,40 * * * *"`</nobr>|3krát za hodinu, 20 minut, 30 minut a 40 minut po hodině|
|Hodnota intervalu`/` (operátor)|<nobr>`"*/10 * * * *"`</nobr>|6krát za hodinu, po 10 minutách, 20 minutách a tak dále, po hodině

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Příklady cronů

|Příklad|Při spuštění  |
|---------|---------|
|`"*/5 * * * *"`|jednou za pět minut|
|`"0 * * * *"`|jednou v horní části každé hodiny|
|`"0 */2 * * *"`|jednou za dvě hodiny|
|`"0 9-17 * * *"`|jednou za hodinu od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|v 9:30 UTC každý den|
|`"30 9 * * 1-5"`|v 9:30 UTC každý všední den|
|`"30 9 * Jan Mon"`|v 9:30 UTC každé pondělí v lednu|

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste vytvořili v této sérii kurzů, včetně registru kontejneru nebo registrů, instance kontejneru, trezoru klíčů a instančního objektu služby, vydejte následující příkazy:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit úlohy registru kontejnerů Azure, které se automaticky aktivují časovačem. 

Příklad použití naplánované úlohy k vyčištění úložišť v registru najdete v tématu [Automatické vymazání ibi z registru kontejnerů Azure](container-registry-auto-purge.md).

Příklady úloh spouštěných potvrzeními zdrojového kódu nebo aktualizacemi základní bitové kopie naleznete v dalších článcích [v sérii kurzů Úkoly ACR](container-registry-tutorial-quick-task.md).



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
