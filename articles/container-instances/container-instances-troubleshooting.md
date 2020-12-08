---
title: Řešení běžných potíží
description: Naučte se řešit běžné problémy při nasazení, spuštění nebo správě Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779511"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Řešení běžných potíží se službou Azure Container Instances

Tento článek popisuje, jak řešit běžné problémy při správě a nasazování kontejnerů do Azure Container Instances. Přečtěte si také [Nejčastější dotazy](container-instances-faq.md).

Pokud potřebujete další podporu, přečtěte si část dostupná podpora a možnosti **podpory** v [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problémy během nasazování skupiny kontejnerů
### <a name="naming-conventions"></a>Zásady vytváření názvů

Při definování specifikace kontejneru vyžadují některé parametry dodržování omezení pojmenování. Níže je tabulka se specifickými požadavky na vlastnosti skupiny kontejnerů. Další informace najdete v tématu [konvence pojmenování][azure-name-restrictions] v cetrum architektury Azure a [pravidla pro pojmenování a omezení pro prostředky Azure][naming-rules].

| Rozsah | Délka | Velikost písmen | Platné znaky | Navrhovaný vzor | Příklad |
| --- | --- | --- | --- | --- | --- |
| Název kontejneru<sup>1</sup> | 1–63 |Malá písmena | Alfanumerické znaky a spojovníky kdekoli s výjimkou prvního nebo posledního znaku |`<name>-<role>-container<number>` |`web-batch-container1` |
| Porty kontejneru | Mezi 1 a 65535 |Integer |Celé číslo od 1 do 65535 |`<port-number>` |`443` |
| Popisek názvu DNS | 5-63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a spojovníky kdekoli s výjimkou prvního nebo posledního znaku |`<name>` |`frontend-site1` |
| Proměnná prostředí | 1–63 |Malá a velká písmena se nerozlišují. |Alfanumerické znaky a podtržítka (_) kdekoli s výjimkou prvního nebo posledního znaku |`<name>` |`MY_VARIABLE` |
| Název svazku | 5-63 |Malá písmena |Alfanumerické znaky a pomlčky kdekoli s výjimkou prvního nebo posledního znaku. Nemůže obsahovat dvě po sobě jdoucí spojovníky. |`<name>` |`batch-output-volume` |

<sup>1</sup> Omezení také pro názvy skupin kontejnerů, pokud nejsou zadány nezávisle na instancích kontejnerů, například pomocí `az container create` nasazení příkazů.

### <a name="os-version-of-image-not-supported"></a>Verze operačního systému image není podporovaná.

Pokud zadáte obrázek, který Azure Container Instances nepodporuje, `OsVersionNotSupported` vrátí se chyba. Tato chyba je podobná následujícímu, kde `{0}` je název bitové kopie, kterou jste se pokusili nasadit:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

K této chybě nejčastěji dochází při nasazování bitových kopií systému Windows, které jsou založené na Semi-Annualovém kanálu verze 1709 nebo 1803, což není podporováno. Podporované image Windows v Azure Container Instances najdete v tématu [Nejčastější dotazy](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Nelze načíst obrázek.

Pokud Azure Container Instances nedokáže načíst vaši image, opakuje se v časovém intervalu. Pokud dojde k selhání operace vyžádání image, ACI nakonec selže nasazení a může se zobrazit `Failed to pull image` Chyba.

Chcete-li tento problém vyřešit, odstraňte instanci kontejneru a opakujte nasazení. Ujistěte se, že bitová kopie existuje v registru a že jste správně zadali název bitové kopie.

Pokud se image nedá načíst, zobrazí se ve výstupu [AZ Container show][az-container-show]následující události, jako jsou tyto:

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
### <a name="resource-not-available-error"></a>Chyba prostředku není k dispozici

Kvůli různým místním zátěži prostředků v Azure se může při pokusu o nasazení instance kontejneru zobrazit následující chyba:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Tato chyba označuje, že kvůli vysokému zatížení v oblasti, ve které se pokoušíte nasadit, nelze v daném čase přidělit prostředky zadané pro váš kontejner. K vyřešení vašeho problému použijte jeden nebo několik následujících kroků pro zmírnění rizik.

* Ověřte, že nastavení nasazení kontejneru spadají do parametrů definovaných v [oblasti dostupnosti pro Azure Container Instances](container-instances-region-availability.md)
* Určete nižší nastavení procesoru a paměti pro kontejner.
* Nasazení do jiné oblasti Azure
* Nasazení později

## <a name="issues-during-container-group-runtime"></a>Problémy při běhu skupiny kontejnerů
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontejner se průběžně zastavuje a restartuje (bez dlouhotrvajícího procesu)

Skupiny kontejnerů jsou standardně nastavené na [zásady restartování](container-instances-restart-policy.md) **vždy**, takže kontejnery ve skupině kontejnerů se po spuštění po dokončení vždy restartují. Je možné, že tuto změnu budete muset změnit na **neúspěšné** nebo **nikdy** , pokud máte v úmyslu spouštět kontejnery založené na úlohách. Pokud zadáte **chybu** a stále se zobrazuje nepřetržité restartování, může se jednat o problém s aplikací nebo skriptem provedeným ve vašem kontejneru.

Pokud spouštíte skupiny kontejnerů bez dlouhotrvajících procesů, můžete se setkat se opakovanými ukončeními a restarty s obrázky, jako je Ubuntu nebo Alpine. Připojení přes [exec](container-instances-exec.md) nebude fungovat, protože kontejner nemá žádný proces, který ho udržuje aktivní. Chcete-li tento problém vyřešit, zahrňte do nasazení skupiny kontejnerů příkaz Start podobný tomuto:, aby byl kontejner spuštěný.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Rozhraní Container Instances API a Azure Portal obsahuje `restartCount` vlastnost. Pokud chcete zjistit počet restartování pro kontejner, můžete použít příkaz [AZ Container show][az-container-show] v rozhraní příkazového řádku Azure CLI. V následujícím příkladu výstupu (který byl zkrácen pro zkrácení) můžete zobrazit `restartCount` vlastnost na konci výstupu.

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
> Většina imagí kontejneru pro distribuce systému Linux jako výchozí příkaz nastaví prostředí, jako je například bash. Vzhledem k tomu, že vlastní prostředí nepředstavuje dlouhotrvající službu, tyto kontejnery se okamžitě ukončí a přejdou do smyčky restartu, pokud je nakonfigurovaná s výchozí zásadou **vždy** restartovat.

### <a name="container-takes-a-long-time-to-start"></a>Spuštění kontejneru trvá dlouhou dobu

Tři primární faktory, které přispívají k času spuštění kontejneru v Azure Container Instances jsou:

* [Velikost obrázku](#image-size)
* [Umístění obrázku](#image-location)
* [Obrázky v mezipaměti](#cached-images)

Bitové kopie systému Windows mají [Další požadavky](#cached-images).

#### <a name="image-size"></a>Velikost obrázku

Pokud je váš kontejner spuštěný dlouhou dobu, ale nakonec úspěch, začněte tím, že prohlížíte velikost své image kontejneru. Vzhledem k tomu, že Azure Container Instances načte image kontejneru na vyžádání, je zobrazený čas spuštění přímo v souvislosti s jeho velikostí.

Velikost Image kontejneru můžete zobrazit pomocí `docker images` příkazu v Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Klíčem pro uchování velikosti obrázků je zajistit, že finální obrázek neobsahuje cokoli, co není nutné za běhu. Jedním ze způsobů, jak to udělat, je [sestavení ve více fázích][docker-multi-stage-builds]. Sestavení s více fázemi usnadňují zajištění, že finální image obsahuje jenom artefakty, které potřebujete pro vaši aplikaci, a ne žádný další obsah, který byl požadován v době sestavení.

#### <a name="image-location"></a>Umístění obrázku

Dalším způsobem, jak snížit dopad navýšení obrázku na dobu spuštění kontejneru, je hostovat image kontejneru v [Azure Container Registry](../container-registry/index.yml) ve stejné oblasti, ve které chcete nasadit instance kontejnerů. Tím se zkrátí síťová cesta, kterou bitová kopie kontejneru potřebuje cestovat, což významně zkrátí dobu stahování.

#### <a name="cached-images"></a>Obrázky v mezipaměti

Azure Container Instances používá mechanismus ukládání do mezipaměti, který urychluje čas spuštění kontejneru pro image vytvořené na běžných [obrázcích Windows Base](container-instances-faq.md#what-windows-base-os-images-are-supported), včetně `nanoserver:1809` , `servercore:ltsc2019` a `servercore:1809` . Běžně používané image Linux, například `ubuntu:1604` a, `alpine:3.6` jsou také uloženy v mezipaměti. Pro image Windows i Linux Nepoužívejte `latest` značku. Projděte si [osvědčené postupy pro značky Image](../container-registry/container-registry-image-tag-version.md) Container registry pro doprovodné materiály. Aktuální seznam imagí a značek uložených v mezipaměti najdete v rozhraní API pro [vypsání imagí v mezipaměti][list-cached-images] .

> [!NOTE]
> Používání imagí založených na Windows serveru 2019 v Azure Container Instances je ve verzi Preview.

#### <a name="windows-containers-slow-network-readiness"></a>Pomalá připravenost na síť kontejnery Windows

Při počátečním vytváření nemusí kontejnery Windows mít žádná příchozí nebo odchozí připojení po dobu až 30 sekund (ve výjimečných případech nebo déle). Pokud vaše aplikace kontejneru potřebuje připojení k Internetu, přidejte zpoždění a logiku opakování, aby bylo možné navázat připojení k internetu za 30 sekund. Po počátečním nastavení by se měly sítě kontejneru správně obnovit.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nejde se připojit k základnímu rozhraní Docker API nebo ke spuštění privilegovaných kontejnerů.

Azure Container Instances nevystavuje přímý přístup k podkladové infrastruktuře, která je hostitelem skupin kontejnerů. To zahrnuje přístup k rozhraní API Docker běžícímu na hostiteli kontejneru a spouštění privilegovaných kontejnerů. Pokud potřebujete interakci Docker, podívejte se do [Referenční dokumentace REST](/rest/api/container-instances/) a podívejte se, co podporuje rozhraní ACI API. Pokud chybí nějaký objekt, odešlete žádost ve [fórech ACI Feedback](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>IP adresa skupiny kontejnerů nemusí být dostupná kvůli neshodě portů

Azure Container Instances ještě nepodporuje mapování portů jako s normální konfigurací Docker. Pokud zjistíte, že IP adresa skupiny kontejnerů není dostupná, pokud se domníváte, že by měla být, ujistěte se, že jste nakonfigurovali image kontejneru, aby naslouchala stejným portům, které zveřejníte ve skupině kontejnerů s `ports` vlastností.

Pokud chcete potvrdit, že Azure Container Instances může naslouchat na portu, který jste nakonfigurovali v imagi kontejneru, otestujte nasazení `aci-helloworld` image, která port zveřejňuje. Aplikaci také spusťte `aci-helloworld` , aby naslouchala na portu. `aci-helloworld` přijme volitelnou proměnnou prostředí `PORT` pro přepsání výchozího portu 80, na kterém naslouchá. Například pro otestování portu 9000 nastavte [proměnnou prostředí](container-instances-environment-variables.md) při vytváření skupiny kontejnerů:

1. Nastavte skupinu kontejnerů k vystavení portu 9000 a předejte číslo portu jako hodnotu proměnné prostředí. Příklad je naformátován pro prostředí bash shell. Pokud dáváte přednost jinému prostředí, například PowerShellu nebo příkazovému řádku, budete muset odpovídajícím způsobem upravit přiřazení proměnné.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Ve výstupu příkazu vyhledejte IP adresu skupiny kontejnerů `az container create` . Vyhledejte hodnotu **IP**. 
1. Po úspěšném zřízení kontejneru přejděte na adresu IP a port aplikace kontejneru v prohlížeči, například: `192.0.2.0:9000` . 

    Měla by se zobrazit zpráva "Vítá vás Azure Container Instances!" zpráva zobrazená webovou aplikací
1. Až budete s kontejnerem hotovi, odeberte ho pomocí `az container delete` příkazu:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Další kroky

Naučte se, jak [načíst protokoly a události kontejneru](container-instances-get-logs.md) , které vám pomůžou s laděním vašich kontejnerů.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
