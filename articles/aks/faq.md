---
title: Nejčastější dotazy ke službě Azure Kubernetes (AKS)
description: Vyhledejte odpovědi na některé běžné dotazy ke službě Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 1531308a8d0bd5a09952d8ad8ccd03c92f2f99eb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252945"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy týkající se služby Azure Kubernetes (AKS)

Tento článek popisuje časté otázky ke službě Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Které oblasti Azure aktuálně poskytují AKS?

Úplný seznam dostupných oblastí najdete v tématu [AKS oblasti a dostupnost][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Podporuje AKS automatické škálování uzlu?

Ano, schopnost automaticky škálovat uzly agentů vodorovně v AKS je v současnosti dostupná ve verzi Preview. Pokyny najdete [v tématu Automatické škálování clusteru pro splnění požadavků aplikace v AKS][aks-cluster-autoscaler] . Automatické škálování AKS je založené na [Kubernetes automatického škálování][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžu AKS nasadit do existující virtuální sítě?

Ano, cluster AKS můžete nasadit do existující virtuální sítě pomocí [funkce pokročilé sítě][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Můžu omezit, kdo má přístup k serveru rozhraní Kubernetes API?

Ano, můžete omezit přístup k serveru rozhraní Kubernetes API pomocí [rozsahů IP adres autorizovaných serverem API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Můžu Server Kubernetes API zpřístupnit jenom v rámci své virtuální sítě?

V tuto chvíli se ale plánuje. Průběh můžete sledovat v [úložišti GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Můžu v jednom clusteru mít různé velikosti virtuálních počítačů?

Ano, v clusteru AKS můžete pomocí různých velikostí virtuálních počítačů vytvořit [více fondů uzlů][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení aplikovány na uzly agenta AKS?

Azure automaticky aplikuje opravy zabezpečení na uzly Linux v clusteru v nočním plánu. Zodpovídáte však za zajištění, že se tyto uzly Linux restartují podle potřeby. Pro restartování uzlů máte několik možností:

- Ručně prostřednictvím Azure Portal nebo rozhraní příkazového řádku Azure CLI.
- Upgradem clusteru AKS. Cluster upgraduje [uzly Cordon a vyprazdňuje][cordon-drain] automaticky a potom přinese nový uzel do online režimu s nejnovější imagí Ubuntu a novou verzí opravy nebo menší verzí Kubernetes. Další informace najdete v tématu [upgrade clusteru AKS][aks-upgrade].
- Pomocí [Kured](https://github.com/weaveworks/kured)je open-source démon pro Kubernetes. Kured běží jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a monitoruje každý uzel pro přítomnost souboru, který indikuje, že je potřeba restartovat počítač. V rámci clusteru se restartování operačního systému spravují stejným [cordonm a vyprázdněným procesem][cordon-drain] jako upgrade clusteru.

Další informace o použití kured najdete v tématu [použití zabezpečení a aktualizací jádra na uzlech v AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Uzly Windows serveru

V případě uzlů Windows serveru (aktuálně ve verzi Preview v AKS) web Windows Update nespustí automaticky a použije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání web Windows Update a vlastního procesu ověřování byste měli provést upgrade v clusteru a fondech uzlů Windows serveru v clusteru AKS. Tento proces upgradu vytvoří uzly, na kterých běží nejnovější image a opravy Windows serveru, a pak odebere starší uzly. Další informace o tomto procesu najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?

AKS se sestavuje na několika prostředcích infrastruktury Azure, včetně služby Virtual Machine Scale Sets, virtuálních sítí a spravovaných disků. Díky tomu můžete využívat spoustu základních funkcí platformy Azure v rámci spravovaného prostředí Kubernetes, které poskytuje AKS. Například většina typů virtuálních počítačů Azure se dá použít přímo s AKS a Azure Reservations se dá použít k automatickému přijetí slev na tyto prostředky.

Pro povolení této architektury zahrnuje každé nasazení AKS dvě skupiny prostředků:

1. Vytvoříte první skupinu prostředků. Tato skupina obsahuje pouze prostředek služby Kubernetes. Poskytovatel prostředků AKS během nasazování automaticky vytvoří druhou skupinu prostředků. Příkladem druhé skupiny prostředků je *MC_myResourceGroup_myAKSCluster_eastus*. Informace o tom, jak zadat název této druhé skupiny prostředků, najdete v další části.
1. Druhá skupina prostředků, označovaná jako *Skupina prostředků uzlu*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Mezi tyto prostředky patří virtuální počítače uzlu Kubernetes, virtuální síť a úložiště. Ve výchozím nastavení má skupina prostředků uzlu název, jako *MC_myResourceGroup_myAKSCluster_eastus*. AKS automaticky odstraní prostředek uzlu, když se cluster odstraní, takže by se měl používat jenom pro prostředky, které sdílejí životní cyklus clusteru.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Můžu pro skupinu prostředků uzlu AKS zadat vlastní název?

Ano. Ve výchozím nastavení AKS pojmenuje skupinu prostředků uzlu *MC_resourcegroupname_clustername_location*, ale můžete také zadat vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI [AKS-Preview][aks-preview-cli] verze *0.3.2* nebo novější. Při vytváření clusteru AKS pomocí příkazu [AZ AKS Create][az-aks-create] použijte parametr *--Node-Resource-Group* a zadejte název skupiny prostředků. Pokud k nasazení clusteru AKS [použijete šablonu Azure Resource Manager][aks-rm-template] , můžete definovat název skupiny prostředků pomocí vlastnosti *nodeResourceGroup* .

* Sekundární skupinu prostředků automaticky vytvoří poskytovatel prostředků Azure ve vlastním předplatném.
* Vlastní název skupiny prostředků můžete zadat jenom při vytváření clusteru.

Při práci s skupinou prostředků uzlu Pamatujte na to, že nemůžete:

* Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
* Zadejte jiné předplatné pro skupinu prostředků uzlu.
* Po vytvoření clusteru změňte název skupiny prostředků uzlu.
* Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
* Upravte nebo odstraňte značky spravovaných prostředků v rámci skupiny prostředků uzlu. (Další informace najdete v další části.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Můžu změnit značky a další vlastnosti prostředků AKS ve skupině prostředků uzlu?

Pokud upravíte nebo odstraníte značky vytvořené v Azure a další vlastnosti prostředku v rámci skupiny prostředků uzlu, můžete získat neočekávané výsledky, jako je například škálování a upgrade chyb. AKS umožňuje vytvářet a upravovat vlastní značky. Můžete chtít vytvořit nebo upravit vlastní značky, například pro přiřazení obchodní jednotky nebo nákladového centra. Změnou prostředků v rámci skupiny prostředků uzlu v clusteru AKS zrušíte cíl na úrovni služby (SLO). Další informace najdete v tématu [AKS nabízí smlouvu o úrovni služeb?](#does-aks-offer-a-service-level-agreement)

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

## <a name="is-azure-key-vault-integrated-with-aks"></a>Je Azure Key Vault integrována s AKS?

AKS není aktuálně nativně integrována s Azure Key Vault. [Azure Key Vault FlexVolume for Kubernetes Project][keyvault-flexvolume] však umožňuje přímé začlenění z Kubernetes do Key Vault tajných kódů.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžu na AKS spouštět kontejnery Windows serveru?

Ano, kontejnery Windows serveru jsou dostupné ve verzi Preview. Pokud chcete spouštět kontejnery Windows serveru v AKS, vytvořte fond uzlů, na kterém běží Windows Server, jako hostovaný operační systém. Kontejnery Windows serveru můžou používat jenom Windows Server 2019. Informace o tom, jak začít, najdete v tématu [Vytvoření clusteru AKS s fondem uzlů Windows serveru][aks-windows-cli].

Podpora Windows serveru pro fond uzlů obsahuje některá omezení, která jsou součástí nadřazeného Windows serveru v projektu Kubernetes. Další informace o těchto omezeních najdete v tématu věnovaném [omezením AKS v kontejnerech Windows serveru][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

V rámci smlouvy o úrovni služeb (SLA) poskytovatel souhlasí s tím, že zákazníkovi vrátí náklady na službu, pokud není splněna publikovaná úroveň služby. Vzhledem k tomu, že je AKS zdarma, nejsou k dispozici žádné náklady, takže AKS nemá žádnou oficiální smlouvu SLA. AKS se ale snaží zachovat dostupnost minimálně 99,5% pro server Kubernetes API.

Je důležité rozpoznat rozdíl mezi dostupností služby AKS, která odkazuje na dobu provozu Kubernetes řídicí roviny a dostupnost konkrétního zatížení, které běží na Azure Virtual Machines. I když rovina ovládacího prvku nemusí být k dispozici, pokud řídicí plocha není připravená, úlohy clusteru běžící na virtuálních počítačích Azure stále můžou fungovat. Vzhledem k tomu, že virtuální počítače Azure jsou placené prostředky, se účtují pomocí finanční smlouvy SLA. [Další podrobnosti](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) o smlouvě SLA pro virtuální počítače Azure a o tom, jak tuto dostupnost zvýšit, najdete tady: [zóny dostupnosti][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Proč nemohu nastavit maxPods nižší než 30?

V AKS můžete nastavit hodnotu `maxPods` při vytváření clusteru pomocí Azure CLI a Azure Resource Manager šablon. Kubenet i Azure CNI ale vyžadují *minimální hodnotu* (v době vytváření ověřený čas):

| Sítě | Minimální | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Vzhledem k tomu, že AKS je spravovaná služba, nasadíme a spravujeme doplňky a lusky jako součást clusteru. V minulosti mohli uživatelé definovat hodnotu `maxPods` nižší, než je hodnota, kterou spravované lusky vyžadují ke spuštění (například 30). AKS nyní vypočítá minimální počet lusků pomocí tohoto vzorce: ((maxPods nebo (maxPods * vm_count)) > spravovaného doplňku minimálních hodnot lusky.

Uživatelé nemohou přepsat minimální `maxPods` ověřování.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Můžu u svých uzlů agentů AKS uplatnit slevy na rezervované platformy Azure?

Uzly agenta AKS se účtují jako standardní virtuální počítače Azure, takže pokud jste zakoupili [rezervace Azure][reservation-discounts] pro velikost virtuálního počítače, kterou používáte v AKS, budou se tyto slevy používat automaticky.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Můžu svůj cluster přesunout nebo migrovat mezi klienty Azure?

Pomocí příkazu `az aks update-credentials` můžete přesunout cluster AKS mezi klienty Azure. Postupujte podle pokynů v části [Zvolte možnost aktualizovat nebo vytvořit instanční objekt](https://docs.microsoft.com/azure/aks/update-credentials) a pak [aktualizujte cluster AKS pomocí nových přihlašovacích údajů](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Můžu cluster přesunout/migrovat mezi předplatnými?

Přesun clusterů mezi předplatnými není aktuálně podporován.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Můžu svoje clustery AKS přesunout z aktuálního předplatného Azure do jiného? 

Přesunutí clusteru AKS a přidružené prostředky mezi předplatnými Azure se nepodporuje.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Proč je můj cluster trvá tak dlouho? 

Většina clusterů se po vyžádání uživatele odstraní; v některých případech, zejména v případě, že zákazníci přinášejí svoji vlastní skupinu prostředků nebo že odstraňování úloh mezi RGy může trvat delší dobu nebo selhat. Pokud máte problém s odstraněním, poklikejte na RG, že nejsou k dispozici žádné prostředky mimo RG od RG atd.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Pokud mám v/v nasazení stav ' NodeLost ' nebo ' neznámé ', je možné cluster stále upgradovat?

To platí, ale AKS to nedoporučuje. Upgrady by se měly provádět v ideálním případě, když je stav clusteru známý a v pořádku.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Pokud mám cluster s minimálně jedním uzlem ve stavu není v pořádku nebo je možné ho vypnout, mohu provést upgrade?

Ne, odstraňte nebo odeberte všechny uzly ve stavu selhání nebo jinak odeberte z clusteru před upgradem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Spustil (a) jsem cluster s odstraněním, ale zobrazí se chyba `[Errno 11001] getaddrinfo failed` 

Nejčastěji to je způsobeno tím, že uživatelé, kteří mají jednu nebo více skupin zabezpečení sítě (skupin zabezpečení sítě) stále používány a jsou přidruženi ke clusteru.  Odeberte je prosím a pokuste se o odstranění znovu.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Spustil (a) jsem upgrade, ale teď jsou moje lusky v cyklech havárií a testy připravenosti selžou?

Potvrďte prosím, že váš instanční objekt nevypršel.  Viz: [AKS instanční objekt](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) a [přihlašovací údaje pro AKS aktualizace](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Můj cluster fungoval, ale náhle neumožňuje zřídit LoadBalancers, připojení PVC atd.? 

Potvrďte prosím, že váš instanční objekt nevypršel.  Viz: [AKS instanční objekt](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) a [přihlašovací údaje pro AKS aktualizace](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Můžu použít rozhraní API sady škálování pro virtuální počítače k ručnímu škálování?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují. Použijte rozhraní API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Můžu pomocí sady škálování virtuálních počítačů ručně škálovat na 0 uzlů?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Můžu zastavit nebo zrušit přidělení všech virtuálních počítačů?

I když AKS má mechanismy odolnosti k vyvýšení těchto konfigurací a obnovení z něj, nejedná se o doporučenou konfiguraci.

## <a name="can-i-use-custom-vm-extensions"></a>Můžu použít vlastní rozšíření virtuálních počítačů?

Žádná AKS není spravovaná služba a manipulace s prostředky IaaS není podporovaná. Instalace vlastních komponent atd. Využijte prosím rozhraní API a mechanismů Kubernetes. Můžete například využít DaemonSets k instalaci požadovaných součástí.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
