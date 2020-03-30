---
title: Automatická oprava uzlů služby Azure Kubernetes Service (AKS)
description: Přečtěte si o funkcích automatické opravy uzlů a o tom, jak AKS opravuje poškozené pracovní uzly.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284836"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatická oprava uzlu Azure Kubernetes Service (AKS)

AKS průběžně kontroluje stav pracovních uzlů a provádí automatickou opravu uzlů, pokud se stanou nefunkční. Tato dokumentace popisuje, jak služba Azure Kubernetes Service (AKS) monitoruje pracovní uzly a opravuje nefunkční pracovní uzly.  V dokumentaci je informovat operátory AKS o chování funkce opravy uzlu. Je také důležité si uvědomit, že platforma Azure [provádí údržbu na virtuálních počítačích,][vm-updates] které mají problémy. AKS a Azure spolupracují na minimalizaci přerušení služeb pro vaše clustery.

> [!Important]
> Funkce automatické opravy uzlů není aktuálně podporována pro fondy uzlů systému Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS kontroluje uzly není v pořádku

> [!Note]
> AKS provede opravu na uzlech s **uživatelským účtem aks-remediator**.

AKS používá pravidla k určení, pokud uzel je stav není v pořádku a potřebuje opravu. AKS používá následující pravidla k určení, zda je potřeba automatickou opravu.

* Uzel hlásí stav **NotReady** při po sobě jdoucích kontrolách v časovém rámci 10 minut
* Uzel nehlásí stav do 10 minut

Můžete ručně zkontrolovat stav uzlů s kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak funguje automatická oprava

> [!Note]
> AKS provede opravu na uzlech s **uživatelským účtem aks-remediator**.

Toto chování je pro **škálovací sady virtuálních strojů**.  Automatická oprava trvá několik kroků k opravě poškozeného uzlu.  Pokud je uzel určen jako nefunkční, AKS se pokusí několik kroků nápravy.  Kroky jsou prováděny v tomto pořadí:

1. Po kontejneru runtime přestane reagovat po dobu 10 minut, selhání runtime služby jsou restartovány na uzlu.
2. Pokud uzel není připraven do 10 minut, uzel je restartován.
3. Pokud uzel není připraven do 30 minut, uzel je znovu zobrazen.

> [!Note]
> Pokud více uzlů není v pořádku, jsou opraveny jeden po druhém

## <a name="next-steps"></a>Další kroky

Pomocí [zón dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost s úlohami clusteru AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
