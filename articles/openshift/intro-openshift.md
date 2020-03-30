---
title: Úvod do Azure Red Hat OpenShift
description: Seznamte se s funkcemi a výhodami aplikace Microsoft Azure Red Hat OpenShift pro nasazení a správu aplikací založených na kontejnerech.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276059"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Služba Microsoft *Azure Red Hat OpenShift* umožňuje nasazovat plně spravované clustery [OpenShift.](https://www.openshift.com/)

Azure Red Hat OpenShift rozšiřuje [Kubernetes](https://kubernetes.io/). Spuštění kontejnerů v produkčním prostředí s Kubernetes vyžaduje další nástroje a prostředky, jako je například registr bitových obrázků, správa úložiště, síťová řešení a nástroje pro protokolování a monitorování, které musí být testovány a testovány společně. Vytváření aplikací založených na kontejnerech vyžaduje ještě větší integraci práce s middlewarem, frameworky, databázemi a nástroji CI/CD. Azure Red Hat OpenShift to vše kombinuje do jediné platformy, přináší snadné operace it týmům a zároveň poskytuje aplikačním týmům vše, co potřebují k provedení.

Azure Red Hat OpenShift je společně navržen, provozován a podporován společností Red Hat a Microsoft, aby poskytoval integrované prostředí podpory. Neexistují žádné virtuální počítače pro provoz a není vyžadováno žádné opravy. Hlavní uzly, uzly infrastruktury a aplikací jsou opraveny, aktualizovány a sledovány vaším jménem společností Red Hat a Microsoft. Vaše clustery Azure Red Hat OpenShift se nasazují do vašeho předplatného Azure a jsou součástí vaší účtu Azure.

Můžete si vybrat vlastní řešení registru, sítě, úložiště a CI/CD nebo použít integrovaná řešení pro automatizovanou správu zdrojového kódu, sestavení kontejnerů a aplikací, nasazení, škálování, správu stavu a další. Azure Red Hat OpenShift poskytuje integrované přihlašovací prostředí prostřednictvím služby Azure Active Directory.

Chcete-li začít, dokončete kurz [vytvoření clusteru Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro lepší zabezpečení a správu umožňuje Azure Red Hat OpenShift integraci s Azure Active Directory (Azure AD) a používat řízení přístupu na základě rolí Kubernetes (RBAC). Můžete také monitorovat stav vašeho clusteru a prostředků.

## <a name="cluster-and-node"></a>Clustery a uzly

Uzly Azure Red Hat OpenShift běží na virtuálních počítačích Azure. Můžete úložiště připojit k uzlům a podům, upgradovat komponenty clusteru a použít GPU.

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

Cluster [Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) můžete připojit k existující virtuální síti prostřednictvím partnerského vztahu. V této konfiguraci pody můžete připojit k jiným službám v partnerské virtuální síti.

Podrobnosti najdete [v tématu Připojení virtuální sítě clusteru k existující virtuální síti.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Služba Azure Red Hat OpenShift byla certifikována jako kubernetes conformant.

## <a name="next-steps"></a>Další kroky

Seznamte se s předpoklady pro Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Vytvoření a nastavení vývojového prostředí](howto-setup-environment.md)
