---
title: (NEPOUŽÍVANÉ) Monitorování clusteru služby Azure DC/OS – ELK stack
description: Monitorování clusteru DC/OS v clusteru Azure Container Service pomocí ELK (Elasticsearch, Logstash a Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998147"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Container Service pomocí ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku jsme ukazují, jak nasazení stacku ELK (Elasticsearch, Logstash, Kibana) na clusteru DC/OS v Azure Container Service. 

## <a name="prerequisites"></a>Požadavky
[Nasazení](container-service-deployment.md) a [připojení](../container-service-connect.md) cluster DC/OS nakonfigurovaný v Azure Container Service. Prozkoumejte řídicí panel DC/OS a Marathonu služby [tady](container-service-mesos-marathon-ui.md). Nainstalovat také [Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK stack je kombinací Elasticsearch, Logstash a Kibana, která poskytuje komplexní zásobníku, který slouží ke sledování a analýza protokolů ve vašem clusteru.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurace ELK stack na clusteru DC/OS
Přístup k vaší uživatelské rozhraní DC/OS prostřednictvím [ http://localhost:80/ ](http://localhost:80/) jednou v Uživatelském rozhraní DC/OS přejděte do **Universe**. Hledání a nainstalujte Elasticsearch, Logstash a Kibana v rozhraní DC/OS Universe a v tomto konkrétním pořadí. Další informace o konfiguraci Pokud přejdete do **rozšířené instalace** odkaz.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Jakmile ELK kontejnery a jsou rychle zprovoznit, je potřeba povolit Kibana je možný přes Marathon-LB. Přejděte do **služby** > **kibana**a klikněte na tlačítko **upravit** jak je znázorněno níže.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Přepnutím **režim JSON** a přejděte dolů do části štítky.
Je třeba přidat `"HAPROXY_GROUP": "external"` položka zde jak je znázorněno níže.
Po kliknutí na **změny nasadí**, restartování kontejneru.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Pokud chcete ověřit, že Kibana je zaregistrováno jako služba na řídicím panelu HAPROXY, budete muset otevřít port 9090 v clusteru agenta HAPROXY běží na port 9090.
Ve výchozím nastavení, se nám otevřít porty 80, 8080 a 443 v clusteru DC/OS agenta.
Pokyny k otevření portu a zadejte veřejné vyhodnocení [tady](container-service-enable-public-access.md).

Chcete-li přístup k řídicímu panelu HAPROXY, otevřete Správce rozhraní Marathon-LB na: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Až přejdete na adresu URL, řídicí panel HAPROXY byste měli vidět, jak je znázorněno níže a měli byste vidět položku služby pro Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Chcete-li přístup k řídicímu panelu Kibana, který je nasazen na portu 5601, otevřením portu 5601. Postupujte podle pokynů [tady](container-service-enable-public-access.md). Otevřete řídicí panel Kibana na: `http://localhost:5601`.

## <a name="next-steps"></a>Další postup

* Pro systém a aplikace protokolu předávání a nastavení, najdete v části [Správa protokolů v DC/OS pomocí ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Chcete-li filtrovat protokoly, naleznete v tématu [filtrování protokolů pomocí ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

