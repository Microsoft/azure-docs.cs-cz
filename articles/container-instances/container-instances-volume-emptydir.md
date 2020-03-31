---
title: Připojení svazku emptyDir do skupiny kontejnerů
description: Zjistěte, jak připojit svazek emptyDir ke sdílení dat mezi kontejnery ve skupině kontejnerů v instanci kontejneru Azure
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117752"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Připojení svazku emptyDir v instancích kontejnerů Azure

Zjistěte, jak připojit svazek *emptyDir* ke sdílení dat mezi kontejnery ve skupině kontejnerů v Azure Container Instances. Použijte *emptyDir* svazky jako dočasné mezipaměti pro vaše kontejnerizované úlohy.

> [!NOTE]
> Připojení svazku *emptyDir* je aktuálně omezeno na kontejnery Linuxu. Zatímco pracujeme na tom, aby všechny funkce do kontejnerů Systému Windows, můžete najít aktuální rozdíly platformy v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir svazek

Svazek *emptyDir* poskytuje zapisovatelný adresář přístupný každému kontejneru ve skupině kontejnerů. Kontejnery ve skupině můžete číst a zapisovat stejné soubory ve svazku a lze jej připojit pomocí stejné nebo různé cesty v každém kontejneru.

Některé příklady používá pro prázdný svazek *Dir:*

* Mezera při poškrábání
* Vytváření kontrolních bodů během dlouhotrvajících úloh
* Ukládat data načtená kontejnerem sajdkára a obsluhovaná kontejnerem aplikace

Data ve svazku *emptyDir* je trvalé prostřednictvím kontejneru selhání. Kontejnery, které jsou restartovány, však není zaručeno, že zachovat data ve svazku *emptyDir.* Pokud zastavíte skupinu kontejnerů, svazek *emptyDir* nebude trvalý.

Maximální velikost linuxového svazku *emptyDir* je 50 GB.

## <a name="mount-an-emptydir-volume"></a>Připojení prázdného svazku Dir

Chcete-li připojit prázdný svazek Dir v instanci kontejneru, můžete nasadit pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), [souboru YAML](container-instances-reference-yaml.md)nebo jiných programových metod pro nasazení skupiny kontejnerů.

Nejprve naplňte `volumes` pole v `properties` části skupiny kontejnerů souboru. Dále pro každý kontejner ve skupině kontejnerů, ve kterém chcete připojit `volumeMounts` svazek `properties` *emptyDir,* naplňte pole v části definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se ze dvou kontejnerů, z nichž každý připojí svazek *emptyDir:*

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Příklady nasazení skupiny kontejnerů najdete [v tématu Nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](container-instances-multi-container-group.md) a nasazení [skupiny více kontejnerů pomocí souboru YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Další kroky

Zjistěte, jak připojit další typy svazků v instanci kontejneru Azure:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku gitRepo v instanci kontejneru Azure](container-instances-volume-gitrepo.md)
* [Připojení tajného svazku v instancích kontejnerů Azure](container-instances-volume-secret.md)