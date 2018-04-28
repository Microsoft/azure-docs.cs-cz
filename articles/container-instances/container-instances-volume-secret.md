---
title: Připojení tajný svazku v Azure kontejner instancí
description: Zjistěte, jak připojit tajný svazek k ukládání informací pro přístup k vaší kontejner instancí
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: e20dc3b37bc766b2a8a352eb62c03f5e2ee14c52
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Připojení tajný svazku v Azure kontejner instancí

Zjistěte, jak se připojit *tajný klíč* svazku v vaše instance kontejner pro ukládání a načítání citlivé informace podle kontejnerů ve skupinách kontejneru.

> [!NOTE]
> Připojení *tajný klíč* svazek je aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>tajný svazku

Můžete použít *tajný klíč* svazku obsahují citlivé informace, které kontejnery v kontejneru skupiny. *Tajný klíč* svazku jsou soubory ve svazku, které přístup kontejnery ve vaší skupině kontejneru zadaný tajných klíčů. Pomocí tajných klíčů v *tajný klíč* svazku, se můžete vyhnout umístěním citlivých dat jako klíče SSH nebo přihlašovací údaje databáze v kódu aplikace.

Všechny *tajný klíč* svazky jsou zajišťované [tmpfs][tmpfs], RAM záložních souborů; jejich obsah se nikdy zapisují na stálé úložiště.

## <a name="mount-a-secret-volume"></a>Připojení tajný svazku

Připojit *tajný klíč* svazku v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplnit `volumes` pole ve skupině kontejneru `properties` část šablony. Dále pro každý kontejner ve skupině kontejner, ve kterém byste chtěli připojit *tajný klíč* svazku, naplnit `volumeMounts` pole v `properties` části definici kontejneru.

Například následující šablony Resource Manageru vytvoří skupinu kontejneru, který se skládá z jednoho kontejneru. Připojení zařízení kontejneru *tajný klíč* svazku, který se skládá z dva tajné klíče s kódováním Base64.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Příklad nasazení kontejneru instance pomocí šablony Azure Resource Manager, najdete v sekci [nasazení skupiny více kontejnerů v Azure kontejner instancí](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit jiné typy svazku v Azure kontejner instancí:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojení svazku gitRepo v Azure kontejner instancí](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs