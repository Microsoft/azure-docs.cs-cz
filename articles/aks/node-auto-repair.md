---
title: Automatické opravy uzlů AKS (Azure Kubernetes Service)
description: Přečtěte si o funkcích automatických oprav uzlů a o tom, jak AKS opravuje poškozené pracovní uzly.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89490101"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatická oprava uzlu služby Azure Kubernetes Service (AKS)

AKS nepřetržitě kontroluje stav pracovních uzlů a provádí automatickou opravu uzlů, pokud se stanou stavem není v pořádku. Tento dokument informuje operátory o tom, jak se funkce automatických oprav uzlů chová pro uzly Windows i Linux. Kromě oprav AKS provádí platforma virtuálních počítačů Azure [údržbu Virtual Machines, u][vm-updates] kterých dochází i k problémům. AKS a virtuální počítače Azure spolupracují na minimalizaci výpadků služeb pro clustery.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS kontroluje uzly, které nejsou v pořádku

AKS používá pravidla k určení, jestli je uzel v nesprávném stavu a potřebuje opravit. AKS používá následující pravidla k určení, jestli je potřeba Automatická oprava.

* Uzel hlásí stav při následné kontrole po sobě jdoucích **kontrol v rámci** období 10 minut.
* Uzel nehlásí stav do 10 minut.

Pomocí kubectl můžete ručně kontrolovat stav svých uzlů.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak funguje automatická oprava

> [!Note]
> AKS inicializuje operace opravy pomocí uživatelského účtu **AKS-** revazovat.

Pokud uzel není v pořádku na základě výše uvedených pravidel a zůstane v pořádku po dobu 10 po sobě jdoucích minut, budou provedeny následující akce.

1. Restartovat uzel
1. Pokud není restartování úspěšné, přeobrazte uzel
1. Pokud se znovu nezdařila bitová kopie, vytvořte nový uzel a přeobrazte ho.

Pokud žádná z těchto akcí není úspěšná, jsou další nápravy prozkoumány AKS technici. Pokud během kontroly stavu není v pořádku víc uzlů, každý uzel se opraví jednotlivě před zahájením další opravy.

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
