---
title: Konfigurace a GitOps – Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkcí GitOps a konfigurací pro Kubernetes s podporou ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery, konfigurace, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561478"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurace a GitOps s povoleným Kubernetes ARC Azure

V souvislosti s Kubernetes je GitOps postup, který deklaruje požadovaný stav konfigurací clusterů Kubernetes (nasazení, obory názvů atd.) v úložišti Git. Tato deklarace je následována nasazením těchto clusterů, které je založené na vyžádání, pomocí operátoru. Úložiště Git může obsahovat:
* YAML – manifesty popisující jakékoli platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd.
* Helm grafy pro nasazení aplikací.

[Tok, oblíbený](https://docs.fluxcd.io/)open source nástroj v GitOps prostoru, se dá nasadit do clusteru Kubernetes, aby se usnadnil průběh konfigurací z úložiště Git do clusteru Kubernetes. Tok podporuje nasazení operátoru v oborech názvů clusteru i oboru názvů. Operátor toku nasazený pomocí oboru názvů může nasadit pouze objekty Kubernetes v rámci tohoto konkrétního oboru názvů. Možnost výběru mezi clusterem nebo oborem názvů vám pomůže dosáhnout víceúrovňového vzoru nasazení na stejném clusteru Kubernetes.

## <a name="configurations"></a>Konfigurace

[![Architektura ](./media/conceptual-configurations.png) konfigurací](./media/conceptual-configurations.png#lightbox)

Připojení mezi clusterem a úložištěm Git se vytvoří jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` prostředek rozšíření na Kubernetes prostředku s povoleným ARC Azure (reprezentovaný `Microsoft.Kubernetes/connectedClusters` ) v Azure Resource Manager. 

`sourceControlConfiguration`Vlastnosti prostředku se používají k nasazení operátora toku v clusteru s příslušnými parametry, jako je třeba úložiště Git, ze kterého se mají načíst manifesty, a interval dotazování, ve kterém se mají načíst. `sourceControlConfiguration`Data jsou uložena v zašifrovaném umístění v databázi Azure Cosmos DB, aby se zajistila důvěrnost dat.

Služba `config-agent` spuštěná v clusteru zodpovídá za:
* Sledování nových nebo aktualizovaných `sourceControlConfiguration` prostředků rozšíření u prostředku Kubernetes s povoleným ARC Azure
* Nasazení operátora toku pro sledování úložiště Git pro každý z nich `sourceControlConfiguration` .
* Aplikují se jakékoli aktualizace, které jste udělali `sourceControlConfiguration` . 

Pro dosažení víceklientské architektury můžete vytvořit více prostředků s oborem názvů `sourceControlConfiguration` na jednom clusteru Kubernetes s povoleným ARC Azure.

> [!NOTE]
> * Vzhledem k `config-agent` tomu, že monitory pro nové nebo aktualizované `sourceControlConfiguration` prostředky rozšíření budou k dispozici na prostředku Kubernetes s povoleným ARC Azure, agenti vyžadují připojení k požadovanému stavu, aby se mohl do clusteru připojit. Kdykoli se agenti nemohou připojit k Azure, nejsou v clusteru aplikovány požadované vlastnosti stavu deklarované u `sourceControlConfiguration` prostředku v Azure Resource Manager.
> * Citlivé vstupy zákazníka, jako je soukromý klíč, známý obsah hostitelů, uživatelské jméno HTTPS a token/heslo, se neukládají po dobu více než 48 hodin v rámci povolených služeb Kubernetes ARC Azure. Pokud používáte citlivé vstupy pro konfigurace, doporučujeme převést clustery do online režimu.

## <a name="apply-configurations-at-scale"></a>Aplikování konfigurací ve velkém měřítku

Vzhledem k tomu, že Azure Resource Manager spravuje vaše konfigurace, můžete pomocí nástroje Azure Policy automatizovat vytváření stejné konfigurace ve všech prostředcích Azure ARC s povoleným Kubernetes prostředky v oboru předplatného nebo skupiny prostředků. 

Tato vynucená ochrana zajišťuje, že se společná základní konfigurace (obsahující konfigurace jako ClusterRoleBindings, RoleBindings a NetworkPolicy) dá použít v celém loďstvu nebo v inventáři clusterů Kubernetes s povoleným rozšířením Azure ARC.

## <a name="next-steps"></a>Další kroky

* [Připojit cluster ke službě Azure ARC](./connect-cluster.md)
* [Vytvoření konfigurací na clusteru Kubernetes s povoleným obloukem](./use-gitops-connected-cluster.md)
* [Použití Azure Policy k aplikování konfigurací ve velkém měřítku](./use-azure-policy.md)