---
title: Skupin kontejnerů Azure Container Instances
description: Pochopení skupiny vícekontejnerové jak práce ve službě Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005529"
---
# <a name="container-groups-in-azure-container-instances"></a>Skupin kontejnerů ve službě Azure Container Instances

Prostředek nejvyšší úrovně ve službě Azure Container Instances je *skupinu kontejnerů*. Tento článek popisuje, co jsou skupiny kontejnerů a typy scénáře, které umožňují.

## <a name="how-a-container-group-works"></a>Jak funguje skupiny kontejnerů

Skupiny kontejnerů je kolekce kontejnerů, které získáte naplánována na stejném hostitelském počítači. Kontejnery ve skupině kontejnerů sdílejí životní cyklus, prostředky, místní sítě a svazky úložiště. Je podobný koncept *pod* v [Kubernetes][kubernetes-pod].

Následující diagram ukazuje příklad, který zahrnuje několik kontejnerů skupiny kontejnerů:

![Diagram skupiny kontejnerů][container-groups-example]

Tento příklad skupiny kontejnerů:

* Je naplánovaná na jeden hostitelský počítač.
* Popisek názvu DNS je přiřazen.
* Poskytuje jednu veřejnou IP adresu, s vystavené jeden port.
* Se skládá ze dvou kontejnerů. Jeden kontejner naslouchá na portu 80, při další naslouchá na portu 5000.
* Zahrnuje dva sdílených složek Azure jako svazek připojí, a každý kontejner připojí jeden z místní složky.

> [!NOTE]
> Skupiny vícekontejnerové aktuálně podporují pouze kontejnery Linuxu. Pro kontejnery Windows Azure Container Instances podporuje pouze nasazení jedné instance. Pracujeme na všechny funkce byly do kontejnerů Windows, můžete najít aktuální rozdíly pro tyto platformy ve službě [přehled](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Nasazení

Tady jsou dvě běžné způsoby nasazení skupiny vícekontejnerové: použití [šablony Resource Manageru] [ resource-manager template] nebo [soubor YAML][yaml-file]. Šablony Resource Manageru se doporučuje, pokud potřebujete nasadit prostředky dalších služeb Azure (třeba [sdílené složky Azure Files][azure-files]) při nasazování instancí kontejnerů. Protože potřebujeme stručnější formátu YAML soubor YAML se doporučuje, pokud vaše nasazení obsahuje pouze instance kontejneru.

Pokud chcete zachovat konfiguraci skupiny kontejnerů, můžete konfiguraci exportovat do souboru YAML pomocí příkazového řádku Azure [az container export][az-container-export]. Export umožňuje ukládat vaše konfigurace skupiny kontejnerů ve správě verzí pro "konfigurace jako kódu." Nebo můžete použít exportovaný soubor jako výchozí bod při vývoji nové konfigurace v YAML.

## <a name="resource-allocation"></a>Přidělování prostředků

Služba Azure Container Instances přiděluje prostředky, jako jsou procesory, paměť a volitelně [GPU] [ gpus] (preview) pro skupinu kontejnerů tak, že přidáte [požadavky prostředků] [ resource-requests] instancí ve skupině. Převedení prostředků procesoru jako příklad, pokud vytvoříte skupinu kontejnerů se dvěma instancemi, každé 1 žádost o procesoru, pak skupinu kontejnerů se přidělené 2 procesory.

Maximální prostředky, které jsou k dispozici pro skupinu kontejnerů se spoléháte [oblasti Azure] [ region-availability] použijete pro toto nasazení.

### <a name="container-resource-requests-and-limits"></a>Omezení a požadavky na kontejner prostředku

* Ve výchozím nastavení instance kontejnerů ve skupině sdílet požadované prostředky skupiny. Ve skupině se dvěma instancemi každou žádost o 1 procesor, skupinu jako celek má přístup k 2 procesory. Každá instance může využít 2 procesory a instance může soutěží o prostředky procesoru při spuštění.

* Chcete-li omezit využití prostředků ve skupině instancí, volitelně nastavit [limitu prostředků] [ resource-limits] pro instanci. Ve skupině se dvěma instancemi požaduje 1 procesor, jeden z kontejnerů může vyžadovat více procesorů pro spuštění než ten druhý.

  V tomto scénáři můžete nastavit zdroj limitem 0,5 CPU pro jednu instanci a maximálně 2 procesory druhé. Tato konfigurace omezuje využití prostředků první kontejner na 0,5 CPU, což druhý kontejner využít k úplné 2 procesory, pokud je k dispozici.

Další informace najdete v tématu [ResourceRequirements] [ resource-requirements] vlastnost v kontejneru skupiny rozhraní REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimální a maximální přidělení

* Přidělit **minimální** 1 procesoru a 1 GB paměti pro skupinu kontejnerů. Je možné zřídit instance jednotlivých kontejnerů ve skupině s menší než 1 procesoru a 1 GB paměti. 

* Pro **maximální** prostředky ve skupině kontejnerů, najdete v článku [dostupnost prostředků] [aci--dostupnosti oblastí] pro Azure Container Instances v oblasti nasazení.

## <a name="networking"></a>Sítě

Skupin kontejnerů sdílet IP adresy a portu oboru názvů na tuto IP adresu. Pokud chcete povolit externí klienti dosáhli kontejner v rámci skupiny, musí vystavit porty u IP adresy a z kontejneru. Vzhledem k tomu, že kontejnery v rámci skupiny sdílení portu oboru názvů, mapování portů se nepodporuje. Kontejnery v rámci skupiny dosáhnout mezi sebou prostřednictvím místního hostitele na portech, které jsou zpřístupnit, i v případě, že tyto porty nejsou přístupná externě na skupiny IP adres.

Volitelně můžete nasadit skupiny kontejnerů do [virtuální síť Azure] [ virtual-network] (preview) umožňuje kontejnery pro bezpečnou komunikaci s ostatními prostředky ve virtuální síti.

## <a name="storage"></a>Storage

Můžete zadat externího svazků připojit v rámci skupiny kontejnerů. Můžete namapovat tyto svazky do konkrétních cest v rámci jednotlivých kontejnerů ve skupině.

## <a name="common-scenarios"></a>Obvyklé scénáře

Skupiny vícekontejnerové jsou užitečné v případech, ve které chcete rozdělit jeden úkol funkční na malý počet imagí kontejnerů. Tyto Image pak mohl být doručován podle různých týmů a mají požadavky na samostatný prostředek.

Příklad použití může zahrnovat:

* Kontejner obsluhuje webovou aplikaci a kontejner stahování nejnovější obsah ze správy zdrojového kódu.
* Protokolování kontejner a kontejner aplikace. Protokolování kontejneru shromažďuje protokoly a metriky výstup hlavní aplikaci a zapisuje je do dlouhodobého úložiště.
* Monitorování kontejnerů a kontejner aplikace. Monitorování kontejnerů pravidelně odešle požadavek do aplikace k zajištění, že je spuštěný a správně reaguje a vyvolá výstrahu, pokud není.
* Kontejner front-endu a back-end kontejner. Front-endu může mít webovou aplikaci s back-end službou načíst data. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak nasadit skupiny vícekontejnerové kontejnerů pomocí šablony Azure Resource Manageru:

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
