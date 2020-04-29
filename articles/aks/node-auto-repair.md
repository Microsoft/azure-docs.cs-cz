---
title: Automatické opravy uzlů AKS (Azure Kubernetes Service)
description: Přečtěte si o funkcích automatických oprav uzlů a o tom, jak AKS opravuje poškozené pracovní uzly.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284836"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatická oprava uzlu služby Azure Kubernetes Service (AKS)

AKS nepřetržitě kontroluje stav pracovních uzlů a provádí automatickou opravu uzlů, pokud se stanou stavem není v pořádku. Tato dokumentace popisuje, jak služba Azure Kubernetes Service (AKS) monitoruje pracovní uzly a opravuje poškozené pracovní uzly.  V dokumentaci je potřeba informovat AKS operátory pro chování funkce oprav uzlů. Je také důležité si uvědomit, že platforma Azure [provádí údržbu Virtual Machines, u][vm-updates] kterých dochází k problémům. AKS a Azure spolupracují na minimalizaci výpadků služeb pro vaše clustery.

> [!Important]
> Funkce Automatické opravy uzlů se v současné době pro fondy uzlů Windows serveru nepodporují.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS kontroluje uzly, které nejsou v pořádku

> [!Note]
> AKS provádí opravnou akci na uzlech s uživatelským účtem **AKS-** re.

AKS používá pravidla k určení, jestli je uzel ve stavu není v pořádku a potřebuje opravit. AKS používá následující pravidla k určení, jestli je potřeba Automatická oprava.

* Uzel hlásí stav při následné kontrole po sobě jdoucích **kontrol v rámci** období 10 minut.
* Uzel nehlásí stav do 10 minut.

Pomocí kubectl můžete ručně kontrolovat stav svých uzlů. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak funguje automatická oprava

> [!Note]
> AKS provádí opravnou akci na uzlech s uživatelským účtem **AKS-** re.

Toto chování je pro **Virtual Machine Scale Sets**.  Automatická oprava provede několik kroků k opravě poškozeného uzlu.  Pokud je zjištěno, že uzel není v pořádku, AKS pokusy o několik nápravných kroků.  Kroky se provádějí v tomto pořadí:

1. Jakmile modul runtime kontejneru přestane reagovat po dobu 10 minut, na uzlu se restartuje neúspěšné služby modulu runtime.
2. Pokud uzel není připravený do 10 minut, uzel se restartuje.
3. Pokud uzel není připravený do 30 minut, uzel se znovu naobrázkí.

> [!Note]
> Pokud je více uzlů ve špatném stavu, jsou opraveny po jednom.

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
