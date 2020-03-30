---
title: OpenShift v Azure – přehled
description: Přehled OpenShift v Azure.
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
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035423"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřená a rozšiřitelná kontejnerová aplikační platforma, která přináší Docker a Kubernetes do podniku.  

OpenShift zahrnuje Kubernetes pro orchestraci a správu kontejnerů. Přidává nástroje zaměřené na vývojáře a nástroje zaměřené na operace, které umožňují:

- Rychlý vývoj aplikací.
- Snadné nasazení a škálování.
- Dlouhodobá údržba životního cyklu pro týmy a aplikace.

K dispozici je více verzí OpenShiftu.  Z těchto verzí jsou dnes k dispozici pouze dvě pro zákazníky k nasazení v Azure: OpenShift Container Platform a OKD (dříve OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift je plně spravovaná nabídka OpenShiftu běžícího v Azure. Tato služba je společně spravována a podporována společnostmi Microsoft a Red Hat. Další podrobnosti najdete v dokumentaci ke [službě Azure Red Hat OpenShift Service.](https://docs.microsoft.com/azure/openshift/)

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform je [komerční verze](https://www.openshift.com) připravená pro podniky od společnosti Red Hat a podporovaná společností Red Hat. V této verzi si zákazníci zakoupí potřebné nároky na platformu OpenShift Container Platform a jsou zodpovědní za instalaci a správu celé infrastruktury.

Vzhledem k tomu, že zákazníci "vlastní" celou platformu, můžou ji nainstalovat do svého místního datového centra nebo do veřejného cloudu (například Azure).

## <a name="okd"></a>OKD

OKD je [open-source](https://www.okd.io/) upstream projekt OpenShift, který je podporován komunitou. OKD lze nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Další kroky

- [Konfigurace běžných předpokladů pro OpenShift v Azure](./openshift-container-platform-3x-prerequisites.md)
- [Nasazení kontejnerové platformy OpenShift v Azure](./openshift-container-platform-3x.md)
- [Nasazení nabídky samoobslužné tržiště platformy OpenShift Container Platform](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Nasazení OpenShiftu v Azure Stacku](./openshift-azure-stack.md)
- [Úlohy po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Poradce při potížích s nasazením OpenShift](./openshift-container-platform-3x-troubleshooting.md)
