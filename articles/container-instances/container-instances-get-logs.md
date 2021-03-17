---
title: Získat protokoly instance kontejneru & události
description: Naučte se, jak načíst protokoly kontejnerů a události v Azure Container Instances, které vám pomůžou vyřešit problémy s kontejnery.
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746971"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Načtení událostí a protokolů kontejnerů ve službě Azure Container Instances

Pokud se v Azure Container Instances nejedná o kontejner, začněte tím, že zobrazíte jeho protokoly pomocí [AZ Container logs][az-container-logs]a Streamujte jeho standardní a standardní chybu pomocí [AZ Container Attach][az-container-attach]. Můžete také zobrazit protokoly a události pro instance kontejnerů ve Azure Portal, nebo odeslat data protokolu a události pro skupiny kontejnerů do [protokolů Azure monitor](container-instances-log-analytics.md).

## <a name="view-logs"></a>Zobrazení protokolů

Chcete-li zobrazit protokoly z kódu aplikace v rámci kontejneru, můžete použít příkaz [AZ Container logs][az-container-logs] .

Níže je uveden výstup protokolu z ukázkového kontejneru založeného na úlohách v [Nastavení příkazového řádku v instanci kontejneru](container-instances-start-command.md#azure-cli-example), a to po zadání neplatné adresy URL pomocí přepsání příkazového řádku:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Připojení výstupních datových proudů

Příkaz [AZ Container Attach][az-container-attach] poskytuje diagnostické informace při spuštění kontejneru. Po spuštění kontejneru streamuje STDOUT a STDERR do místní konzoly.

Například zde je výstup z kontejneru založeného na úlohách v [Nastavení příkazového řádku v instanci kontejneru](container-instances-start-command.md#azure-cli-example), poté, co doplní platnou adresu URL souboru velkého textu ke zpracování:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Získat diagnostické události

Pokud se Váš kontejner nepodaří úspěšně nasadit, Projděte si diagnostické informace, které poskytl poskytovatel prostředků Azure Container Instances. Pokud chcete zobrazit události pro svůj kontejner, spusťte příkaz [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Výstup zahrnuje základní vlastnosti vašeho kontejneru spolu s událostmi nasazení (tady se zobrazují zkráceně):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Další kroky
Naučte se [řešit běžné problémy s kontejnerem a nasazením](container-instances-troubleshooting.md) pro Azure Container Instances.

Naučte se odesílat data protokolů a událostí pro skupiny kontejnerů a [protokoly Azure monitor](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
