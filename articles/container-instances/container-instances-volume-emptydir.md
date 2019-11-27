---
title: Připojit svazek emptyDir ke skupině kontejnerů
description: Naučte se připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances
ms.topic: article
ms.date: 02/08/2018
ms.openlocfilehash: 0440bcc490b766c12b2117d2453557707df2a1c4
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533237"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojení svazku emptyDir v Azure Container Instances

Naučte se připojit svazek *emptyDir* ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances.

> [!NOTE]
> Připojení svazku *emptyDir* je aktuálně omezené na kontejnery Linux. Pracujeme na tom, abychom do kontejnerů Windows přenesli všechny funkce. aktuální rozdíly na platformách najdete v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir svazek

Svazek *emptyDir* poskytuje zapisovatelný adresář přístupný každému kontejneru ve skupině kontejnerů. Kontejnery ve skupině mohou číst a zapisovat stejné soubory ve svazku a lze je připojit pomocí stejných nebo různých cest v každém kontejneru.

Několik příkladů použití pro *emptyDir* svazek:

* Pomocné místo
* Vytváření kontrolních bodů během dlouhotrvajících úloh
* Uložení dat načtených kontejnerem webvozíku a obsluhou kontejnerem aplikace

Data ve svazku *emptyDir* jsou trvalá prostřednictvím havárií kontejneru. U kontejnerů, které jsou restartovány, však není zaručeno zachování dat ve svazku *emptyDir* .

## <a name="mount-an-emptydir-volume"></a>Připojení svazku emptyDir

Chcete-li připojit svazek emptyDir v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Nejdřív naplňte pole `volumes` do části `properties` skupiny kontejnerů v šabloně. Pak u každého kontejneru ve skupině kontejnerů, do které chcete připojit svazek *emptyDir* , naplňte pole `volumeMounts` v části `properties` definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se ze dvou kontejnerů, z nichž každý je připojen ke svazku *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklad nasazení instance kontejneru s Azure Resource Manager šablonou najdete v tématu [nasazení skupin více kontejnerů v Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další kroky

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku Gitrepo nepodporují v Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojit tajný svazek v Azure Container Instances](container-instances-volume-secret.md)