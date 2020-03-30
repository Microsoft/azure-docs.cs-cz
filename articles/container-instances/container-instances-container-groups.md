---
title: Úvod do skupin kontejnerů
description: Další informace o skupinách kontejnerů v instanci kontejnerů Azure, kolekci instancí, které sdílejí životní cyklus a prostředky, jako je úložiště a síť
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247212"
---
# <a name="container-groups-in-azure-container-instances"></a>Skupiny kontejnerů ve službě Azure Container Instances

Prostředek nejvyšší úrovně v instanci kontejnerů Azure je *skupina kontejnerů*. Tento článek popisuje, jaké skupiny kontejnerů jsou a typy scénářů, které povolují.

## <a name="what-is-a-container-group"></a>Co je skupina kontejnerů?

Skupina kontejnerů je kolekce kontejnerů, které jsou naplánovány na stejném hostitelském počítači. Kontejnery ve skupině kontejnerů sdílejí životní cyklus, prostředky, místní síť a svazky úložiště. Je to podobné v pojetí *pod* v [Kubernetes][kubernetes-pod].

Následující diagram znázorňuje příklad skupiny kontejnerů, která obsahuje více kontejnerů:

![Diagram skupin kontejnerů][container-groups-example]

Tento příklad skupiny kontejnerů:

* Je naplánováno na jednom hostitelském počítači.
* Je přiřazen popisek názvu DNS.
* Zpřístupní jednu veřejnou IP adresu s jedním vystaveným portem.
* Skládá se ze dvou kontejnerů. Jeden kontejner naslouchá na portu 80, zatímco druhý poslouchá na portu 5000.
* Zahrnuje dvě sdílené složky Azure jako připojení svazku a každý kontejner připojí jednu ze sdílených složek místně.

> [!NOTE]
> Skupiny s více kontejnery v současné době podporují pouze kontejnery Linuxu. Pro kontejnery Windows instance kontejneru Azure podporuje jenom nasazení jedné instance kontejneru. Zatímco pracujeme na tom, aby všechny funkce do kontejnerů systému Windows, můžete najít aktuální rozdíly platformy v [přehledu](container-instances-overview.md#linux-and-windows-containers)služby .

## <a name="deployment"></a>Nasazení

Zde jsou dva běžné způsoby nasazení skupiny s více kontejnery: použijte [šablonu Správce prostředků][resource-manager template] nebo [soubor YAML][yaml-file]. Šablona Správce prostředků se doporučuje, když potřebujete nasadit další prostředky služby Azure (například [sdílené složky Azure Files)][azure-files]při nasazení instancí kontejneru. Vzhledem k stručnější povaze formátu YAML se doporučuje soubor YAML, pokud vaše nasazení obsahuje pouze instance kontejneru. Podrobnosti o vlastnostech, které můžete nastavit, naleznete v [odkazu na šablonu Správce prostředků](/azure/templates/microsoft.containerinstance/containergroups) nebo v referenční dokumentaci [yaml.](container-instances-reference-yaml.md)

Chcete-li zachovat konfiguraci skupiny kontejnerů, můžete exportovat konfiguraci do souboru YAML pomocí [exportu kontejneru az][az-container-export]příkazu Azure CLI . Export umožňuje ukládat konfigurace skupinkontejnerů ve slučovacím řízení verzí pro "konfiguraci jako kód". Nebo použijte exportovaný soubor jako výchozí bod při vývoji nové konfigurace v YAML.



## <a name="resource-allocation"></a>Přidělení zdrojů

Azure Container Instances přiděluje prostředky, jako jsou procesory, paměť a volitelně [GPU][gpus] (preview) do skupiny více [kontejnerů][resource-requests] přidáním požadavků na prostředky instancí ve skupině. Vezmeme-li prostředky procesoru jako příklad, pokud vytvoříte skupinu kontejnerů se dvěma instancemi kontejneru, z nichž každá požaduje 1 procesor, pak je skupina kontejnerů přidělena 2 procesory.

### <a name="resource-usage-by-container-instances"></a>Využití prostředků podle instancí kontejneru

Každé instanci kontejneru ve skupině jsou přiděleny prostředky zadané v jeho požadavku na prostředky. Maximální prostředky používané instancí kontejneru ve skupině se však mohou lišit, pokud nakonfigurujete jeho volitelnou vlastnost [limitu prostředků.][resource-limits] Limit prostředků instance kontejneru musí být větší nebo roven vlastnosti [povinného požadavku na prostředky.][resource-requests]

* Pokud nezadáte limit prostředků, maximální využití prostředků instance kontejneru je stejné jako jeho požadavek na prostředky.

* Pokud zadáte limit pro instanci kontejneru, maximální využití instance může být větší než požadavek, až do limitu, který nastavíte. Odpovídajícím způsobem může snížit využití prostředků jinými instancemi kontejneru ve skupině. Maximální limit prostředků, který můžete nastavit pro instanci kontejneru, je celkový počet prostředků přidělených skupině.
    
Například ve skupině se dvěma instancemi kontejneru, z nichž každá požaduje 1 procesor, může jeden z vašich kontejnerů spustit úlohu, která vyžaduje spuštění více procesorů než ostatní.

V tomto scénáři můžete nastavit limit prostředků 2 procesory pro instanci kontejneru. Tato konfigurace umožňuje instanci kontejneru používat až do úplné 2 procesory, pokud jsou k dispozici.

### <a name="minimum-and-maximum-allocation"></a>Minimální a maximální příděl

* Přidělit **minimálně** 1 procesor a 1 GB paměti do skupiny kontejnerů. Jednotlivé instance kontejneru v rámci skupiny lze zřídit s méně než 1 procesoru a 1 GB paměti. 

* **Maximální** prostředky ve skupině kontejnerů najdete v [tématu dostupnost prostředků][region-availability] pro instance kontejneru Azure v oblasti nasazení.

## <a name="networking"></a>Síťové služby

Skupiny kontejnerů mohou sdílet externí ADRESU IP, jeden nebo více portů na této adrese IP a popisek DNS s plně kvalifikovaným názvem domény (Plně kvalifikovaný název domény). Chcete-li povolit externí mů e-li externí klienti dosáhnout kontejneru v rámci skupiny, je nutné vystavit port na IP adresu a z kontejneru. Vzhledem k tomu, že kontejnery ve skupině sdílejí obor názvů portů, mapování portů není podporováno. Ip adresa skupiny kontejnerů a hlavní obsah kvalifikovaných adres budou uvolněny při odstranění skupiny kontejnerů. 

V rámci skupiny kontejnerů se instance kontejneru mohou vzájemně oslovit prostřednictvím localhost na libovolném portu, i když tyto porty nejsou vystaveny externě na IP adrese skupiny nebo z kontejneru.

Volitelně nasazujte skupiny kontejnerů do [virtuální sítě Azure,][virtual-network] aby kontejnery mohly bezpečně komunikovat s jinými prostředky ve virtuální síti.

## <a name="storage"></a>Úložiště

Můžete určit externí svazky, které chcete připojit v rámci skupiny kontejnerů. Mezi podporované svazky patří:
* [Sdílená složka Azure][azure-files]
* [Tajný kód][secret]
* [Prázdný adresář][empty-directory]
* [Klonované git úložiště][volume-gitrepo]

Tyto svazky můžete namapovat na konkrétní cesty v rámci jednotlivých kontejnerů ve skupině. 

## <a name="common-scenarios"></a>Obvyklé scénáře

Skupiny s více kontejnery jsou užitečné v případech, kdy chcete rozdělit jednu funkční úlohu na malý počet iobrazek kontejneru. Tyto obrázky pak mohou být doručeny různými týmy a mají samostatné požadavky na prostředky.

Příklad použití může zahrnovat:

* Kontejner obsluhující webovou aplikaci a kontejner, který vytahuje nejnovější obsah ze správy zdrojového kódu.
* Kontejner aplikace a kontejner protokolování. Kontejner protokolování shromažďuje protokoly a metriky výstup hlavní aplikace a zapíše je do dlouhodobého úložiště.
* Kontejner aplikace a kontejner monitorování. Kontejner monitorování pravidelně provádí požadavek na aplikaci, aby bylo zajištěno, že je spuštěna a správně reaguje, a vyvolá výstrahu, pokud není.
* Front-end kontejner a back-end kontejner. Front-end může sloužit webové aplikace, s back-end běží služba pro načtení dat. 

## <a name="next-steps"></a>Další kroky

Zjistěte, jak nasadit skupinu kontejnerů s více kontejnery pomocí šablony Azure Resource Manageru:

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
