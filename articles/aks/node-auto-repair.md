---
title: Automatické opravy uzlů AKS (Azure Kubernetes Service)
description: Přečtěte si o funkcích automatických oprav uzlů a o tom, jak AKS opravuje poškozené pracovní uzly.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105200"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatická oprava uzlu služby Azure Kubernetes Service (AKS)

AKS nepřetržitě monitoruje stav pracovních uzlů a provádí automatickou opravu uzlů, pokud se stanou špatnými. Platforma virtuálních počítačů Azure [provádí údržbu][vm-updates] virtuálních počítačů, ve kterých dochází k problémům. 

AKS a virtuální počítače Azure spolupracují na minimalizaci výpadků služeb pro clustery.

V tomto dokumentu se dozvíte, jak se funkce Automatické opravy uzlů chovají pro uzly Windows i Linux. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS kontroluje uzly, které nejsou v pořádku

AKS používá následující pravidla k určení, jestli je uzel v nesprávném stavu a potřebuje opravit: 
* Uzel **hlásí stav po** sobě jdoucích kontrol po sobě jdoucích kontrol v rámci období 10 minut.
* Uzel neoznamuje žádné stavy během 10 minut.

Pomocí kubectl můžete ručně kontrolovat stav svých uzlů.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak funguje automatická oprava

> [!Note]
> AKS inicializuje operace opravy pomocí uživatelského účtu **AKS-** revazovat.

Pokud AKS identifikuje uzel, který není v pořádku, který zůstane v pořádku po dobu 10 minut, AKS provede tyto akce:

1. Restartujte uzel.
1. Pokud není restartování úspěšné, přeobrazte uzel.
1. Pokud se znovu nezdařila bitová kopie, vytvořte a znovu vytvořte image nového uzlu.

V případě, že je Automatická oprava neúspěšná, prošetří vývojáři AKS alternativní opravy. 

Pokud AKS najde během kontroly stavu několik uzlů, které nejsou v pořádku, každý uzel se opraví jednotlivě před zahájením další opravy.

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
