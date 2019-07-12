---
title: Připojení svazku emptyDir ve službě Azure Container Instances
description: Zjistěte, jak připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů ve službě Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: d91706da898e84effc6194a74dce69a66be0f4ac
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657605"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojení svazku emptyDir ve službě Azure Container Instances

Zjistěte, jak připojit *emptyDir* svazku ke sdílení dat mezi kontejnery ve skupině kontejnerů ve službě Azure Container Instances.

> [!NOTE]
> Připojení *emptyDir* svazek je momentálně omezené jenom na Linuxové kontejnery. Pracujeme na všechny funkce byly do kontejnerů Windows, můžete najít aktuální rozdíly pro tyto platformy v [přehled](container-instances-overview.md#linux-and-windows-containers).

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

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklad nasazením instance kontejneru pomocí šablony Azure Resource Manageru najdete v tématu [nasazení skupin více kontejnerů ve službě Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit svazek gitRepo ve službě Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)