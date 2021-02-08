---
title: Nejčastější dotazy ke službě Azure Kubernetes (AKS)
description: Vyhledejte odpovědi na některé běžné dotazy ke službě Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6c2eddf3b7002b101fed8face4a58f2d2b2f4878
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820258"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy ohledně služby Azure Kubernetes Service (AKS)

Tento článek popisuje časté otázky ke službě Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Které oblasti Azure aktuálně poskytují AKS?

Úplný seznam dostupných oblastí najdete v tématu [AKS oblasti a dostupnost][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Je možné rozložit cluster AKS napříč různými oblastmi?

No. Clustery AKS jsou regionální prostředky a nemůžou zahrnovat oblasti. Pokyny k vytvoření architektury, která obsahuje více oblastí, najdete v tématu [osvědčené postupy pro provozní kontinuitu a zotavení po havárii][bcdr-bestpractices] .

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Je možné rozložit cluster AKS napříč zónami dostupnosti?

Ano. Cluster AKS můžete nasadit v rámci jedné nebo více [zón dostupnosti][availability-zones] v [oblastech, které je podporují][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Můžu omezit, kdo má přístup k serveru rozhraní Kubernetes API?

Ano. K dispozici jsou dvě možnosti omezení přístupu k serveru rozhraní API:

- Použijte [rozsahy povolených IP adres serveru API][api-server-authorized-ip-ranges] , pokud chcete zachovat veřejný koncový bod pro Server rozhraní API, ale omezte přístup na sadu důvěryhodných IP adres.
- Pokud chcete omezit Server API tak, aby byl dostupný *jenom* v rámci vaší virtuální sítě, použijte [Privátní cluster][private-clusters] .

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Můžu v jednom clusteru mít různé velikosti virtuálních počítačů?

Ano, v clusteru AKS můžete pomocí různých velikostí virtuálních počítačů vytvořit [více fondů uzlů][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení aplikovány na uzly agenta AKS?

Azure automaticky aplikuje opravy zabezpečení na uzly Linux v clusteru v nočním plánu. Zodpovídáte však za to, že budete mít jistotu, že se tyto uzly Linux restartují podle potřeby. Pro restartování uzlů máte několik možností:

- Ručně prostřednictvím Azure Portal nebo rozhraní příkazového řádku Azure CLI.
- Upgradem clusteru AKS. Cluster upgraduje [uzly Cordon a vyprazdňuje][cordon-drain] automaticky a potom přinese nový uzel do online režimu s nejnovější imagí Ubuntu a novou verzí opravy nebo menší verzí Kubernetes. Další informace najdete v tématu [upgrade clusteru AKS][aks-upgrade].
- Pomocí [upgradu bitové kopie uzlu](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Uzly Windows serveru

V případě uzlů Windows serveru se web Windows Update automaticky nespustí a nepoužije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání web Windows Update a vlastního procesu ověřování byste měli provést upgrade v clusteru a fondech uzlů Windows serveru v clusteru AKS. Tento proces upgradu vytvoří uzly, na kterých běží nejnovější image a opravy Windows serveru, a pak odebere starší uzly. Další informace o tomto procesu najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?

AKS se sestavuje na několika prostředcích infrastruktury Azure, včetně služby Virtual Machine Scale Sets, virtuálních sítí a spravovaných disků. Díky tomu můžete využívat spoustu základních funkcí platformy Azure v rámci spravovaného prostředí Kubernetes, které poskytuje AKS. Například většina typů virtuálních počítačů Azure se dá použít přímo s AKS a Azure Reservations se dá použít k automatickému přijetí slev na tyto prostředky.

Pro povolení této architektury zahrnuje každé nasazení AKS dvě skupiny prostředků:

1. Vytvoříte první skupinu prostředků. Tato skupina obsahuje pouze prostředek služby Kubernetes. Poskytovatel prostředků AKS během nasazování automaticky vytvoří druhou skupinu prostředků. Příkladem druhé skupiny prostředků je *MC_myResourceGroup_myAKSCluster_eastus*. Informace o tom, jak zadat název této druhé skupiny prostředků, najdete v další části.
1. Druhá skupina prostředků, označovaná jako *Skupina prostředků uzlu*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Mezi tyto prostředky patří virtuální počítače uzlu Kubernetes, virtuální síť a úložiště. Ve výchozím nastavení má skupina prostředků uzlu název, jako *MC_myResourceGroup_myAKSCluster_eastus*. AKS automaticky odstraní prostředek uzlu, když se cluster odstraní, takže by se měl používat jenom pro prostředky, které sdílejí životní cyklus clusteru.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Můžu pro skupinu prostředků uzlu AKS zadat vlastní název?

Ano. Ve výchozím nastavení AKS pojmenuje skupinu prostředků uzlu *MC_resourcegroupname_clustername_location*, ale můžete také zadat vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI [AKS-Preview][aks-preview-cli] verze *0.3.2* nebo novější. Při vytváření clusteru AKS pomocí příkazu [AZ AKS Create][az-aks-create] použijte `--node-resource-group` parametr a zadejte název skupiny prostředků. Pokud k nasazení clusteru AKS [použijete šablonu Azure Resource Manager][aks-rm-template] , můžete definovat název skupiny prostředků pomocí vlastnosti *nodeResourceGroup* .

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

Úpravy všech **značek vytvořených v Azure** u prostředků v rámci skupiny prostředků uzlu v clusteru AKS se ale nejedná o nepodporovanou akci, která zruší cíl na úrovni služby (SLO). Další informace najdete v tématu [AKS nabízí smlouvu o úrovni služeb?](#does-aks-offer-a-service-level-agreement)

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
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

V současné době nemůžete upravit seznam řadičů pro příjem v AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Můžu na AKS použít Webhooky kontroleru přístupu?

Ano, v AKS můžete použít Webhooky kontroleru přístupu. Doporučuje se vyloučit interní obory názvů AKS, které jsou označené **popiskem řídicí roviny.** Například přidáním níže do konfigurace Webhooku:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS brány firewall odchozího serveru rozhraní API, aby Webhooky kontroleru pro přístup musely být dostupné v rámci clusteru.

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

AKS poskytuje záruky smlouvy SLA jako volitelnou doplňkovou funkci s smlouvou [SLA pro provozuschopnost][uptime-sla]. 

Bezplatná smlouva SLA nabízená ve výchozím nastavení nezaručuje vysoce dostupný koncový bod serveru API (náš cíl na úrovni služby je 99,5%). Může dojít k tomu, že v případě upgradů dojde k problémům s přechodným připojením, které nemají v pořádku Underlay uzly, údržba platformy atd... Pokud vaše úloha neumožňuje tolerovat APIServer restart, doporučujeme použít smlouvu SLA pro provozuschopnost.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Můžu u svých uzlů agentů AKS uplatnit slevy na rezervované platformy Azure?

Uzly agentů AKS se účtují jako standardní virtuální počítače Azure, takže pokud jste si zakoupili [rezervace Azure][reservation-discounts] pro velikost virtuálního počítače, který používáte v AKS, budou se tyto slevy používat automaticky.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Můžu svůj cluster přesunout nebo migrovat mezi klienty Azure?

Přesunutí clusteru AKS mezi klienty se aktuálně nepodporuje.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Můžu cluster přesunout/migrovat mezi předplatnými?

Přesun clusterů mezi předplatnými není aktuálně podporován.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Můžu svoje clustery AKS přesunout z aktuálního předplatného Azure do jiného?

Přesunutí clusteru AKS a jeho přidružených prostředků mezi předplatnými Azure se nepodporuje.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Můžu svůj cluster AKS nebo prostředky infrastruktury AKS přesunout do jiných skupin prostředků nebo je přejmenovat?

Přesunutí nebo přejmenování clusteru AKS a jeho přidružených prostředků se nepodporuje.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Proč je můj cluster trvá tak dlouho?

Většina clusterů se po vyžádání uživatele odstraní; v některých případech, zejména v případě, že zákazníci přinášejí svoji vlastní skupinu prostředků nebo že odstraňování úloh mezi RGy může trvat delší dobu nebo selhat. Pokud máte problém s odstraněním, poklikejte na RG, že nejsou k dispozici žádné prostředky mimo RG od RG, a tak dále.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Pokud mám v/v nasazení stav ' NodeLost ' nebo ' neznámé ', je možné cluster stále upgradovat?

Můžete to, ale AKS to nedoporučuje. Upgrady by měly být provedeny, pokud je stav clusteru známý a v pořádku.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Pokud mám cluster s minimálně jedním uzlem ve stavu není v pořádku nebo je možné ho vypnout, mohu provést upgrade?

Ne, odstranit nebo odebrat všechny uzly ve stavu selhání nebo jinak odebrat z clusteru před upgradem

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Spustil (a) jsem cluster s odstraněním, ale zobrazí se chyba `[Errno 11001] getaddrinfo failed`

Nejčastěji to je způsobeno tím, že uživatelé, kteří mají jednu nebo více skupin zabezpečení sítě (skupin zabezpečení sítě) stále používány a jsou přidruženi ke clusteru.  Odstraňte je a zkuste to znovu.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Spustil (a) jsem upgrade, ale teď jsou moje lusky v cyklech havárií a testy připravenosti selžou?

Potvrďte, že váš instanční objekt vypršel.  Viz: [AKS instanční objekt](./kubernetes-service-principal.md) a [přihlašovací údaje pro AKS aktualizace](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Můj cluster fungoval, ale náhle nedokáže zřídit LoadBalancers, připojení PVC atd.?

Potvrďte, že váš instanční objekt vypršel.  Viz: [AKS instanční objekt](./kubernetes-service-principal.md)  a [přihlašovací údaje pro AKS aktualizace](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Můžu svůj cluster AKS škálovat na nulu?
[Běžící cluster AKS](start-stop-cluster.md)můžete úplně zastavit a ušetřit tak příslušné výpočetní náklady. Kromě toho můžete také [škálovat nebo automatické škálování všech nebo konkrétních `User` fondů uzlů](scale-cluster.md#scale-user-node-pools-to-0) na hodnotu 0 a zachovat jenom nezbytnou konfiguraci clusteru.
[Fondy systémových uzlů](use-system-pools.md) nemůžete přímo škálovat na hodnotu nula.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Můžu použít rozhraní API sady škálování pro virtuální počítače k ručnímu škálování?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují. Použijte rozhraní API AKS ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Můžu pomocí sady škálování virtuálních počítačů ručně škálovat na nulové uzly?

Ne, operace škálování s použitím rozhraní API sady škálování virtuálních počítačů se nepodporují. Rozhraní AKS API můžete použít ke škálování na nulové fondy nesystémových uzlů nebo pro [zastavování clusteru](start-stop-cluster.md) .

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Můžu zastavit nebo zrušit přidělení všech virtuálních počítačů?

I když AKS má mechanismy odolnosti k vyvýšení těchto konfigurací a obnovení z něj, nejedná se o podporovanou konfiguraci. Místo toho [zastavte svůj cluster](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Můžu použít vlastní rozšíření virtuálních počítačů?

Agent Log Analytics je podporovaný, protože jde o rozšíření spravované Microsoftem. V opačném případě ne, AKS je spravovaná služba a manipulace s prostředky IaaS není podporovaná. Chcete-li nainstalovat vlastní součásti, použijte rozhraní API a mechanismy Kubernetes. Použijte například DaemonSets k instalaci požadovaných součástí.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Ukládá AKS data o zákaznících mimo oblast clusteru?

Funkce pro ukládání zákaznických dat v jedné oblasti je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) Asie a Tichomoří geografické oblasti. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky.

## <a name="are-aks-images-required-to-run-as-root"></a>Jsou AKS image vyžadují, aby se spouštěly pod kořenovým adresářem?

S výjimkou následujících dvou imagí nemusíte AKS image spouštět jako kořenový adresář:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Co je transparentní režim a režim přemostění Azure CNI?

Z 1.2.0 Azure CNI bude mít transparentní režim jako výchozí pro nasazení s jednou tenantů pro CNI Linux. Transparentní režim nahrazuje režim mostu. V této části se podíváme na Další informace o rozdílech v obou režimech a o tom, jaké jsou výhody a omezení používání transparentního režimu v Azure CNI.

### <a name="bridge-mode"></a>Režim mostu

Jak je název navržený, v režimu mostu Azure CNI ve formě "právě včas" vytvoří most L2 s názvem "azure0". Všechna párové rozhraní na straně hostitele `veth` se připojí k tomuto mostu. Takže se v rámci tohoto mostu přejdou Pod-Pod komunikace mezi virtuálními počítači a zbývající provoz. Daný most je virtuální zařízení vrstvy 2, které sám o sobě nemůže přijímat ani přenášet, pokud k němu navážete jedno nebo více skutečných zařízení. Z tohoto důvodu se eth0 virtuálního počítače se systémem Linux převedou na most "azure0". Tím se vytvoří složitá topologie sítě v rámci virtuálního počítače se systémem Linux a jako příznak CNI se musela postarat o další síťové funkce, jako je třeba aktualizace serveru DNS atd.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologie režimu mostu":::

Tady je příklad toho, jak se nastavení trasy protokolu IP v režimu mostu jeví jako. Bez ohledu na to, kolik z obou uzlů má uzel, budou existovat pouze dvě trasy. První z nich znamená, že veškerý provoz s výjimkou místních v azure0 přechází do výchozí brány podsítě prostřednictvím rozhraní s IP adresou "src 10.240.0.4" (což je primární IP adresa uzlu) a druhým prvním vyslovením "10.20. x. x" v rámci jádra pro rozhodování.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transparentní režim
Transparentní režim používá k nastavení sítě pro Linux přímý přístup. V tomto režimu Azure CNI nemění žádné vlastnosti rozhraní eth0 na virtuálním počítači se systémem Linux. Tento minimální přístup ke změně vlastností sítě pro Linux pomáhá snižovat složité problémy v rohových případech, které by mohly způsobit clustery s režimem mostu. V transparentním režimu vytvoří Azure CNI a přidá `veth` rozhraní dvojice na straně hostitele, které se přidá do sítě hostitele. Komunikace mezi virtuálními počítači pod virtuálním počítačem je prostřednictvím tras IP, které budou CNI přidávat. Nepostradatelná komunikace pod vrstvou od 3 do 1 se přesměruje prostřednictvím pravidel směrování L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologie transparentního režimu":::

Níže je uveden příklad nastavení protokolu IP v transparentním režimu, přičemž každé rozhraní pod bude mít připojenou statickou trasu, aby se provoz s cílovým protokolem IP, který se nachází pod, odesílal přímo na rozhraní dvojice na straně hostitele pod `veth` ním.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Výhody transparentního režimu

- Poskytuje omezení pro `conntrack` paralelní časování DNS a vyhne se problémům s latencí služby DNS 5 – s bez nutnosti nastavovat místní DNS uzlu (z důvodů výkonu můžete i místní DNS uzel použít).
- Eliminuje počáteční režim mostu CNI pro latenci služby DNS v řádu 5 s
- Jedním z rohových případů v režimu mostu je to, že Azure CNI nemůže uchovat aktualizace vlastního serveru DNS seznam uživatelů, kteří se přidávají buď do sítě VNET, nebo do síťového adaptéru. Výsledkem je, že CNI vybírá jenom první instanci seznamu serverů DNS. Vyřeší v transparentním režimu, protože CNI nemění žádné vlastnosti eth0. Další informace najdete [tady](https://github.com/Azure/azure-container-networking/issues/713).
- Poskytuje lepší zacházení s přenosy UDP a omezením pro zahlcení zahlcení UDP při vypršení časového limitu protokolu ARP. Pokud most nezná v režimu mostu adresu MAC cílového umístění v rámci komunikace mezi virtuálním počítačem a s tím, jak to bude mít za následek vytvoření paketu na všechny porty. Vyřeší v transparentním režimu, protože v cestě nejsou žádná zařízení L2. Další informace najdete [tady](https://github.com/Azure/azure-container-networking/issues/704).
- Transparentní režim v rámci komunikace mezi virtuálními počítači a s přenosovou hodnotou v porovnání s režimem mostu zajišťuje lepší přenos z hlediska propustnosti a latence.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Jak zabránit vlastnictví oprávnění nastavení pomalých problémů, když má svazek velké množství souborů?

Tradičně, pokud je vaše zařízení spuštěno jako uživatel, který není kořenovým uživatelem (který byste měli mít), je nutné zadat `fsGroup` uvnitř kontextu zabezpečení pod, aby bylo možné svazek číst a zapisovat pod ním. Tento požadavek se podrobněji popisuje [tady](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

Ale jedním z vedlejších případů nastavení `fsGroup` je, že při každém připojení svazku musí Kubernetes rekurzivně `chown()` a `chmod()` všechny soubory a adresáře uvnitř svazku, a to s několika výjimkami, které jsou uvedené níže. K tomu dojde i v případě, že vlastnictví skupiny daného svazku již odpovídá požadovanému `fsGroup` a může být poměrně nákladné pro větší svazky s velkým množstvím malých souborů, což způsobí, že po spuštění bude trvat dlouhou dobu. V tomto scénáři byl známý problém před 1,2 a alternativním řešením je nastavení kořenového adresáře "pod" spustit jako:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Problém vyřešila Kubernetes v 1,20. Další informace najdete v tématu [Kubernetes 1,20: podrobné řízení změn oprávnění ke svazkům](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) .


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
