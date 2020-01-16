---
title: Nejčastější dotazy týkající se Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Vyhledejte odpovědi na některé běžné otázky týkající se Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: d5ab56edfe4799d51fb7f08642aad9e2ee01db05
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044964"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Nejčastější dotazy týkající se Azure Dev Spaces

Tato adresa obsahuje nejčastější dotazy týkající se Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Které oblasti Azure aktuálně poskytují Azure Dev Spaces?

Úplný seznam dostupných oblastí najdete v části [podporované oblasti][supported-regions] .

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Můžu Azure Dev Spaces použít bez veřejné IP adresy?

Ne, nemůžete zřídit Azure Dev Spaces v clusteru AKS bez veřejné IP adresy. [Azure dev Spaces pro směrování vyžaduje][dev-spaces-routing]veřejnou IP adresu.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Můžu použít vlastní příchozí přenosy s Azure Dev Spaces?

Ano, můžete nakonfigurovat vlastní příchozí přenos dat na straně Azure Dev Spaces vytvoří. Můžete například použít [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Můžu použít HTTPS s Azure Dev Spaces?

Ano, můžete nakonfigurovat příchozí příchozí přenosy pomocí protokolu HTTPS pomocí [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Můžu použít Azure Dev Spaces na clusteru, který používá CNI místo kubenet? 

Ano, Azure Dev Spaces můžete použít v clusteru AKS, který používá CNI pro práci v síti. Můžete například použít Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers], které používají CNI pro práci v síti. Další informace o používání CNI pro sítě s Azure Dev Spaces [najdete tady](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Můžu Azure Dev Spaces použít s kontejnery Windows?

V současné době je Azure Dev Spaces určena ke spouštění pouze v systémech Linux a pouze na uzlech. můžete však spustit Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Můžu použít Azure Dev Spaces u clusterů AKS s povoleným rozsahem IP adres serveru API?

Ano, můžete použít Azure Dev Spaces u clusterů AKS s povolenými [rozsahy IP adres serveru API][aks-auth-range] . Další informace o používání clusterů AKS s povolenými rozsahy IP adres serveru API s povoleným Azure Dev Spaces je k dispozici [zde](configure-networking.md#using-api-server-authorized-ip-ranges).

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Můžu použít Azure Dev Spaces u clusterů AKS s omezeným provozem odchozích dat pro uzly clusteru?

Ano, v případě, že jsou povolené správné plně kvalifikované názvy domén, můžete použít Azure Dev Spaces v clusterech AKS s [omezeným přenosem dat pro uzly clusteru][aks-restrict-egress-traffic] povolené. Další informace o použití AKS clusterů s omezeným přenosem dat pro uzly clusteru s povoleným Azure Dev Spaces je k dispozici [zde](configure-networking.md#ingress-and-egress-network-traffic-requirements).

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md