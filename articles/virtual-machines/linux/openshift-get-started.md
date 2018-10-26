---
title: OpenShift v Azure – přehled | Dokumentace Microsoftu
description: Přehled OpenShift v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
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
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085435"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřené a rozšiřitelné kontejneru aplikační platforma, která přináší Docker a Kubernetes pro podniky.  

OpenShift zahrnuje pro správu a Orchestrace kontejnerů Kubernetes. Přidá zaměřené na vývojáře a zaměřené operace nástroje této povolit:

- Rychlý vývoj aplikací.
- Snadné nasazení a škálování.
- Dlouhodobé životní cyklus údržby pro týmy a aplikace.

Nejsou k dispozici více verzí OpenShift:

- OpenShift Container Platform
- OpenShift v Azure (plně spravovanou OpenShift už v rané fázi CY2019)
- OKD (dříve OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

Pět verzí popsaná v tomto článku pouze dva jsou dnes dostupná pro zákazníky, kteří nasazují v Azure: OpenShift Container Platform a OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform je připravená pro podnikové [komerční verze](https://www.openshift.com) z a podporuje Red Hat. S touto verzí zákazníci zakoupit nezbytná oprávnění pro OpenShift Container Platform a zodpovídají za instalaci a správu celé infrastruktury.

Protože zákazníkům "vlastní" celou platformu, jejich ji nainstalujte ve svém místním datovém centru, nebo ve veřejném cloudu (jako je například Azure, AWS a Google).

## <a name="openshift-on-azure"></a>OpenShift v Azure

OpenShift v Azure je plně spravovaná nabídka služby OpenShift v Azure. Tato služba je společně spravované a podporované společností Microsoft a Red Hat. Cluster se nasadí do předplatného Azure zákazníka. Služba je aktuálně ve verzi Private Preview a bude GA v dřívější CY 2019. Další informace vám poskytneme jako nabídka získá blíž ke zveřejnění obecné dostupnosti

## <a name="okd-formerly-openshift-origin"></a>OKD (dříve OpenShift Origin)

Je OKD [open source](https://www.okd.io/) nadřazený projekt OpenShift, která je podporovaná komunitou. OKD můžete nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Vyhrazené je Red Hat spravovaná *jednoho tenanta* OpenShift, který používá OpenShift Container Platform. Red Hat spravuje všechny základní infrastruktury (virtuální počítače, OpenShift clusteru, sítě, úložiště atd.). Cluster je specifická pro jednoho zákazníka a spouští ve veřejném cloudu (jako je AWS a Google). Počáteční cluster obsahuje čtyři uzly aplikace a všechny poplatky jsou předem roční a placené.

## <a name="openshift-online"></a>OpenShift Online

Online je Red Hat spravovaná *víceklientské* OpenShift, který používá kontejnerovou platformu. Red Hat spravuje všechny základní infrastruktury (třeba virtuální počítače, OpenShift clusteru, sítě a úložiště). 

S touto verzí se zákazník nasadí kontejnery, ale nemá žádnou kontrolu nad hostitele spouštět kontejnery. Protože Online je víceklientská, kontejnery, může být umístěna na stejného hostitele virtuálního počítače jako kontejnery od ostatních zákazníků. Náklady se děje u každého kontejneru.

## <a name="next-steps"></a>Další postup

- [Konfigurace běžných požadavků pro OpenShift v Azure](./openshift-prerequisites.md)
- [Nasazení OpenShift Container Platform v Azure](./openshift-container-platform.md)
- [Nasazení OKD v Azure](./openshift-okd.md)
- [Nasadit OpenShift v Azure stacku](./openshift-azure-stack.md)
- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení Openshiftu](./openshift-troubleshooting.md)
