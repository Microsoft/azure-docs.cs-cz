---
title: Nasazení kontejnerové platformy OpenShift 4.x v Azure
description: Nasazení platformy OpenShift Container Platform 4.x v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759488"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Nasazení kontejnerové platformy OpenShift 4.x v Azure

Nasazení platformy OpenShift Container Platform (OCP) 4.2 je teď v Azure podporované prostřednictvím modelu Infrastruktury zřízené instalační službou (IPI).  Vstupní stránka pro pokus o OpenShift 4 je [try.openshift.com](https://try.openshift.com/). Chcete-li nainstalovat OCP 4.2 v Azure, navštivte stránku [Správce clusteru Red Hat OpenShift.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Pro přístup k tomuto webu jsou vyžadována pověření red hatu.


## <a name="notes"></a>Poznámky 

 - K instalaci a spuštění OCP 4.x v Azure je potřeba instanční objekt služby Azure Active Directory (AAD)
     - Sp musí být uděleno oprávnění rozhraní API **Application.ReadWrite.OwnedBy** pro Azure Active Directory Graph
     - Správce klienta AAD musí udělit souhlas správce, aby se toto oprávnění rozhraní API projevilo.
     - Sp musí být udělena **přispěvatel a** **správce přístupu uživatelů** role předplatného
 - Model instalace pro OCP 4.x se liší od 3.x a nejsou k dispozici žádné šablony Azure Resource Manager pro nasazení OCP 4.x v Azure
 - Pokud během procesu instalace dojde k problémům, obraťte se na příslušnou společnost (Microsoft nebo Red Hat).

| Popis problému | Kontaktní místo |
|-------------------|---------------|
| Specifické problémy Azure (AAD, SP, předplatné Azure atd.)                              | Microsoft |
| Problémy specifické pro OpenShift (selhání instalace / chyby, předplatné Red Hat atd.) |  Red Hat  |




## <a name="next-steps"></a>Další kroky

- [Začínáme s platformou OpenShift Container Platform](https://docs.openshift.com)
