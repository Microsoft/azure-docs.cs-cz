---
title: OpenShift v Azure – přehled | Microsoft Docs
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
ms.openlocfilehash: 80f0ec57aa287bae9b10d9a4cdb013014aab3332
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082558"
---
# <a name="openshift-in-azure"></a>OpenShift v Azure

OpenShift je otevřená a rozšiřitelná platforma pro aplikace kontejnerů, která přináší Docker a Kubernetes do podniku.  

OpenShift zahrnuje Kubernetes pro orchestraci a správu kontejnerů. Přidává nástroje zaměřené na vývojáře a operace zaměřené na provoz, které umožňují:

- Rychlý vývoj aplikací
- Snadné nasazení a škálování.
- Dlouhodobá údržba v životním cyklu pro týmy a aplikace.

K dispozici je více verzí OpenShift.  Z těchto verzí jsou dnes k dispozici pouze dvě pro zákazníky k nasazení v Azure: OpenShift Container Platform a OKD (dřív OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift je plně spravovaná nabídka OpenShift spuštěná v Azure. Tato služba je společně spravovaná a podporovaná Microsoftem a Red Hat. Další podrobnosti najdete v dokumentaci ke [službě Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontejneru je [komerční verze](https://www.openshift.com) připravená pro podnikové prostředí od společnosti Red Hat a podporovaná. V této verzi si zákazníci zakoupí nezbytné nároky na OpenShift kontejnerové platformy a zodpovídá za instalaci a správu celé infrastruktury.

Vzhledem k tomu, že zákazníci "vlastní", můžou ho nainstalovat do místního datového centra nebo ve veřejném cloudu (například Azure).

## <a name="okd"></a>OKD

OKD je [Open Source](https://www.okd.io/) projekt pro OpenShift, který je podporován komunitou. OKD se dá nainstalovat na CentOS nebo Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Další postup

- [Konfigurace běžných požadavků pro OpenShift v Azure](./openshift-prerequisites.md)
- [Nasazení platformy kontejnerů OpenShift v Azure](./openshift-container-platform.md)
- [Nasazení nabídky samoobslužného tržiště spravovaného kontejnerem OpenShift](./openshift-marketplace-self-managed.md)
- [Nasazení OpenShift v Azure Stack](./openshift-azure-stack.md)
- [Úkoly po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazením OpenShift](./openshift-troubleshooting.md)
