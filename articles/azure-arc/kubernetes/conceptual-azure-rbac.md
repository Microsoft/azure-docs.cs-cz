---
title: Azure RBAC – Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkce Azure RBAC na Kubernetes s povoleným ARC Azure.
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450968"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC v Kubernetes s povoleným ARC Azure

Kubernetes [ClusterRoleBinding a](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) typy objektů RoleBinding usnadňují definování autorizace v Kubernetes nativně. Pomocí služby Azure RBAC můžete pomocí Azure Active Directory (Azure AD) a přiřazení rolí v Azure řídit kontroly autorizace v clusteru.

Díky této funkci se teď pro váš cluster Kubernetes s povoleným použitím Azure ARC použijí všechny výhody přiřazení rolí Azure, jako jsou protokoly aktivit zobrazující všechny změny v rámci Azure RBAC.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architektura – Azure RBAC v Kubernetes s povoleným ARC Azure

[![Architektura ](./media/conceptual-azure-rbac.png) Azure RBAC](./media/conceptual-azure-rbac.png#lightbox)

Aby bylo možné směrovat všechny kontroly přístupu k autorizaci na autorizační službu v Azure, je na clusteru nasazen server Webhooku ([Guard](https://github.com/appscode/guard)).

`apiserver`Cluster je nakonfigurovaný tak, aby používal [ověřování tokenu Webhooku](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) a [autorizaci Webhooku](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) , takže se `TokenAccessReview` `SubjectAccessReview` požadavky na server Webhooku Guard směrují a požadavky se na ně směrují. `TokenAccessReview`Žádosti a `SubjectAccessReview` se spouštějí požadavky na prostředky Kubernetes odeslané do `apiserver` .

Guard pak provede `checkAccess` volání autorizační služby v Azure a zjistí, jestli má žádající entita Azure AD přístup k prostředku, ke kterému se vztahuje. 

Pokud role v přiřazení, která povoluje tento přístup, existuje, `allowed` pošle se odpověď z ochrany autorizační služby. Guard zase pošle `allowed` odpověď na `apiserver` , a umožňuje volající entitě přístup k požadovanému prostředku Kubernetes.


Pokud role v přiřazení umožňující tento přístup neexistuje, `denied` pošle se z autorizační služby odpověď, která chrání. Guard pošle `denied` odpověď na `apiserver` , která volá entitu Called a 403 zakázáno u požadovaného prostředku.

## <a name="next-steps"></a>Další kroky

* Pomocí našeho rychlého startu můžete [připojit cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* [Nastavte službu Azure RBAC](./azure-rbac.md) na clusteru Kubernetes clusteru s povoleným obloukem Azure.