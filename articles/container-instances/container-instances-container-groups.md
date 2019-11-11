---
title: Azure Container Instances skupiny kontejnerů
description: Princip fungování skupin více kontejnerů v Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 11/01/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a785ecbfa09c54d3affa97c220d4808f9fe8d90b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904454"
---
# <a name="container-groups-in-azure-container-instances"></a>Skupiny kontejnerů v Azure Container Instances

Prostředek nejvyšší úrovně v Azure Container Instances je *Skupina kontejnerů*. Tento článek popisuje, co jsou skupiny kontejnerů a typy scénářů, které povolují.

## <a name="how-a-container-group-works"></a>Jak funguje skupina kontejnerů

Skupina kontejnerů je kolekce kontejnerů, které se naplánovaly na stejném hostitelském počítači. Kontejnery ve skupině kontejnerů sdílejí životní cyklus, prostředky, místní síť a svazky úložiště. V [Kubernetes][kubernetes-pod]je to v konceptu podobné jako *pod* .

Následující diagram znázorňuje příklad skupiny kontejnerů, která obsahuje více kontejnerů:

![Diagram skupin kontejneru][container-groups-example]

Tato ukázková skupina kontejnerů:

* Je naplánován na jednom hostitelském počítači.
* Má přiřazený popisek názvu DNS.
* Zveřejňuje jednu veřejnou IP adresu s jedním vystaveným portem.
* Se skládá ze dvou kontejnerů. Jeden kontejner naslouchá na portu 80, zatímco druhý naslouchá na portu 5000.
* Zahrnuje dvě sdílené složky Azure jako připojení svazku a každý kontejner připojí jednu z těchto sdílených složek místně.

> [!NOTE]
> Skupiny více kontejnerů aktuálně podporují pouze kontejnery Linux. V případě kontejnerů Windows podporuje Azure Container Instances jenom nasazení jedné instance. Pracujeme na tom, abychom do kontejnerů Windows provedli všechny funkce. v [přehledu](container-instances-overview.md#linux-and-windows-containers)služeb můžete najít aktuální rozdíly v platformách.

## <a name="deployment"></a>Nasazení

Tady jsou dva běžné způsoby, jak nasadit skupinu s více kontejnery: použijte [šablonu správce prostředků][resource-manager template] nebo [soubor YAML][yaml-file]. Když při nasazování instancí kontejnerů potřebujete nasadit další prostředky služby Azure (například [sdílenou složku Azure Files][azure-files]), doporučuje se šablona správce prostředků. Vzhledem k výstižnější povaze formátu YAML se doporučuje soubor YAML, pokud nasazení zahrnuje jenom instance kontejnerů. Podrobnosti o vlastnostech, které lze nastavit, naleznete v tématu [Správce prostředků odkaz na šablonu](/azure/templates/microsoft.containerinstance/containergroups) nebo v referenční dokumentaci k [YAML](container-instances-reference-yaml.md) .

Pokud chcete zachovat konfiguraci skupiny kontejnerů, můžete tuto konfiguraci exportovat do souboru YAML pomocí příkazu rozhraní příkazového řádku Azure CLI [AZ Container export][az-container-export]. Export umožňuje ukládat konfigurace skupiny kontejnerů ve správě verzí pro "konfiguraci jako kód". Nebo použijte exportovaný soubor jako výchozí bod při vývoji nové konfigurace v YAML.



## <a name="resource-allocation"></a>Přidělení prostředků

Azure Container Instances přiděluje prostředky, jako jsou CPU, paměť a volitelně [GPU][gpus] (Preview), do skupiny kontejnerů přidáním [požadavků na prostředky][resource-requests] instancí ve skupině. Pokud jako příklad vytvoříte skupinu kontejnerů se dvěma instancemi, každý z nich bude mít každý požadavek 1 procesor, skupina kontejnerů se přidělí 2 procesory.

### <a name="resource-usage-by-instances"></a>Využití prostředků podle instancí

Každá instance kontejneru má přidělené prostředky, které jsou zadány v žádosti o prostředky. Využití prostředků v instanci kontejneru ve skupině ale závisí na tom, jak nakonfigurujete její volitelnou vlastnost [limitu prostředků][resource-limits] .

* Pokud nezadáte omezení prostředků, maximální využití prostředků instance je stejné jako jeho požadavek na prostředek.

* Pokud pro instanci zadáte omezení prostředků, můžete pro její úlohu upravit využití prostředků instance, buď snížit nebo zvýšit využití vzhledem k žádosti o prostředky. Maximální počet prostředků, které můžete nastavit, je celkový počet prostředků přidělených skupině.
    
    Například ve skupině se dvěma instancemi požadujících 1 procesor může jeden z vašich kontejnerů spustit úlohu, která vyžaduje více procesorů pro spuštění než druhá.

    V tomto scénáři můžete nastavit limit prostředků 0,5 procesor pro jednu instanci a limit 2 procesorů pro druhý. Tato konfigurace omezuje využití prostředků prvního kontejneru na 0,5 procesor, což umožňuje, aby druhý kontejner používal až plný 2 procesory, pokud jsou k dispozici.

Další informace naleznete v tématu vlastnost [ResourceRequirements][resource-requirements] ve skupinách kontejneru REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimální a maximální přidělení

* Přidělte skupině kontejnerů **minimálně** jeden procesor a 1 GB paměti. Jednotlivé instance kontejneru v rámci skupiny můžou být zřízené s méně než 1 PROCESORem a 1 GB paměti. 

* **Maximální** prostředky ve skupině kontejnerů najdete v tématu [dostupnost prostředků][region-availability] pro Azure Container Instances v oblasti nasazení.

## <a name="networking"></a>Sítě

Skupiny kontejnerů sdílejí IP adresu a obor názvů portu na této IP adrese. Chcete-li povolit externím klientům, aby dosáhli kontejneru v rámci skupiny, je nutné vystavit port na IP adrese a v kontejneru. Vzhledem k tomu, že kontejnery v rámci skupiny sdílejí obor názvů portu, mapování portů se nepodporuje. Kontejnery v rámci skupiny se můžou vzájemně kontaktovat přes localhost na portech, které jsou vystavené, a to i v případě, že se tyto porty nezveřejňují externě na IP adrese skupiny.

Volitelně nasaďte skupiny kontejnerů do služby [Azure Virtual Network][virtual-network] (Preview), abyste kontejnerům umožnili zabezpečenou komunikaci s ostatními prostředky ve virtuální síti.

## <a name="storage"></a>Úložiště

Můžete zadat externí svazky, které se připojí v rámci skupiny kontejnerů. Tyto svazky můžete namapovat na konkrétní cesty v rámci jednotlivých kontejnerů ve skupině.

## <a name="common-scenarios"></a>Obvyklé scénáře

Skupiny více kontejnerů jsou užitečné v případech, kdy chcete rozdělit jednu funkční úlohu na malý počet imagí kontejneru. Tyto Image je pak možné doručovat různými týmy a mít samostatné požadavky na prostředky.

Příklad použití může zahrnovat:

* Kontejner obsluhující webovou aplikaci a kontejner, který vybírá nejnovější obsah ze správy zdrojového kódu.
* Kontejner aplikace a kontejner protokolování. Kontejner protokolování shromažďuje výstup protokolů a metrik pomocí hlavní aplikace a zapisuje je do dlouhodobého úložiště.
* Kontejner aplikace a kontejner monitorování. Kontejner monitorování pravidelně vytváří požadavek na aplikaci, aby bylo zajištěno, že je spuštěná a správně reaguje, a vyvolá výstrahu, pokud není.
* Front-end kontejner a back-end kontejner. Front-end může sloužit webové aplikaci s back-end, který spouští službu pro načítání dat. 

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nasadit skupinu kontejnerů s více kontejnery pomocí šablony Azure Resource Manager:

> [!div class="nextstepaction"]
> [Nasazení skupiny kontejnerů][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
