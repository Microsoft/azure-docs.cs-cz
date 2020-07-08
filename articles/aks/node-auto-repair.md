---
title: Automatické opravy uzlů AKS (Azure Kubernetes Service)
description: Přečtěte si o funkcích automatických oprav uzlů a o tom, jak AKS opravuje poškozené pracovní uzly.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735621"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatická oprava uzlu služby Azure Kubernetes Service (AKS)

AKS nepřetržitě kontroluje stav pracovních uzlů a provádí automatickou opravu uzlů, pokud se stanou stavem není v pořádku. Tento dokument informuje operátory o tom, jak se chová funkce Automatické opravy uzlů. Kromě oprav AKS provádí platforma virtuálních počítačů Azure [údržbu Virtual Machines, u][vm-updates] kterých dochází i k problémům. AKS a virtuální počítače Azure spolupracují na minimalizaci výpadků služeb pro clustery.

> [!Important]
> Funkce Automatické opravy uzlů se v současné době pro fondy uzlů Windows serveru nepodporují.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS kontroluje uzly, které nejsou v pořádku

> [!Note]
> AKS provádí opravnou akci na uzlech s uživatelským účtem **AKS-** re.

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

Automatická oprava je ve výchozím nastavení podporovaná pro clustery s typem sady virtuálních počítačů **Virtual Machine Scale Sets**. Pokud je zjištěno, že uzel není v pořádku na základě výše uvedených pravidel, AKS restartuje uzel po 10 po sobě jdoucích nestavových minutách. Pokud uzly zůstanou po počáteční operaci opravy chybné, prošetří je další náprava AKS inženýry.
  
Pokud během kontroly stavu není v pořádku víc uzlů, každý uzel se opraví jednotlivě před zahájením další opravy.

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
