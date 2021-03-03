---
title: Upgrade agentů Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Upgrady agenta řízení pro Kubernetes s povoleným rozšířením Azure ARC
keywords: Kubernetes, oblouk, Azure, K8s, kontejnery, agent, upgrade
ms.openlocfilehash: 3a5355269db4ccfc91612f661b464556b22c1d35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662266"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Upgrade agentů Kubernetes s povoleným ARC Azure

Kubernetes s povolenou funkcí ARC pro Azure poskytuje svým agentům možnosti automatického upgradu a ručního upgradu. Pokud použijete zakázat automatický upgrade a místo toho spoléhat na ruční upgrade, platí zásady podpory verzí pro agenty ARC a podkladový cluster Kubernetes.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Zapnout nebo vypnout automatický upgrade při připojování clusteru ke službě Azure ARC

Kubernetes s povolenou funkcí ARC Azure poskytuje svým agentům možnosti automatického upgradu.

Následující příkaz připojí cluster ke službě Azure ARC s **povoleným** automatickým upgradem:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Když je povolený automatický upgrade, agent dotazuje službu Azure hourly na dostupnost novější verze agentů. Pokud agent najde dostupnou novější verzi, aktivuje upgrade grafu Helm pro agenty Azure ARC.

Pokud chcete automatický upgrade odhlásit, zadejte `--disable-auto-upgrade` parametr při připojování clusteru ke službě Azure ARC. Následující příkaz připojí cluster ke službě Azure Arc se **zakázaným** automatickým upgradem:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Pokud máte v plánu zakázat automatický upgrade, přečtěte si prosím [zásady podpory verzí](#version-support-policy) pro Kubernetes s povoleným rozšířením Azure ARC.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Zapnout nebo vypnout automatický upgrade po připojení clusteru ke službě Azure ARC

Po připojení clusteru ke službě Azure ARC můžete zapnout funkci automatického upgradu pomocí `az connectedk8s update` příkazu, jak je znázorněno níže:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Ruční upgrade agentů

Pokud jste zakázali automatický upgrade agentů, můžete ručně zahájit upgrade pro tyto agenty pomocí `az connectedk8s upgrade` příkazu, jak je znázorněno níže:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Kubernetes s povoleným Azure ARC následuje po standardním [schématu sémantických verzí](https://semver.org/) `MAJOR.MINOR.PATCH` pro správu verzí svých agentů. 

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* **Hlavní verze** se změní, když dojde k nekompatibilním aktualizacím rozhraní API nebo ke zpětné kompatibilitě.
* **Vedlejší verze** se změní, když jsou změny funkčnosti zpětně kompatibilní s ostatními podverzemi.
* **Opravy verzí** se mění, když jsou provedeny zpětně kompatibilní opravy chyb.

## <a name="version-support-policy"></a>Zásady podpory verze

Když vytváříte problémy podpory, postupy Kubernetes pro Azure ARC povolují následující zásady podpory:

* Agenti Kubernetes s povoleným Azure ARC mají okno podpory "N-2", kde N je nejnovější dílčí verze agentů. 
  * Pokud například Azure ARC s podporou Kubernetes zavádí 0.28. a Today, verze 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e a 0.26. f, jsou podporovány pomocí ARC Azure.

* Clustery Kubernetes připojující se ke službě Azure ARC mají okno podpory "N-2", kde N je nejnovější stabilní dílčí vydaná verze [nadřazeného Kubernetesu](https://github.com/kubernetes/kubernetes/releases). 
  * Například pokud Kubernetes zavádí hodnotu 1,20. a Today, verze 1,20. a, 1,20. b, 1.19. c, 1.19. d, 1.18. e a 1.18. f, jsou podporovány.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Jak často mají Kubernetes k dispozici vydání dílčí verze s povoleným ARC Azure?

Jedna podverze agentů Azure ARC s povoleným Kubernetes je vydaná přibližně jednou za měsíc.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Co se stane, když používám verzi agenta nebo verzi Kubernetes mimo oficiální okno podpory?

' Mimo podporu ' znamená, že verze, které jsou spuštěny, jsou mimo podporované verze agentů a Kubernetes clusterů s podporou "N-2". Pokud chcete pokračovat v potížích s podporou, budete vyzváni k upgradu clusteru a agentů na podporovanou verzi.

## <a name="next-steps"></a>Další kroky

* [Připojit cluster ke službě Azure ARC](./connect-cluster.md)
* [Vytvoření konfigurací na clusteru Kubernetes s povoleným obloukem](./use-gitops-connected-cluster.md)
* [Použití Azure Policy k aplikování konfigurací ve velkém měřítku](./use-azure-policy.md)