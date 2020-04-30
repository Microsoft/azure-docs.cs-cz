---
title: OpenShift v Azure – přehled
description: Přehled OpenShift v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c338e9a6a793d1c2d0557d70242996175d5a85ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759453"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřená a rozšiřitelná platforma pro aplikace kontejnerů, která přináší Docker a Kubernetes do podniku.  

OpenShift zahrnuje Kubernetes pro orchestraci a správu kontejnerů. Přidává nástroje zaměřené na vývojáře a operace zaměřené na provoz, které umožňují:

- Rychlý vývoj aplikací
- Snadné nasazení a škálování.
- Dlouhodobá údržba v životním cyklu pro týmy a aplikace.

K dispozici je více verzí OpenShift.  Z těchto verzí jsou v současnosti k dispozici pouze dvě pro zákazníky, kteří chtějí nasadit v Azure: OpenShift Container Platform a OKD (dříve OpenShift původ).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift je plně spravovaná nabídka OpenShift spuštěná v Azure. Tato služba je společně spravovaná a podporovaná Microsoftem a Red Hat. Další podrobnosti najdete v dokumentaci ke [službě Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontejneru je [komerční verze](https://www.openshift.com) připravená pro podnikové prostředí od společnosti Red Hat a podporovaná. V této verzi si zákazníci zakoupí nezbytné nároky na OpenShift kontejnerové platformy a zodpovídá za instalaci a správu celé infrastruktury.

Vzhledem k tomu, že zákazníci "vlastní", můžou ho nainstalovat do místního datového centra nebo ve veřejném cloudu (například Azure).

## <a name="okd"></a>OKD

OKD je [Open Source](https://www.okd.io/) projekt pro OpenShift, který je podporován komunitou. OKD se dá nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Další kroky

- [Konfigurace běžných požadavků pro OpenShift v Azure](./openshift-container-platform-3x-prerequisites.md)
- [Nasazení platformy kontejnerů OpenShift v Azure](./openshift-container-platform-3x.md)
- [Nasazení nabídky samoobslužného tržiště spravovaného kontejnerem OpenShift](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Nasazení OpenShift v Azure Stack](./openshift-azure-stack.md)
- [Úkoly po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Řešení potíží s nasazením OpenShift](./openshift-container-platform-3x-troubleshooting.md)
