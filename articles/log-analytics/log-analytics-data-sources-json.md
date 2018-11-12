---
title: Shromažďovat vlastní data JSON v Log Analytics | Dokumentace Microsoftu
description: Vlastní zdroje dat JSON dají shromažďovat do Log Analytics pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat mohou být jednoduché skripty, které vrací JSON jako curl nebo jeden z jeho FluentD více než 300 moduly plug-in. Tento článek popisuje konfigurace požadované pro tuto kolekci data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9e54a586ac20835078ae3a3734122b0db34ea69e
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006749"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-log-analytics"></a>Shromažďovat vlastní zdroje dat JSON pomocí agenta Log Analytics pro Linux ve službě Log Analytics
[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]
Vlastní zdroje dat JSON dají shromažďovat do Log Analytics pomocí agenta Log Analytics pro Linux.  Tyto vlastní zdroje dat mohou být jednoduché skripty, které vrací JSON, jako [curl](https://curl.haxx.se/) nebo jeden z [FluentD na více než 300 moduly plug-in](http://www.fluentd.org/plugins/all). Tento článek popisuje konfigurace požadované pro tuto kolekci data.

> [!NOTE]
> Agenta log Analytics pro Linux v1.1.0-217 + je třeba použít vlastní Data JSON

## <a name="configuration"></a>Konfigurace

### <a name="configure-input-plugin"></a>Konfigurace vstupu modulu plug-in

Chcete-li shromažďovat data JSON ve službě Log Analytics, přidejte `oms.api.` začátek FluentD značku ve vstupu modulu plug-in.

Například tady je samostatného konfiguračního souboru `exec-json.conf` v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Tady se používá modul plug-in FluentD `exec` ke spuštění příkazu curl každých 30 sekund.  Výstup tohoto příkazu se shromažďují pomocí modulu plug-in výstup JSON.

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
Konfigurační soubor přidány pod `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` bude vyžadovat, aby jeho vlastnictví změnit pomocí následujícího příkazu.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurace výstupu modulu plug-in 
Přidejte následující konfiguraci modulu plug-in výstup do hlavní konfigurace v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` nebo samostatného konfiguračního souboru uváděné v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-log-analytics-agent-for-linux"></a>Restartujte agenta Log Analytics pro Linux
Restartujte agenta Log Analytics pro služby service pro Linux pomocí následujícího příkazu.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Výstup
Data se shromažďují v Log Analytics s typem záznamu `<FLUENTD_TAG>_CL`.

Například vlastní značku `tag oms.api.tomcat` v Log Analytics s typem záznamu `tomcat_CL`.  Může načíst všechny záznamy z tohoto typu s následující prohledávání protokolů.

    Type=tomcat_CL

Vnořené data JSON zdroje jsou podporované, ale jsou indexovány založen na nadřazené pole. Například následující data JSON je vrácen z hledání Log Analytics jako `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Další postup
* Další informace o [prohledávání protokolů](log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 
 
