---
title: ZASTARALÉ Monitorování clusteru Azure DC/OS – ELK Stack
description: Monitorujte cluster DC/OS v Azure Container Service clusteru pomocí ELK (Elasticsearch, Logstash a Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 586b8d25a9f391487640e9b1f8adb3be0e4be6db
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166168"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>ZASTARALÉ Monitorování clusteru Azure Container Service pomocí ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku ukážeme, jak nasadit zásobník ELK (Elasticsearch, Logstash, Kibana) na cluster DC/OS v Azure Container Service. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [Připojte](../container-service-connect.md) cluster DC/OS nakonfigurovaný Azure Container Service. [Tady](container-service-mesos-marathon-ui.md)si Prozkoumejte řídicí panel DC/OS a služby Marathon Services. Nainstalujte také [Load Balancer Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK Stack je kombinací Elasticsearch, Logstash a Kibana, která poskytuje kompletní zásobník, který se dá použít k monitorování a analýze protokolů v clusteru.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurace sady ELK Stack v clusteru DC/OS
Přístup k uživatelskému rozhraní DC/ `http://localhost:80/` OS pomocí nástroje jednou v UŽIVATELSKÉM rozhraní DC/OS přejděte do části **Universe**. Vyhledejte a nainstalujte Elasticsearch, Logstash a Kibana z universu DC/OS a v tomto konkrétním pořadí. Pokud přejdete na odkaz **rozšířené instalace** , můžete získat další informace o konfiguraci.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Po ELK kontejnerech a jejich zprovoznění je nutné povolit Kibana k zpřístupnění prostřednictvím Marathon-9,1. Přejděte na **služby** > **kibana**a klikněte na **Upravit** , jak je znázorněno níže.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Přepněte do **režimu JSON** a posuňte se dolů k části labels.
Sem je třeba přidat `"HAPROXY_GROUP": "external"` položku, jak je uvedeno níže.
Jakmile kliknete na **nasadit změny**, váš kontejner se restartuje.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Pokud chcete ověřit, že Kibana je zaregistrován jako služba na řídicím panelu HAPROXY, je třeba v clusteru agenta otevřít port 9090, který bude HAPROXY běžet na portu 9090.
Ve výchozím nastavení se v clusteru agenta DC/OS otevírají porty 80, 8080 a 443.
Pokyny k otevření portu a poskytnutí veřejného vyhodnocení [najdete tady](container-service-enable-public-access.md).

Pro přístup k řídicímu panelu HAPROXY otevřete rozhraní pro správu Marathon-9,1 na `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`adrese:.
Po přechodu na adresu URL byste měli vidět řídicí panel HAPROXY, jak je znázorněno níže, a měla by se zobrazit položka služby pro Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Chcete-li získat přístup k řídicímu panelu Kibana, který je nasazen na portu 5601, je nutné otevřít port 5601. Postupujte podle [pokynů.](container-service-enable-public-access.md) Pak otevřete řídicí panel Kibana na adrese `http://localhost:5601`:.

## <a name="next-steps"></a>Další kroky

* Informace o předávání a nastavení protokolů systému a aplikací najdete v tématu [Správa protokolů v DC/OS pomocí Elk](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Pokud chcete filtrovat protokoly, přečtěte si téma [filtrování protokolů pomocí Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

