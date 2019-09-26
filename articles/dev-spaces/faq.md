---
title: Nejčastější dotazy týkající se Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Vyhledejte odpovědi na některé běžné otázky týkající se Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305974"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Nejčastější dotazy týkající se Azure Dev Spaces

Tato adresa obsahuje nejčastější dotazy týkající se Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Které oblasti Azure aktuálně poskytují Azure Dev Spaces?

Úplný seznam oblastí, které jsou k dispozici, najdete v tématu [podporované oblasti a konfigurace][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Můžu Azure Dev Spaces použít bez veřejné IP adresy?

Ne, nemůžete zřídit Azure Dev Spaces v clusteru AKS bez veřejné IP adresy. [Azure dev Spaces pro směrování vyžaduje][dev-spaces-routing]veřejnou IP adresu.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Můžu použít vlastní příchozí přenosy s Azure Dev Spaces?

Ano, můžete nakonfigurovat vlastní příchozí přenos dat na straně Azure Dev Spaces vytvoří. Můžete například použít [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Můžu použít HTTPS s Azure Dev Spaces?

Ano, můžete nakonfigurovat příchozí příchozí přenosy pomocí protokolu HTTPS pomocí [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Můžu použít Azure Dev Spaces na clusteru, který používá CNI místo kubenet? 

Ano, Azure Dev Spaces můžete použít v clusteru AKS, který používá CNI pro práci v síti. Můžete například použít Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers], které používají CNI pro práci v síti.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Můžu Azure Dev Spaces použít s kontejnery Windows?

V současné době je Azure Dev Spaces určena ke spouštění pouze v systémech Linux a pouze na uzlech. můžete však spustit Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers].


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md