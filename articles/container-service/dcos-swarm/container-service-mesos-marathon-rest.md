---
title: (ZASTARALÉ) Správa clusteru Azure DC/OS pomocí rozhraní Marathon REST API
description: Nasazujte kontejnery do clusteru řadičů domény/operačního systému Služby Azure Container Service pomocí rozhraní MARATHON REST API.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277784"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(ZASTARALÉ) Správa kontejnerů DC/OS prostřednictvím rozhraní MARATHON REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS poskytuje prostředí pro nasazování a škálování clusterových úloh a zároveň poskytuje abstrakci používaného hardwaru. Nad DC/OS je rozhraní, které spravuje plánování a provádění výpočetních úloh. Přestože jsou architektury k dispozici pro mnoho oblíbených úloh, tento dokument vám dá začít vytvářet a škálovat nasazení kontejnerů pomocí rozhraní MARATHON REST API. 

## <a name="prerequisites"></a>Požadavky

Než si projdete tyto příklady, budete potřebovat cluster DC/OS nakonfigurovaný v Azure Container Service. Kromě toho je nutné mít možnost se k tomuto clusteru připojit vzdáleně. Další informace k těmto záležitostem najdete v těchto článcích:

* [Nasazení clusteru Azure Container Service](container-service-deployment.md)
* [Připojení ke clusteru Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Přístup k řadičům DOMÉNY/os api
Po připojení k clusteru služby Azure Container Service můžete přistupovat k řadiči\/domény/operačního systému a souvisejícím mAT REST prostřednictvím protokolu http: /localhost:local-port. Příklady v tomto dokumentu předpokládají, že máte k dispozici tunel na portu 80. Například koncové body Marathon lze dosáhnout na identifikátory\/URI začínající http: /localhost/marathon/v2/. 

Další informace o různých rozhraních API najdete v dokumentaci Mesosphere pro rozhraní [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) a [Chronos API](https://mesos.github.io/chronos/docs/api.html) a v dokumentaci Apache pro rozhraní [Mesos Scheduler API](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Získání informací z DC/OS a Marathonu
Před nasazením kontejnerů do clusteru DC/OS shromážděte některé informace o clusteru DC/OS, jako jsou názvy a stav agentů řadiče domény/operačního systému. To provedete tak, že zašlete dotaz na koncový bod `master/slaves` rozhraní REST API DC/OS. Pokud všechno proběhne správně, dotaz vrátí seznam agentů DC/OS a u každého z nich několik vlastností.

```bash
curl http://localhost/mesos/master/slaves
```

Nyní pomocí koncového bodu Marathon `/apps` zkontrolujte aktuální nasazení aplikací v clusteru DC/OS. Pokud je to nový cluster, uvidíte prázdné pole aplikací.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Nasazení kontejneru formátovaného Dockerem
Kontejnery ve formátu Dockeru nasadíte prostřednictvím rozhraní MARATHON REST API pomocí souboru JSON, který popisuje zamýšlené nasazení. Následující ukázka nasazuje kontejner Nginx do soukromého agenta v clusteru. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Chcete-li nasadit kontejner ve formátu Dockeru, uložte soubor JSON na přístupném místě. Pak následujícím příkazem nasaďte kontejner. Zadejte název souboru JSON (`marathon.json` v tomto příkladu).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Výstup je podobný tomuto:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Nyní když Marathonu odešlete dotaz na aplikace, zobrazí se tato nová aplikace ve výstupu.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Dosáhněte kontejneru

Můžete ověřit, že Nginx běží v kontejneru na jednom z privátních agentů v clusteru. Chcete-li najít hostitele a port, kde je kontejner spuštěn, dotaz Marathon pro spuštěné úlohy: 

```bash
curl localhost/marathon/v2/tasks
```

Najděte hodnotu `host` ve výstupu (IP `10.32.0.x`adresu podobnou `ports`) a hodnotu .


Nyní vytvořte připojení terminálu SSH (nikoli tunelové připojení) k správě fqdn clusteru. Po připojení proveďte následující požadavek a napovědte `host` `ports`správné hodnoty písmen e):

```bash
curl http://host:ports
```

Výstup serveru Nginx je podobný následujícímu:

![Nginx z kontejneru](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Škálování kontejnerů
Rozhraní Marathon API můžete použít k horizontálnímu navýšení kapacity nebo škálování v nasazení aplikací. V předchozím příkladu jste nasadili jednu instanci aplikace. Nyní škálování aplikace navyšme na tři instance. To provedete tak, že pomocí následujícího textu JSON vytvoříte soubor JSON a uložíte ho na dostupném místě.

```json
{ "instances": 3 }
```

Z tunelového připojení spusťte následující příkaz pro horizontální navýšení kapacity aplikace.

> [!NOTE]
> Identifikátor URI je\/http: /localhost/marathon/v2/apps/ následovaný ID aplikace pro škálování. Pokud používáte ukázku Nginx, která je k dispozici\/zde, identifikátor URI bude http: /localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Nakonec pošlete na koncový bod Marathon dotaz na aplikace. Vidíte, že tam jsou nyní tři kontejnery Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Ekvivalentní příkazy PowerShellu
V systému Windows můžete tyto stejné akce provést pomocí příkazů PowerShellu.

Chcete-li shromáždit informace o clusteru DC/OS, jako jsou názvy agentů a stav agenta, spusťte následující příkaz:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Kontejnery formátované Dockerem nasadíte přes Marathon pomocí souboru JSON, který popisuje zamýšlené nasazení. Následující ukázka nasadí kontejner Nginx a sváže port 80 agenta DC/OS s portem 80 kontejneru.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Chcete-li nasadit kontejner ve formátu Dockeru, uložte soubor JSON na přístupném místě. Pak následujícím příkazem nasaďte kontejner. Zadejte cestu k souboru`marathon.json` JSON ( v tomto příkladu).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Rozhraní Marathon API je možné použít i k nasazením aplikací se škálováním pro horizontální navýšení nebo snížení kapacity. V předchozím příkladu jste nasadili jednu instanci aplikace. Nyní škálování aplikace navyšme na tři instance. To provedete tak, že pomocí následujícího textu JSON vytvoříte soubor JSON a uložíte ho na dostupném místě.

```json
{ "instances": 3 }
```

Spuštěním následujícího příkazu můžete aplikaci škálovat:

> [!NOTE]
> Identifikátor URI je\/http: /localhost/marathon/v2/apps/ následovaný ID aplikace pro škálování. Pokud používáte ukázku Nginx, která je zde\/k dispozici, identifikátor URI bude http: /localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Další kroky
* [Další informace o koncových bodech Mesos HTTP](https://mesos.apache.org/documentation/latest/endpoints/)
* [Přečtěte si více o rozhraní MARATHON REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

