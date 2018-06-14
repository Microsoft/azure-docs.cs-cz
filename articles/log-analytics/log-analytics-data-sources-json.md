---
title: Shromažďování vlastní data JSON v OMS Log Analytics | Microsoft Docs
description: Vlastní zdroje dat JSON je možné sbírat do analýzy protokolů pro Linux pomocí agenta OMS.  Tyto zdroje dat vlastní může být jednoduché skripty vrácení JSON například curl nebo jeden z FluentD na 300 + modulů plug-in. Tento článek popisuje konfigurace požadované pro tuto kolekci data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855219"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Shromažďování vlastní zdroje dat JSON s agentem OMS pro Linux v analýzy protokolů
Vlastní zdroje dat JSON je možné sbírat do analýzy protokolů pro Linux pomocí agenta OMS.  Tyto zdroje dat vlastní může být jednoduché skripty, jako vrácení JSON [curl](https://curl.haxx.se/) nebo jeden z [FluentD na 300 + modulů plug-in](http://www.fluentd.org/plugins/all). Tento článek popisuje konfigurace požadované pro tuto kolekci data.

> [!NOTE]
> OMS agenta pro Linux v1.1.0-217 + je vyžadována pro vlastní Data JSON

## <a name="configuration"></a>Konfigurace

### <a name="configure-input-plugin"></a>Konfigurace vstupní modulu plug-in

Chcete-li shromažďovat data JSON v analýzy protokolů, přidejte `oms.api.` na začátek FluentD tag vstupní modulu plug-in.

Například následující je jiný konfigurační soubor `exec-json.conf` v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Používá modul plug-in FluentD `exec` spustit příkaz curl každých 30 sekund.  Výstup tohoto příkazu se shromažďují modulem plug-in výstup JSON.

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
Konfigurační soubor přidají pod `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` bude nutné mít jeho vlastnictví změnit pomocí následujícího příkazu.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurace modulu plug-in výstup 
Přidejte následující konfigurace modulu plug-in výstup do hlavní konfigurace v nástroji `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` nebo jako jiný konfigurační soubor umístěn v`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-oms-agent-for-linux"></a>Restartujte agenta OMS pro Linux
Restartujte agenta OMS služby Linux pomocí následujícího příkazu.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Výstup
Data budou shromážděny v Log Analytics s typem záznamu `<FLUENTD_TAG>_CL`.

Například vlastní značky `tag oms.api.tomcat` v Log Analytics s typem záznamu `tomcat_CL`.  Může načíst všechny záznamy tohoto typu s následující hledání protokolů.

    Type=tomcat_CL

Vnořené data JSON zdroje jsou podporované, ale jsou uloženy na základě nadřazené pole. Například následující data JSON je vrácen z vyhledávání analýzy protokolů jako `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Další kroky
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 
 