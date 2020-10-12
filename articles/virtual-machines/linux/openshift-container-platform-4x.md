---
title: Nasazení OpenShift kontejneru Platform 4. x v Azure
description: Nasaďte OpenShift kontejner Platform 4. x v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373534"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Nasazení OpenShift kontejneru Platform 4. x v Azure

Nasazení OpenShift Container Platform (OCP) 4,2 se teď podporuje v Azure prostřednictvím modelu infrastruktury Installer-Provisioned (IPI).  Cílová stránka pro vyzkoušení OpenShift 4 je [Try.OpenShift.com](https://try.openshift.com/). Pokud chcete nainstalovat OCP 4,2 v Azure, přejděte na stránku [Správce clusterů Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Pro přístup k tomuto webu se vyžadují přihlašovací údaje Red Hat.


## <a name="notes"></a>Poznámky 

 - K instalaci a spuštění aktualizace OCP 4. x v Azure je nutný instanční objekt Azure Active Directory (AAD).
     - SP musí mít udělené oprávnění API pro **Application. OwnedBy** pro Azure Active Directory Graph.
     - Správce klienta AAD musí udělit souhlas správce, aby toto oprávnění API vstoupilo v platnost.
     - Do předplatného musí být udělena role **Správce přístupu** pro **přispěvatele** a uživatele.
 - Model instalace pro OCP 4. x je jiný než 3. x a pro nasazení OCP 4. x v Azure nejsou k dispozici žádné šablony Azure Resource Manager.
 - Pokud se během procesu instalace vyskytnou problémy, obraťte se na příslušnou společnost (Microsoft nebo Red Hat).

| Popis problému | Kontaktní bod |
|-------------------|---------------|
| Problémy specifické pro Azure (AAD, SP, předplatné Azure atd.)                              | Partnerský vztah Microsoftu |
| Problémy specifické pro OpenShift (selhání instalace/chyby, předplatné Red Hat atd.) |  Red Hat  |




## <a name="next-steps"></a>Další kroky

- [Začínáme s kontejnerovou platformou OpenShift](https://docs.openshift.com)
