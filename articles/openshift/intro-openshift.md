---
title: Seznámení s Azure Red Hat OpenShift
description: Přečtěte si o funkcích a výhodách Microsoft Azure Red Hat OpenShift a nasaďte a spravujte aplikace založené na kontejnerech.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: 87126d1beca97a905f0f3b09b565c12c37a4774d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82203909"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Služba Microsoft *Azure Red Hat OpenShift* umožňuje nasadit plně spravované clustery [OpenShift](https://www.openshift.com/) .

Azure Red Hat OpenShift rozšiřuje [Kubernetes](https://kubernetes.io/). Spouštění kontejnerů v produkčním prostředí s Kubernetes vyžaduje další nástroje a prostředky. To často zahrnuje nutnost najuggle Registry imagí, správu úložiště, síťová řešení a nástroje pro protokolování a monitorování – všechny musí být ve verzi a testovány společně. Sestavování aplikací založených na kontejnerech vyžaduje ještě větší integraci s nástroji pro middleware, architektury, databáze a CI/CD. Azure Red Hat OpenShift kombinuje všechny tyto operace s jednou platformou a usnadňuje operacím IT týmům při poskytování aplikací pro týmy, které potřebují ke spuštění.

Služba Azure Red Hat OpenShift je společně vyvinutá, provozovaná a podporovaná v Red Hat a Microsoftu, aby poskytovala integrované možnosti podpory. Neexistují žádné virtuální počítače, které by bylo možné provozovat, a nevyžaduje se žádná oprava. Uzly hlavní, infrastruktura a aplikace jsou opraveny, aktualizovány a sledovány vaším jménem pomocí Red Hat a společnosti Microsoft. Clustery Azure Red Hat OpenShift se nasadí do vašeho předplatného Azure a jsou zahrnuté na faktuře Azure.

Můžete zvolit vlastní Registry, sítě, úložiště a CI/CD, nebo použít předdefinovaná řešení pro automatizované správy zdrojového kódu, kontejnerů a sestavení aplikací, nasazení, škálování, správu stavu a další. Azure Red Hat OpenShift poskytuje integrované přihlašovací prostředí prostřednictvím Azure Active Directory.

Začněte tím, že dokončíte kurz [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro zlepšení zabezpečení a správy vám Azure Red Hat OpenShift umožňuje integrovat s Azure Active Directory (Azure AD) a používat Kubernetes řízení přístupu na základě role (RBAC). Můžete také monitorovat stav vašeho clusteru a prostředků.

## <a name="cluster-and-node"></a>Clustery a uzly

Uzly Azure Red Hat OpenShift běží na virtuálních počítačích Azure. Můžete úložiště připojit k uzlům a podům, upgradovat komponenty clusteru a použít GPU.

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

[Virtuální síť clusteru můžete připojit k existující virtuální síti nebo vytvořit virtuální síť při vytváření clusteru](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets).

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Služba Azure Red Hat OpenShift byla CNCF certifikována jako Kubernetesá shoda.

## <a name="next-steps"></a>Další kroky

Seznamte se s požadavky na Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Vytvoření a nastavení vývojového prostředí](howto-setup-environment.md)
