---
title: Streamování protokolů aplikace Azure Spring Cloudu v reálném čase
description: Jak používat streamování protokolu k okamžitému zobrazení protokolů aplikací
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192196"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Streamování protokolů aplikace Azure Spring Cloudu v reálném čase
Azure Spring Cloud umožňuje streamování protokolů v Azure CLI získat protokoly konzoly aplikací v reálném čase pro řešení potíží. Můžete také [analyzovat protokoly a metriky s nastavením diagnostiky](./diagnostic-services.md).

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [rozšíření Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) pro Spring Cloud, minimální verze 0.2.0 .
* Instance **Azure Spring Cloud** s spuštěnou aplikací, například spring [cloudová aplikace](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  Rozšíření CLI ASC je aktualizováno z verze 0.2.0 na 0.2.1. Tato změna ovlivní syntaxi příkazu `az spring-cloud app log tail`pro streamování protokolu: `az spring-cloud app logs`, který je nahrazen: . Příkaz: `az spring-cloud app log tail` bude v budoucí verzi zastaralá. Pokud jste používali verzi 0.2.0, můžete upgradovat na 0.2.1. Nejprve odeberte starou verzi `az extension remove -n spring-cloud`pomocí příkazu: .  Poté nainstalujte 0.2.1 příkazem: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Použití cli k protokolům ocasu

Chcete-li se vyhnout opakovanému zadávání názvu skupiny prostředků a instance služby, nastavte výchozí název skupiny prostředků a název clusteru.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
V následujících příkladech bude v příkazech vynechána skupina prostředků a název služby.

### <a name="tail-log-for-app-with-single-instance"></a>Protokol ocasu pro aplikaci s jedinou instancí
Pokud aplikace s názvem auth-service má pouze jednu instanci, můžete zobrazit protokol instance aplikace pomocí následujícího příkazu:
```
az spring-cloud app logs -n auth-service
```
Tím se vrátí protokoly:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Protokol ocasu pro aplikaci s více instancemi
Pokud pro aplikaci s `auth-service`názvem existuje více instancí `-i/--instance` , můžete protokol instance zobrazit pomocí možnosti. 

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
Potom můžete streamovat protokoly instance aplikace `-i/--instance` s možností:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Podrobnosti o instancích aplikací můžete taky získat z webu Azure Portal.  Po výběru **aplikací** v levém navigačním podokně **služby**Azure Spring Cloud vyberte Instance aplikací .

### <a name="continuously-stream-new-logs"></a>Nepřetržité streamování nových protokolů
Ve výchozím `az spring-cloud ap log tail` nastavení vytiskne pouze existující protokoly vysílané do konzoly aplikace a potom ukončí. Pokud chcete streamovat nové protokoly, přidejte -f (--follow):  

```
az spring-cloud app logs -n auth-service -f
``` 
Kontrola všech podporovaných možností protokolování:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Další kroky

* [Analýza protokolů a metrik pomocí nastavení diagnostiky](./diagnostic-services.md)

 





