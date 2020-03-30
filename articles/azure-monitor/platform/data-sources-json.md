---
title: Shromažďování vlastních dat JSON ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Vlastní zdroje dat JSON lze shromažďovat do Azure Monitor pomocí Agent analýzy protokolů pro Linux.  Tyto vlastní zdroje dat mohou být jednoduché skripty vracející JSON, jako je curl nebo jeden z 300 + pluginů FluentD. Tento článek popisuje konfiguraci požadovanou pro shromažďování dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662157"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Shromažďování vlastních zdrojů dat JSON pomocí agenta Log Analytics pro Linux v Azure Monitoru
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Vlastní zdroje dat JSON lze shromažďovat do [Azure Monitorpomocí](data-platform.md) agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat mohou být jednoduché skripty vracející JSON, jako je [curl](https://curl.haxx.se/) nebo jeden z [300 + pluginů FluentD](https://www.fluentd.org/plugins/all). Tento článek popisuje konfiguraci požadovanou pro shromažďování dat.


> [!NOTE]
> Agent Log Analytics pro Linux v1.1.0-217+ je vyžadován pro vlastní data JSON

## <a name="configuration"></a>Konfigurace

### <a name="configure-input-plugin"></a>Konfigurace vstupního pluginu

Pokud chcete shromažďovat data JSON `oms.api.` ve službě Azure Monitor, přidejte na začátek značky FluentD ve vstupním pluginu.

Následuje například samostatný konfigurační soubor `exec-json.conf` v . `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`  To používá modul `exec` plug-in FluentD ke spuštění příkazu curl každých 30 sekund.  Výstup z tohoto příkazu je shromažďován pluginem pro výstup JSON.

```
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
Konfigurační `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` soubor přidaný v rámci bude vyžadovat změnu jeho vlastnictví pomocí následujícího příkazu.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurace výstupního pluginu 
Přidejte následující konfiguraci výstupních `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` modulů plug-in do hlavní konfigurace v nebo jako samostatný konfigurační soubor umístěný`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
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
S následujícím příkazem restartujte agenta log Analytics pro Linux.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Výstup
Data se budou shromažďovat v Azure Monitoru s typem záznamu `<FLUENTD_TAG>_CL`.

Například vlastní značka `tag oms.api.tomcat` ve službě Azure Monitor `tomcat_CL`s typem záznamu .  Všechny záznamy tohoto typu můžete načíst pomocí následujícího dotazu protokolu.

    Type=tomcat_CL

Vnořené zdroje dat JSON jsou podporovány, ale jsou indexovány na základě nadřazeného pole. Například následující data JSON je vrácena `tag_s : "[{ "a":"1", "b":"2" }]`z dotazu protokolu jako .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení. 
