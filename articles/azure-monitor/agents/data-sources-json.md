---
title: Shromažďování vlastních zdrojů dat JSON pomocí agenta Log Analytics pro Linux v Azure Monitor
description: Vlastní zdroje dat JSON můžete shromažďovat do Azure Monitor pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat můžou být jednoduché skripty vracející JSON, jako je například kudrlinkou, nebo jeden z nich má více než 300 modulů plug-in. Tento článek popisuje konfiguraci nutnou pro tuto kolekci dat.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: fe80a5c117e8c94e5df946813a1c025747ff40e8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050694"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Shromažďování vlastních zdrojů dat JSON pomocí agenta Log Analytics pro Linux v Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Vlastní zdroje dat JSON můžete shromažďovat do [Azure monitor](../data-platform.md) pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat můžou být jednoduché skripty vracející JSON, jako je například [kudrlinkou](https://curl.haxx.se/) , nebo jeden z nich má více než [300 modulů plug-in](https://www.fluentd.org/plugins/all). Tento článek popisuje konfiguraci nutnou pro tuto kolekci dat.


> [!NOTE]
> Pro vlastní data JSON se vyžaduje agent Log Analytics pro Linux v 1.1.0-217 +.

## <a name="configuration"></a>Konfigurace

### <a name="configure-input-plugin"></a>Nakonfigurovat vstupní modul plug-in

Pokud chcete shromažďovat data JSON v Azure Monitor, přidejte `oms.api.` na vstupní modul plug-in na začátek Fluent značky.

Například následující je samostatný konfigurační soubor `exec-json.conf` v nástroji `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  K tomu se používá modul plug-in, `exec` který spouští příkaz ve složeném intervalu 30 sekund.  Výstup z tohoto příkazu je shromážděn modulem plug-in JSON Output.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

Konfigurační soubor přidaný v části `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` bude vyžadovat, aby se jeho vlastnictví změnilo pomocí následujícího příkazu.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurovat výstupní modul plug-in 
Přidejte následující konfiguraci výstupního modulu plug-in do hlavní konfigurace v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` nebo jako samostatný konfigurační soubor umístěný v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Restartovat agenta Log Analytics pro Linux
Restartujte agenta Log Analytics pro Linux Service pomocí následujícího příkazu.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Výstup
Data budou shromažďována v Azure Monitor s typem záznamu `<FLUENTD_TAG>_CL` .

Například vlastní značka `tag oms.api.tomcat` v Azure monitor s typem záznamu `tomcat_CL` .  Můžete načíst všechny záznamy tohoto typu s následujícím dotazem protokolu.

```console
Type=tomcat_CL
```

Vnořené zdroje dat JSON jsou podporovány, ale jsou indexované na základě nadřazeného pole. Například následující data JSON se vrátí z dotazu protokolu jako `tag_s : "[{ "a":"1", "b":"2" }]` .

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../logs/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.