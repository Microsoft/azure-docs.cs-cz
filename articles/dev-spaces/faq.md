---
title: Nejčastější dotazy týkající se Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Najděte odpovědi na některé běžné otázky týkající se Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: 1fd176084cd0737a290e037ed19af510fcab3e78
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382001"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Nejčastější dotazy týkající se Azure Dev Spaces

To řeší nejčastější dotazy týkající se Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Které oblasti Azure aktuálně poskytují Azure Dev Spaces?

Úplný seznam dostupných oblastí najdete v [části Podporované oblasti.][supported-regions]

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Můžu migrovat svůj cluster AKS pomocí Azure Dev Spaces do jiné oblasti?

Ano, pokud chcete přesunout cluster AKS s Azure Dev Spaces do jiné [podporované oblasti][supported-regions], doporučujeme vytvořit nový cluster v jiné oblasti a pak nainstalovat a nakonfigurovat Azure Dev Spaces a nasadit prostředky a aplikace do nového clusteru. Další informace o migraci AKS najdete v [tématu migrace do služby Azure Kubernetes Service (AKS).][aks-migration]

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Můžu používat Azure Dev Spaces s existujícími dockerovými soubory nebo grafy helmu?

Ano, pokud váš projekt už má Dockerfile nebo Helm grafu, můžete použít tyto soubory s Azure Dev Spaces. Při spuštění `azds prep`použijte `--chart` parametr a určete umístění grafu. Azure Dev Spaces bude stále generovat soubor *azds.yaml* a *Dockerfile.develop,* ale nenahradí ani neupraví existující Dockerfile nebo graf Helm. Možná budete muset upravit *azds.yaml* a *Dockerfile.develop* soubory, aby vše fungovalo `azds up`správně s vaší stávající aplikace při spuštění .

Při použití vlastního grafu Dockerfile nebo Helm existují následující omezení:
* Pokud používáte pouze jeden soubor Dockerfile, musí obsahovat vše, co potřebujete k povolení scénářů vývoje, jako je například jazyk SDK není jen runtime. Pokud používáte samostatný soubor Dockerfile pro Azure Dev Spaces, jako je například Dockerfile.develop, vše, co potřebujete k povolení scénářů vývoje, musí být zahrnuto do tohoto souboru Dockerfile.
* Helm graf musí podporovat předávání části nebo celé značky obrázku jako hodnotu z *values.yaml*.
* Pokud něco upravujete pomocí příchozího přenosu dat, můžete také aktualizovat helmový graf tak, aby používal řešení příchozího přenosu dat poskytované azure dev spaces.
* Pokud chcete použít [možnosti směrování poskytované Azure Dev Spaces][dev-spaces-routing], všechny služby pro jednotlivé projekty musí být vejde do jednoho oboru názvů Kubernetes a musí být nasazen s jednoduchým pojmenováním, například *service-a*. Ve standardních grafech helmu lze tuto aktualizaci pojmenování provést zadáním hodnoty vlastnosti *fullnameOverride.*

Chcete-li porovnat vlastní graf Dockerfile nebo Helm s existující verzí, která pracuje s Azure Dev Spaces, zkontrolujte soubory generované v [rychlém startu][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Můžu upravit soubory generované Azure Dev Spaces?

Ano, můžete upravit soubor *azds.yaml,* Dockerfile a Helm graf [generovaný Azure Dev Spaces při přípravě projektu][dev-spaces-prep]. Úprava těchto souborů změní způsob, jakým je projekt sestaven a spuštěn.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Můžu používat Azure Dev Spaces bez veřejné IP adresy?

Ne, azure dev spaces nelze zřídit v clusteru AKS bez veřejné IP adresy. Azure [Dev Spaces potřebuje pro směrování veřejnou][dev-spaces-routing]IP adresu.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Můžu použít vlastní příchozí přenos dat s Azure Dev Spaces?

Ano, můžete nakonfigurovat vlastní příchozí přenos dat spolu s příchozí azure dev spaces vytvoří. Můžete například použít [traefik][ingress-traefik] nebo [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Můžu používat protokol HTTPS s Azure Dev Spaces?

Ano, můžete nakonfigurovat vlastní příchozí přenos pomocí protokolu HTTPS pomocí [traefik][ingress-https-traefik] nebo [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Můžu použít Azure Dev Spaces v clusteru, který používá CNI místo kubenet? 

Ano, azure dev spaces můžete použít v clusteru AKS, který používá CNI pro vytváření sítí. Můžete například použít Azure Dev Spaces v clusteru AKS s [existujícími kontejnery Windows][windows-containers], který používá CNI pro vytváření sítí. Další informace o použití CNI pro vytváření sítí s Azure Dev Spaces jsou k dispozici [zde](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Můžu s kontejnery windows používat Azure Dev Spaces?

V současné době azure dev spaces je určen ke spuštění pouze na linuxových podech a uzly, ale můžete spustit Azure Dev Spaces v clusteru AKS s [existující kontejnery Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Můžu v clusterech AKS používat Azure Dev Spaces s povolenými rozsahy IP adres na serveru API?

Ano, Azure Dev Spaces můžete použít v clusterech AKS s [povolenými rozsahy IP adres na serveru API.][aks-auth-range] Další informace o používání clusterů AKS s rozsahy IP adres autorizovanými servery API, které jsou povoleny v Azure Dev Spaces, jsou k dispozici [zde](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Můžu použít Azure Dev Spaces v clusterech AKS s omezeným přenosem odchozího přenosu pro uzly clusteru?

Ano, azure dev spaces v clusterech AKS s [omezeným přenosem odchozího přenosu pro uzly clusteru][aks-restrict-egress-traffic] povolené po povolení správné sítě souborů se kvofací. Další informace o používání clusterů AKS s omezeným přenosem odchozího přenosu pro uzly clusteru povolené pomocí Azure Dev Spaces jsou k dispozici [zde](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Můžu používat Azure Dev Spaces v clusterech AKS s podporou RBAC?

Ano, azure dev spaces v clusterech AKS s povoleným RBAC nebo bez něj.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co se stane, když povolím příchozí přenos dat pro projekt v sadě Visual Studio?

Při použití sady Visual Studio k přípravě projektu máte možnost povolení příchozího přenosu dat pro vaši službu. Povolení příchozího přenosu dat vytvoří veřejný koncový bod pro přístup ke službě při spuštění v clusteru AKS, což je volitelné. Pokud nepovolíte příchozí přenos dat, vaše služba je přístupná pouze z clusteru AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Můžu používat spravované identity podu s Azure Dev Spaces?

Ano, můžete použít [pod spravované identity][aks-pod-managed-id] v clusterech AKS s azure dev spaces povoleno, ale existují [další kroky konfigurace][dev-spaces-pod-managed-id-steps] po povolení Azure Dev Spaces ve vašem clusteru s pod spravované identity. Pokud máte nainstalovány spravované identity pod a chcete je odinstalovat, najdete další podrobnosti v poznámkách k [odinstalaci][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Můžu v aplikaci používat Azure Dev Spaces s více mikroslužbami?

Ano, můžete použít Azure Dev Spaces v aplikaci s více mikroslužeb, ale je nutné připravit a spustit jednotlivé mikroslužeb v jejich kořenovém adresáři. Azure Dev Spaces CLI, rozšíření Azure Dev Spaces VS Code a úlohy Visual Studio Azure Development očekávají, že soubor *azds.yaml* bude v kořenovém adresáři mikroslužby za účelem spuštění a ladění. Podívejte se na [ukázkovou aplikaci Bike Sharing][bike-sharing] pro příklad více mikroslužeb v jedné aplikaci.

V kódu Visual Studio je možné [otevřít samostatné projekty v jednom pracovním prostoru][vs-code-multi-root-workspaces] a ladit je samostatně prostřednictvím Azure Dev Spaces. Každý z projektů musí být samostatný a připravený pro Azure Dev Spaces.

V sadě Visual Studio je možné nakonfigurovat řešení .NET Core pro ladění prostřednictvím Azure Dev Spaces.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md