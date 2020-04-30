---
title: Nasazení OpenShift kontejneru Platform 4. x v Azure
description: Nasaďte OpenShift kontejner Platform 4. x v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759488"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Nasazení OpenShift kontejneru Platform 4. x v Azure

Nasazení OpenShift Container Platform (OCP) 4,2 se teď v Azure podporuje přes model IPI (zřízené Instalační služba).  Cílová stránka pro vyzkoušení OpenShift 4 je [Try.OpenShift.com](https://try.openshift.com/). Pokud chcete nainstalovat OCP 4,2 v Azure, přejděte na stránku [Správce clusterů Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Pro přístup k tomuto webu se vyžadují přihlašovací údaje Red Hat.


## <a name="notes"></a>Poznámky 

 - K instalaci a spuštění aktualizace OCP 4. x v Azure je nutný instanční objekt Azure Active Directory (AAD).
     - SP musí mít udělené oprávnění API pro **Application. OwnedBy** pro Azure Active Directory Graph.
     - Správce klienta AAD musí udělit souhlas správce, aby toto oprávnění API vstoupilo v platnost.
     - Do předplatného musí být udělena role **Správce přístupu** pro **přispěvatele** a uživatele.
 - Model instalace pro OCP 4. x je jiný než 3. x a pro nasazení OCP 4. x v Azure nejsou k dispozici žádné šablony Azure Resource Manager.
 - Pokud se během procesu instalace vyskytnou problémy, obraťte se na příslušnou společnost (Microsoft nebo Red Hat).

| Popis problému | Kontaktní bod |
|-------------------|---------------|
| Problémy specifické pro Azure (AAD, SP, předplatné Azure atd.)                              | Microsoft |
| Problémy specifické pro OpenShift (selhání instalace/chyby, předplatné Red Hat atd.) |  Red Hat  |




## <a name="next-steps"></a>Další kroky

- [Začínáme s kontejnerovou platformou OpenShift](https://docs.openshift.com)
