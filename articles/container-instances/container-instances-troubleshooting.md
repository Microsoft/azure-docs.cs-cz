---
title: Řešení potíží s Azure Container Instances
description: Zjistěte, jak řešit problémy se službou Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6f57bc41cddc997a69f92ba4e8ca66faaeb29738
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424598"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Řešení běžných potíží ve službě Azure Container Instances

Tento článek ukazuje, jak řešení běžných potíží pro řízení a nasazení kontejnerů do služby Azure Container Instances.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Při definování vašeho kontejneru specifikace, vyžadují některé parametry dodržování omezení pojmenování. Níže je tabulka s konkrétní požadavky pro kontejner vlastnosti skupiny. Další informace o vytváření názvů Azure najdete v tématu [zásady vytváření názvů] [ azure-name-restrictions] v Azure Architecture Center.

| Rozsah | Délka | Velikost písmen | Platné znaky | Navrhovaný model | Příklad: |
| --- | --- | --- | --- | --- | --- | --- |
| Název skupiny kontejnerů | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Název kontejneru | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontejneru | Mezi 1 a 65535. |Integer |Celé číslo mezi 1 a 65535. |`<port-number>` |`443` |
| Popisek názvu DNS | 5 63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>` |`frontend-site1` |
| Proměnná prostředí | 1–63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a kdekoli s výjimkou první ani poslední znak podtržítka (_) |`<name>` |`MY_VARIABLE` |
| Název svazku | 5 63 |Malá a velká písmena se nerozlišují. |Malá písmena a číslice a spojovníky kdekoli s výjimkou první ani poslední znak. Nesmí obsahovat dvě po sobě jdoucí pomlčky. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Verze operačního systému z bitové kopie není podporována

Pokud zadáte bitovou kopii, která nepodporuje Azure Container Instances, `OsVersionNotSupported` chyba je vrácena. Chyba je podobná následující, kde `{0}` je název obrázku, který jste se pokusili získat nasazení:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

K této chybě nejčastěji dochází při nasazení Image Windows, které jsou založeny na půlroční kanál (SAC) verze. Například Windows verze 1709 a 1803 jsou SAC verze a generování této chyby při nasazování.

Služba Azure Container Instances podporuje pouze na základě dlouhodobé údržby kanálu (LTSC) verze Image Windows. Chcete-li tento problém zmírnit, při nasazování kontejnerů Windows, vždy nasazení na základě LTSC imagí.

Podrobnosti o LTSC a SAC verzích Windows najdete v tématu [přehled Windows serveru prostřednictvím půlročního kanálu][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Nejde o přijetí změn image

Pokud se zpočátku nepovedlo se získat image Azure Container Instances, počet opakování pro určitou dobu. Pokud operace přijetí změn image nadále selhávat, ACI nakonec dojde k chybě nasazení a může se zobrazit `Failed to pull image` chyby.

Chcete-li vyřešit tento problém, instanci kontejneru odstranit a opakujte nasazení. Ujistěte se, že image existuje v registru a názvu image jste správně zadali.

Pokud nelze načíst obrázek, události, jako jsou následující se zobrazí ve výstupu příkazu [az container show][az-container-show]:

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

## <a name="container-continually-exits-and-restarts"></a>Kontejner průběžně ukončí a restartuje

Pokud váš kontejner úloha poběží do konce a automaticky restartuje, je nutné nastavit [zásady restartování](container-instances-restart-policy.md) z **OnFailure** nebo **nikdy**. Pokud zadáte **OnFailure** a stále viz neustálého restartování, může být problém s aplikací nebo skript spustit v kontejneru.

Zahrnuje rozhraní API instance kontejneru `restartCount` vlastnost. Pokud chcete zkontrolovat počet restartování pro kontejner, můžete použít [az container show] [ az-container-show] příkaz v rozhraní příkazového řádku Azure. V následujícím příkladu výstupu (který byl zkrácen pro zkrácení), zobrazí se `restartCount` vlastnost na konci výstupu.

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
> Většina imagí kontejneru pro distribuce Linuxu prostředí, jako je například bash, nastavit jako výchozí příkaz. Protože prostředí sama o sobě není dlouhodobé služby, tyto kontejnery okamžitě ukončete a spadají do smyčce restartování v případě nastavena výchozí hodnota **vždy** zásady restartování.

## <a name="container-takes-a-long-time-to-start"></a>Kontejneru trvá dlouhou dobu spuštění

Jsou dva primární faktory, které přispívají k čas spuštění kontejneru ve službě Azure Container Instances:

* [Velikost bitové kopie](#image-size)
* [Umístění obrázku](#image-location)

Image Windows obsahují [další aspekty](#cached-windows-images).

### <a name="image-size"></a>Velikost bitové kopie

Pokud vašeho kontejneru trvá dlouhou dobu spuštění, ale nakonec bude úspěšné, začněte zobrazením velikost image kontejneru. Protože Azure Container Instances získává svou image kontejneru na vyžádání, čas spuštění, který se zobrazí přímo souvisí s jeho velikost.

Velikost vaší image kontejneru můžete zobrazit pomocí `docker images` příkaz v rozhraní příkazového řádku Dockeru:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Klíčem k udržování velikosti obrázků malé zajišťuje, že finální image neobsahuje cokoli, co se nevyžaduje v době běhu. Můžete provést například je [vícefázových sestavení][docker-multi-stage-builds]. Vícefázových sestavení ověřte usnadňují zajistěte, aby konečná image obsahuje pouze artefakty, které potřebujete pro vaši aplikaci a nejsou žádné nadbytečné obsah, který se vyžaduje v okamžiku sestavení.

### <a name="image-location"></a>Umístění obrázku

Dalším způsobem, jak omezit dopad obrázek o přijetí změn na dobu spuštění vašeho kontejneru je hostování image kontejneru v [Azure Container Registry](/azure/container-registry/) ve stejné oblasti, kde máte v úmyslu nasadit kontejner instancí. To zkracuje síťovou cestu, která image kontejneru musí projít, výrazně zkrácení doby stahování.

### <a name="cached-windows-images"></a>Bitové kopie v mezipaměti Windows

Služba Azure Container Instances pomocí mechanismu ukládání do mezipaměti Doba spuštění kontejneru rychlost imagí založených na určité Image Windows.

Pokud chcete zajistit nejrychlejší doba spuštění kontejneru Windows, použijte jednu z **poslední tři** verze následující **dvě bitové kopie** jako základní image:

* [Windows Server 2016] [ docker-hub-windows-core] (pouze LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Připravenost pomalou síť kontejnery Windows

Kontejnery Windows mohou vám být naúčtovány žádné příchozí nebo odchozí připojení při počátečním vytvoření až na 5 sekund. Po počátečním nastavení sítě kontejnerů by měla pokračovat v odpovídajícím způsobem.

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba

Z důvodu místních prostředků pro různé zatížení v Azure, může být zobrazí následující chybová zpráva při pokusu nasadit instanci kontejneru:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba označuje, že z důvodu zátěží v oblasti, ve které se pokoušíte nasadit prostředky zadané pro váš kontejner se nedá přidělit v daném čase. Použijte nejméně jeden z následujících kroků pro zmírnění rizika pomáhající při řešení problému.

* Ověřte nastavení nasazení kontejneru spadají do parametrů definovaných v [kvóty a dostupnost oblastí pro Azure Container Instances](container-instances-quotas.md#region-availability)
* Zadejte nižší nastavení procesoru a paměti pro kontejner
* Nasazení do jiné oblasti Azure
* Nasazení později

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nelze se připojit k základního rozhraní API Dockeru nebo spouštění privilegovaných kontejnerů

Služba Azure Container Instances nevystavuje přímý přístup k základní infrastruktury, který je hostitelem skupiny kontejnerů. To zahrnuje přístup k rozhraní API Dockeru, běží na hostiteli kontejneru a spouštění privilegovaných kontejnerů. Pokud budete potřebovat interakce Dockeru, zkontrolujte [referenční dokumentace k REST](https://aka.ms/aci/rest) co podporuje rozhraní API konektoru ACI. Pokud existuje něco chybí, odešlete žádost na [ACI zpětnou vazbu fóra](https://aka.ms/aci/feedback).

## <a name="next-steps"></a>Další postup
Zjistěte, jak [načíst události a protokoly kontejneru](container-instances-get-logs.md) pro ladění vaše kontejnery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
