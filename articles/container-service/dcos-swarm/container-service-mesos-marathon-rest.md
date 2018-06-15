---
title: Správa clusteru Azure DC/OS pomocí rozhraní REST API Marathonu
description: Nasazení kontejnerů do clusteru Azure Container Service DC/OS pomocí rozhraní REST API Marathonu.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: edd66be25bf2571a7315372898300476fec101ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165601"
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Správa kontejnerů DC/OS přes rozhraní REST API Marathonu

DC/OS poskytuje prostředí pro nasazování a škálování clusterových úloh a zároveň poskytuje abstrakci používaného hardwaru. Nad DC/OS je rozhraní, které spravuje plánování a provádění výpočetních úloh. I když jsou k dispozici pro mnoho populárních úloh rozhraní, tento dokument vám pomůže začít vytváření a škálování nasazení kontejnerů pomocí rozhraní REST API Marathonu. 

## <a name="prerequisites"></a>Požadavky

Než si projdete tyto příklady, budete potřebovat cluster DC/OS nakonfigurovaný v Azure Container Service. Kromě toho je nutné mít možnost se k tomuto clusteru připojit vzdáleně. Další informace k těmto záležitostem najdete v těchto článcích:

* [Nasazení clusteru Azure Container Service](container-service-deployment.md)
* [Připojení ke clusteru Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Přístup k rozhraní API DC/OS
Po připojení ke clusteru Azure Container Service, můžete přístup k DC/OS a související rozhraní REST API prostřednictvím http://localhost:local-port. Příklady v tomto dokumentu předpokládají, že máte k dispozici tunel na portu 80. Například Marathon koncové body k dispozici na adrese identifikátory URI počínaje `http://localhost/marathon/v2/`. 

Další informace o různých rozhraních API najdete v dokumentaci Mesosphere pro rozhraní [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) a [Chronos API](https://mesos.github.io/chronos/docs/api.html) a v dokumentaci Apache pro rozhraní [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Získání informací z DC/OS a Marathonu
Než do clusteru DC/OS nasadíte kontejnery, zjistěte si určité informace o clusteru DC/OS, například názvy a stav agentů DC/OS. To provedete tak, že zašlete dotaz na koncový bod `master/slaves` rozhraní REST API DC/OS. Pokud všechno proběhne správně, dotaz vrátí seznam agentů DC/OS a u každého z nich několik vlastností.

```bash
curl http://localhost/mesos/master/slaves
```

Nyní pomocí koncového bodu Marathon `/apps` zkontrolujte aktuální nasazení aplikací v clusteru DC/OS. Pokud je to nový cluster, uvidíte prázdné pole aplikací.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Nasazení kontejneru formátovaného Dockerem
Kontejnery formátované Dockerem prostřednictvím rozhraní REST API Marathonu nasadíte pomocí souboru JSON, který popisuje zamýšlené nasazení. Následující ukázka nasadí kontejner Nginx do privátní agenta v clusteru. 

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

Abyste mohli nasadit kontejner formátovaný, ukládat do přístupného umístění souboru JSON. Pak následujícím příkazem nasaďte kontejner. Zadejte název souboru JSON (`marathon.json` v tomto příkladu).

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

## <a name="reach-the-container"></a>Dosažení kontejneru

Můžete ověřit, že Nginx běží v kontejneru na jednom ze soukromých agentů v clusteru. Chcete-li najít hostitele a portu, na kterém je spuštěný kontejneru, dotaz Marathon pro spuštěné úlohy: 

```bash
curl localhost/marathon/v2/tasks
```

Vrátí hodnotu `host` ve výstupu (podobně jako IP adresu `10.32.0.x`) a hodnota `ports`.


Nyní terminálu připojení SSH (ne tunelového propojení) proveďte správu plně kvalifikovaný název domény clusteru. Po připojení, zkontrolujte následující požadavek, nahraďte správné hodnoty z `host` a `ports`:

```bash
curl http://host:ports
```

Výstup serveru Nginx je podobný následujícímu:

![Nginx z kontejneru](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Škálování kontejnerů
Můžete použít rozhraní API Marathonu horizontální navýšení kapacity nebo škálování v nasazení aplikace. V předchozím příkladu jste nasadili jednu instanci aplikace. Nyní škálování aplikace navyšme na tři instance. To provedete tak, že pomocí následujícího textu JSON vytvoříte soubor JSON a uložíte ho na dostupném místě.

```json
{ "instances": 3 }
```

Z tunelového propojení spusťte následující příkaz pro horizontální škálování aplikace.

> [!NOTE]
> Identifikátor URI je http://localhost/marathon/v2/apps/ následuje Identifikátor aplikace škálování. Pokud používáte ukázku Nginx, která se zde, identifikátor URI by http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Nakonec pošlete na koncový bod Marathon dotaz na aplikace. Vidíte, že tam jsou nyní tři kontejnery Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Ekvivalentní příkazy PowerShellu
V systému Windows můžete tyto stejné akce provést pomocí příkazů PowerShellu.

Získat informace o clusteru DC/OS, jako jsou názvy agentů a stavu agentů, spusťte následující příkaz:

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

Abyste mohli nasadit kontejner formátovaný, ukládat do přístupného umístění souboru JSON. Pak následujícím příkazem nasaďte kontejner. Zadejte cestu k souboru JSON (`marathon.json` v tomto příkladu).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Rozhraní Marathon API je možné použít i k nasazením aplikací se škálováním pro horizontální navýšení nebo snížení kapacity. V předchozím příkladu jste nasadili jednu instanci aplikace. Nyní škálování aplikace navyšme na tři instance. To provedete tak, že pomocí následujícího textu JSON vytvoříte soubor JSON a uložíte ho na dostupném místě.

```json
{ "instances": 3 }
```

Spusťte následující příkaz pro horizontální škálování aplikace:

> [!NOTE]
> Identifikátor URI je http://localhost/marathon/v2/apps/ následuje Identifikátor aplikace škálování. Pokud používáte ukázku Nginx poskytuje zde, identifikátor URI by http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Další postup
* [Další informace o koncových bodech Mesos HTTP](http://mesos.apache.org/documentation/latest/endpoints/)
* [Další informace o rozhraní REST API Marathonu](https://mesosphere.github.io/marathon/docs/rest-api.html)

