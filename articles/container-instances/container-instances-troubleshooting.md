---
title: Řešení potíží s instancí Azure kontejnerů
description: Zjistěte, jak vyřešit problémy s instancí kontejnerů Azure
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 39c43c079ea4d10686bd656ba2d451ff42aac9f6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700226"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Řešení běžných problémů s Azure kontejner instancí

Tento článek ukazuje, jak řešení běžných problémů pro správu nebo nasazení kontejnerů do Azure kontejner instancí.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Při definování specifikaci kontejneru, vyžadují některé parametry a omezení pojmenování. Níže je tabulka s konkrétní požadavky pro kontejner vlastnosti skupiny.
Další informace o Azure zásady vytváření názvů najdete v tématu [konvence vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) architektura centra Azure.

| Rozsah | Délka | Velikost písmen | Platné znaky | Navrhované vzor | Příklad: |
| --- | --- | --- | --- | --- | --- | --- |
| Název kontejneru skupiny | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli kromě první nebo poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Název kontejneru | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli kromě první nebo poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontejneru | Mezi 1 a 65535. |Integer |Celé číslo mezi 1 a 65535. |`<port-number>` |`443` |
| Popisek názvu DNS | 5 až 63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli kromě první nebo poslední znak |`<name>` |`frontend-site1` |
| Proměnná prostředí | 1–63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a chracter '_' kdekoli kromě první nebo poslední znak |`<name>` |`MY_VARIABLE` |
| Název svazku | 5 až 63 |Malá a velká písmena se nerozlišují. |Malá písmena, číslice a pomlčky kdekoli s výjimkou na první nebo poslední znak. Nemůže obsahovat dvě pomlčky po sobě. |`<name>` |`batch-output-volume` |

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

Bitových kopií systému Windows mají [další aspekty](#cached-windows-images).

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

### <a name="cached-windows-images"></a>V mezipaměti bitových kopií systému Windows

Azure instancí kontejnerů používá mechanismus ukládání do mezipaměti ke spuštění rychlost kontejner pro bitové kopie založené na určité bitových kopií systému Windows.

Chcete-li zajistit nejrychlejší čas spuštění kontejneru systému Windows, použijte jednu z **poslední tři** verzích následující **dvě bitové kopie** jako základní bitovou kopii:

* [Windows Server 2016] [ docker-hub-windows-core] (pouze LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows kontejnery pomalou síť připravenosti

Kontejnery Windows mohou být účtovány žádná příchozí nebo odchozí připojení pro až 5 sekund na úvodní vytvoření. Po počáteční instalaci by měl správně obnovit kontejner sítě.

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba

Z důvodu různých místních prostředků zatížení v Azure, může dojít k následující chybě, při pokusu o nasazení instance kontejneru:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba znamená, že kvůli případě velkého zatížení v oblasti, ve které se pokoušíte nasadit prostředky zadané pro váš kontejner nelze přidělit v daném čase. Pomocí jednoho nebo více z následujících kroků pro zmírnění dopadů váš problém vyřešit.

* Zkontrolujte vaše nastavení nasazení kontejneru spadal do parametrech definovaných v [kvóty a dostupnost v oblastech Azure kontejner instancí](container-instances-quotas.md#region-availability)
* Zadejte nižší nastavení procesoru a paměti pro kontejner
* Nasazení v jiné oblasti Azure
* Nasazení později

## <a name="next-steps"></a>Další postup
Zjistěte, jak [načíst kontejneru protokoly & události](container-instances-get-logs.md) pomoci při ladění kontejnerů.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show