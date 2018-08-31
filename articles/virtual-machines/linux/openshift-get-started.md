---
title: OpenShift v Azure – přehled | Dokumentace Microsoftu
description: Přehled OpenShift v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190339"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřené a rozšiřitelné kontejneru aplikační platforma, která přináší Docker a Kubernetes pro podniky.  

OpenShift zahrnuje pro správu a Orchestrace kontejnerů Kubernetes. Přidá a operace zaměřené na vývojáře nástroje, které umožňují:

- Rychlý vývoj aplikací.
- Snadné nasazení a škálování.
- Dlouhodobé životní cyklus údržby pro týmy a aplikace.

Nejsou k dispozici více verzí OpenShift:

- OKD (dříve OpenShift Origin)
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Čtyři verzí popsaná v tomto článku pouze dva jsou dostupné pro zákazníky, kteří nasazují v Azure: OpenShift Origin a OpenShift Container Platform.

## <a name="okd-formerly-openshift-origin"></a>OKD (dříve OpenShift Origin)

Je OKD [open source](https://www.okd.io/) nadřazený projekt OpenShift, která je podporovaná komunitou. OKD můžete nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform je připravená pro podnikové [komerční verze](https://www.openshift.com) z a podporuje Red Hat. S touto verzí zákazníci zakoupit nezbytná oprávnění pro OpenShift Container Platform a zodpovídají za instalaci a správu celé infrastruktury.

Protože zákazníkům "vlastní" celou platformu, jejich ji nainstalujte ve svém místním datovém centru, nebo ve veřejném cloudu (jako je například Azure, AWS a Google).

## <a name="openshift-online"></a>OpenShift Online

Online je Red Hat spravovaná *víceklientské* OpenShift, který používá kontejnerovou platformu. Red Hat spravuje všechny základní infrastruktury (třeba virtuální počítače, OpenShift clusteru, sítě a úložiště). 

S touto verzí se zákazník nasadí kontejnery, ale nemá žádnou kontrolu nad hostitele spouštět kontejnery. Protože Online je víceklientská, kontejnery, může být umístěna na stejného hostitele virtuálního počítače jako kontejnery od ostatních zákazníků. Náklady se děje u každého kontejneru.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Vyhrazené je Red Hat spravovaná *jednoho tenanta* OpenShift, který používá kontejnerovou platformu. Red Hat spravuje všechny základní infrastruktury (virtuální počítače, OpenShift clusteru, sítě, úložiště atd.). Cluster je specifická pro jednoho zákazníka a spouští ve veřejném cloudu (jako je AWS a Google pomocí Azure už v rané fázi 2018). Počáteční cluster obsahuje čtyři uzly aplikace pro 48,000 za rok (placené ještě před zahájením).

## <a name="next-steps"></a>Další postup

- [Konfigurace běžných požadavků pro OpenShift v Azure](./openshift-prerequisites.md)
- [Nasazení Openshiftu Origin v Azure](./openshift-origin.md)
- [Nasazení OpenShift Container Platform v Azure](./openshift-container-platform.md)
- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení Openshiftu](./openshift-troubleshooting.md)
