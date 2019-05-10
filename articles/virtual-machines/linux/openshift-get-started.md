---
title: OpenShift v Azure – přehled | Dokumentace Microsoftu
description: Přehled OpenShift v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c8ebd10544331d3b7d887124542fe4ee2753c714
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233138"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřené a rozšiřitelné kontejneru aplikační platforma, která přináší Docker a Kubernetes pro podniky.  

OpenShift zahrnuje pro správu a Orchestrace kontejnerů Kubernetes. Přidá zaměřené na vývojáře a zaměřené operace nástroje této povolit:

- Rychlý vývoj aplikací.
- Snadné nasazení a škálování.
- Dlouhodobé životní cyklus údržby pro týmy a aplikace.

Nejsou k dispozici více verzí OpenShift.  Z těchto verzí jsou pouze dva jsou v současnosti dostupné pro zákazníky, kteří nasazují v Azure: OpenShift Container Platform a OKD (dříve OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift je plně spravovaná nabídka služby OpenShift v Azure. Tato služba je společně spravované a podporované společností Microsoft a Red Hat. Úplnou dokumentaci může být [tady](https://docs.microsoft.com/azure/openshift/). 

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform je připravená pro podnikové [komerční verze](https://www.openshift.com) z a podporuje Red Hat. S touto verzí zákazníci zakoupit nezbytná oprávnění pro OpenShift Container Platform a zodpovídají za instalaci a správu celé infrastruktury.

Protože zákazníkům "vlastní" celou platformu, jejich ji nainstalujte ve svém místním datovém centru, nebo ve veřejném cloudu (jako je Azure).

## <a name="okd"></a>OKD

Je OKD [open source](https://www.okd.io/) nadřazený projekt OpenShift, která je podporovaná komunitou. OKD můžete nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Další postup

- [Konfigurace běžných požadavků pro OpenShift v Azure](./openshift-prerequisites.md)
- [Nasazení OpenShift Container Platform v Azure](./openshift-container-platform.md)
- [Nasazení OpenShift Container platformy samosprávné Marketplace nabídku](./openshift-marketplace-self-managed.md)
- [Nasadit OpenShift v Azure stacku](./openshift-azure-stack.md)
- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení Openshiftu](./openshift-troubleshooting.md)
