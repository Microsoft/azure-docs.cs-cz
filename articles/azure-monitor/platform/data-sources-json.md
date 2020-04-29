---
title: Shromažďují se vlastní data JSON v Azure Monitor | Microsoft Docs
description: Vlastní zdroje dat JSON můžete shromažďovat do Azure Monitor pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat můžou být jednoduché skripty vracející JSON, jako je například kudrlinkou, nebo jeden z nich má více než 300 modulů plug-in. Tento článek popisuje konfiguraci nutnou pro tuto kolekci dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77662157"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Shromažďování vlastních zdrojů dat JSON pomocí agenta Log Analytics pro Linux v Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Vlastní zdroje dat JSON můžete shromažďovat do [Azure monitor](data-platform.md) pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat můžou být jednoduché skripty vracející JSON, jako je například [kudrlinkou](https://curl.haxx.se/) , nebo jeden z nich má více než [300 modulů plug-in](https://www.fluentd.org/plugins/all). Tento článek popisuje konfiguraci nutnou pro tuto kolekci dat.


> [!NOTE]
> Pro vlastní data JSON se vyžaduje agent Log Analytics pro Linux v 1.1.0-217 +.

## <a name="configuration"></a>Konfigurace

### <a name="configure-input-plugin"></a>Nakonfigurovat vstupní modul plug-in

Pokud chcete shromažďovat data JSON v Azure Monitor, `oms.api.` přidejte na vstupní modul plug-in na začátek Fluent značky.

Například následující je samostatný konfigurační soubor `exec-json.conf` v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`nástroji.  K tomu se používá modul plug `exec` -in, který spouští příkaz ve složeném intervalu 30 sekund.  Výstup z tohoto příkazu je shromážděn modulem plug-in JSON Output.

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
Konfigurační soubor přidaný v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` části bude vyžadovat, aby se jeho vlastnictví změnilo pomocí následujícího příkazu.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurovat výstupní modul plug-in 
Přidejte následující konfiguraci výstupního modulu plug-in do hlavní `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` konfigurace v nebo jako samostatný konfigurační soubor umístěný v`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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
Restartujte agenta Log Analytics pro Linux Service pomocí následujícího příkazu.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Výstup
Data budou shromažďována v Azure Monitor s typem záznamu `<FLUENTD_TAG>_CL`.

Například vlastní značka `tag oms.api.tomcat` v Azure monitor s typem záznamu `tomcat_CL`.  Můžete načíst všechny záznamy tohoto typu s následujícím dotazem protokolu.

    Type=tomcat_CL

Vnořené zdroje dat JSON jsou podporovány, ale jsou indexované na základě nadřazeného pole. Například následující data JSON se vrátí z dotazu protokolu jako `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 
