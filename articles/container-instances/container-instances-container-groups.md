---
title: Skupiny kontejnerů Azure instancí kontejnerů
description: Pochopit, jak fungují skupiny kontejnerů v Azure kontejner instancí
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b1eeebacb55ffc7af4e2014f26dd9d5643f5478
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Skupiny kontejnerů v Azure kontejner instancí

Je nejvyšší úrovně prostředků v Azure kontejner instancí *skupina kontejneru*. Tento článek popisuje, jaké jsou skupiny kontejnerů a typy scénáře, které umožňují.

## <a name="how-a-container-group-works"></a>Jak funguje skupina kontejneru

Skupina kontejneru je kolekce kontejnery, které získat naplánovat na stejném hostitelském počítači. Kontejnery v kontejneru skupiny sdílet životního cyklu, místní sítí a svazky úložiště. Je podobný koncept jako *pod* v [Kubernetes] [ kubernetes-pod] a [DC/OS][dcos-pod].

Následující diagram ukazuje příklad skupinou kontejneru, která obsahuje více kontejnerů:

![Diagram skupiny kontejneru][container-groups-example]

Tato skupina kontejneru příklad:

* Je naplánován na jeden hostitelský počítač.
* Popisek názvu DNS není přiřazen.
* Zpřístupní jednu veřejnou IP adresu, s zveřejněné jeden port.
* Se skládá ze dvou kontejnery. Jeden kontejner naslouchá na portu 80, při dalších sleduje na port 5000.
* Zahrnuje dvě Azure sdílené složky jako svazek připojení zařízení a každý kontejner připojí některé sdílené složky místně.

> [!NOTE]
> Více kontejner skupiny jsou aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="deployment"></a>Nasazení

Kontejner *skupiny* mít přidělení minimální prostředků 1 virtuálních procesorů a 1 GB paměti. Jednotlivé *kontejnery* do kontejneru, skupiny dají zřizovat s menší než 1 virtuálních procesorů a 1 GB paměti. V rámci kontejneru skupiny lze přizpůsobit distribuci prostředky do více kontejnerů v rámci navázat na úrovni skupiny kontejneru. Například dva kontejnery každý s 0,5 virtuální procesory, které se nacházejí ve skupině kontejneru, který má přidělené 1 virtuální procesor.

## <a name="networking"></a>Sítě

Skupiny kontejnerů sdílet IP adresy a portu obor názvů na tuto IP adresu. Pokud chcete povolit externí klienti dosáhli na kontejner v rámci skupiny, musí vystavit port na IP adrese a z kontejneru. Vzhledem k tomu, že kontejnery v rámci skupiny sdílet port obor názvů, mapování portů se nepodporuje. Kontejnery v rámci skupiny dosáhnout vzájemně prostřednictvím místního hostitele na porty, které se mají vystavený, i když tyto porty se nezobrazí externě na IP adrese skupiny.

## <a name="storage"></a>Úložiště

Můžete zadat externí svazky připojit v rámci skupiny kontejneru. Můžete namapovat tyto svazky do konkrétních cest v rámci jednotlivých kontejnerů ve skupině.

## <a name="common-scenarios"></a>Obvyklé scénáře

Více kontejner skupiny jsou užitečné v případech, ve které chcete rozdělit jeden úkol funkční na malý počet bitových kopií kontejneru. Tyto bitové kopie pak mohou být dodány pomocí různé týmy a mít požadavky na samostatný prostředky.

Příklad použití můžou zahrnovat:

* Kontejner aplikace a kontejner protokolování. Kontejner protokolování shromažďuje protokoly a metriky výstup v hlavní aplikaci a zapíše do dlouhodobého úložiště.
* Kontejner aplikace a kontejner monitorování. Monitorování kontejneru pravidelně vytváří požadavek na aplikaci zkontrolujte, zda je spuštěná a správně reagovat a vyvolá výstrahu, pokud není.
* Kontejner obsluhující webové aplikace a kontejner stahování nejnovější obsah od správy zdrojového kódu.

## <a name="next-steps"></a>Další postup

Informace o nasazení více kontejneru kontejner skupiny pomocí šablony Azure Resource Manager:

> [!div class="nextstepaction"]
> [Nasazení kontejneru skupiny](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
