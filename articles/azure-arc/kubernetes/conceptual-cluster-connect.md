---
title: Připojení ke clusteru – Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkce připojení clusteru s povoleným Kubernetes ARC Azure.
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450967"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Připojení clusteru k Kubernetes s povoleným ARC Azure

Funkce Kubernetes *cluster Connect* s povolenou službou Azure ARC umožňuje připojení k `apiserver` clusteru bez nutnosti povolit v bráně firewall žádný port pro příchozí spojení. Agent reverzního proxy serveru běžícího na clusteru může bezpečně spustit relaci se službou Azure ARC odchozím způsobem. 

Cluster Connect umožňuje vývojářům přistupovat ke svým clusterům odkudkoli pro interaktivní vývoj a ladění. Také umožňuje uživatelům clusteru a správcům přistupovat ke svým clusterům a spravovat je odkudkoli. Můžete dokonce použít hostované agenty a spouštěče Azure Pipelines, akce GitHubu nebo jakékoli jiné hostované služby CI/CD pro nasazení aplikací do clusterů Prem, aniž by vyžadovali samoobslužné agenty.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

[![Architektura ](./media/conceptual-cluster-connect.png) připojení clusteru](./media/conceptual-cluster-connect.png#lightbox)

Na straně clusteru provede reverzní proxy Agent s názvem `clusterconnect-agent` nasazený jako součást grafu Helm agenta odchozí volání do služby Azure ARC za účelem vytvoření relace.

Když uživatel volá `az connectedk8s proxy` :
1. Binární soubor proxy ARC Azure je stažený a na klientském počítači se vystaví jako proces. 
1. Proxy server Azure ARC načte `kubeconfig` soubor přidružený ke clusteru Kubernetes s povoleným obloukem Azure, na kterém `az connectedk8s proxy` je vyvolaný.
    * Proxy server Azure ARC používá token pro přístup k Azure a název Azure Resource Manager ID volajícího. 
1. `kubeconfig`Soubor uložený na počítači pomocí služby Azure ARC proxy odkazuje na adresu URL serveru na koncový bod v procesu proxy ARC Azure.

Když uživatel odešle požadavek pomocí tohoto `kubeconfig` souboru:
1. Proxy ARC Azure namapuje koncový bod, který obdrží požadavek službě Azure ARC. 
1. Služba Azure ARC pak předá požadavek do `clusterconnect-agent` provozu v clusteru. 
1. Předání `clusterconnect-agent` na žádost `kube-aad-proxy` součásti, která provádí ověřování Azure AD na volající entitě. 
1. Po ověření Azure AD `kube-aad-proxy` používá funkce [zosobnění uživatele](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) Kubernetes k přeposlání žádosti do clusteru `apiserver` .

## <a name="next-steps"></a>Další kroky

* Pomocí našeho rychlého startu můžete [připojit cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* Připojte [se ke clusteru](./cluster-connect.md) bezpečně odkudkoli přes cluster Connect.