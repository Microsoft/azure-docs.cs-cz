---
title: Připojení svazku emptyDir ve službě Azure Container Instances
description: Zjistěte, jak připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů ve službě Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857648"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojení svazku emptyDir ve službě Azure Container Instances

Zjistěte, jak připojit *emptyDir* svazku ke sdílení dat mezi kontejnery ve skupině kontejnerů ve službě Azure Container Instances.

> [!NOTE]
> Připojení *emptyDir* svazek je momentálně omezené jenom na Linuxové kontejnery. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>emptyDir svazku

*EmptyDir* svazek poskytuje zapisovatelné adresář přístupný pro každý kontejner ve skupině kontejnerů. Kontejnery ve skupině může číst a zapisovat stejné soubory ve svazku a je možné připojit pomocí stejných nebo různých cest v jednotlivých kontejnerech.

Některé příklad používá pro *emptyDir* svazku:

* Pomocné místo
* Vytváření kontrolních bodů průběhu dlouho běžících úloh
* Data Store načte kontejner sajdkára a obsluhovat kontejner aplikace

Data *emptyDir* prostřednictvím kontejneru selhání je trvalý svazek. Kontejnery, které se restartují, není však zaručeno uchovávat data v *emptyDir* svazku.

## <a name="mount-an-emptydir-volume"></a>Připojit emptyDir svazek

Připojit emptyDir svazek v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplnit `volumes` pole ve skupině kontejnerů `properties` část šablony. Dále pro každý kontejner ve skupině kontejnerů, ve kterém byste chtěli připojit *emptyDir* svazku, naplnění `volumeMounts` obsahuje pole `properties` část definice kontejneru.

Například následující šablony Resource Manageru vytvoří skupinu kontejnerů, který se skládá z dvou kontejnerů, každý z které připojí *emptyDir* svazku:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json --> [!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklad nasazením instance kontejneru pomocí šablony Azure Resource Manageru najdete v tématu [nasazení skupin více kontejnerů ve službě Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit svazek gitRepo ve službě Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)