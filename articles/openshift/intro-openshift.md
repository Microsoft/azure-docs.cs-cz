---
title: Seznámení s Azure Red Hat OpenShift
description: Přečtěte si o funkcích a výhodách Microsoft Azure Red Hat OpenShift a nasaďte a spravujte aplikace založené na kontejnerech.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: 1bf3141876ee56ee1361f19a67689ca3b2f4f89a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685286"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Služba Microsoft *Azure Red Hat OpenShift* umožňuje nasadit plně spravované clustery [OpenShift](https://www.openshift.com/) .

Azure Red Hat OpenShift rozšiřuje [Kubernetes](https://kubernetes.io/). Spouštění kontejnerů v produkčním prostředí s Kubernetes vyžaduje další nástroje a prostředky. To často zahrnuje nutnost najuggle Registry imagí, správu úložiště, síťová řešení a nástroje pro protokolování a monitorování – všechny musí být ve verzi a testovány společně. Sestavování aplikací založených na kontejnerech vyžaduje ještě větší integraci s nástroji pro middleware, architektury, databáze a CI/CD. Azure Red Hat OpenShift kombinuje všechny tyto operace s jednou platformou a usnadňuje operacím IT týmům při poskytování aplikací pro týmy, které potřebují ke spuštění.

Služba Azure Red Hat OpenShift je společně vyvinutá, provozovaná a podporovaná v Red Hat a Microsoftu, aby poskytovala integrované možnosti podpory. Neexistují žádné virtuální počítače, které by bylo možné provozovat, a nevyžaduje se žádná oprava. Uzly hlavní, infrastruktura a aplikace jsou opraveny, aktualizovány a sledovány vaším jménem pomocí Red Hat a společnosti Microsoft. Clustery Azure Red Hat OpenShift se nasadí do vašeho předplatného Azure a jsou zahrnuté na faktuře Azure.

Můžete zvolit vlastní Registry, sítě, úložiště a CI/CD, nebo použít předdefinovaná řešení pro automatizované správy zdrojového kódu, kontejnerů a sestavení aplikací, nasazení, škálování, správu stavu a další. Azure Red Hat OpenShift poskytuje integrované přihlašovací prostředí prostřednictvím Azure Active Directory.

Začněte tím, že dokončíte kurz [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro zlepšení zabezpečení a správy vám Azure Red Hat OpenShift umožňuje integrovat s Azure Active Directory (Azure AD) a používat Kubernetes řízení přístupu na základě role (Kubernetes RBAC). Můžete také monitorovat stav vašeho clusteru a prostředků.

## <a name="cluster-and-node"></a>Clustery a uzly

Uzly Azure Red Hat OpenShift běží na virtuálních počítačích Azure. Úložiště můžete připojit k uzlům a luskům a upgradovat součásti clusteru.

## <a name="service-level-agreement"></a>smlouva SLA

Azure Red Hat OpenShift nabízí smlouva SLA k zajištění, že služba bude k dispozici 99,95% času. Další podrobnosti o smlouvě SLA najdete v tématu věnovaném [smlouvě SLA pro Azure Red Hat OpenShift](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/).

## <a name="next-steps"></a>Další kroky

Seznamte se s požadavky na Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Vytvoření a nastavení vývojového prostředí](tutorial-create-cluster.md)
