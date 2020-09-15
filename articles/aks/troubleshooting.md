---
title: Řešení běžných problémů se službou Azure Kubernetes
description: Přečtěte si, jak řešit problémy a řešit běžné problémy při používání služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 855e5e5e23371f600a7e73139f2e6da1eebc91d0
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068825"
---
# <a name="aks-troubleshooting"></a>Řešení potíží s AKS

Když vytváříte nebo spravujete clustery Azure Kubernetes Service (AKS), může občas docházet k problémům. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Kde se obecně naleznou informace o ladění problémů s Kubernetes?

Vyzkoušejte si [oficiální Průvodce odstraňováním potíží s clustery Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Je zde také [Průvodce odstraňováním potíží](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), který publikoval pracovník Microsoftu pro řešení potíží s lusky, uzly, clustery a dalšími funkcemi.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Zobrazuje se chyba překročení kvóty při vytváření nebo upgradu. Co bych měl/a dělat? 

 [Vyžádejte si více jader](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Jaké je nastavení maximálního počtu lusků na uzel pro AKS?

Pokud nasadíte cluster AKS do Azure Portal, je nastavení maximálního počtu lusků na jeden uzel ve výchozím nastavení 30.
Pokud nasadíte cluster AKS v rozhraní příkazového řádku Azure, je nastavení maximálního počtu lusků na jeden uzel standardně 110. (Ujistěte se, že používáte nejnovější verzi rozhraní příkazového řádku Azure CLI). Toto nastavení lze změnit pomocí `–-max-pods` příznaku v `az aks create` příkazu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Při nasazování clusteru AKS s pokročilými sítěmi se zobrazuje chyba insufficientSubnetSize. Co bych měl/a dělat?

Tato chyba znamená, že podsíť, která se používá pro cluster, už nemá k dispozici žádné IP adresy v rámci jejího CIDR pro úspěšné přiřazení prostředku. U clusterů Kubenet je požadavek pro každý uzel v clusteru dostatečným místem pro IP adresu. U clusterů Azure CNI je požadavek pro každý uzel a pod v clusteru dostatečným místem pro IP adresu.
Přečtěte si další informace o [návrhu Azure CNI, abyste přiřadili IP adresy do lusků](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Tyto chyby jsou také v [AKS Diagnostics](./concepts-diagnostics.md) , které aktivně povrchují problémy, jako je například nedostatečná velikost podsítě.

V následujících třech případech dojde k chybě nedostatečné velikosti podsítě:

1. Škálování na AKS nebo AKS Nodepool
   1. Pokud používáte Kubenet, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of new nodes requested` .
   1. Pokud používáte Azure CNI, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of nodes requested times (*) the node pool's --max-pod value` .

1. Upgrade AKS nebo upgrade AKS Nodepool
   1. Pokud používáte Kubenet, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of buffer nodes needed to upgrade` .
   1. Pokud používáte Azure CNI, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Ve výchozím nastavení jsou clustery AKS nastavené na hodnotu maximálního nárůstu (vyrovnávací paměti pro upgrade) jednoho (1), ale toto chování při upgradu se dá přizpůsobit nastavením [maximální hodnoty přetečení fondu uzlů](upgrade-cluster.md#customize-node-surge-upgrade-preview) , která zvýší počet dostupných IP adres potřebných k dokončení upgradu.

1. AKS Create nebo AKS Nodepool Add
   1. Pokud používáte Kubenet, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of nodes requested for the node pool` .
   1. Pokud používáte Azure CNI, k tomu dochází, když `number of free IPs in the subnet` je **menší než** `number of nodes requested times (*) the node pool's --max-pod value` .

Následující omezení se dá učinit vytvořením nových podsítí. Kvůli neschopnosti aktualizovat rozsah CIDR existující podsítě se vyžaduje oprávnění k vytvoření nové podsítě.

1. Sestavte novou podsíť s větším rozsahem CIDR postačující pro cíle operace:
   1. Vytvoří novou podsíť s novým požadovaným rozsahem, který se překrývá.
   1. Vytvořte novou nodepool na nové podsíti.
   1. Vyprázdní se z původního nodepoolu umístěného v staré podsíti, která se má nahradit.
   1. Odstraňte starou podsíť a starou nodepool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Můj pod je zablokovaný v CrashLoopBackOff režimu. Co bych měl/a dělat?

V tomto režimu mohou být v případě, že se zablokuje, k dispozici různé důvody. Můžete se podívat na:

* Sám pod sebou, pomocí `kubectl describe pod <pod-name>` .
* Protokoly pomocí `kubectl logs <pod-name>` .

Další informace o řešení problémů v nástroji najdete v tématu [ladění aplikací](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Zobrazuje se `TCP timeouts` při používání `kubectl` nebo jiných nástrojích třetích stran, které se připojují k serveru rozhraní API
AKS má řídicí plochy vysoké úrovně, které se vertikálně škálují podle počtu jader, aby bylo zajištěno, že jejich cíle na úrovni služby (SLO) a smlouvy o úrovni služeb (SLA). Pokud dochází k vypršení časového limitu připojení, podívejte se na následující:

- **Má vaše příkazy rozhraní API konzistentně vyprší nebo jenom pár?** Pokud je pouze pár, vaše `tunnelfront` pod nebo `aks-link` pod, odpovědná za komunikaci mezi řídicími rovinami node->, nemusí být ve spuštěném stavu. Zajistěte, aby uzly hostující tento uzel nevyužily nebo byly zatíženy. Zvažte jejich přesun do svého vlastního [ `system` fondu uzlů](use-system-pools.md).
- **Otevřeli jste všechny požadované porty, plně kvalifikované názvy domény a IP adresy, které jsou uvedené na webu [AKS omezit výstupní přenos dokumentů](limit-egress-traffic.md)?** Jinak může dojít k selhání několika volání příkazů.
- **Vztahuje se na vaši aktuální IP [rozsahy v rámci autorizovaných rozsahů IP adres API](api-server-authorized-ip-ranges.md)?** Pokud tuto funkci používáte a vaše IP adresa není zahrnutá v rozsahu, ve kterém budou volání zablokovaná. 
- **Máte navrácení volání do serveru API klientem nebo aplikací?** Nezapomeňte použít kukátka místo častých volání Get a, aby vaše aplikace třetích stran neunikla taková volání. Například Chyba v Istio mixer způsobí, že se vytvoří nové připojení serveru rozhraní API, které bude pokaždé, když se tajný klíč přečte interně. Vzhledem k tomu, že k tomuto chování dochází v pravidelných intervalech, Sledujte připojení rychle a nakonec způsobí přetížení serveru rozhraní API bez ohledu na vzor škálování. https://github.com/istio/istio/issues/19481
- **Máte v nasazeních Helm spoustu verzí?** V tomto scénáři může dojít k tomu, že v případě, že by oba uzly v uzlech používaly příliš mnoho paměti, a také velké množství `configmaps` , což může způsobit zbytečné špičky na serveru rozhraní API. Zvažte konfiguraci `--history-max` na `helm init` a využijte novou Helm 3. Další podrobnosti o těchto problémech: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Je interní provoz mezi uzly blokován?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Přijímám `TCP timeouts` , například `dial tcp <Node_IP>:10250: i/o timeout`

Tyto časové limity můžou souviset s vnitřním přenosem mezi blokovanými uzly. Ověřte, že tento provoz není blokovaný, například [skupiny zabezpečení sítě](concepts-security.md#azure-network-security-groups) v podsíti pro uzly clusteru.

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>V existujícím clusteru se snažím povolit Access Control na základě rolí (RBAC). Jak to můžu udělat?

Povolení řízení přístupu na základě role (RBAC) na existujících clusterech se v tuto chvíli nepodporuje, musí se nastavit při vytváření nových clusterů. Při použití rozhraní příkazového řádku, portálu nebo verze rozhraní API je ve výchozím nastavení povolena RBAC `2020-03-01` .

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Vytvořili jste cluster s povoleným RBAC a teď jsem na řídicím panelu Kubernetes spousta upozornění. Řídicí panel, který se používá pro práci bez upozornění. Co bych měl/a dělat?

Důvodem upozornění je, že cluster má zapnutou RBAC a přístup k řídicímu panelu je teď ve výchozím nastavení omezený. Obecně platí, že tento přístup je dobrým zvykem, protože výchozí expozicí řídicího panelu všem uživatelům clusteru může vést k bezpečnostním hrozbám. Pokud přesto chcete řídicí panel povolit, postupujte podle kroků v [tomto blogovém příspěvku](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nemůžu získat protokoly pomocí protokolů kubectl nebo se nemůžu připojit k serveru rozhraní API. Zobrazuje se chyba ze serveru: Chyba při vytáčení back-endu: vytočit TCP... Co bych měl/a dělat?

Zajistěte, aby byly porty 22, 9000 a 1194 otevřené pro připojení k serveru rozhraní API. Pomocí příkazu ověřte, zda `tunnelfront` `aks-link` je nebo pod spuštěno v oboru názvů *Kube-System* `kubectl get pods --namespace kube-system` . Pokud ne, vynutí odstranění položky pod a restartuje se.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Získávám `"tls: client offered only unsupported versions"` klienta při připojování k rozhraní AKS API. Co bych měl/a dělat?

Minimální podporovaná verze TLS v AKS je TLS 1,2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Snažím se upgradovat nebo škálovat a připravuje se `"Changing property 'imageReference' is not allowed"` Chyba. Návody tento problém vyřešit?

K této chybě může dojít, protože jste změnili značky v uzlech agentů v clusteru AKS. Úprava nebo odstranění značek a dalších vlastností prostředků ve skupině prostředků MC_ * může vést k neočekávaným výsledkům. Změna prostředků v rámci skupiny MC_ * v clusteru AKS zruší cíl na úrovni služby (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Zobrazují se chyby, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude vyřešeno.

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-cluster-failed*

K této chybě dojde v případě, že clustery vstupují do neúspěšného stavu z více důvodů. Použijte následující postup, chcete-li vyřešit neúspěšný stav clusteru před opakováním dříve nezdařené operace:

1. Dokud nebude cluster mimo `failed` stav `upgrade` a `scale` operace nebudou úspěšné. Mezi běžné kořenové problémy a jejich řešení patří:
    * Škálování s **nedostatečnou výpočetní (CRP) kvótou**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle těchto [kroků a vyžádejte si zvýšení kvóty výpočetních](../azure-portal/supportability/resource-manager-core-quotas-request.md) prostředků předtím, než se pokusíte o horizontální navýšení limitu kvóty.
    * Škálování clusteru pomocí pokročilých síťových a **nedostatečných podsítí (síťových) prostředků**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle [těchto kroků a vyžádejte si zvýšení kvóty prostředků](../azure-resource-manager/templates/error-resource-quota.md#solution) , než se pokusíte o horizontální navýšení kapacity nad rámec počáteční kvóty.
2. Jakmile se podkladová příčina selhání upgradu vyřeší, cluster by měl být v úspěšném stavu. Po ověření stavu úspěšného dokončení zopakujte původní operaci.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Při pokusu o upgrade nebo škálování stavu, ve kterém se cluster aktualizuje nebo se nezdařil upgrade, dochází k chybám

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-pending-upgrade*

 Cluster nebo fond uzlů nejde současně upgradovat a škálovat. Místo toho musí být každý typ operace dokončen u cílového prostředku před dalším požadavkem na stejný prostředek. V důsledku toho jsou operace omezené, když dojde k aktivnímu upgradu nebo operacím škálování nebo k pokusu. 

Aby bylo možné diagnostikovat potíže s tím, že se `az aks show -g myResourceGroup -n myAKSCluster -o table` v clusteru načtou podrobné informace o stavu. Na základě výsledku:

* Pokud se cluster aktivně upgraduje, počkejte, až se operace dokončí. Pokud byla úspěšná, zkuste znovu provést dříve neúspěšnou operaci.
* Pokud se upgrade clusteru nezdařil, postupujte podle kroků uvedených v předchozí části.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Můžu svůj cluster přesunout do jiného předplatného nebo mého předplatného s clusterem do nového tenanta?

Pokud jste svůj cluster AKS přesunuli do jiného předplatného nebo do předplatného clusteru pro nového tenanta, cluster nebude fungovat kvůli chybějícím oprávněním identity clusteru. **AKS nepodporuje přesunutí clusterů mezi předplatnými nebo klienty** z důvodu tohoto omezení.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Dochází k chybám při pokusu o použití funkcí, které vyžadují Virtual Machine Scale Sets

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-vmss-enablement*

Může dojít k chybám, které naznačují, že váš cluster AKS není v sadě škálování virtuálního počítače, například v následujícím příkladu:

**Neznámá `<agentpoolname>` má nastaveno automatické škálování jako povolené, ale není na Virtual Machine Scale Sets**

Funkce, jako je například automatické škálování clusteru nebo fondy více uzlů, vyžadují jako součást virtuální počítač Scale Sets `vm-set-type` .

Abyste mohli správně vytvořit cluster *AKS, postupujte* podle pokynů v příslušném dokumentu:

* [Použití automatického škálování clusteru](cluster-autoscaler.md)
* [Vytvoření a použití více fondů uzlů](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jaká omezení pro pojmenování se vynutila pro prostředky a parametry AKS?

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-naming-rules*

Omezení pojmenování jsou implementovaná platformou Azure i AKS. Pokud název prostředku nebo parametr zruší jedno z těchto omezení, bude vrácena chyba s výzvou k zadání jiného vstupu. Platí následující obecné pokyny pro pojmenovávání:

* Názvy clusterů musí být 1-63 znaků. Jediné povolené znaky jsou písmena, číslice, pomlčky a podtržítka. První a poslední znak musí být písmeno nebo číslo.
* Název skupiny prostředků AKS uzel/*MC_* kombinuje název skupiny prostředků a název prostředku. Automaticky vygenerovaná syntaxe `MC_resourceGroupName_resourceName_AzureRegion` nesmí být větší než 80 znaků. V případě potřeby snižte délku názvu skupiny prostředků nebo názvu clusteru AKS. Můžete také [přizpůsobit název skupiny prostředků uzlu](cluster-configuration.md#custom-resource-group-name) .
* *Pole dnsprefix* musí začínat a končit alfanumerickými hodnotami a musí mít 1-54 znaků. Mezi platné znaky patří alfanumerické hodnoty a spojovníky (-). *Pole dnsprefix* nemůže obsahovat speciální znaky, jako je například tečka (.).
* Názvy fondů uzlů AKS musí mít malými písmeny a musí mít 1-11 znaků pro fondy uzlů Linux a 1-6 znaků pro fondy uzlů Windows. Název musí začínat písmenem a jediným povoleným znakem jsou písmena a číslice.
* *Uživatelské jméno*správce, které nastavuje uživatelské jméno správce pro uzly Linux, musí začínat písmenem, může obsahovat jenom písmena, číslice, spojovníky a podtržítka a maximální délka 64 znaků.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Při pokusu o vytvoření, aktualizaci, škálování, odstranění nebo upgrade clusteru dochází k chybám. Tato operace není povolená, protože probíhá jiná operace.

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-pending-operation*

Operace clusteru jsou omezené, když stále probíhá předchozí operace. Chcete-li získat podrobný stav clusteru, použijte `az aks show -g myResourceGroup -n myAKSCluster -o table` příkaz. Podle potřeby použijte vlastní skupinu prostředků a název clusteru AKS.

Na základě výstupu stavu clusteru:

* Pokud je cluster v jakémkoli stavu zřizování než *úspěšný* nebo *neúspěšný*, počkejte, než se dokončí operace (*upgrade/aktualizace/vytvoření/škálování/odstranění/migrace*). Po dokončení předchozí operace zkuste zopakovat svoji nejnovější operaci clusteru.

* Pokud dojde k selhání upgradu clusteru, postupujte podle kroků uvedených v části mi dochází k [chybám, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude opraveno](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Při pokusu o vytvoření nového clusteru se zobrazila chyba s informací, že se nepovedlo najít hlavní název služby nebo je neplatný.

Při vytváření clusteru AKS vyžaduje objekt služby nebo spravovanou identitu k vytváření prostředků vaším jménem. AKS může automaticky vytvořit nový instanční objekt v době vytváření clusteru nebo získat existující. Při použití automaticky vytvořeného prvku Azure Active Directory nutné ho rozšířit do každé oblasti, aby bylo vytváření úspěšné. Když šíření trvá příliš dlouho, cluster se nedaří ověřit a vytvořit, protože nenalezne dostupný instanční objekt. 

Pro tento problém použijte následující alternativní řešení:
* Použijte existující instanční objekt, který už je šířený v různých oblastech a existuje pro předání do AKS v době vytváření clusteru.
* Pokud používáte skripty pro automatizaci, přidejte časovou prodlevu mezi vytvořením instančního objektu a vytvořením clusteru AKS.
* Pokud používáte Azure Portal, vraťte se do nastavení clusteru během vytváření a zkuste stránku ověření zopakovat po několika minutách.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Zobrazuje se `"AADSTS7000215: Invalid client secret is provided."` při používání rozhraní AKS API. Co bych měl/a dělat?

Většinou je důvodem vypršení platnosti přihlašovacích údajů instančního objektu. [Aktualizujte přihlašovací údaje pro cluster AKS.](update-credentials.md)

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Po omezení odchozího provozu mi dochází k chybám

Při omezení odchozího provozu z clusteru AKS se [vyžadují a volitelné Doporučené](limit-egress-traffic.md) Odchozí porty/pravidla sítě a plně kvalifikovaný název domény nebo pravidla použití pro AKS. Pokud jsou nastavení v konfliktu s některým z těchto pravidel, nebudou některé `kubectl` příkazy správně fungovat. Při vytváření clusteru AKS můžete také zobrazit chyby.

Ověřte, že vaše nastavení nejsou v konfliktu s žádným z požadovaných nebo volitelných odchozích portů/síťových pravidel a plně kvalifikovaného názvu domény nebo pravidel pro aplikace.

## <a name="azure-storage-and-aks-troubleshooting"></a>Řešení potíží s Azure Storage a AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jaké jsou doporučené stabilní verze Kubernetes pro disk Azure? 

| Verze Kubernetes | Doporučená verze |
|--|:--:|
| 1.12 | 1.12.9 nebo novější |
| 1.13 | 1.13.6 nebo novější |
| 1,14 | 1.14.2 nebo novější |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach se nezdařilo pro disk Azure: analýza "/dev/disk/Azure/scsi1/lun1": Neplatná syntaxe

V Kubernetes verze 1,10 může MountVolume. WaitForAttach selhat s opětovným připojením k disku Azure.

V systému Linux se může zobrazit nesprávná chyba formátu DevicePath. Příklad:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Ve Windows se může zobrazit nesprávná chyba na číslo DevicePath (LUN). Příklad:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze Kubernetes | Pevná verze |
|--|:--:|
| 1.10 | 1.10.2 nebo novější |
| 1,11 | 1.11.0 nebo novější |
| 1,12 a novější | – |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Při nastavování UID a GID v mountOptions pro disk Azure došlo k chybě.

Disk Azure používá ve výchozím nastavení systém souborů ext4, XFS a mountOptions, jako je UID = x, GID = x, nejde nastavit v době připojení. Například pokud jste se pokusili nastavit mountOptions UID = 999, GID = 999, uvidí chybu jako:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Problém můžete zmírnit tím, že uděláte jednu z možností:

* [Nakonfigurujte kontext zabezpečení pro objekt pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) nastavením UID v runAsUser a GID v fsGroup. Například následující nastavení se nastaví pod kořenem spustit jako a zpřístupní ho pro libovolný soubor:

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

  >[!NOTE]
  > Vzhledem k tomu, že GID a UID jsou ve výchozím nastavení připojeny jako kořen nebo 0. Pokud jsou GID nebo UID nastaveny jako neroot, například 1000, použije Kubernetes `chown` ke změně všech adresářů a souborů v tomto disku. Tato operace může být časově náročná a může způsobit velmi pomalé připojení disku.

* Použijte `chown` v initContainers k nastavení GID a UID. Příklad:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Selhání odpojení disku Azure vedlo k potenciálnímu problému s podmínkou časování a neplatnému seznamu datových disků.

Když se disk s Azure nepovede odpojit, zopakuje se pokus o odpojení disku pomocí exponenciálního pozadí. Bude také obsahovat zámek na úrovni uzlu v seznamu datových disků po dobu přibližně 3 minut. Pokud se seznam disků v této době aktualizuje ručně, způsobí to, že seznam disků uložený zámkem na úrovni uzlu bude zastaralý a způsobil nestabilitu uzlu.

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze Kubernetes | Pevná verze |
|--|:--:|
| 1.12 | 1.12.9 nebo novější |
| 1.13 | 1.13.6 nebo novější |
| 1,14 | 1.14.2 nebo novější |
| 1,15 a novější | – |

Pokud používáte verzi Kubernetes, která nemá opravu pro tento problém a váš uzel obsahuje zastaralý seznam disků, můžete zmírnit odpojením všech neexistujících disků z virtuálního počítače jako hromadnou operaci. **Samostatné odpojení neexistujících disků může selhat.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Velký počet disků Azure způsobuje pomalé připojení a odpojení.

Pokud je počet operací připojení a odpojení disku Azure, které cílí na virtuální počítač s jedním uzlem, větší než 10 nebo větší než 3 při cílení na jeden fond škálování sady virtuálních počítačů, můžou být pomalejší, než se čekalo, protože se provádějí postupně. Tento problém je známý a v současné době neexistuje žádné alternativní řešení. [Položka uživatelského hlasu pro podporu paralelního připojení/odpojení nad číslem.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)..

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Selhání odpojení disku Azure vedlo k potenciálnímu virtuálnímu počítači uzlu ve stavu selhání

V některých hraničních případech může odpojení disku Azure částečně selhat a opustit virtuální počítač uzlu ve stavu selhání.

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze Kubernetes | Pevná verze |
|--|:--:|
| 1.12 | 1.12.10 nebo novější |
| 1.13 | 1.13.8 nebo novější |
| 1,14 | 1.14.4 nebo novější |
| 1,15 a novější | – |

Pokud používáte verzi Kubernetes, která nemá opravu pro tento problém, a váš uzel je ve stavu selhání, můžete zmírnit ruční aktualizací stavu virtuálního počítače pomocí jedné z následujících akcí:

* Pro cluster založený na sadě dostupnosti:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Pro cluster založený na VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Řešení potíží se soubory Azure a AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jaké jsou doporučené stabilní verze Kubernetes pro soubory Azure?
 
| Verze Kubernetes | Doporučená verze |
|--|:--:|
| 1.12 | 1.12.6 nebo novější |
| 1.13 | 1.13.4 nebo novější |
| 1,14 | 1.14.0 nebo novější |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jaké jsou výchozí mountOptions při používání služby soubory Azure?

Doporučené nastavení:

| Verze Kubernetes | hodnota fileMode a dirMode|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 a novější | 0777 |

Možnosti připojení lze zadat u objektu třídy úložiště. Následující příklad nastaví *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Některá další užitečná nastavení *mountOptions* :

* *mfsymlinks* provede podporu protokolu CIFS (Azure Files Mount), která podporuje symbolické odkazy.
* *nobrl* zabrání odeslání požadavků na zámek rozsahu bajtů do serveru. Toto nastavení je nezbytné pro některé aplikace, které jsou přerušeny pomocí stylu CIFS povinných zámků rozsahu bajtů. Většina serverů CIFS ještě nepodporují požadavky na zámky rozsahu v poradním bajtech. Pokud nepoužíváte *nobrl*, můžou aplikace, které mají přerušení s povinnými zámky rozsahu bajtů, způsobit chybové zprávy podobné:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Při použití souborů Azure se stala chyba: nepovedlo se změnit oprávnění.

Při spuštění PostgreSQL v modulu plug-in soubory Azure se může zobrazit chyba podobná této:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Tato chyba je způsobená modulem plug-in soubory Azure pomocí protokolu CIFS/SMB. Při použití protokolu CIFS/SMB se oprávnění k souborům a adresářům po připojení nedala změnit.

Pokud chcete tento problém vyřešit, použijte dílčí *cestu* spolu s modulem plug-in Azure disk. 

> [!NOTE] 
> Pro typ disku ext3/4 existuje po formátování disku ztracené a nalezené adresáře.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Při zpracování mnoha malých souborů má Azure Files v porovnání s diskem Azure vysokou latenci.

V některých případech, jako je zpracování mnoha malých souborů, se může při použití souborů Azure v porovnání s diskem Azure vyskytnout vysoká latence.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Chyba při povolování nastavení povolit přístup povolit přístup z vybrané sítě v účtu úložiště

Pokud povolíte možnost *Povolit přístup z vybrané sítě* v účtu úložiště, který se používá pro dynamické zřizování v AKS, zobrazí se chyba, když AKS vytvoří sdílenou složku:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Tato chyba je způsobená tím, že se *kontroler Kubernetes persistentvolume* nenachází v síti zvolené při nastavení *Povolení přístupu z vybrané sítě*.

Problém můžete zmírnit pomocí [statického zřizování se soubory Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Opětovné připojení souborů Azure v systému Windows pod se nezdařilo.

Pokud se odstraní Windows s připojením služby soubory Azure a pak se naplánuje jeho opětovné vytvoření ve stejném uzlu, připojení se nezdaří. Příčinou této chyby je selhání příkazu, protože `New-SmbGlobalMapping` připojení k souborům Azure je už na uzlu připojené.

Například se může zobrazit chyba podobná této:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze Kubernetes | Pevná verze |
|--|:--:|
| 1.12 | 1.12.6 nebo novější |
| 1.13 | 1.13.4 nebo novější |
| 1,14 a novější | – |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Připojení k souborům Azure selhalo kvůli změně klíče účtu úložiště.

Pokud se váš klíč účtu úložiště změnil, může se zobrazit chyba připojení souborů Azure.

Můžete zmírnit ruční aktualizací `azurestorageaccountkey` pole v tajných souborech Azure pomocí klíče účtu úložiště s kódováním base64.

K zakódování klíče účtu úložiště ve formátu base64 můžete použít `base64` . Příklad:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Pokud chcete aktualizovat svůj tajný soubor Azure, použijte `kubectl edit secret` . Příklad:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po několika minutách uzel agenta znovu pokusí službu Azure File Mount s aktualizovaným klíčem úložiště.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Automatické škálování clusteru se nepovedlo škálovat. Chyba při opravě velikosti skupin uzlů

Pokud váš automatický škálování clusteru nemění vertikální navýšení nebo snížení kapacity a v [protokolech automatického škálování clusteru][view-master-logs]se zobrazí chyba podobná následující.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Tato chyba je způsobena konfliktem časování v případě automatického škálování nadřazeného clusteru. V takovém případě funkce automatického škálování clusteru končí jinou hodnotou než ta, která je ve skutečnosti v clusteru. Pokud se chcete dostat z tohoto stavu, zakažte a znovu povolte [Automatické škálování clusteru][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Pomalé přílohy disku, GetAzureDiskLun trvá 10 až 15 minut a zobrazí se chyba.

Ve verzích Kubernetes **starších než 1.15.0**se může zobrazit chyba, jako je například **Error WaitForAttach. pro disk se nepovedlo najít logickou jednotku (LUN)**.  Alternativním řešením tohoto problému je počkat přibližně 15 minut a pak to zkuste znovu.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md

### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Důvody selhání upgradu na Kubernetes 1,16 při použití popisků uzlů s předponou kubernetes.io

Od Kubernetes [1,16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) je možné použít kubelet na uzly [pouze definovanou podmnožinu popisků s předponou Kubernetes.IO](https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/0000-20170814-bounding-self-labeling-kubelets.md#proposal) . AKS nemůže odebrat aktivní popisky bez souhlasu, protože by mohlo dojít k výpadkům ovlivněných úloh.

V důsledku toho můžete tyto problémy zmírnit:

1. Upgradovat rovinu řízení clusteru na 1,16 nebo vyšší
2. Přidat nový nodepoool na 1,16 nebo vyšší bez nepodporovaných popisků kubernetes.io
3. Odstranit starší nodepool

AKS zkoumá schopnost využít aktivní popisky na nodepool pro zlepšení tohoto zmírnění.
