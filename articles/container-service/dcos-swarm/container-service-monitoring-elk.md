---
title: (ZASTARALÉ) Monitorování clusteru Azure DC/OS – zásobník ELK
description: Monitorujte cluster stejnosměrného zařízení/operačního systému v clusteru služby Azure Container Service pomocí elk (Elasticsearch, Logstash a Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277762"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(ZASTARALÉ) Monitorování clusteru služby Azure Container Service pomocí sady ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku ukážeme, jak nasadit zásobník ELK (Elasticsearch, Logstash, Kibana) v clusteru DC/OS ve službě Azure Container Service. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [připojte](../container-service-connect.md) cluster řadičů domény/operačních systémů nakonfigurovaný službou Azure Container Service. Seznamte se s řídicím panelem DC/OS a službami Marathon [zde](container-service-mesos-marathon-ui.md). Nainstalujte také [systém Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ZÁSOBNÍK ELK je kombinací Elasticsearch, Logstash a Kibana, která poskytuje komplexní zásobník, který lze použít ke sledování a analýze protokolů v clusteru.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurace zásobníku ELK v clusteru DC/OS
Přístup k vašemu DC [http://localhost:80/](http://localhost:80/) /OS UI přes Jakmile v DC / OS UI přejít do **vesmíru**. Hledat a instalovat Elasticsearch, Logstash, a Kibana z DC / OS Universe a v tomto konkrétním pořadí. Další informace o konfiguraci naleznete v odkazu **Rozšířená instalace.**

![Elk1](./media/container-service-monitoring-elk/elk1.PNG) ![Elk2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Jakmile jsou kontejnery ELK a jsou v provozu, musíte povolit přístup k kibana prostřednictvím Marathon-LB. Přejděte na **položku Services** > **kibana**a klikněte na **Upravit,** jak je znázorněno níže.

![Elk4](./media/container-service-monitoring-elk/elk4.PNG)


Přepněte do **režimu JSON** a posuňte se dolů do oddílu popisky.
Zde musíte přidat `"HAPROXY_GROUP": "external"` položku, jak je uvedeno níže.
Po klepnutí na tlačítko **Nasadit změny**se kontejner restartuje.

![5K5](./media/container-service-monitoring-elk/elk5.PNG)


Pokud chcete ověřit, že Kibana je registrována jako služba v řídicím panelu HAPROXY, musíte otevřít port 9090 v clusteru agentů, protože HAPROXY běží na portu 9090.
Ve výchozím nastavení otevíráme porty 80, 8080 a 443 v clusteru agentů DC/OS.
Pokyny k otevření přístavu a poskytnout veřejné posouzení jsou uvedeny [zde](container-service-enable-public-access.md).

Chcete-li získat přístup k řídicímu panelu `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`HAPROXY, otevřete rozhraní správy Marathon-LB na adrese: .
Jakmile přejdete na adresu URL, měli byste vidět řídicí panel HAPROXY, jak je uvedeno níže, a měli byste vidět položku služby pro Kibana.

![Elk6](./media/container-service-monitoring-elk/elk6.PNG)


Chcete-li získat přístup k řídicímu panelu Kibana, který je nasazen na portu 5601, musíte otevřít port 5601. Postupujte podle pokynů [zde](container-service-enable-public-access.md). Poté otevřete řídicí panel Kibana na adrese: `http://localhost:5601`.

## <a name="next-steps"></a>Další kroky

* Pro předávání a nastavení protokolu systému a aplikací viz [Správa protokolů v DC/OS s ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Informace o filtrování protokolů naleznete [v tématu Filtrování protokolů pomocí sady ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

