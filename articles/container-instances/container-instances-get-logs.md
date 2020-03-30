---
title: Získat protokoly instancí kontejneru & události
description: Zjistěte, jak načíst protokoly kontejnerů a události v instanci kontejneru Azure, které vám pomůžou vyřešit problémy s kontejnery.
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249999"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Načtení událostí a protokolů kontejnerů ve službě Azure Container Instances

Když máte nechová kontejner v Azure Container Instances, začněte zobrazením jeho protokoly s [protokoly kontejneru az][az-container-logs]a streamujte jeho standardní a standardní chybu s [připojením kontejneru az][az-container-attach]. Můžete také zobrazit protokoly a události pro instance kontejnerů na webu Azure Portal nebo odeslat data protokolu a událostí pro skupiny kontejnerů do [protokolů Azure Monitor](container-instances-log-analytics.md).

## <a name="view-logs"></a>Zobrazení protokolů

Chcete-li zobrazit protokoly z kódu aplikace v rámci kontejneru, můžete použít příkaz [protokoly kontejneru az.][az-container-logs]

Následuje výstup protokolu z ukázkového kontejneru založeného na úlohách v [příkazovém řádku v instanci kontejneru](container-instances-start-command.md#azure-cli-example)poté, co jste zadali neplatnou adresu URL pomocí přepsání příkazového řádku:

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

Příkaz [az kontejner připojit][az-container-attach] poskytuje diagnostické informace při spuštění kontejneru. Po spuštění kontejneru datových proudů STDOUT a STDERR do místní konzole.

Zde je například výstup z kontejneru založeného na úlohách v [nastavení příkazového řádku v instanci kontejneru](container-instances-start-command.md#azure-cli-example)poté, co jste zadali platnou adresu URL velkého textového souboru ke zpracování:

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

## <a name="get-diagnostic-events"></a>Získání diagnostických událostí

Pokud se váš kontejner nepodaří úspěšně nasadit, zkontrolujte diagnostické informace poskytované poskytovatelem prostředků Azure Container Instances. Chcete-li zobrazit události pro váš kontejner, spusťte příkaz [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Výstup obsahuje základní vlastnosti kontejneru spolu s událostmi nasazení (zde zkrácený):

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
Zjistěte, jak [řešit běžné problémy s kontejnery a nasazením](container-instances-troubleshooting.md) pro instance kontejnerů Azure.

Zjistěte, jak odesílat data protokolu a událostí pro skupiny kontejnerů do [protokolů Azure Monitor .](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show