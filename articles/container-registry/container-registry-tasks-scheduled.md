---
title: Kurz – naplánování úlohy ACR
description: V tomto kurzu se dozvíte, jak spustit úlohu Azure Container Registry podle definovaného plánu nastavením jedné nebo více triggerů časovače.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030607"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Kurz: spuštění úlohy ACR podle definovaného plánu

V tomto kurzu se dozvíte, jak spustit [úlohu ACR](container-registry-tasks-overview.md) podle plánu. Naplánování úlohy nastavením jedné nebo více *aktivačních událostí časovače*. Triggery časovače lze použít samostatně nebo v kombinaci s jinými triggery úloh.

V tomto kurzu se naučíte plánovat úlohy a:

> [!div class="checklist"]
> * Vytvoření úlohy pomocí triggeru časovače
> * Správa aktivačních událostí časovače

Plánování úlohy je užitečné pro následující scénáře:

* Spusťte úlohu kontejneru pro plánované operace údržby. Pokud například chcete odebrat nepotřebné image z registru, spusťte aplikaci s kontejnerem.
* Spusťte sadu testů v provozní imagi během pracovního dne jako součást živého monitorování webu.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Plánování úlohy

* **Aktivační událost s výrazem cron** – aktivační událost časovače pro úlohu používá *výraz cron*. Výraz je řetězec s pěti poli, které určují minuty, hodinu, den, měsíc a den v týdnu, kdy se má úkol aktivovat. Frekvence se podporuje až jednou za minutu.

  Výraz například `"0 12 * * Mon-Fri"` aktivuje úlohu v poledne standardu UTC každého dne v týdnu. Další [informace najdete v části dále](#cron-expressions) v tomto článku.
* **Více triggerů časovače** – přidávání více časovačů k úkolu je povoleno, pokud se plány liší.
    * Při vytváření úlohy zadejte více triggerů časovače nebo je přidejte později.
    * Volitelně můžete pojmenovat triggery pro snadnější správu, jinak budou ACR úlohy poskytovat výchozí názvy aktivačních událostí.
    * Pokud se plány časovače překrývají v čase, úlohy ACR aktivují úlohu v naplánovaném čase každého časovače.
* **Další triggery úloh** – v úloze aktivované časovačem můžete také povolit triggery na základě [potvrzení zdrojového kódu](container-registry-tutorial-build-task.md) nebo [aktualizací Base image](container-registry-tutorial-base-image-update.md). Stejně jako jiné úlohy ACR můžete také [ručně spustit][az-acr-task-run] naplánovanou úlohu.

## <a name="create-a-task-with-a-timer-trigger"></a>Vytvoření úlohy pomocí triggeru časovače

### <a name="task-command"></a>Příkaz úkolu

Nejdřív naplňte následující proměnnou prostředí prostředí o hodnotu vhodnou pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud nezaplníte proměnnou prostředí, je nutné ručně nahradit každou hodnotu, pokud se zobrazí v ukázkových příkazech.

[![Vložené spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Při vytváření úlohy pomocí příkazu [AZ ACR Task Create][az-acr-task-create] můžete volitelně přidat Trigger časovače. Přidejte `--schedule` parametr a předejte výraz cron pro časovač.

Jednoduchým příkladem je, že následující úloha spouští `hello-world` bitovou kopii od společnosti Microsoft Container Registry každý den v 21:00 UTC. Úloha se spustí bez kontextu zdrojového kódu.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Spuštěním příkazu [AZ ACR Task show][az-acr-task-show] zobrazíte, že je nakonfigurovaná aktivační událost časovače. Ve výchozím nastavení je také povolená aktivační událost základní aktualizace bitové kopie.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Aktivovat úlohu

Ruční aktivace úlohy pomocí [AZ ACR Task Run][az-acr-task-run] , aby bylo zajištěno, že je správně nastavená:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Pokud se kontejner úspěšně spustí, bude výstup podobný následujícímu. Výstup je zúžený, aby se zobrazily klíčové kroky.

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Po naplánovaném čase spusťte příkaz [AZ ACR Task list-][az-acr-task-list-runs] runs a ověřte, že časovač aktivoval úlohu podle očekávání:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Po úspěšném časovači je výstup podobný následujícímu:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Správa aktivačních událostí časovače

Pomocí příkazů [AZ ACR Task Timer][az-acr-task-timer] můžete spravovat triggery časovače pro úlohu ACR.

### <a name="add-or-update-a-timer-trigger"></a>Přidat nebo aktualizovat aktivační událost časovače

Po vytvoření úkolu můžete pomocí příkazu [AZ ACR Task Timer Add][az-acr-task-timer-add] přidat Trigger časovače. Následující příklad přidá název aktivační události časovače *timer2* do *timertask* vytvořené dříve. Tento časovač aktivuje úlohu každý den v 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aktualizujte plán existujícího triggeru nebo změňte jeho stav pomocí příkazu [AZ ACR Task Timer Update][az-acr-task-timer-update] . Například aktualizujte Trigger s názvem *timer2* , který aktivuje úkol v 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Vypsat aktivační události časovače

Příkaz [AZ ACR Task Timer list][az-acr-task-timer-list] zobrazuje aktivační události časovače nastavené pro úlohu:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
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

### <a name="remove-a-timer-trigger"></a>Odebrání triggeru časovače

Pomocí příkazu [AZ ACR Task Timer Remove][az-acr-task-timer-remove] odeberte aktivační událost časovače z úlohy. Následující příklad odebere aktivační událost *timer2* z *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Výrazy cron

ACR úlohy používají knihovnu [NCronTab](https://github.com/atifaziz/NCrontab) k interpretaci cron výrazů. Podporované výrazy v úlohách ACR mají pět povinných polí oddělených prázdným znakem:

`{minute} {hour} {day} {month} {day-of-week}`

Časové pásmo používané s výrazy cron je koordinovaný světový čas (UTC). Hodiny jsou ve 24hodinovém formátu.

> [!NOTE]
> ACR úkoly nepodporují `{second}` `{year}` pole ani ve výrazech cron. Pokud zkopírujete výraz cron používaný v jiném systému, nezapomeňte tato pole odebrat, pokud se používají.

Každé pole může mít jeden z následujících typů hodnot:

|Typ  |Příklad  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>`"5 * * * *"`</nobr>|každou hodinu 5 minut po hodině|
|Všechny hodnoty ( `*` )|<nobr>`"* 5 * * *"`</nobr>|každou minutu hodiny začínající 5:00 UTC (60 krát den)|
|Rozsah ( `-` operátor)|<nobr>`"0 1-3 * * *"`</nobr>|3 časy za den, v 1:00, 2:00 a 3:00 UTC|
|Sada hodnot ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 časy za hodinu, 20 minut, 30 minut a 40 minut po hodině|
|Hodnota intervalu ( `/` operátor)|<nobr>`"*/10 * * * *"`</nobr>|6 časů za hodinu, 10 minut, 20 minut atd., po celou hodinu

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Příklady cron

|Příklad|Při aktivaci  |
|---------|---------|
|`"*/5 * * * *"`|každých pět minut|
|`"0 * * * *"`|jednou na začátku každé hodiny|
|`"0 */2 * * *"`|každé dvě hodiny|
|`"0 9-17 * * *"`|jedenkrát za každou hodinu od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|v 9:30 UTC každý den|
|`"30 9 * * 1-5"`|v 9:30 UTC každý den v týdnu|
|`"30 9 * Jan Mon"`|v 9:30 UTC každé pondělí v lednu|

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili v této sérii kurzů, včetně registru kontejnerů, registrů, instance kontejneru, trezoru klíčů a instančního objektu, vydejte tyto příkazy:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit Azure Container Registry úlohy, které se automaticky aktivují pomocí časovače. 

Příklad použití naplánované úlohy k vyčištění úložišť v registru najdete v tématu [Automatické vymazání imagí z služby Azure Container Registry](container-registry-auto-purge.md).

Příklady úloh aktivovaných potvrzeními zdrojového kódu nebo základními aktualizacemi imagí najdete v dalších článcích v [řadě kurzů ACR Tasks](container-registry-tutorial-quick-task.md).



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
