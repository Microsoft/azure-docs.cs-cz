---
title: Úvod do Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Další funkce a výhody Microsoft Azure Red Hat OpenShift k nasazení a správě kontejnerových aplikací.
services: container-service
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 7cabedaec1190437aa9f225397afa8871cb06e88
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306372"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* služby můžete nasazovat plně spravované [OpenShift](https://www.openshift.com/) clustery.

Azure Red Hat OpenShift rozšiřuje [Kubernetes](https://kubernetes.io/). Spuštěné kontejnery v produkčním prostředí s využitím Kubernetes vyžaduje další nástroje a prostředky, jako je například registru imagí, správy úložiště, síťové řešení a protokolování a nástroje pro monitorování, které musí být označené verzí a otestovaná společně. Sestavování aplikací založených na kontejnerech vyžaduje ještě více práce integrace s middleware, architektur, databází a nástrojích CI/CD. Azure Red Hat OpenShift je kombinací všech do jedné platformy, přináší snadné operací pro IT týmy přitom aplikace týmy, které potřebují k provedení.

Azure Red Hat OpenShift společně je analyzován, provozovaný a podporuje Red Hat a Microsoft k poskytování integrovaném prostředí podpory. Nejsou žádné virtuální počítače k provozu a bez opravy je povinný. Hlavní server, infrastruktury a aplikací uzlů se opravit, aktualizovat a sledováno Red Hat a Microsoft vaším jménem. Své clustery Azure Red Hat OpenShift se nasazuje do vašeho předplatného Azure a jsou zahrnuty na faktuře Azure.

Můžete vlastního registru, sítě, úložiště a CI/CD řešení, nebo použití sestavení integrované řešení pro Správa automatizovaných zdrojového kódu, kontejner a aplikace, nasazení, škálování, Správa stavu a další. Azure Red Hat OpenShift poskytuje integrované přihlašovací prostředí prostřednictvím Azure Active Directory.

Pokud chcete začít, proveďte [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro lepší zabezpečení a správy Azure Red Hat OpenShift umožňuje integraci se službou Azure Active Directory (Azure AD) a používat Kubernetes řízení přístupu na základě role (RBAC). Můžete také monitorovat stav vašeho clusteru a prostředků.

## <a name="cluster-and-node"></a>Clustery a uzly

Azure Red Hat OpenShift uzly spouštět na virtuálních počítačích Azure. Můžete úložiště připojit k uzlům a podům, upgradovat komponenty clusteru a použít GPU.

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

Cluster Azure Red Hat OpenShift můžete připojit k existující virtuální síti přes partnerský vztah. V této konfiguraci podů můžete připojit k jiným službám v partnerské virtuální síti a místními sítěmi přes [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nebo site-to-site (S2S) VPN připojení.

Zobrazit [připojení virtuální sítě clusteru pro existující virtuální síť](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) podrobnosti.

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Služba Azure Red Hat OpenShift byla CNCF certifikát jako vyhovující Kubernetes.

## <a name="next-steps"></a>Další postup

Přečtěte si požadavky pro Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Vytvoření a nastavení vývojového prostředí](howto-setup-environment.md)