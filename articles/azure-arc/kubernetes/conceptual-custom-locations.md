---
title: Vlastní umístění – Kubernetes s povoleným obloukem Azure
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled možností vlastních umístění s povoleným Kubernetesem Azure ARC.
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450975"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Vlastní umístění nad Kubernetes s povoleným ARC Azure

Jako rozšíření konstrukce umístění Azure poskytuje *vlastní umístění* způsob, jak můžou správci klientů používat clustery Kubernetes s povoleným obloukem Azure jako cílová umístění pro nasazení instancí služeb Azure. Mezi příklady prostředků Azure patří spravovaná instance SQL ARC a s povoleným PostgreSQLm rozšířením Azure ARC.

Podobně jako u umístění Azure můžou koncoví uživatelé v rámci tenanta s přístupem k vlastním umístěním nasazovat prostředky, které používají soukromý výpočetní výkon své společnosti.

[![Vrstvy ](./media/conceptual-arc-platform-layers.png) platformy ARC](./media/conceptual-arc-platform-layers.png#lightbox)

Můžete vizualizovat vlastní umístění jako abstraktní vrstvu nad clusterem Kubernetes s povoleným rozšířením Azure, připojením clusteru a rozšířeními clusteru. Vlastní umístění vytvoří podrobné [RoleBindings a ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) nutné pro další služby Azure pro přístup ke clusteru. Tyto další služby Azure vyžadují přístup clusteru ke správě prostředků, které chce zákazník nasadit na svých clusterech.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

Když správce povolí v clusteru funkci vlastní umístění, vytvoří se v clusteru ClusterRoleBinding a autorizuje se aplikace Azure AD, kterou používá poskytovatel prostředků vlastních umístění (RP). Po autorizaci můžou vlastní umístění RP vytvořit ClusterRoleBindings nebo RoleBindings, které potřebuje jiný Azure RPs pro vytváření vlastních prostředků v tomto clusteru. Rozšíření clusteru nainstalovaná v clusteru určují seznam RPs pro autorizaci.

[![Použít vlastní umístění ](./media/conceptual-custom-locations-usage.png)](./media/conceptual-custom-locations-usage.png#lightbox)

Když uživatel vytvoří instanci datové služby v clusteru: 
1. Požadavek PUT se pošle na Azure Resource Manager.
1. Požadavek PUT se přepošle na Azure ARC s povoleným Data Services RP. 
1. RP načte `kubeconfig` soubor přidružený ke clusteru Kubernetes s povoleným ARC Azure, na kterém existuje vlastní umístění. 
   * Na vlastní umístění se odkazuje jako `extendedLocation` v původní žádosti o vložení. 
1. Azure ARC s podporou Data Services RP používá `kubeconfig` ke komunikaci s clusterem k vytvoření vlastního prostředku typu Data Services s povoleným ARC Azure v oboru názvů namapovaném na vlastní umístění. 
   * Byl nasazený operátor Data Services ARC Azure s povoleným vytvořením rozšíření clusteru před tím, než vlastní umístění existovalo. 
1. Operátor Data Services s podporou ARC Azure přečte nový vlastní prostředek vytvořený v clusteru a vytvoří řadič dat, který se přeloží na realizaci požadovaného stavu v clusteru. 

Pořadí kroků vytvoření spravované instance SQL a instance PostgreSQL jsou stejné jako pořadí kroků popsaných výše.

## <a name="next-steps"></a>Další kroky

* Pomocí našeho rychlého startu můžete [připojit cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* [Vytvořte vlastní umístění](./custom-locations.md) v clusteru Kubernetes s povoleným ARC Azure.