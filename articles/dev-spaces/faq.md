---
title: Nejčastější dotazy týkající se Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Vyhledejte odpovědi na některé běžné otázky týkající se Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 526e8a247241bd3ea0e61b6cd3b025cd473b29aa
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942511"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Nejčastější dotazy týkající se Azure Dev Spaces

Tato adresa obsahuje nejčastější dotazy týkající se Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Které oblasti Azure aktuálně poskytují Azure Dev Spaces?

Úplný seznam dostupných oblastí najdete v části [podporované oblasti][supported-regions] .

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Můžu migrovat AKS svůj cluster s Azure Dev Spaces do jiné oblasti?

Ano, pokud chcete přesunout cluster AKS s Azure Dev Spaces do jiné [podporované oblasti][supported-regions], doporučujeme vytvořit nový cluster v jiné oblasti a pak nainstalovat a nakonfigurovat Azure dev Spaces a nasadit své prostředky a aplikace do nového clusteru. Další informace o migraci AKS najdete v tématu [migrace do služby Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Můžu použít Azure Dev Spaces se stávajícími grafy fázemi nebo Helm?

Ano, pokud projekt již obsahuje souboru Dockerfile nebo Helm, můžete tyto soubory použít s Azure Dev Spaces. Při spuštění `azds prep`použijte parametr `--chart` a určete umístění grafu. Azure Dev Spaces bude stále generovat soubor *azds. yaml* a *souboru Dockerfile. vývoj* , ale nenahradí ani nezmění existující souboru Dockerfile nebo graf Helm. Může být nutné upravit *azds. yaml* a *souboru Dockerfile. vývoj* souborů, aby vše správně fungovalo se stávající aplikací při spuštění `azds up`.

Při použití vlastního grafu souboru Dockerfile nebo Helm existují tato omezení:
* Pokud používáte jenom jeden souboru Dockerfile, musí obsahovat všechno, co potřebujete k povolení vývojářských scénářů, jako je například jazyková sada SDK, nikoli jenom modul runtime. Pokud používáte samostatné souboru Dockerfile pro Azure Dev Spaces, jako je například souboru Dockerfile. vývoj, je nutné do tohoto souboru Dockerfile zahrnout všechno, co potřebujete k povolení vývojářských scénářů.
* Váš graf Helm musí podporovat předání části nebo celé značky obrázku jako hodnoty z *Values. yaml*.
* Pokud upravujete cokoli pomocí příchozího přenosu dat, můžete graf Helm také aktualizovat tak, aby používal řešení příchozího přenosu dat, které poskytuje Azure Dev Spaces.
* Pokud chcete použít [Možnosti směrování poskytované Azure dev Spaces][dev-spaces-routing], musí se všechny služby pro jednotlivé projekty vejít do jednoho oboru názvů Kubernetes a musí být nasazeny s jednoduchým pojmenování, například *služba-a*. V standardních grafech Helm se tato aktualizace názvů dá udělat zadáním hodnoty vlastnosti *fullnameOverride* .

Pokud chcete porovnat vlastní souboru Dockerfile nebo Helm graf s existující verzí, která funguje s Azure Dev Spaces, zkontrolujte soubory vygenerované v [rychlém][quickstart-cli]startu.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Můžu měnit soubory generované Azure Dev Spaces?

Ano, můžete upravit soubor *azds. yaml* , souboru Dockerfile a Helm, který [vygeneroval Azure dev Spaces při přípravě projektu][dev-spaces-prep]. Změna těchto souborů mění způsob sestavení a spuštění projektu.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Můžu Azure Dev Spaces použít bez veřejné IP adresy?

Ne, nemůžete zřídit Azure Dev Spaces v clusteru AKS bez veřejné IP adresy. [Azure dev Spaces pro směrování vyžaduje][dev-spaces-routing]veřejnou IP adresu.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Můžu použít vlastní příchozí přenosy s Azure Dev Spaces?

Ano, můžete nakonfigurovat příchozí příchozí přenos dat na straně Azure Dev Spaces vytvoření. Můžete například použít [traefik][ingress-traefik] nebo [Nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Můžu použít HTTPS s Azure Dev Spaces?

Ano, můžete nakonfigurovat příchozí příchozí přenosy pomocí protokolu HTTPS pomocí [traefik][ingress-https-traefik] nebo [Nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Můžu použít Azure Dev Spaces na clusteru, který používá CNI místo kubenet? 

Ano, Azure Dev Spaces můžete použít v clusteru AKS, který používá CNI pro práci v síti. Můžete například použít Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers], které používají CNI pro práci v síti. Další informace o používání CNI pro sítě s Azure Dev Spaces [najdete tady](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Můžu Azure Dev Spaces použít s kontejnery Windows?

V současné době je Azure Dev Spaces určena ke spouštění pouze v systémech Linux a pouze na uzlech. můžete však spustit Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Můžu použít Azure Dev Spaces u clusterů AKS s povoleným rozsahem IP adres serveru API?

Ano, můžete použít Azure Dev Spaces u clusterů AKS s povolenými [rozsahy IP adres serveru API][aks-auth-range] . Další informace o používání clusterů AKS s povolenými rozsahy IP adres serveru API s povoleným Azure Dev Spaces je k dispozici [zde](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Můžu použít Azure Dev Spaces u clusterů AKS s omezeným provozem odchozích dat pro uzly clusteru?

Ano, v případě, že jsou povolené správné plně kvalifikované názvy domén, můžete použít Azure Dev Spaces v clusterech AKS s [omezeným přenosem dat pro uzly clusteru][aks-restrict-egress-traffic] povolené. Další informace o použití AKS clusterů s omezeným přenosem dat pro uzly clusteru s povoleným Azure Dev Spaces je k dispozici [zde](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Můžu použít Azure Dev Spaces u clusterů AKS s podporou RBAC?

Ano, můžete použít Azure Dev Spaces v clusterech AKS s povolenou RBAC nebo bez ní.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co se stane, když povolím příchozí přenos dat pro projekt v aplikaci Visual Studio?

Při použití sady Visual Studio k přípravě projektu máte možnost Povolit pro vaši službu příchozí přenos dat. Když příchozí připojení povolíte, vytvoří se pro přístup ke službě při spuštění v clusteru AKS veřejný koncový bod, který je nepovinný. Pokud příchozí příchozí přenosy nepovolíte, bude služba dostupná jenom z vašeho clusteru AKS.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md