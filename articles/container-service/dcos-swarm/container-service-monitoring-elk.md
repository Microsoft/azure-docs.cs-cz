---
title: Monitorování Azure DC/OS cluster - ELK zásobníku
description: Monitorování cluster DC/OS v clusteru Azure Container Service s ELK (Elasticsearch, Logstash a Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: dc863894d8846e066c90bdf7b309f141d32a1186
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163176"
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Monitorování clusteru Azure Container Service s ELK

V tomto článku jsme ukazují, jak nasadit zásobníku ELK (Elasticsearch, Logstash, Kibana) v clusteru DC/OS v Azure Container Service. 

## <a name="prerequisites"></a>Požadavky
[Nasazení](container-service-deployment.md) a [připojit](../container-service-connect.md) cluster DC/OS nakonfigurovaný v Azure Container Service. Prozkoumejte řídicí panel DC/OS a Marathonu služby [zde](container-service-mesos-marathon-ui.md). Taky nainstalovat [nástroj pro vyrovnávání zatížení Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Zásobník ELK je kombinací Elasticsearch, Logstash a Kibana, která poskytuje koncová zásobníku, který slouží ke sledování a analýza protokolů v clusteru.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurace zásobníku ELK na cluster DC/OS
Přístup přes uživatelské rozhraní DC/OS [ http://localhost:80/ ](http://localhost:80/) jednou v uživatelském rozhraní DC/OS přejděte na **Universe**. Vyhledávat a instalovat Elasticsearch, Logstash a Kibana Universe DC/OS a v tomto konkrétní pořadí. Další informace o konfiguraci Pokud přejdete do **rozšířený instalace** odkaz.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Jakmile ELK kontejnery a jsou fungovaly, budete muset povolit Kibana přístup přes Marathon-LB. Přejděte na **služby** > **kibana**a klikněte na tlačítko **upravit** jak je uvedeno níže.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Přepnutím **režim JSON** a přejděte dolů v části štítky.
Je nutné přidat `"HAPROXY_GROUP": "external"` položka zde jak je uvedené níže.
Po kliknutí na tlačítko **nasadit změny**, restartování kontejneru.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Pokud chcete ověřit, že Kibana je zaregistrován jako služba v řídicím panelu HAPROXY, budete muset otevřít port 9090 v clusteru agenta HAPROXY běží na port 9090.
Ve výchozím nastavení, jsme otevřít porty 80, 8080 a 443 v clusteru DC/OS agenta.
Pokyny k otevření portu a zadejte veřejné vyhodnocení [zde](container-service-enable-public-access.md).

Přístup k řídicímu panelu HAPROXY, otevřete rozhraní Marathon-LB správce v: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Když přejdete na adresu URL, byste měli vidět řídicí panel HAPROXY, jak je uvedeno níže a měli byste vidět položku služby pro Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Pro přístup k řídicímu panelu Kibana, který je nasazen na portu 5601, budete muset otevřít port 5601. Postupujte podle pokynů [zde](container-service-enable-public-access.md). Otevřete řídicí panel Kibana v: `http://localhost:5601`.

## <a name="next-steps"></a>Další postup

* Pro systémové a aplikační protokol předávání a nastavení, najdete v části [Správa protokolu v DC/OS s ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Filtrování protokolů, najdete v článku [filtrování protokoly s ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

