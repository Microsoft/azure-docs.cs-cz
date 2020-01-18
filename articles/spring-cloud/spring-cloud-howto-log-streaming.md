---
title: Streamování protokolů aplikace v cloudu Azure na jaře v reálném čase
description: Jak používat streamování protokolů k okamžitému zobrazení protokolů aplikací
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263995"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Streamování protokolů aplikace v cloudu Azure na jaře v reálném čase
Azure jaře Cloud umožňuje streamování protokolů v Azure CLI a získat tak řešení potíží v protokolech konzoly aplikací v reálném čase. Můžete také [analyzovat protokoly a metriky pomocí nastavení diagnostiky](./diagnostic-services.md).

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [rozšíření Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) pro jarní Cloud a minimální verzi 0.2.0.
* Instance **jarního cloudu Azure** se spuštěnou aplikací, například [jarní cloudová aplikace](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Použít CLI pro protokoly Tail

Abyste se vyhnuli opakovanému zadání vaší skupiny prostředků a názvu instance služby, nastavte výchozí název skupiny prostředků a název clusteru.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
V následujících příkladech bude v příkazech vynechána skupina prostředků a název služby.

### <a name="tail-log-for-app-with-single-instance"></a>Protokol Tail pro aplikaci s jednou instancí
Pokud má aplikace s názvem auth-Service pouze jednu instanci, můžete zobrazit protokol instance aplikace pomocí následujícího příkazu:
```
az spring-cloud app log tail -n auth-service
```
Tato akce vrátí protokoly:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Protokol Tail pro aplikaci s více instancemi
Pokud pro aplikaci s názvem `auth-service`existuje více instancí, můžete protokol instance zobrazit pomocí možnosti `-i/--instance`. 

Nejprve můžete získat názvy instancí aplikace pomocí následujícího příkazu.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
S výsledky:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Pak můžete zasílat protokoly instance aplikace s možností `-i/--instance` možnosti:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Můžete také získat podrobnosti o instancích aplikace z Azure Portal.  Po výběru možnosti **aplikace** v levém navigačním podokně vaší jarní cloudové služby Azure vyberte **instance aplikací**.

### <a name="continuously-stream-new-logs"></a>Průběžné streamování nových protokolů
Ve výchozím nastavení `az spring-cloud ap log tail` vytiskne pouze existující protokoly streamované do konzoly aplikace a pak se ukončí. Pokud chcete streamovat nové protokoly, přidejte-f (--Sledujte):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Pro kontrolu všech podporovaných možností protokolování:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Další kroky

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](./diagnostic-services.md)

 





