---
title: Seznámení s Azure Red Hat OpenShift
description: Přečtěte si o funkcích a výhodách Microsoft Azure Red Hat OpenShift a nasaďte a spravujte aplikace založené na kontejnerech.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276059"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Služba Microsoft *Azure Red Hat OpenShift* umožňuje nasadit plně spravované clustery [OpenShift](https://www.openshift.com/) .

Azure Red Hat OpenShift rozšiřuje [Kubernetes](https://kubernetes.io/). Spouštění kontejnerů v produkčním prostředí s Kubernetes vyžaduje další nástroje a prostředky, jako je například registr imagí, Správa úložiště, síťová řešení a nástroje pro protokolování a monitorování, přičemž všechny musí být ve verzi a testovány společně. Sestavování aplikací založených na kontejnerech vyžaduje ještě větší integraci s nástroji pro middleware, architektury, databáze a CI/CD. Azure Red Hat OpenShift kombinuje všechny tyto operace s jednou platformou a usnadňuje operacím IT týmům při poskytování aplikací pro týmy, které potřebují ke spuštění.

Služba Azure Red Hat OpenShift je společně vyvinutá, provozovaná a podporovaná v Red Hat a Microsoftu, aby poskytovala integrované možnosti podpory. Neexistují žádné virtuální počítače, které by bylo možné provozovat, a nevyžaduje se žádná oprava. Uzly hlavní, infrastruktura a aplikace jsou opraveny, aktualizovány a sledovány vaším jménem pomocí Red Hat a společnosti Microsoft. Clustery Azure Red Hat OpenShift se nasadí do vašeho předplatného Azure a jsou zahrnuté na faktuře Azure.

Můžete zvolit vlastní Registry, sítě, úložiště a CI/CD, nebo použít předdefinovaná řešení pro automatizované správy zdrojového kódu, kontejnerů a sestavení aplikací, nasazení, škálování, správu stavu a další. Azure Red Hat OpenShift poskytuje integrované přihlašovací prostředí prostřednictvím Azure Active Directory.

Začněte tím, že dokončíte kurz [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro zlepšení zabezpečení a správy vám Azure Red Hat OpenShift umožňuje integrovat s Azure Active Directory (Azure AD) a používat Kubernetes řízení přístupu na základě role (RBAC). Můžete také monitorovat stav vašeho clusteru a prostředků.

## <a name="cluster-and-node"></a>Clustery a uzly

Uzly Azure Red Hat OpenShift běží na virtuálních počítačích Azure. Můžete úložiště připojit k uzlům a podům, upgradovat komponenty clusteru a použít GPU.

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

[Cluster Azure Red Hat OpenShift můžete připojit k existující virtuální síti](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) prostřednictvím partnerského vztahu. V této konfiguraci se můžou lusky připojit k jiným službám v partnerské virtuální síti.

Podrobnosti najdete v tématu [připojení virtuální sítě clusteru k existující virtuální síti](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Služba Azure Red Hat OpenShift byla CNCF certifikována jako Kubernetesá shoda.

## <a name="next-steps"></a>Další kroky

Seznamte se s požadavky na Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Vytvoření a nastavení vývojového prostředí](howto-setup-environment.md)
