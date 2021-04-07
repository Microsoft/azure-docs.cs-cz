---
title: Konfigurace a GitOps – Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkcí GitOps a konfigurací pro Kubernetes s podporou ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery, konfigurace, GitOps
ms.openlocfilehash: a4193783a976f205b46cb8c5df29d4cef9da1d2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022870"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurace a GitOps s povoleným Kubernetes ARC Azure

V souvislosti s Kubernetes je GitOps postup, který deklaruje požadovaný stav konfigurací clusterů Kubernetes (nasazení, obory názvů atd.) v úložišti Git. Tato deklarace je následována nasazením těchto clusterů, které je založené na vyžádání, pomocí operátoru. Úložiště Git může obsahovat:
* YAML – manifesty popisující jakékoli platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd.
* Helm grafy pro nasazení aplikací.

[Tok, oblíbený](https://docs.fluxcd.io/)open source nástroj v GitOps prostoru, se dá nasadit do clusteru Kubernetes, aby se usnadnil tok konfigurací z úložiště Git do clusteru Kubernetes. Tok podporuje nasazení operátoru v oborech názvů clusteru i oboru názvů. Operátor toku nasazený pomocí oboru názvů může nasadit pouze objekty Kubernetes v rámci tohoto konkrétního oboru názvů. Možnost výběru mezi clusterem nebo oborem názvů vám pomůže dosáhnout víceúrovňového vzoru nasazení na stejném clusteru Kubernetes.

## <a name="configurations"></a>Konfigurace

[![Architektura ](./media/conceptual-configurations.png) konfigurací](./media/conceptual-configurations.png#lightbox)

Připojení mezi clusterem a úložištěm Git je vytvořeno jako prostředek konfigurace ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) na Kubernetes prostředku s povoleným ARC Azure (reprezentovaný `Microsoft.Kubernetes/connectedClusters` ) v Azure Resource Manager. 

Vlastnosti prostředku konfigurace se používají k nasazení operátora toku v clusteru s příslušnými parametry, jako je třeba úložiště Git, ze kterého se mají načíst manifesty, a interval dotazování, ve kterém se mají načíst. Data prostředků konfigurace jsou uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.

Služba `config-agent` spuštěná v clusteru zodpovídá za:
* Sleduje se nové nebo aktualizované prostředky konfigurace u prostředku Kubernetes s povoleným ARC Azure.
* Nasazení operátora toku pro sledování úložiště Git pro každý prostředek konfigurace.
* Aplikují se jakékoli aktualizace provedené u libovolného prostředku konfigurace. 

Pro dosažení víceklientské architektury můžete vytvořit více prostředků konfigurace s rozsahem oboru názvů na stejném clusteru Kubernetes s povoleným rozšířením Azure ARC.

> [!NOTE]
> * `config-agent` sleduje nové nebo aktualizované prostředky konfigurace, které jsou k dispozici v prostředku Kubernetes s povoleným ARC. Proto agenti vyžadují připojení k požadovanému stavu, který se má do clusteru připojit. Pokud se agenti nepodaří připojit k Azure, nastane zpoždění při rozšiřování požadovaného stavu do clusteru.
> * Citlivé vstupy zákazníka, jako je soukromý klíč, známý obsah hostitelů, uživatelské jméno HTTPS a token/heslo, se neukládají po dobu více než 48 hodin v rámci povolených služeb Kubernetes ARC Azure. Pokud používáte citlivé vstupy pro konfigurace, převeďte clustery do online režimu, co je to možné.

## <a name="apply-configurations-at-scale"></a>Aplikování konfigurací ve velkém měřítku

Vzhledem k tomu, že Azure Resource Manager spravuje vaše konfigurace, můžete automatizovat vytváření stejné konfigurace ve všech prostředcích Azure ARC s podporou Kubernetes pomocí Azure Policy, a to v oboru předplatného nebo skupiny prostředků. 

Toto v rámci vynuceného navýšení zajišťuje společnou základní konfiguraci (obsahující konfigurace jako ClusterRoleBindings, RoleBindings a NetworkPolicy), která se dá použít v celém loďstvu nebo v inventáři clusterů Kubernetes s povoleným rozšířením Azure ARC.

## <a name="next-steps"></a>Další kroky

* Projděte si náš rychlý Start, který [připojí cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* Už máte připojený cluster Kubernetes ke clusteru Azure ARC? [Vytvořte konfigurace na clusteru Kubernetes s povoleným obloukem](./tutorial-use-gitops-connected-cluster.md).
* Naučte se [používat Azure Policy k aplikování konfigurací se škálováním](./use-azure-policy.md).