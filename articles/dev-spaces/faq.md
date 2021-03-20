---
title: Nejčastější dotazy týkající se Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Vyhledejte odpovědi na některé běžné otázky týkající se Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548830"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Nejčastější dotazy týkající se Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Tato adresa obsahuje nejčastější dotazy týkající se Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Jaké verze Kubernetes se podporují pro Azure Dev Spaces?

Azure Dev Spaces podporuje [aktuálně podporované verze Kubernetes ve všeobecné dostupnosti (GA) v AKS až 1,18][aks-supported-k8s]. Kubernetes 1,19 a novější na AKS používá kontejner jako modul runtime kontejneru, který nefunguje s Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Které oblasti Azure aktuálně poskytují Azure Dev Spaces?

Úplný seznam dostupných oblastí najdete v části [podporované oblasti][supported-regions] .

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Můžu migrovat AKS svůj cluster s Azure Dev Spaces do jiné oblasti?

Ano, pokud chcete přesunout cluster AKS s Azure Dev Spaces do jiné [podporované oblasti][supported-regions], doporučujeme vytvořit nový cluster v jiné oblasti a pak nainstalovat a nakonfigurovat Azure dev Spaces a nasadit své prostředky a aplikace do nového clusteru. Další informace o migraci AKS najdete v tématu [migrace do služby Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Můžu použít Azure Dev Spaces se stávajícími grafy fázemi nebo Helm?

Ano, pokud projekt již obsahuje souboru Dockerfile nebo Helm, můžete tyto soubory použít s Azure Dev Spaces. Při spuštění `azds prep` použijte `--chart` parametr a zadejte umístění grafu. Azure Dev Spaces bude stále generovat soubor *azds. yaml* a *souboru Dockerfile. vývoj* , ale nenahradí ani nezmění existující souboru Dockerfile nebo graf Helm. Je možné, že budete muset upravit soubory *azds. yaml* a *souboru Dockerfile. vývoj* , aby vše správně fungovalo se stávající aplikací při spuštění `azds up` .

Při použití vlastního grafu souboru Dockerfile nebo Helm existují tato omezení:
* Pokud používáte jenom jeden souboru Dockerfile, musí obsahovat všechno, co potřebujete k povolení vývojářských scénářů, jako je například jazyková sada SDK, nikoli jenom modul runtime. Pokud používáte samostatné souboru Dockerfile pro Azure Dev Spaces, jako je například souboru Dockerfile. vývoj, je nutné do tohoto souboru Dockerfile zahrnout všechno, co potřebujete k povolení vývojářských scénářů.
* Váš graf Helm musí podporovat předání části nebo celé značky obrázku jako hodnoty z *Values. yaml*.
* Pokud upravujete cokoli pomocí příchozího přenosu dat, můžete graf Helm také aktualizovat tak, aby používal řešení příchozího přenosu dat, které poskytuje Azure Dev Spaces.
* Pokud chcete použít [Možnosti směrování poskytované Azure dev Spaces][dev-spaces-routing], musí se všechny služby pro jednotlivé projekty vejít do jednoho oboru názvů Kubernetes a musí být nasazeny s jednoduchým pojmenování, například *služba-a*. V standardních grafech Helm se tato aktualizace názvů dá udělat zadáním hodnoty vlastnosti *fullnameOverride* .

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

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Můžu použít Azure Dev Spaces v clusterech AKS s podporou RBAC a Kubernetes?

Ano, můžete použít Azure Dev Spaces v clusterech AKS s povoleným nebo bez Kubernetes řízení přístupu na základě role (Kubernetes RBAC).

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co se stane, když povolím příchozí přenos dat pro projekt v aplikaci Visual Studio?

Při použití sady Visual Studio k přípravě projektu máte možnost Povolit pro vaši službu příchozí přenos dat. Když příchozí připojení povolíte, vytvoří se pro přístup ke službě při spuštění v clusteru AKS veřejný koncový bod, který je nepovinný. Pokud příchozí příchozí přenosy nepovolíte, bude služba dostupná jenom z vašeho clusteru AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Můžu použít spravované identity pod Azure Dev Spaces?

Ano, v clusterech AKS s povoleným Azure Dev Spaces můžete použít [spravované identity pod][aks-pod-managed-id] , ale existují [Další konfigurační kroky][dev-spaces-pod-managed-id-steps] , po povolení Azure dev Spaces v clusteru pomocí spravované identity pod. Pokud máte nainstalované spravované identity a chcete ji odinstalovat, můžete najít další podrobnosti v [poznámkách k odinstalaci][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Můžu v aplikaci použít Azure Dev Spaces s více mikroslužbami?

Ano, můžete použít Azure Dev Spaces v aplikaci s více mikroslužbami, ale musíte připravit a spustit jednotlivé mikroslužby ve svém kořeni. Služba Azure Dev Spaces CLI, Azure Dev Spaces VS Code a vývoj aplikací Visual Studio pro vývoj předpokládá, že se soubor *azds. yaml* nachází v kořenovém adresáři mikroslužby, aby mohl spustit a ladit. Příklad několika mikroslužeb v jedné aplikaci najdete v [ukázkové aplikaci pro sdílení kol][bike-sharing] .

V Visual Studio Code je možné [otevřít samostatné projekty v jednom pracovním prostoru][vs-code-multi-root-workspaces] a ladit je samostatně prostřednictvím Azure dev Spaces. Každý projekt musí být samostatný a připravený pro Azure Dev Spaces.

V aplikaci Visual Studio je možné nakonfigurovat řešení .NET Core pro ladění prostřednictvím Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Můžu Azure Dev Spaces použít s sítí služby?

V tuto chvíli nemůžete použít Azure Dev Spaces s mřížkami služeb, jako je [Istio][istio] nebo [linker][linkerd]. Můžete spouštět Azure Dev Spaces a síť služby na stejném clusteru AKS, ale ve stejném oboru názvů nemůžete mít povolenou Azure Dev Spaces a síť služby.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md