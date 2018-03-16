---
title: "Řešení potíží s instancí Azure kontejnerů"
description: "Zjistěte, jak vyřešit problémy s instancí kontejnerů Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Řešení potíží s nasazením s instancemi Azure kontejneru

Tento článek ukazuje, jak vyřešit problémy při nasazení kontejnerů do Azure kontejner instancí. Také popisuje některé běžné problémy, které můžete narazit na.

## <a name="view-logs-and-stream-output"></a>Zobrazit protokoly a výstup datového proudu

Když máte identifikovala kontejner, spusťte zobrazením protokoly s [az kontejneru protokoly][az-container-logs]a vysílání datového proudu jeho standardní na více systémů a standardní cíl chybové s [az kontejneru připojit] [az-container-attach].

### <a name="view-logs"></a>Zobrazit protokoly

Chcete-li zobrazit protokoly z kódu aplikace v rámci kontejneru, můžete použít [az kontejneru protokoly] [ az-container-logs] příkaz.

Protokol výstupu z kontejneru příklad založený na úlohách v [spuštění kontejnerizované úlohy v ACI](container-instances-restart-policy.md)po nutnosti ho dodáni neplatná adresa URL ke zpracování:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
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

### <a name="attach-output-streams"></a>Připojte výstupní datové proudy

[Az kontejneru připojit] [ az-container-attach] příkaz nabízí diagnostické informace během spouštění kontejneru. Po zahájení kontejneru, proudů STDOUT a STDERR do místní konzoly.

Například je zde výstup z kontejneru založený na úlohách v [spuštění kontejnerizované úlohy v ACI](container-instances-restart-policy.md)po s zadat platnou adresu URL ke zpracování velkých textového souboru:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

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

Pokud vaše kontejneru se nepodaří úspěšně nasadit, budete muset diagnostické informace poskytované poskytovatelem prostředků Azure kontejner instancí. Pokud chcete zobrazit události pro vaše kontejneru, spusťte [az kontejneru zobrazit] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Výstup obsahuje základní vlastnosti kontejneru, společně s událostí nasazení (viz zde zkrácený):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
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

## <a name="common-deployment-issues"></a>Běžné problémy při nasazení

Následující části popisují běžné problémy, tento účet pro většinu chyb v nasazení kontejneru:

* [Nepodporovaná verze bitové kopie](#image-version-not-supported)
* [Nelze pro vyžádání obsahu image](#unable-to-pull-image)
* [Ukončení a restartování průběžně kontejneru](#container-continually-exits-and-restarts)
* [Kontejner trvá dlouhou dobu spuštění](#container-takes-a-long-time-to-start)
* [Chyba "Prostředek není k dispozici"](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Nepodporovaná verze bitové kopie

Pokud zadáte image, která instancí kontejneru Azure nepodporuje, `ImageVersionNotSupported` je vrácena chyba. Je hodnota Chyba `The version of image '{0}' is not supported.`a aktuálně platí pro Windows. 1709 bitové kopie. Chcete-li zmírnit tento problém, použijte bitovou kopii systému Windows LTS. Podpora pro bitové kopie systému Windows. 1709 probíhá.

## <a name="unable-to-pull-image"></a>Nelze pro vyžádání obsahu image

Pokud instance kontejner Azure nemůže původně vyžádání bitové kopie, se pokusí po nějakou dobu před selháním nakonec. Pokud nelze načíst obrázek, událostmi, jako je následující jsou uvedeny ve výstupu [az kontejneru zobrazit][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Vyřešit, odstraňte kontejneru a opakujte vaše nasazení, platící zvýšené pozornosti, že jste správně zadali název bitové kopie.

## <a name="container-continually-exits-and-restarts"></a>Ukončení a restartování průběžně kontejneru

Pokud vaše kontejneru dokončí a automaticky restartuje, je nutné nastavit [restartujte zásad](container-instances-restart-policy.md) z **OnFailure –** nebo **nikdy**. Pokud zadáte **OnFailure** a stále najdete potom restartování, může být problém s aplikací nebo skript spustit ve vašem kontejneru.

Zahrnuje rozhraní API instancí kontejneru `restartCount` vlastnost. Chcete-li zkontrolovat počet restartování pro kontejner, můžete použít [az kontejneru zobrazit] [ az-container-show] v Azure CLI 2.0. V následující příklad výstupu (který byl zkrácen jako stručný výtah), se zobrazí `restartCount` vlastnost na konci výstupu.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Většina kontejneru bitových kopií pro Linuxových distribucích prostředí, jako je například bash, nastavit jako výchozí příkaz. Vzhledem k tomu, že prostředí svoje vlastní není služba dlouho běžící, tyto kontejnery okamžitě ukončit a spadají do smyčku restartování při konfiguraci s výchozím **vždy** začít znovu.

## <a name="container-takes-a-long-time-to-start"></a>Kontejner trvá dlouhou dobu spuštění

Dva primární faktory, které přispívají k kontejneru spuštění v Azure kontejner instancí se:

* [Velikost bitové kopie](#image-size)
* [Umístění bitové kopie](#image-location)

Bitových kopií systému Windows mají [další aspekty](#use-recent-windows-images).

### <a name="image-size"></a>Velikost bitové kopie

Pokud vaše kontejneru trvá dlouhou dobu spuštění, ale nakonec úspěšná, začít hledáním na velikost bitové kopie kontejneru. Protože Azure kontejner instancí vrátí kontejner image na vyžádání, čas spuštění, které zaznamenáte přímo souvisí s jeho velikost.

Velikost vaší image kontejneru můžete zobrazit pomocí `docker images` v rozhraní příkazového řádku Dockeru:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Klíč k udržování velikosti obrázků malé zajišťuje, že finální image neobsahuje nic, který není nutný za běhu. Jeden ze způsobů, jak provést toto je s [více fáze sestavení][docker-multi-stage-builds]. Více fáze sestavení zkontrolujte usnadňují zajistěte, aby finální image obsahuje pouze artefakty potřebné pro vaši aplikaci a ne všechny nadbytečné obsahu, kterou nebyla nutná v čase vytvoření buildu.

### <a name="image-location"></a>Umístění bitové kopie

Jiný způsob, jak snížit dopad vyžádání obsahu bitové kopie na vaše kontejneru spuštění je pro hostování obrázek kontejneru [registru kontejner Azure](/azure/container-registry/) ve stejné oblasti, kde chcete nasadit instancí kontejnerů. To zkracuje síťové cestě, která bitovou kopii kontejneru je potřeba cestují, výrazně zkrátit dobu stahování.

### <a name="use-recent-windows-images"></a>Použijte poslední bitových kopií systému Windows

Azure instancí kontejnerů používá mechanismus ukládání do mezipaměti ke spuštění rychlost kontejner pro bitové kopie založené na určité bitových kopií systému Windows.

Chcete-li zajistit nejrychlejší čas spuštění kontejneru systému Windows, použijte jednu z **poslední tři** verzích následující **dvě bitové kopie** jako základní bitovou kopii:

* [Windows Server 2016] [ docker-hub-windows-core] (pouze LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba

Z důvodu různých místních prostředků zatížení v Azure, může dojít k následující chybě, při pokusu o nasazení instance kontejneru:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba znamená, že kvůli případě velkého zatížení v oblasti, ve které se pokoušíte nasadit prostředky zadané pro váš kontejner nelze přidělit v daném čase. Pomocí jednoho nebo více z následujících kroků pro zmírnění dopadů váš problém vyřešit.

* Zkontrolujte vaše nastavení nasazení kontejneru spadal do parametrech definovaných v [kvóty a dostupnost v oblastech Azure kontejner instancí](container-instances-quotas.md#region-availability)
* Zadejte nižší nastavení procesoru a paměti pro kontejner
* Nasazení v jiné oblasti Azure
* Nasazení později

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show