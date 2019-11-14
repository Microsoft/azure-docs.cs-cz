---
title: Nasazení OpenShift kontejneru Platform 4. x v Azure
description: Nasaďte OpenShift kontejner Platform 4. x v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035441"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Nasazení OpenShift kontejneru Platform 4. x v Azure

Nasazení OpenShift Container Platform (OCP) 4,2 se teď v Azure podporuje přes model IPI (zřízené Instalační služba).  Cílová stránka pro vyzkoušení OpenShift 4 je [Try.OpenShift.com](https://try.openshift.com/). Pokud chcete nainstalovat OCP 4,2 v Azure, přejděte na stránku [Správce clusterů Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Pro přístup k tomuto webu se vyžadují přihlašovací údaje Red Hat.


## <a name="notes"></a>Poznámky: 

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
