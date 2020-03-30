---
title: Nejčastější dotazy týkající se služby Azure Kubernetes Service (AKS)
description: Najděte odpovědi na některé běžné otázky týkající se služby Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497769"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy ohledně služby Azure Kubernetes Service (AKS)

Tento článek řeší časté otázky týkající se služby Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Které oblasti Azure aktuálně poskytují AKS?

Úplný seznam dostupných oblastí najdete v tématu [Oblasti AKS a dostupnost][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Podporuje AKS automatické škálování uzlů?

Ano, možnost automaticky škálovat uzly agentů vodorovně v AKS je aktuálně k dispozici ve verzi Preview. Pokyny najdete [v tématu Automatické škálování clusteru podle požadavků aplikací v AKS.][aks-cluster-autoscaler] Automatické škálování AKS je založeno na [automatickém škálování Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžu aks nasadit do stávající virtuální sítě?

Ano, cluster AKS můžete nasadit do existující virtuální sítě pomocí [rozšířené síťové funkce][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Mohu omezit, kdo má přístup k serveru Kubernetes API?

Ano, přístup k serveru Rozhraní API Kubernetes můžete omezit pomocí [oblastí IP autorizovaných serveru API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Mohu zpřístupnit server Api Kubernetes pouze v rámci své virtuální sítě?

Ne v tuto chvíli, ale je to plánováno. Můžete sledovat průběh na [úložišti AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Můžu mít různé velikosti virtuálních počítače v jednom clusteru?

Ano, můžete použít různé velikosti virtuálních počítačů v clusteru AKS vytvořením [více fondů uzlů][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení použity pro uzly agenta AKS?

Azure automaticky použije opravy zabezpečení na linuxové uzly ve vašem clusteru podle nočního plánu. Jste však zodpovědní za zajištění, že tyto uzly Linux jsou restartovány podle potřeby. Máte několik možností pro restartování uzlů:

- Ručně prostřednictvím portálu Azure nebo azure cli.
- Upgradem clusteru AKS. Cluster upgraduje [kordon a vypouštění uzlů][cordon-drain] automaticky a pak přinést nový uzel on-line s nejnovějším obrázkem Ubuntu a novou verzi patch nebo menší verzi Kubernetes. Další informace naleznete [v tématu Upgrade clusteru AKS][aks-upgrade].
- Pomocí [Kured](https://github.com/weaveworks/kured), open-source restart daemon pro Kubernetes. Kured běží jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a monitoruje každý uzel pro přítomnost souboru, který označuje, že je vyžadováno restartování. V celém clusteru jsou restartování operačního systému spravována stejným [procesem vyprazdňování a vypouštění][cordon-drain] jako upgrade clusteru.

Další informace o používání kured, naleznete [v tématu Použití zabezpečení a aktualizace jádra uzly v AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Uzly systému Windows Server

U uzlů systému Windows Server (aktuálně ve verzi Preview v AKS) se služba Windows Update automaticky nespustí a nepoužije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání služby Windows Update a vlastního procesu ověřování byste měli provést upgrade v clusteru a fondu uzlů systému Windows Server v clusteru AKS. Tento proces upgradu vytvoří uzly, které spustí nejnovější bitovou kopii a opravy systému Windows Server, a potom odebere starší uzly. Další informace o tomto procesu naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořeny pomocí AKS?

AKS staví na řadě prostředků infrastruktury Azure, včetně škálovacích sad virtuálních počítačů, virtuálních sítí a spravovaných disků. To vám umožní využít mnoho základních funkcí platformy Azure v rámci spravovaného prostředí Kubernetes poskytovaného AKS. Například většinu typů virtuálních počítačů Azure lze použít přímo s AKS a Rezervace Azure lze použít k automatickému získání slev na tyto prostředky.

Chcete-li povolit tuto architekturu, každé nasazení AKS zahrnuje dvě skupiny prostředků:

1. Vytvoříte první skupinu prostředků. Tato skupina obsahuje pouze prostředek služby Kubernetes. Zprostředkovatel prostředků AKS automaticky vytvoří druhou skupinu prostředků během nasazení. Příkladem druhé skupiny prostředků je *MC_myResourceGroup_myAKSCluster_eastus*. Informace o tom, jak zadat název této druhé skupiny prostředků, naleznete v další části.
1. Druhá skupina prostředků, označovaná jako *skupina prostředků uzlu*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Mezi tyto prostředky patří virtuální počítače uzlů Kubernetes, virtuální sítě a úložiště. Ve výchozím nastavení má skupina prostředků uzlu název *jako MC_myResourceGroup_myAKSCluster_eastus*. AKS automaticky odstraní prostředek uzlu při každém odstranění clusteru, takže by měl být použit pouze pro prostředky, které sdílejí životní cyklus clusteru.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Mohu zadat svůj vlastní název pro skupinu prostředků uzlu AKS?

Ano. Ve výchozím nastavení aks pojmenuje skupinu prostředků uzlu *MC_resourcegroupname_clustername_location*, ale můžete také zadat vlastní název.

Chcete-li zadat název vlastní skupiny prostředků, nainstalujte rozšíření Azure CLI [verze][aks-preview-cli] *0.3.2* nebo novější. Při vytváření clusteru AKS pomocí příkazu [az aks create][az-aks-create] použijte parametr *--node-resource-group* a zadejte název skupiny prostředků. Pokud k nasazení clusteru AKS [použijete šablonu Azure Resource Manager,][aks-rm-template] můžete definovat název skupiny prostředků pomocí vlastnosti *nodeResourceGroup.*

* Sekundární skupina prostředků se automaticky vytvoří poskytovatelem prostředků Azure ve vašem vlastním předplatném.
* Název vlastní skupiny prostředků můžete zadat pouze při vytváření clusteru.

Při práci se skupinou prostředků uzlu mějte na paměti, že nemůžete:

* Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
* Zadejte jiné předplatné pro skupinu prostředků uzlu.
* Po vytvoření clusteru změňte název skupiny prostředků uzlu.
* Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
* Upravte nebo odstraňte značky spravovaných prostředků v rámci skupiny prostředků uzlu. (Další informace naleznete v další části.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Lze upravit značky a další vlastnosti prostředků AKS ve skupině prostředků uzlu?

Pokud změníte nebo odstraníte značky vytvořené Azure a další vlastnosti prostředků ve skupině prostředků uzlu, můžete získat neočekávané výsledky, jako je škálování a upgrady chyb. AKS umožňuje vytvářet a upravovat vlastní značky. Můžete chtít vytvořit nebo upravit vlastní značky, například pro přiřazení organizační jednotky nebo nákladového střediska. Úpravou prostředků ve skupině prostředků uzlu v clusteru AKS přerušíte cíl na úrovni služby (SLO). Další informace naleznete v [tématu Nabízí AKS smlouvu o úrovni služeb?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Co AKS podporuje? Mohou být přijímací kontrolory přidány nebo odebrány?

AKS podporuje následující [přijímací kontrolory][admission-controllers]:

- *Obor názvůŽivotní cyklus*
- *LimitRanger*
- *Účet služby*
- *Výchozí třída úložiště*
- *Výchozí toleraceSekundy*
- *MutatingAdmissionWebhook*
- *ValidaceAdmissionWebhook*
- *Kvóta zdrojů*

V současné době nelze upravit seznam přijímacích řadičů v AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Je Azure Key Vault integrovaný s AKS?

AKS není aktuálně nativně integrovaná s Azure Key Vault. [Azure Key Vault FlexVolume pro kubernetes projektu][keyvault-flexvolume] však umožňuje přímou integraci z kubernetes pods do key vault tajných kódů.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Mohu spustit kontejnery Windows Server na AKS?

Ano, kontejnery windows serveru jsou k dispozici ve verzi Preview. Chcete-li spustit kontejnery systému Windows Server v systému AKS, vytvořte fond uzlů, ve které je systém Windows Server spuštěn jako hostovaný operační systém. Kontejnery Windows Serveru mohou používat pouze Windows Server 2019. Informace o zahájení začínání najdete [v tématu Vytvoření clusteru AKS s fondem uzlů systému Windows Server][aks-windows-cli].

Podpora systému Windows Server pro fond uzlů zahrnuje některá omezení, která jsou součástí projektu windows server v kubernetes. Další informace o těchto omezeních naleznete [v tématu Kontejnery systému Windows Server v omezeních AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

Ve smlouvě o úrovni služeb (SLA) poskytovatel souhlasí s tím, že zákazníkovi uhradí náklady na službu, pokud není splněna zveřejněná úroveň služeb. Vzhledem k tomu, AKS je zdarma, žádné náklady jsou k dispozici pro placení, takže AKS nemá žádné formální SLA. AKS se však snaží zachovat dostupnost alespoň 99,5 procenta pro server rozhraní API Kubernetes.

Je důležité si uvědomit rozdíl mezi dostupností služby AKS, která odkazuje na dobu provozuprovozu řídicí roviny Kubernetes a dostupností vašeho konkrétního pracovního vytížení, které běží na virtuálních počítačích Azure. I když rovina ovládacího prvku může být nedostupná, pokud rovina ovládacího prvku není připravená, úlohy clusteru spuštěné na virtuálních počítačích Azure mohou stále fungovat. Vzhledem k tomu, že virtuální počítače Azure jsou placené prostředky, které jsou zálohovány finanční sla. Přečtěte si [zde další podrobnosti o](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sla virtuálního počítače Azure a jak zvýšit dostupnost s funkcemi, jako jsou zóny [dostupnosti][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Proč nemohu nastavit maxPods pod 30?

V AKS můžete nastavit `maxPods` hodnotu při vytváření clusteru pomocí azure CLI a Azure Resource Manager šablony. Kubenet i Azure CNI však vyžadují *minimální hodnotu* (ověřenou v době vytvoření):

| Síťové služby | Minimální | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet (Kubenet) | 30 | 110 |

Vzhledem k tomu, že AKS je spravovaná služba, nasazujeme a spravujeme doplňky a pody jako součást clusteru. V minulosti uživatelé `maxPods` mohli definovat hodnotu nižší než hodnota, která spravované pody potřebné ke spuštění (například 30). AKS nyní vypočítá minimální počet lusků pomocí tohoto vzorce: ((maxPods nebo (maxPods * vm_count)) > řízené add-on lusky minimum.

Uživatelé nemohou přepsat minimální `maxPods` ověření.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Můžu použít slevy na rezervace Azure na uzly agenta AKS?

Uzly agenta AKS se účtují jako standardní virtuální počítače Azure, takže pokud jste zakoupili [rezervace Azure][reservation-discounts] pro velikost virtuálního počítače, kterou používáte v AKS, tyto slevy se automaticky použijí.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Můžu přesouvat nebo migrovat svůj cluster mezi klienty Azure?

Příkaz `az aks update-credentials` lze přesunout clusterU AKS mezi klienty Azure. Postupujte podle pokynů v [části Zvolte aktualizaci nebo vytvoření instančního objektu a](https://docs.microsoft.com/azure/aks/update-credentials) potom [aktualizujte cluster AKS s novými pověřeními](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Můžu přesouvat nebo migrovat svůj cluster mezi předplatnými?

Pohyb clusterů mezi odběry není v současné době podporován.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Můžu přesunout clustery AKS z aktuálního předplatného Azure do jiného? 

Přesunutí clusteru AKS a přidružených prostředků mezi předplatnými Azure není podporováno.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Proč odstranění clusteru trvá tak dlouho? 

Většina clusterů je na žádost uživatele odstraněna. v některých případech, zejména pokud zákazníci přinášejí vlastní skupinu prostředků nebo provádějí křížové úlohy RG, může trvat více času nebo selhání. Pokud máte problém s odstraní, zkontrolujte, že nemáte zámky na RG, že všechny prostředky mimo RG jsou odpojeny od RG, atd.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Pokud mám pod / nasazení ve stavu 'NodeLost' nebo 'Neznámý' mohu ještě upgradovat svůj cluster?

Můžete, ale AKS to nedoporučuje. Upgrady by měly být v ideálním případě provedeny, pokud je stav clusteru známý a v pořádku.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Pokud mám cluster s jedním nebo více uzly ve stavu Není v pořádku nebo vypnout, lze provést upgrade?

Ne, odstraňte nebo odeberte všechny uzly ve stavu selhání nebo jinak odeberte z clusteru před upgradem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Bylo spuštěno odstranění clusteru, ale došlo k chybě`[Errno 11001] getaddrinfo failed` 

Nejčastěji je to způsobeno tím, že uživatelé mají jednu nebo více skupin zabezpečení sítě (NSG) stále používána a přidružené ke clusteru.  Odeberte je a pokuste se o odstranění znovu.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Běžel jsem upgrade, ale teď moje lusky jsou v crash smyčky, a připravenost sondy nezdaří?

Potvrďte, že platnost instančního objektu nevypršela.  Viz: [Instanční objekt Služby AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) a [pověření pro aktualizaci AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Můj cluster pracoval, ale najednou nemůže zřídit LoadBalancers, připojit PVCs, atd.? 

Potvrďte, že platnost instančního objektu nevypršela.  Viz: [Instanční objekt Služby AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) a [pověření pro aktualizaci AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Můžu použít nastavení nastavení velikosti virtuálního počítače k ručnímu škálování?

Ne, operace škálování pomocí nastavení nastavení škálování virtuálních strojů nejsou podporovány. Použijte aks api`az aks scale`( ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Můžu použít škálovací sady virtuálních strojů k ručnímu škálování na 0 uzlů?

Ne, operace škálování pomocí nastavení nastavení škálování virtuálních strojů nejsou podporovány.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Můžu zastavit nebo de-alokovat všechny své virtuální hody?

Zatímco AKS má mechanismy odolnosti, aby vydržely takovou konfiguraci a zotavily se z ní, nejedná se o doporučenou konfiguraci.

## <a name="can-i-use-custom-vm-extensions"></a>Můžu použít vlastní rozšíření virtuálních virtuálních montovek?

Žádná služba AKS není spravovaná služba a manipulace s prostředky IaaS není podporována. Chcete-li nainstalovat vlastní komponenty, atd. využijte api a mechanismy Kubernetes. Například využít DaemonSets k instalaci požadovaných součástí.

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
