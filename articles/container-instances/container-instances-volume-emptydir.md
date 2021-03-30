---
title: Připojit svazek emptyDir ke skupině kontejnerů
description: Naučte se připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117752"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojení svazku emptyDir v Azure Container Instances

Naučte se připojit svazek *emptyDir* ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances. Používejte svazky *emptyDir* jako dočasné mezipaměti pro vaše kontejnerové úlohy.

> [!NOTE]
> Připojení svazku *emptyDir* je aktuálně omezené na kontejnery Linux. Pracujeme na tom, abychom do kontejnerů Windows přenesli všechny funkce. aktuální rozdíly na platformách najdete v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir svazek

Svazek *emptyDir* poskytuje zapisovatelný adresář přístupný každému kontejneru ve skupině kontejnerů. Kontejnery ve skupině mohou číst a zapisovat stejné soubory ve svazku a lze je připojit pomocí stejných nebo různých cest v každém kontejneru.

Několik příkladů použití pro *emptyDir* svazek:

* Pomocné místo
* Vytváření kontrolních bodů během dlouhotrvajících úloh
* Uložení dat načtených kontejnerem webvozíku a obsluhou kontejnerem aplikace

Data ve svazku *emptyDir* jsou trvalá prostřednictvím havárií kontejneru. U kontejnerů, které jsou restartovány, však není zaručeno zachování dat ve svazku *emptyDir* . Pokud zastavíte skupinu kontejnerů, svazek *emptyDir* není trvale uložený.

Maximální velikost svazku *emptyDir* pro Linux je 50 GB.

## <a name="mount-an-emptydir-volume"></a>Připojení svazku emptyDir

Pokud chcete připojit svazek emptyDir v instanci kontejneru, můžete nasadit pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), [souboru YAML](container-instances-reference-yaml.md)nebo jiné programové metody pro nasazení skupiny kontejnerů.

Nejprve naplňte `volumes` pole do části Skupina kontejnerů v `properties` souboru. Dále pro každý kontejner ve skupině kontejnerů, do kterého chcete připojit svazek *emptyDir* , vyplňte `volumeMounts` pole v `properties` části definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se ze dvou kontejnerů, z nichž každý je připojen ke svazku *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklady nasazení skupiny kontejnerů najdete v tématu [nasazení skupiny s více kontejnery pomocí Správce prostředků šablony](container-instances-multi-container-group.md) a [nasazení více kontejnerových skupin pomocí souboru YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Další kroky

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku Gitrepo nepodporují v Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojit tajný svazek v Azure Container Instances](container-instances-volume-secret.md)