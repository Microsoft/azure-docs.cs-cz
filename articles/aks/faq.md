---
title: Nejčastější dotazy ke službě Azure Kubernetes (AKS)
description: Vyhledejte odpovědi na některé běžné dotazy ke službě Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: d46b3ba9e3df5e2b3600db2be2a41789fed5242f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207967"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy ohledně služby Azure Kubernetes Service (AKS)

Tento článek popisuje časté otázky ke službě Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Které oblasti Azure aktuálně poskytují AKS?

Úplný seznam dostupných oblastí najdete v tématu [AKS oblasti a dostupnost][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Je možné rozložit cluster AKS napříč různými oblastmi?

Ne. Clustery AKS jsou regionální prostředky a nemůžou zahrnovat oblasti. Pokyny k vytvoření architektury, která obsahuje více oblastí, najdete v tématu [osvědčené postupy pro provozní kontinuitu a zotavení po havárii][bcdr-bestpractices] .

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Je možné rozložit cluster AKS napříč zónami dostupnosti?

Yes. Cluster AKS můžete nasadit v rámci jedné nebo více [zón dostupnosti][availability-zones] v [oblastech, které je podporují][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Můžu omezit, kdo má přístup k serveru rozhraní Kubernetes API?

Yes. K dispozici jsou dvě možnosti omezení přístupu k serveru rozhraní API:

- Použijte [rozsahy povolených IP adres serveru API][api-server-authorized-ip-ranges] , pokud chcete zachovat veřejný koncový bod pro Server rozhraní API, ale omezte přístup na sadu důvěryhodných IP adres.
- Pokud chcete omezit Server API tak, aby byl dostupný *jenom* v rámci vaší virtuální sítě, použijte [Privátní cluster][private-clusters] .

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Můžu v jednom clusteru mít různé velikosti virtuálních počítačů?

Ano, v clusteru AKS můžete pomocí různých velikostí virtuálních počítačů vytvořit [více fondů uzlů][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení aplikovány na uzly agenta AKS?

Azure automaticky aplikuje opravy zabezpečení na uzly Linux v clusteru v nočním plánu. Zodpovídáte však za zajištění, že se tyto uzly Linux restartují podle potřeby. Pro restartování uzlů máte několik možností:

- Ručně prostřednictvím Azure Portal nebo rozhraní příkazového řádku Azure CLI.
- Upgradem clusteru AKS. Cluster upgraduje [uzly Cordon a vyprazdňuje][cordon-drain] automaticky a potom přinese nový uzel do online režimu s nejnovější imagí Ubuntu a novou verzí opravy nebo menší verzí Kubernetes. Další informace najdete v tématu [upgrade clusteru AKS][aks-upgrade].
- Pomocí [Kured](https://github.com/weaveworks/kured)je open-source démon pro Kubernetes. Kured běží jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a monitoruje každý uzel pro přítomnost souboru, který indikuje, že je potřeba restartovat počítač. V rámci clusteru se restartování operačního systému spravují stejným [cordonm a vyprázdněným procesem][cordon-drain] jako upgrade clusteru.

Další informace o použití kured najdete v tématu [použití zabezpečení a aktualizací jádra na uzlech v AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Uzly Windows serveru

V případě uzlů Windows serveru se web Windows Update automaticky nespustí a nepoužije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání web Windows Update a vlastního procesu ověřování byste měli provést upgrade v clusteru a fondech uzlů Windows serveru v clusteru AKS. Tento proces upgradu vytvoří uzly, na kterých běží nejnovější image a opravy Windows serveru, a pak odebere starší uzly. Další informace o tomto procesu najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?

AKS se sestavuje na několika prostředcích infrastruktury Azure, včetně služby Virtual Machine Scale Sets, virtuálních sítí a spravovaných disků. Díky tomu můžete využívat spoustu základních funkcí platformy Azure v rámci spravovaného prostředí Kubernetes, které poskytuje AKS. Například většina typů virtuálních počítačů Azure se dá použít přímo s AKS a Azure Reservations se dá použít k automatickému přijetí slev na tyto prostředky.

Pro povolení této architektury zahrnuje každé nasazení AKS dvě skupiny prostředků:

1. Vytvoříte první skupinu prostředků. Tato skupina obsahuje pouze prostředek služby Kubernetes. Poskytovatel prostředků AKS během nasazování automaticky vytvoří druhou skupinu prostředků. Příkladem druhé skupiny prostředků je *MC_myResourceGroup_myAKSCluster_eastus*. Informace o tom, jak zadat název této druhé skupiny prostředků, najdete v další části.
1. Druhá skupina prostředků, označovaná jako *Skupina prostředků uzlu*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Mezi tyto prostředky patří virtuální počítače uzlu Kubernetes, virtuální síť a úložiště. Ve výchozím nastavení má skupina prostředků uzlu název, jako *MC_myResourceGroup_myAKSCluster_eastus*. AKS automaticky odstraní prostředek uzlu, když se cluster odstraní, takže by se měl používat jenom pro prostředky, které sdílejí životní cyklus clusteru.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Můžu pro skupinu prostředků uzlu AKS zadat vlastní název?

Yes. Ve výchozím nastavení AKS pojmenuje skupinu prostředků uzlu *MC_resourcegroupname_clustername_location*, ale můžete také zadat vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI [AKS-Preview][aks-preview-cli] verze *0.3.2* nebo novější. Při vytváření clusteru AKS pomocí příkazu [AZ AKS Create][az-aks-create] použijte parametr *--Node-Resource-Group* a zadejte název skupiny prostředků. Pokud k nasazení clusteru AKS [použijete šablonu Azure Resource Manager][aks-rm-template] , můžete definovat název skupiny prostředků pomocí vlastnosti *nodeResourceGroup* .

* Sekundární skupinu prostředků automaticky vytvoří poskytovatel prostředků Azure ve vlastním předplatném.
* Vlastní název skupiny prostředků můžete zadat jenom při vytváření clusteru.

Při práci s skupinou prostředků uzlu Pamatujte na to, že nemůžete:

* Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
* Zadejte jiné předplatné pro skupinu prostředků uzlu.
* Po vytvoření clusteru změňte název skupiny prostředků uzlu.
* Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
* Upravte nebo odstraňte značky spravovaných prostředků vytvořené v Azure v rámci skupiny prostředků uzlu. (Další informace najdete v další části.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Můžu změnit značky a další vlastnosti prostředků AKS ve skupině prostředků uzlu?

Pokud upravíte nebo odstraníte značky vytvořené v Azure a další vlastnosti prostředku v rámci skupiny prostředků uzlu, můžete získat neočekávané výsledky, jako je například škálování a upgrade chyb. AKS umožňuje vytvářet a upravovat vlastní značky vytvořené koncovými uživateli. Tyto značky můžete přidat při [vytváření fondu uzlů](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Můžete chtít vytvořit nebo upravit vlastní značky, například pro přiřazení obchodní jednotky nebo nákladového centra. Toho je možné dosáhnout také vytvořením zásad Azure s oborem ve spravované skupině prostředků.

Úprava **značek vytvořených v Azure** u prostředků v rámci skupiny prostředků uzlu v clusteru AKS se ale nejedná o nepodporovanou akci, která zruší cíl na úrovni služby (SLO). Další informace najdete v tématu [AKS nabízí smlouvu o úrovni služeb?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jaké řadiče pro přijímání Kubernetes podporuje AKS? Je možné přidat nebo odebrat řadiče pro přístup?

AKS podporuje následující [řadiče pro přístup][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

V současné době nemůžete upravit seznam řadičů pro příjem v AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Můžu na AKS použít Webhooky kontroleru přístupu?

Ano, v AKS můžete použít Webhooky kontroleru přístupu. Doporučuje se vyloučit interní obory názvů AKS, které jsou označené **označením řídicí roviny.** Například přidáním níže do konfigurace Webhooku:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Můžou Webhooky řadiče pro přístup ovlivnit Kube obory názvů System a Internal AKS?

Za účelem ochrany stability systému a zabránění vlastním řadičům přístupu z důvodu ovlivnění interních služeb v Kube oboru názvů AKS má modul pro **přijímání**, který automaticky vylučuje Kube-systém a AKS interní obory názvů. Tato služba zajišťuje, že vlastní řadiče pro přijímání neovlivňují služby běžící v Kube-System.

Pokud máte důležitý případ použití nástroje, který je nasazený v systému Kube (nedoporučuje se), který vyžadujete, aby se přidal do svého vlastního Webhooku, můžete přidat následující popisek nebo anotaci, aby se přístup vynuceně ignoroval.

Popisek: ```"admissions.enforcer/disabled": "true"``` nebo Poznámka: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Je Azure Key Vault integrována s AKS?

AKS není aktuálně nativně integrována s Azure Key Vault. Nicméně [poskytovatel Azure Key Vault pro úložiště tajných klíčů][csi-driver] umožňuje přímou integraci z Kubernetes lusků do Key Vault tajných kódů.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžu na AKS spouštět kontejnery Windows serveru?

Ano, kontejnery Windows serveru jsou dostupné na AKS. Pokud chcete spouštět kontejnery Windows serveru v AKS, vytvořte fond uzlů, na kterém běží Windows Server, jako hostovaný operační systém. Kontejnery Windows serveru můžou používat jenom Windows Server 2019. Informace o tom, jak začít, najdete v tématu [Vytvoření clusteru AKS s fondem uzlů Windows serveru][aks-windows-cli].

Podpora Windows serveru pro fond uzlů obsahuje některá omezení, která jsou součástí nadřazeného Windows serveru v projektu Kubernetes. Další informace o těchto omezeních najdete v tématu věnovaném [omezením AKS v kontejnerech Windows serveru][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

AKS poskytuje záruky smlouvy SLA jako volitelné přidání funkce s smlouvou [SLA pro provozuschopnost][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Můžu u svých uzlů agentů AKS uplatnit slevy na rezervované platformy Azure?

Uzly agenta AKS se účtují jako standardní virtuální počítače Azure, takže pokud jste zakoupili [rezervace Azure][reservation-discounts] pro velikost virtuálního počítače, kterou používáte v AKS, budou se tyto slevy používat automaticky.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Můžu svůj cluster přesunout nebo migrovat mezi klienty Azure?

Přesunutí clusteru AKS mezi klienty se aktuálně nepodporuje.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Můžu cluster přesunout/migrovat mezi předplatnými?

Přesun clusterů mezi předplatnými není aktuálně podporován.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Můžu svoje clustery AKS přesunout z aktuálního předplatného Azure do jiného? 

Přesunutí clusteru AKS a jeho přidružených prostředků mezi předplatnými Azure se nepodporuje.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Můžu svůj cluster AKS nebo prostředky infrastruktury AKS přesunout do jiných skupin prostředků nebo je přejmenovat?

Přesunutí nebo přejmenování clusteru AKS a jeho přidružených prostředků se nepodporuje.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Proč je můj cluster trvá tak dlouho? 

Většina clusterů se po vyžádání uživatele odstraní; v některých případech, zejména v případě, že zákazníci přinášejí svoji vlastní skupinu prostředků nebo že odstraňování úloh mezi RGy může trvat delší dobu nebo selhat. Pokud máte problém s odstraněním, poklikejte na RG, že nejsou k dispozici žádné prostředky mimo RG od RG atd.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Pokud mám v/v nasazení stav ' NodeLost ' nebo ' neznámé ', je možné cluster stále upgradovat?

To platí, ale AKS to nedoporučuje. Upgrady by se měly provádět v ideálním případě, když je stav clusteru známý a v pořádku.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Pokud mám cluster s minimálně jedním uzlem ve stavu není v pořádku nebo je možné ho vypnout, mohu provést upgrade?

Ne, odstraňte nebo odeberte všechny uzly ve stavu selhání nebo jinak odeberte z clusteru před upgradem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Spustil (a) jsem cluster s odstraněním, ale zobrazí se chyba `[Errno 11001] getaddrinfo failed` 

Nejčastěji to je způsobeno tím, že uživatelé, kteří mají jednu nebo více skupin zabezpečení sítě (skupin zabezpečení sítě) stále používány a jsou přidruženi ke clusteru.  Odeberte je prosím a pokuste se o odstranění znovu.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Spustil (a) jsem upgrade, ale teď jsou moje lusky v cyklech havárií a testy připravenosti selžou?

Potvrďte prosím, že váš instanční objekt nevypršel.  Viz: [AKS instanční objekt](./kubernetes-service-principal.md) a [přihlašovací údaje pro AKS aktualizace](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>Můj cluster fungoval, ale náhle nedokáže zřídit LoadBalancers, připojení PVC atd.? 

Potvrďte prosím, že váš instanční objekt nevypršel.  Viz: [AKS instanční objekt](./kubernetes-service-principal.md)  a [přihlašovací údaje pro AKS aktualizace](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Můžu svůj cluster AKS škálovat na nulu?
[Běžící cluster AKS](start-stop-cluster.md)můžete úplně zastavit a ušetřit tak příslušné výpočetní náklady. Kromě toho můžete také [škálovat nebo automaticky škálovat všechny nebo konkrétní `User` fondy uzlů](scale-cluster.md#scale-user-node-pools-to-0) na hodnotu 0 a přitom zachovat jenom nezbytnou konfiguraci clusteru.
[Fondy systémových uzlů](use-system-pools.md) nemůžete přímo škálovat na 0.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Můžu použít rozhraní API sady škálování pro virtuální počítače k ručnímu škálování?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují. Použijte rozhraní API AKS ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Můžu pomocí sady škálování virtuálních počítačů ručně škálovat na 0 uzlů?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Můžu zastavit nebo zrušit přidělení všech virtuálních počítačů?

I když AKS má mechanismy odolnosti k vyvýšení těchto konfigurací a obnovení z něj, nejedná se o doporučenou konfiguraci.

## <a name="can-i-use-custom-vm-extensions"></a>Můžu použít vlastní rozšíření virtuálních počítačů?

Ne, AKS je spravovaná služba a manipulace s prostředky IaaS není podporovaná. Instalace vlastních komponent atd. Využijte prosím rozhraní API a mechanismů Kubernetes. Můžete například využít DaemonSets k instalaci požadovaných součástí.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Ukládá AKS data o zákaznících mimo oblast clusteru?

Funkce pro ukládání zákaznických dat v jedné oblasti je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) Asie a Tichomoří geografické oblasti. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
