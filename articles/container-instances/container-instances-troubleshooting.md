---
title: Řešení běžných potíží
description: Zjistěte, jak řešit běžné problémy při nasazování, spouštění nebo správě instancí kontejnerů Azure
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533395"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Řešení běžných potíží se službou Azure Container Instances

Tento článek ukazuje, jak řešit běžné problémy se správou nebo nasazováním kontejnerů do instancí kontejnerů Azure. Viz také [Nejčastější dotazy](container-instances-faq.md).

Pokud potřebujete další podporu, podívejte se na dostupné možnosti **nápovědy + podpory** na [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problémy během nasazení skupiny kontejnerů
### <a name="naming-conventions"></a>Zásady vytváření názvů

Při definování specifikace kontejneru vyžadují určité parametry dodržování omezení pojmenování. Níže je tabulka se specifickými požadavky na vlastnosti skupiny kontejnerů. Další informace o konvencích pojmenování Azure najdete v [tématu Konvence pojmenování][azure-name-restrictions] v Centru architektury Azure.

| Rozsah | Délka | Velikost písmen | Platné znaky | Navrhovaný vzor | Příklad |
| --- | --- | --- | --- | --- | --- |
| Název skupiny kontejnerů | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické a pomlčka kdekoli kromě prvního nebo posledního znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Název kontejneru | 1-64 |Malá a velká písmena se nerozlišují. |Alfanumerické a pomlčka kdekoli kromě prvního nebo posledního znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Kontejnerové porty | Mezi 1 a 65535 |Integer |Celé číslo mezi 1 a 65535 |`<port-number>` |`443` |
| Popisek názvu DNS | 5-63 |Malá a velká písmena se nerozlišují. |Alfanumerické a pomlčka kdekoli kromě prvního nebo posledního znaku |`<name>` |`frontend-site1` |
| Proměnná prostředí | 1–63 |Malá a velká písmena se nerozlišují. |Alfanumerické a podtržítko (_) kdekoli kromě prvního nebo posledního znaku |`<name>` |`MY_VARIABLE` |
| Název svazku | 5-63 |Malá a velká písmena se nerozlišují. |Malá písmena a čísla a pomlčky kdekoli kromě prvního nebo posledního znaku. Nesmí obsahovat dvě po sobě jdoucí spojovníky. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Verze bitové kopie operačního systému není podporována

Pokud zadáte image, kterou instance kontejneru Azure `OsVersionNotSupported` nepodporuje, vrátí se chyba. Chyba je podobná následující, `{0}` kde je název bitové kopie, kterou jste se pokusili nasadit:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

K této chybě dochází nejčastěji při nasazování bitových kopií systému Windows, které jsou založeny na verzi 1709 nebo 1803 pololetního kanálu, které nejsou podporovány. Podporované ibi Windows v instancích kontejnerů Azure najdete [v tématu Nejčastější dotazy](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Nelze stáhnout obrázek

Pokud instance kontejneru Azure zpočátku nelze vytáhnout image, opakuje se po určitou dobu. Pokud operace vyžádat bitovou kopii nadále nezdaří, ACI nakonec selže nasazení a může se zobrazit chyba. `Failed to pull image`

Chcete-li tento problém vyřešit, odstraňte instanci kontejneru a opakujte nasazení. Ujistěte se, že bitová kopie existuje v registru a že jste správně zadali název obrázku.

Pokud obrázek nelze vytáhnout, události, jako je následující, jsou zobrazeny ve výstupu [az kontejneru show][az-container-show]:

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
### <a name="resource-not-available-error"></a>Chyba zdroje není k dispozici

Vzhledem k různým zatížení množin prostředků v Azure se může zobrazit následující chyba při pokusu o nasazení instance kontejneru:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba označuje, že z důvodu vysoké zatížení v oblasti, ve které se pokoušíte nasadit, prostředky určené pro váš kontejner nelze přidělit v té době. K vyřešení problému použijte jeden nebo více následujících kroků ke zmírnění rizik.

* Ověřte, že nastavení nasazení kontejneru spadá do parametrů definovaných v [dostupnosti oblasti pro instance kontejnerů Azure](container-instances-region-availability.md)
* Určení nižšího nastavení procesoru a paměti pro kontejner
* Nasazení do jiné oblasti Azure
* Nasazení později

## <a name="issues-during-container-group-runtime"></a>Problémy během běhu skupiny kontejnerů
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontejner neustále ukončuje a restartuje (žádný dlouhotrvající proces)

Kontejner skupiny výchozí [zásady restartování](container-instances-restart-policy.md) **Always**, takže kontejnery ve skupině kontejnerů vždy restartovat po spuštění do dokončení. Možná budete muset změnit na **OnFailure** nebo **Nikdy,** pokud máte v úmyslu spustit kontejnery založené na úlohách. Pokud zadáte **OnFailure** a stále vidět průběžné restartování, může být problém s aplikací nebo skript uprováděný v kontejneru.

Při spuštění skupin kontejnerů bez dlouhotrvajících procesů se mohou zobrazit opakované výstupy a restartování s obrázky, jako je Ubuntu nebo Alpine. Připojení přes [EXEC](container-instances-exec.md) nebude fungovat, protože kontejner nemá žádný proces, který by ho udržoval naživu. Chcete-li tento problém vyřešit, zahrňte příkaz start, jako je následující, s nasazením skupiny kontejnerů, aby byl kontejner spuštěn.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Rozhraní API instancí kontejneru `restartCount` a portál Azure obsahují vlastnost. Chcete-li zkontrolovat počet restartování kontejneru, můžete použít příkaz [az kontejner show][az-container-show] v příkazovém příkazu Konto Azure. V následujícím příkladu výstupu (který byl zkrácen pro stručnost), můžete `restartCount` vidět vlastnost na konci výstupu.

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
> Většina iniciál kontejnerů pro linuxové distribuce nastavila jako výchozí příkaz prostředí, například bash. Vzhledem k tomu, že prostředí samo o sobě není dlouhotrvající služba, tyto kontejnery okamžitě ukončit a spadají do smyčky restartování při konfiguraci s výchozí **vždy** restartovat zásady.

### <a name="container-takes-a-long-time-to-start"></a>Spuštění kontejneru trvá dlouho

Tři primární faktory, které přispívají k době spuštění kontejneru v instanci kontejneru Azure jsou:

* [Velikost obrázku](#image-size)
* [Umístění obrázku](#image-location)
* [Obrázky uložené v mezipaměti](#cached-images)

Bitové kopie systému Windows mají [další důležité informace](#cached-images).

#### <a name="image-size"></a>Velikost obrázku

Pokud váš kontejner trvá dlouhou dobu, ale nakonec uspěje, začněte tím, že se podíváte na velikost image kontejneru. Vzhledem k tomu, že instance kontejneru Azure na vyžádání vyžádá image kontejneru, čas spuštění, který se zobrazí, přímo souvisí s jeho velikostí.

Velikost image kontejneru můžete zobrazit pomocí `docker images` příkazu v příkazu Cli Dockeru:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Klíčem k zachování velikosti obrázku malé je zajistit, že vaše konečná image neobsahuje nic, co není požadováno za běhu. Jedním ze způsobů, jak to udělat, je s [vícestupňové sestavení][docker-multi-stage-builds]. Vícestupňová sestavení usnadňují zajištění, že konečná bitová kopie obsahuje pouze artefakty, které potřebujete pro vaši aplikaci, a ne žádný další obsah, který byl vyžadován v době sestavení.

#### <a name="image-location"></a>Umístění obrázku

Dalším způsobem, jak snížit dopad vyžádat image na dobu spuštění vašeho kontejneru je hostování image kontejneru v [registru kontejnerů Azure](/azure/container-registry/) ve stejné oblasti, kde máte v úmyslu nasadit instance kontejneru. Tím se zkrátí síťová cesta, kterou musí obrázek kontejneru cestovat, což výrazně zkracuje dobu stahování.

#### <a name="cached-images"></a>Obrázky uložené v mezipaměti

Azure Container Instances používá mechanismus ukládání do mezipaměti, který pomáhá urychlit dobu `servercore:ltsc2019`spuštění `servercore:1809`kontejneru pro bitové kopie postavené na běžných [základních ibi Windows](container-instances-faq.md#what-windows-base-os-images-are-supported), včetně `nanoserver:1809`, a . Běžně používané linuxové `ubuntu:1604` obrázky, jako jsou a `alpine:3.6` jsou také uloženy v mezipaměti. Pro aktuální seznam obrázků a značek uložených v mezipaměti použijte rozhraní [API pro obrázky v mezipaměti seznamu.][list-cached-images]

> [!NOTE]
> Použití bitových kopií založených na Windows Serveru 2019 v instanci kontejnerů Azure je ve verzi preview.

#### <a name="windows-containers-slow-network-readiness"></a>Kontejnery windows zpomalují připravenost sítě

Při počátečním vytvoření kontejnery systému Windows může mít žádné příchozí nebo odchozí připojení po dobu až 30 sekund (nebo déle, ve výjimečných případech). Pokud vaše aplikace kontejneru potřebuje připojení k Internetu, přidejte zpoždění a opakujte logiku, abyste povolili 30 sekund navázání připojení k Internetu. Po počátečním nastavení kontejneru sítě by měl y pokračovat odpovídajícím způsobem.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nelze se připojit k podkladovému rozhraní Docker API nebo spustit privilegované kontejnery.

Azure Container Instances nezveřejňuje přímý přístup k základní infrastruktuře, která hostuje skupiny kontejnerů. To zahrnuje přístup k rozhraní API Dockeru spuštěné na hostiteli kontejneru a spuštění privilegovaných kontejnerů. Pokud požadujete interakci Dockeru, podívejte se do [referenční dokumentace REST](https://aka.ms/aci/rest) a zjistěte, co rozhraní API Rozhraní ACI podporuje. Pokud něco chybí, odešlete žádost na [fórech pro zpětnou vazbu ACI](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Ip adresa skupiny kontejnerů nemusí být přístupná z důvodu neodpovídajících portů.

Azure Container Instances ještě nepodporuje mapování portů, jako u běžné konfigurace dockeru. Pokud zjistíte, že IP adresa skupiny kontejnerů není přístupná, pokud se domníváte, že by měla být, `ports` ujistěte se, že jste nakonfigurovali image kontejneru tak, aby naslouchala stejným portům, které zveřejňujete ve skupině kontejnerů s vlastností.

Pokud chcete potvrdit, že instance kontejneru Azure můžete naslouchat na portu, který `aci-helloworld` jste nakonfigurovali v image kontejneru, otestujte nasazení image, která zpřístupňuje port. Také spustit `aci-helloworld` aplikaci tak, aby naslouchá na portu. `aci-helloworld`přijme volitelnou proměnnou `PORT` prostředí, která přepíše výchozí port 80, na který naslouchá. Chcete-li například otestovat port 9000, nastavte [proměnnou prostředí](container-instances-environment-variables.md) při vytváření skupiny kontejnerů:

1. Nastavte skupinu kontejnerů tak, aby zpřístupňovala port 9000, a předajte číslo portu jako hodnotu proměnné prostředí. Příklad je formátován pro prostředí Bash. Pokud dáváte přednost jinému prostředí, například prostředí PowerShell nebo příkazovém řádku, budete muset odpovídajícím způsobem upravit přiřazení proměnných.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Najděte IP adresu skupiny kontejnerů `az container create`ve výstupu příkazu . Podívejte se na hodnotu **ip**. 
1. Po úspěšném zřízení kontejneru přejděte v prohlížeči ip adresu a port aplikace `192.0.2.0:9000`kontejneru, například: . 

    Měli byste vidět "Vítejte na Azure Container Instances!" webové aplikace.
1. Až budete s kontejnerem hotovi, `az container delete` odeberte ho pomocí příkazu:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [načíst protokoly kontejnerů a události,](container-instances-get-logs.md) které vám pomohou ladit kontejnery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
