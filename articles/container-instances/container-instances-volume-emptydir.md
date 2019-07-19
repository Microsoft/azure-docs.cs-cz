---
title: Připojení svazku emptyDir v Azure Container Instances
description: Naučte se připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325451"
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

Nejdřív naplňte `volumes` pole do části skupina `properties` kontejnerů v šabloně. Dále pro každý kontejner ve skupině kontejnerů, do kterého chcete připojit svazek *emptyDir* , `volumeMounts` vyplňte pole v `properties` části definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se ze dvou kontejnerů, z nichž každý je připojen ke svazku *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklad nasazení instance kontejneru s Azure Resource Manager šablonou najdete v tématu [nasazení skupin více kontejnerů v Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojení svazku Gitrepo nepodporují v Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)