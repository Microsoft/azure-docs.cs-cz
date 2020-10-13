---
title: Přístup k vestavěným metrikám – Azure IoT Edge
description: Vzdálený přístup k integrovaným metrikám z komponent IoT Edge runtime
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: df904e183d3f77751d86d0cefab5423d753f146b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979579"
---
# <a name="access-built-in-metrics"></a>Přístup k vestavěným metrikám

Komponenty IoT Edge runtime, centra IoT Edge a Agent IoT Edge poskytují integrované metriky ve [formátu Prometheus Exposition](https://prometheus.io/docs/instrumenting/exposition_formats/). Přístup k těmto metrikám můžete vzdáleně sledovat a pochopit stav zařízení IoT Edge.

Od verze 1.0.10 se metriky automaticky zveřejňují na **portu 9600** modulů **edgeHub** a **edgeAgent** ( `http://edgeHub:9600/metrics` a `http://edgeAgent:9600/metics` ). Ve výchozím nastavení nejsou namapované na hostitele.

Přístup k metrikám z hostitele vystavení a mapování portu metrik z modulu `createOptions` . Následující příklad mapuje výchozí port metriky na port 9601 na hostiteli:

```
{
  "ExposedPorts": {
    "9600/tcp": {},
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Pokud provádíte mapování koncových bodů metriky edgeHub i edgeAgent, vyberte jiná a jedinečná čísla portů hostitele.

> [!NOTE]
> Pokud chcete zakázat metriky, nastavte `MetricsEnabled` proměnnou prostředí na `false` pro **edgeAgent**.

## <a name="available-metrics"></a>Dostupné metriky

Metriky obsahují značky, které vám pomůžou identifikovat povaze shromažďované metriky. Všechny metriky obsahují následující značky:

| Značka | Popis |
|-|-|
| iothub | Centrum, se kterým se zařízení mluví |
| edge_device | ID aktuálního zařízení |
| instance_number | Identifikátor GUID představující aktuální modul runtime. Při restartování budou všechny metriky obnoveny. Tento identifikátor GUID usnadňuje sladění restartování. |

Ve formátu Prometheus Exposition existují čtyři základní typy metriky: čítač, měřidlo, histogram a souhrn. Další informace o různých typech metrik naleznete v [dokumentaci k typům metrik Prometheus](https://prometheus.io/docs/concepts/metric_types/).

Quantiles poskytované pro integrované histogramy a souhrnné metriky jsou 0,1, 0,5, 0,9 a 0,99.

Modul **edgeHub** generuje následující metriky:

| Název | Dimenze | Popis |
|-|-|-|
| `edgehub_gettwin_total` | `source` (zdroj operace)<br> `id` (ID modulu) | Typ: čítač<br> Celkový počet volání prozdvojení |
| `edgehub_messages_received_total` | `route_output` (výstup odeslaných zpráv)<br> `id` | Typ: čítač<br> Celkový počet zpráv přijatých od klientů |
| `edgehub_messages_sent_total` | `from` (zdroj zprávy)<br> `to` (cíl zprávy)<br>`from_route_output`<br> `to_route_input` (vstup cílové zprávy)<br> `priority` (Priorita zprávy do cíle) | Typ: čítač<br> Celkový počet zpráv odeslaných klientům nebo proti nadřazenému<br> `to_route_input` je prázdné, pokud `to` je $upstream |
| `edgehub_reported_properties_total` | `target`(cíl aktualizace)<br> `id` | Typ: čítač<br> Celkový počet hlášených volání aktualizací vlastností |
| `edgehub_message_size_bytes` | `id`<br> | Typ: Souhrn<br> Velikost zprávy od klientů<br> Hodnoty mohou být hlášeny, jako by se v `NaN` určitém časovém období nehlásila žádná nová měření (aktuálně 10 minut); pro `summary` typ se bude vysílat odpovídající `_count` a `_sum` čítače. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Typ: Souhrn<br> Doba potřebná pro získání dvojitých operací |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Typ: Souhrn<br> Čas potřebný k odeslání zprávy |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Typ: Souhrn<br> Doba potřebná ke zpracování zprávy z fronty |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Typ: Souhrn<br> Doba trvání aktualizace hlášených vlastností |
| `edgehub_direct_method_duration_seconds` | `from` volající<br> `to` pozorování | Typ: Souhrn<br> Čas potřebný k vyřešení přímé zprávy |
| `edgehub_direct_methods_total` | `from`<br> `to` | Typ: čítač<br> Celkový počet odeslaných přímých zpráv |
| `edgehub_queue_length` | `endpoint` (zdroj zprávy)<br> `priority` (priorita fronty) | Typ: měřidlo<br> Aktuální délka fronty edgeHub pro danou prioritu |
| `edgehub_messages_dropped_total` | `reason` (no_route ttl_expiry)<br> `from` <br> `from_route_output` | Typ: čítač<br> Celkový počet odstraněných zpráv z důvodu |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Typ: čítač<br> Celkový počet nepotvrzených zpráv, protože chyba úložiště |
| `edgehub_offline_count_total` | `id` | Typ: čítač<br> Celkový počet, kolikrát edgeHub přešly do režimu offline |
| `edgehub_offline_duration_seconds`| `id` | Typ: Souhrn<br> Centrum časových okrajů bylo offline. |
| `edgehub_operation_retry_total` | `id`<br> `operation` (název operace) | Typ: čítač<br> Celkový počet opakovaných edgeHub operací |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (Neověřeno)<br> | Typ: čítač<br> Celkový počet pokusů, kolikrát se klienti nepodařilo připojit k edgeHub |

Modul **edgeAgent** generuje následující metriky:

| Název | Dimenze | Popis |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Typ: měřidlo<br> Doba, po kterou byl modul zadán v nasazení a byl ve spuštěném stavu |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Typ: měřidlo<br> Doba, po kterou byl modul zadán v nasazení |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Typ: čítač<br> Počet pokusů, kolikrát Docker edgeAgent vyzve k spuštění modulu |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Typ: čítač<br> Počet pokusů, kolikrát edgeAgent vyzve k zastavení modulu |
| `edgeAgent_command_latency_seconds` | `command` | Typ: měřidlo<br> Jak dlouho trvalo Docker ke spuštění daného příkazu. Možné příkazy: vytvořit, aktualizovat, odebrat, spustit, zastavit, restartovat |
| `edgeAgent_iothub_syncs_total` | | Typ: čítač<br> Počet úspěšných i neúspěšných pokusů o synchronizaci edgeAgent se iotHubem. Toto číslo zahrnuje jak agenta žádajícího o dvojitou a rozbočovači s upozorňováním na nestejnou aktualizaci. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Typ: čítač<br> Počet, kolikrát se edgeAgent nepovedlo synchronizovat jeho dvojitou hodnotu s iotHub. |
| `edgeAgent_deployment_time_seconds` | | Typ: čítač<br> Doba, po kterou trvalo dokončení nového nasazení po přijetí změny. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Typ: čítač<br> Počet, kolikrát je zavolána integrovaná edgeAgent přímá metoda, jako je například příkaz k odeslání nebo restartování. |
| `edgeAgent_host_uptime_seconds` | | Typ: měřidlo<br> Jak dlouho byl hostitel zapnutý |
| `edgeAgent_iotedged_uptime_seconds` | | Typ: měřidlo<br> Doba běhu iotedged |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Typ: měřidlo<br> Množství místa na disku, které zbývá |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Typ: měřidlo<br> Velikost disku |
| `edgeAgent_used_memory_bytes` | `module_name` | Typ: měřidlo<br> Množství paměti RAM používané všemi procesy |
| `edgeAgent_total_memory_bytes` | `module_name` | Typ: měřidlo<br> Paměť RAM k dispozici |
| `edgeAgent_used_cpu_percent` | `module_name` | Typ: histogram<br> Procento využití procesoru používaných všemi procesy |
| `edgeAgent_created_pids_total` | `module_name` | Typ: měřidlo<br> Počet procesů nebo vláken, které vytvořil kontejner |
| `edgeAgent_total_network_in_bytes` | `module_name` | Typ: měřidlo<br> Počet bajtů přijatých ze sítě |
| `edgeAgent_total_network_out_bytes` | `module_name` | Typ: měřidlo<br> Počet bajtů odeslaných na síť |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Typ: měřidlo<br> Počet bajtů přečtených z disku |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Typ: měřidlo<br> Počet bajtů zapsaných na disk |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Typ: měřidlo<br> Obecná metadata o zařízení. Hodnota je vždycky 0, informace se zakódují do značek. Všimněte si `experimental_features` , `host_information` že jsou objekty JSON. `host_information` Vypadá to jako ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Poznámka `ServerVersion` je verze Docker a `Version` je IoT Edge verze démona zabezpečení. |

## <a name="next-steps"></a>Další kroky

* [Pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md)
* [Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna](module-edgeagent-edgehub.md)
