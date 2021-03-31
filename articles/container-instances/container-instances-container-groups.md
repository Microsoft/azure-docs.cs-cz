---
title: Seznámení se skupinami kontejnerů
description: Přečtěte si o skupinách kontejnerů v Azure Container Instances, kolekci instancí, které sdílejí životní cyklus a prostředky, jako jsou CPU, úložiště a síť.
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89612402"
---
# <a name="container-groups-in-azure-container-instances"></a>Skupiny kontejnerů ve službě Azure Container Instances

Prostředek nejvyšší úrovně v Azure Container Instances je *Skupina kontejnerů*. Tento článek popisuje, co jsou skupiny kontejnerů a typy scénářů, které povolují.

## <a name="what-is-a-container-group"></a>Co je skupina kontejnerů?

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
> Skupiny více kontejnerů aktuálně podporují pouze kontejnery Linux. V případě kontejnerů Windows podporuje Azure Container Instances jenom nasazení jediné instance kontejneru. Pracujeme na tom, abychom do kontejnerů Windows provedli všechny funkce. v [přehledu](container-instances-overview.md#linux-and-windows-containers)služeb můžete najít aktuální rozdíly v platformách.

## <a name="deployment"></a>Nasazení

Tady jsou dva běžné způsoby, jak nasadit skupinu s více kontejnery: použijte [šablonu správce prostředků][resource-manager template] nebo [soubor YAML][yaml-file]. Když při nasazování instancí kontejnerů potřebujete nasadit další prostředky služby Azure (například [sdílenou složku Azure Files][azure-files]), doporučuje se šablona správce prostředků. Vzhledem k výstižnější povaze formátu YAML se doporučuje soubor YAML, pokud nasazení zahrnuje jenom instance kontejnerů. Podrobnosti o vlastnostech, které lze nastavit, naleznete v tématu [Správce prostředků odkaz na šablonu](/azure/templates/microsoft.containerinstance/containergroups) nebo v referenční dokumentaci k [YAML](container-instances-reference-yaml.md) .

Pokud chcete zachovat konfiguraci skupiny kontejnerů, můžete tuto konfiguraci exportovat do souboru YAML pomocí příkazu rozhraní příkazového řádku Azure CLI [AZ Container export][az-container-export]. Export umožňuje ukládat konfigurace skupiny kontejnerů ve správě verzí pro "konfiguraci jako kód". Nebo použijte exportovaný soubor jako výchozí bod při vývoji nové konfigurace v YAML.



## <a name="resource-allocation"></a>Přidělení prostředků

Azure Container Instances přiděluje prostředky, jako jsou CPU, paměť a volitelně [GPU][gpus] (Preview), do skupiny více kontejnerů přidáním požadavků na [prostředky][resource-requests] instancí ve skupině. Když jako příklad vytvoříte skupinu kontejnerů se dvěma instancemi kontejnerů, každý z nich požaduje 1 procesor, skupiny kontejnerů se přidělí 2 procesory.

### <a name="resource-usage-by-container-instances"></a>Využití prostředků podle instancí kontejnerů

Každá instance kontejneru ve skupině má přidělené prostředky zadané v její žádosti o prostředky. Maximální počet prostředků využívaných instancí kontejneru ve skupině se ale může lišit, pokud nakonfigurujete její volitelnou vlastnost [limitu prostředků][resource-limits] . Limit prostředků instance kontejneru musí být větší nebo roven vlastnosti povinného [požadavku na prostředek][resource-requests] .

* Pokud nezadáte omezení prostředků, maximální využití prostředků instance kontejneru je stejné jako jeho požadavek na prostředek.

* Pokud zadáte limit pro instanci kontejneru, maximální využití instance může být větší než požadavek, až do nastaveného limitu. Odpovídající využití prostředků jinými instancemi kontejnerů ve skupině může snížit. Maximální omezení prostředků, které můžete nastavit pro instanci kontejneru, je celkový počet prostředků přidělených skupině.
    
Například ve skupině se dvěma instancemi kontejnerů každý požaduje 1 procesor může jeden z vašich kontejnerů spustit úlohu, která vyžaduje více procesorů, než je druhý.

V tomto scénáři můžete pro instanci kontejneru nastavit limit prostředků na až 2 procesory. Tato konfigurace umožňuje, aby instance kontejneru používala až 2 procesorů, pokud je k dispozici.

> [!NOTE]
> Základní infrastruktura služby používá malý objem prostředků skupiny kontejnerů. Vaše kontejnery budou mít přístup k většině prostředků přiřazených ke skupině, ale ne ke všem. Z tohoto důvodu Naplánujte malou vyrovnávací paměť prostředků při žádosti o prostředky pro kontejnery ve skupině.

### <a name="minimum-and-maximum-allocation"></a>Minimální a maximální přidělení

* Přidělte skupině kontejnerů **minimálně** jeden procesor a 1 GB paměti. Jednotlivé instance kontejneru v rámci skupiny můžou být zřízené s méně než 1 PROCESORem a 1 GB paměti. 

* **Maximální** prostředky ve skupině kontejnerů najdete v tématu [dostupnost prostředků][region-availability] pro Azure Container Instances v oblasti nasazení.

## <a name="networking"></a>Sítě

Skupiny kontejnerů můžou sdílet externí IP adresu, jeden nebo víc portů na této IP adrese a popisek DNS s plně kvalifikovaným názvem domény (FQDN). Chcete-li povolit externím klientům, aby dosáhli kontejneru v rámci skupiny, je nutné vystavit port na IP adrese a v kontejneru. IP adresa a plně kvalifikovaný název domény skupiny kontejnerů se uvolní při odstranění skupiny kontejnerů. 

V rámci skupiny kontejnerů se instance kontejnerů můžou vzájemně kontaktovat přes localhost na jakémkoli portu, a to i v případě, že se tyto porty nezveřejňují externě na IP adrese skupiny nebo z kontejneru.

Volitelně nasaďte skupiny kontejnerů do služby [Azure Virtual Network][virtual-network] , abyste kontejnerům umožnili zabezpečenou komunikaci s ostatními prostředky ve virtuální síti.

## <a name="storage"></a>Storage

Můžete zadat externí svazky, které se připojí v rámci skupiny kontejnerů. Mezi podporované svazky patří:
* [Sdílená složka Azure][azure-files]
* [Otázku][secret]
* [Prázdný adresář][empty-directory]
* [Naklonované úložiště Git][volume-gitrepo]

Tyto svazky můžete namapovat na konkrétní cesty v rámci jednotlivých kontejnerů ve skupině. 

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
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
