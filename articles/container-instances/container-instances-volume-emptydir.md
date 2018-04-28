---
title: Připojit emptyDir svazek v Azure kontejner instancí
description: Zjistěte, jak připojit svazek emptyDir ke sdílení dat mezi kontejnery v kontejneru skupiny v Azure kontejner instancí
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 89289a7a0bb5c486c662d528c5014bdbd8eebaca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojit emptyDir svazek v Azure kontejner instancí

Zjistěte, jak se připojit *emptyDir* svazku ke sdílení dat mezi kontejnery v kontejneru skupiny v Azure kontejner instancí.

> [!NOTE]
> Připojení *emptyDir* svazek je aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>emptyDir svazku

*EmptyDir* svazku poskytuje zapisovatelné adresář přístupný pro každý kontejner ve skupině kontejneru. Kontejnery ve skupině lze číst a zapisovat soubory se stejným ve svazku a ho můžete připojit pomocí stejné nebo jiné cesty v jednotlivých kontejnerech.

Některé příklad používá pro *emptyDir* svazku:

* Pomocné místo
* Vytváření kontrolních bodů během dlouhotrvající úlohy
* Ukládání dat získaných pomocí něho kontejner a obsloužených kontejner aplikace

Data v *emptyDir* svazek je trvalé prostřednictvím kontejneru dojde k chybě. Kontejnery, které se restartují, ale není zaručena bezpečnost uchovávat data v *emptyDir* svazku.

## <a name="mount-an-emptydir-volume"></a>Připojte svazek emptyDir

Připojit emptyDir svazek v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplnit `volumes` pole ve skupině kontejneru `properties` část šablony. Dále pro každý kontejner ve skupině kontejner, ve kterém byste chtěli připojit *emptyDir* svazku, naplnit `volumeMounts` pole v `properties` části definici kontejneru.

Například následující šablony Resource Manageru vytvoří skupině kontejneru, která obsahuje dvě kontejnery, každý z které připojení zařízení *emptyDir* svazku:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklad nasazení kontejneru instance pomocí šablony Azure Resource Manager, najdete v sekci [nasazení skupiny více kontejnerů v Azure kontejner instancí](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit jiné typy svazku v Azure kontejner instancí:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojení svazku gitRepo v Azure kontejner instancí](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)