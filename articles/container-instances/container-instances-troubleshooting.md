---
title: Řešení potíží s Azure Container Instances
description: Zjistěte, jak řešit problémy se službou Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9dc3e19f9429a6055a799f3f013c732538fa370d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070852"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Řešení běžných potíží ve službě Azure Container Instances

Tento článek ukazuje, jak řešení běžných potíží pro řízení a nasazení kontejnerů do služby Azure Container Instances. Viz také [– nejčastější dotazy](container-instances-faq.md).

## <a name="naming-conventions"></a>Zásady vytváření názvů

Při definování vašeho kontejneru specifikace, vyžadují některé parametry dodržování omezení pojmenování. Níže je tabulka s konkrétní požadavky pro kontejner vlastnosti skupiny. Další informace o vytváření názvů Azure najdete v tématu [zásady vytváření názvů] [ azure-name-restrictions] v Azure Architecture Center.

| Rozsah | Délka | Velikost písmen | Platné znaky | Navrhovaný vzor | Příklad: |
| --- | --- | --- | --- | --- | --- |
| Název skupiny kontejnerů | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Název kontejneru | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontejneru | Mezi 1 a 65535. |Integer |Celé číslo mezi 1 a 65535. |`<port-number>` |`443` |
| Popisek názvu DNS | 5-63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou první ani poslední znak |`<name>` |`frontend-site1` |
| Proměnná prostředí | 1–63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a kdekoli s výjimkou první ani poslední znak podtržítka (_) |`<name>` |`MY_VARIABLE` |
| Název svazku | 5-63 |Malá a velká písmena se nerozlišují. |Malá písmena a číslice a spojovníky kdekoli s výjimkou první ani poslední znak. Nesmí obsahovat dvě po sobě jdoucí pomlčky. |`<name>` |`batch-output-volume` |

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

K této chybě nejčastěji dochází při nasazení Image Windows, které jsou založeny na půlroční kanál verze 1709 nebo 1803, které nejsou podporované. Podporované Image Windows v Azure Container Instances, naleznete v tématu [– nejčastější dotazy](container-instances-faq.md#what-windows-base-os-images-are-supported).

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
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontejner průběžně ukončí a restartuje (bez dlouho běžící proces)

Skupin kontejnerů ve výchozím nastavení [zásady restartování](container-instances-restart-policy.md) z **vždy**, takže kontejnery ve skupině kontejnerů vždy restartuje po jejich dokončení. Budete muset změnit tuto hodnotu na **OnFailure** nebo **nikdy** Pokud máte v úmyslu spouštět kontejnery založené na úlohách. Pokud zadáte **OnFailure** a stále viz neustálého restartování, může být problém s aplikací nebo skript spustit v kontejneru.

Při spuštění skupiny kontejnerů bez dlouho běžící procesy se může zobrazit opakované ukončí a restartuje s obrázky, jako je Ubuntu nebo Alpine. Připojení přes [EXEC](container-instances-exec.md) nebude fungovat podle kontejneru nemá žádný proces udržování zachování připojení. Chcete-li tento problém vyřešit, patří start příkaz podobný tomuto s nasazením kontejneru skupiny pro zachování provozu kontejneru.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Zahrnuje rozhraní API Container Instances a Azure portal `restartCount` vlastnost. Pokud chcete zkontrolovat počet restartování pro kontejner, můžete použít [az container show] [ az-container-show] příkaz v rozhraní příkazového řádku Azure. V následujícím příkladu výstupu (který byl zkrácen pro zkrácení), zobrazí se `restartCount` vlastnost na konci výstupu.

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

Image Windows obsahují [další aspekty](#cached-images).

### <a name="image-size"></a>Velikost bitové kopie

Pokud vašeho kontejneru trvá dlouhou dobu spuštění, ale nakonec bude úspěšné, začněte zobrazením velikost image kontejneru. Protože Azure Container Instances získává svou image kontejneru na vyžádání, čas spuštění, který se zobrazí přímo souvisí s jeho velikost.

Velikost vaší image kontejneru můžete zobrazit pomocí `docker images` příkaz v rozhraní příkazového řádku Dockeru:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Klíčem k udržování velikosti obrázků malé zajišťuje, že finální image neobsahuje cokoli, co se nevyžaduje v době běhu. Můžete provést například je [vícefázových sestavení][docker-multi-stage-builds]. Vícefázových sestavení ověřte usnadňují zajistěte, aby konečná image obsahuje pouze artefakty, které potřebujete pro vaši aplikaci a nejsou žádné nadbytečné obsah, který se vyžaduje v okamžiku sestavení.

### <a name="image-location"></a>Umístění obrázku

Dalším způsobem, jak omezit dopad obrázek o přijetí změn na dobu spuštění vašeho kontejneru je hostování image kontejneru v [Azure Container Registry](/azure/container-registry/) ve stejné oblasti, kde máte v úmyslu nasadit kontejner instancí. To zkracuje síťovou cestu, která image kontejneru musí projít, výrazně zkrácení doby stahování.

### <a name="cached-images"></a>Bitové kopie v mezipaměti

Služba Azure Container Instances pomocí mechanismu ukládání do mezipaměti Doba spuštění kontejneru rychlost imagí vytvořených na běžné [základní Image Windows](container-instances-faq.md#what-windows-base-os-images-are-supported), včetně `nanoserver:1809`, `servercore:ltsc2019`, a `servercore:1809`. Nejčastěji používaných imagí Linuxu, jako `ubuntu:1604` a `alpine:3.6` jsou také uloženy v mezipaměti. Aktuální seznam v mezipaměti obrázků a značek, použijte [bitové kopie v mezipaměti seznamu] [ list-cached-images] rozhraní API.

> [!NOTE]
> Použití založené na Windows Server 2019 imagí ve službě Azure Container Instances je ve verzi preview.

### <a name="windows-containers-slow-network-readiness"></a>Připravenost pomalou síť kontejnery Windows

Při počátečním vytvoření kontejnerů Windows může mít žádné příchozí nebo odchozí připojení po dobu až 30 sekund (nebo i delší dobu, ve výjimečných případech). Pokud svou aplikaci typu kontejner potřebuje připojení k Internetu, přidejte zpoždění a opakujte logiku, která umožní 30 sekund k navázání připojení k Internetu. Po počátečním nastavení sítě kontejnerů by měla pokračovat v odpovídajícím způsobem.

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba

Z důvodu místních prostředků pro různé zatížení v Azure, může být zobrazí následující chybová zpráva při pokusu nasadit instanci kontejneru:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba označuje, že z důvodu zátěží v oblasti, ve které se pokoušíte nasadit prostředky zadané pro váš kontejner se nedá přidělit v daném čase. Použijte nejméně jeden z následujících kroků pro zmírnění rizika pomáhající při řešení problému.

* Ověřte nastavení nasazení kontejneru spadají do parametrů definovaných v [dostupnosti oblastí pro Azure Container Instances](container-instances-region-availability.md)
* Zadejte nižší nastavení procesoru a paměti pro kontejner
* Nasazení do jiné oblasti Azure
* Nasazení později

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nelze se připojit k základního rozhraní API Dockeru nebo spouštění privilegovaných kontejnerů

Služba Azure Container Instances nevystavuje přímý přístup k základní infrastruktury, který je hostitelem skupiny kontejnerů. To zahrnuje přístup k rozhraní API Dockeru, běží na hostiteli kontejneru a spouštění privilegovaných kontejnerů. Pokud budete potřebovat interakce Dockeru, zkontrolujte [referenční dokumentace k REST](https://aka.ms/aci/rest) co podporuje rozhraní API konektoru ACI. Pokud existuje něco chybí, odešlete žádost na [ACI zpětnou vazbu fóra](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>IP adresy možná nejsou dostupná z důvodu neodpovídající porty

Služba Azure Container Instances nepodporuje aktuálně port mapování jako s konfigurací regulární dockeru, ale tato oprava se na roadmapě. Pokud zjistíte IP adres nejsou dostupné, když budete přesvědčeni, že by měl být, ujistěte se, nakonfigurovali jste image kontejneru tak, aby naslouchala na stejné porty zveřejnit ve vaší skupině kontejnerů s `ports` vlastnost.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [získat protokoly kontejneru a události](container-instances-get-logs.md) pro ladění vaše kontejnery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
