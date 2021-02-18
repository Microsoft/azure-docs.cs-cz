---
title: Konfigurace a GitOps – Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkcí GitOps a konfigurací pro Kubernetes s podporou ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery, konfigurace, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652543"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurace a GitOps s povoleným Kubernetes ARC Azure

V souvislosti s Kubernetes je GitOps postup, který deklaruje požadovaný stav konfigurací clusterů Kubernetes (nasazení, obory názvů atd.) v úložišti Git. Tato deklarace je následována nasazením těchto clusterů, které je založené na vyžádání, pomocí operátoru. Úložiště Git může obsahovat:
* YAML – manifesty popisující jakékoli platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd.
* Helm grafy pro nasazení aplikací.

[Tok, oblíbený](https://docs.fluxcd.io/)open source nástroj v GitOps prostoru, se dá nasadit do clusteru Kubernetes, aby se usnadnil tok konfigurací z úložiště Git do clusteru Kubernetes. Tok podporuje nasazení operátoru v oborech názvů clusteru i oboru názvů. Operátor toku nasazený pomocí oboru názvů může nasadit pouze objekty Kubernetes v rámci tohoto konkrétního oboru názvů. Možnost výběru mezi clusterem nebo oborem názvů vám pomůže dosáhnout víceúrovňového vzoru nasazení na stejném clusteru Kubernetes.

## <a name="configurations"></a>Konfigurace

[![Architektura ](./media/conceptual-configurations.png) konfigurací](./media/conceptual-configurations.png#lightbox)

Připojení mezi clusterem a úložištěm Git se vytvoří jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` prostředek rozšíření na Kubernetes prostředku s povoleným ARC Azure (reprezentovaný `Microsoft.Kubernetes/connectedClusters` ) v Azure Resource Manager. 

`sourceControlConfiguration`Vlastnosti prostředku se používají k nasazení operátora toku v clusteru s příslušnými parametry, jako je třeba úložiště Git, ze kterého se mají načíst manifesty, a interval dotazování, ve kterém se mají načíst. `sourceControlConfiguration`Data jsou uložena v Azure Cosmos DB databázi zašifrovaná a v klidovém stavu, aby se zajistila důvěrnost dat.

Služba `config-agent` spuštěná v clusteru zodpovídá za:
* Sledování nových nebo aktualizovaných `sourceControlConfiguration` prostředků rozšíření u prostředku Kubernetes s povoleným ARC Azure
* Nasazení operátora toku pro sledování úložiště Git pro každý z nich `sourceControlConfiguration` .
* Aplikují se jakékoli aktualizace, které jste udělali `sourceControlConfiguration` . 

Pro dosažení víceklientské architektury můžete vytvořit více prostředků s oborem názvů `sourceControlConfiguration` na jednom clusteru Kubernetes s povoleným ARC Azure.

> [!NOTE]
> * `config-agent` nepřetržitě monitoruje nové nebo aktualizované `sourceControlConfiguration` prostředky rozšíření dostupné v prostředku Kubernetes s povoleným obloukem Azure ARC. Proto agenti vyžadují konzistentní připojení pro vyžádání vlastností požadovaných stavů do clusteru. Pokud se agenti nepodaří připojit k Azure, požadovaný stav se v clusteru nepoužije.
> * Citlivé vstupy zákazníka, jako je soukromý klíč, známý obsah hostitelů, uživatelské jméno HTTPS a token nebo heslo, se ukládají až 48 hodin v rámci povolených služeb Kubernetes ARC Azure. Pokud používáte citlivé vstupy pro konfigurace, převeďte clustery do online režimu, co je to možné.

## <a name="apply-configurations-at-scale"></a>Aplikování konfigurací ve velkém měřítku

Vzhledem k tomu, že Azure Resource Manager spravuje vaše konfigurace, můžete automatizovat vytváření stejné konfigurace ve všech prostředcích Azure ARC s podporou Kubernetes pomocí Azure Policy, a to v oboru předplatného nebo skupiny prostředků. 

Toto v rámci vynuceného navýšení zajišťuje společnou základní konfiguraci (obsahující konfigurace jako ClusterRoleBindings, RoleBindings a NetworkPolicy), která se dá použít v celém loďstvu nebo v inventáři clusterů Kubernetes s povoleným rozšířením Azure ARC.

## <a name="next-steps"></a>Další kroky

* [Připojit cluster ke službě Azure ARC](./connect-cluster.md)
* [Vytvoření konfigurací na clusteru Kubernetes s povoleným obloukem](./use-gitops-connected-cluster.md)
* [Použití Azure Policy k aplikování konfigurací ve velkém měřítku](./use-azure-policy.md)