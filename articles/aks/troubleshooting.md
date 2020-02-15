---
title: Řešení běžných problémů se službou Azure Kubernetes
description: Přečtěte si, jak řešit problémy a řešit běžné problémy při používání služby Azure Kubernetes Service (AKS).
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 663a1dc597493c7b534b54eab7ccc4bed0ff0e11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209218"
---
# <a name="aks-troubleshooting"></a>Řešení potíží s AKS

Když vytváříte nebo spravujete clustery Azure Kubernetes Service (AKS), může občas docházet k problémům. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Kde se obecně naleznou informace o ladění problémů s Kubernetes?

Vyzkoušejte si [oficiální Průvodce odstraňováním potíží s clustery Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Je zde také [Průvodce odstraňováním potíží](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), který publikoval pracovník Microsoftu pro řešení potíží s lusky, uzly, clustery a dalšími funkcemi.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Zobrazuje se chyba překročení kvóty při vytváření nebo upgradu. Co bych měl/a dělat? 

Musíte [požádat o jádra](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Jaké je nastavení maximálního počtu lusků na uzel pro AKS?

Pokud nasadíte cluster AKS do Azure Portal, je nastavení maximálního počtu lusků na jeden uzel ve výchozím nastavení 30.
Pokud nasadíte cluster AKS v rozhraní příkazového řádku Azure, je nastavení maximálního počtu lusků na jeden uzel standardně 110. (Ujistěte se, že používáte nejnovější verzi rozhraní příkazového řádku Azure CLI). Toto výchozí nastavení lze změnit pomocí příznaku `–-max-pods` v příkazu `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Při nasazování clusteru AKS s pokročilými sítěmi se zobrazuje chyba insufficientSubnetSize. Co bych měl/a dělat?

Pokud se používá Azure CNI (pokročilé sítě), AKS přiděluje IP adresy na základě "Max-lusků" na uzel nakonfigurovaný. V závislosti na nastaveném maximálním počtu lusků na uzel musí být velikost podsítě větší než součin počtu uzlů a nastavení maximálního počtu pod na uzel. Následující rovnice popisuje toto:

Velikost podsítě > počet uzlů v clusteru (berou v úvahu budoucí požadavky na škálování) * max. lusky na jeden uzel sady.

Další informace najdete v tématu [plánování adresování IP adres pro váš cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Můj pod je zablokovaný v CrashLoopBackOff režimu. Co bych měl/a dělat?

V tomto režimu mohou být v případě, že se zablokuje, k dispozici různé důvody. Můžete se podívat na:

* Sám pod sebou, pomocí `kubectl describe pod <pod-name>`.
* Protokoly pomocí `kubectl logs <pod-name>`.

Další informace o řešení problémů v nástroji najdete v tématu [ladění aplikací](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>V existujícím clusteru se snažím povolit RBAC. Jak to můžu udělat?

V současné době bohužel není podporováno povolení řízení přístupu na základě role (RBAC) u existujících clusterů. Je nutné explicitně vytvořit nové clustery. Pokud použijete rozhraní příkazového řádku, bude ve výchozím nastavení povolena možnost RBAC. Pokud použijete portál AKS, je k dispozici přepínací tlačítko pro povolení RBAC v pracovním postupu vytváření.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Vytvořili jste cluster s povolenou RBAC pomocí rozhraní příkazového řádku Azure s výchozími hodnotami nebo Azure Portal a teď se na řídicím panelu Kubernetes zobrazí mnoho upozornění. Řídicí panel, který se používá pro práci bez upozornění. Co bych měl/a dělat?

Důvodem upozornění na řídicím panelu je to, že cluster je teď povolený pomocí RBAC a přístup k němu je ve výchozím nastavení zakázaný. Obecně platí, že tento přístup je dobrým zvykem, protože výchozí expozicí řídicího panelu všem uživatelům clusteru může vést k bezpečnostním hrozbám. Pokud přesto chcete řídicí panel povolit, postupujte podle kroků v [tomto blogovém příspěvku](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nemůžu se připojit k řídicímu panelu. Co bych měl/a dělat?

Nejjednodušší způsob, jak získat přístup ke službě mimo cluster, je spustit `kubectl proxy`, které požadavky proxy odesílají na port místního hostitele 8001 na Server API Kubernetes. Odtud může Server API na vaši službu proxy: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Pokud řídicí panel Kubernetes nevidíte, zkontrolujte, jestli je v oboru názvů `kube-system` spuštěná `kube-proxy` pod. Pokud není ve spuštěném stavu, odstraňte ho a restartuje se.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nemůžu získat protokoly pomocí protokolů kubectl nebo se nemůžu připojit k serveru rozhraní API. Zobrazuje se chyba ze serveru: Chyba při vytáčení back-endu: vytočit TCP... Co bych měl/a dělat?

Ujistěte se, že výchozí skupina zabezpečení sítě není upravená a že jsou pro připojení k serveru rozhraní API otevřené porty 22 a 9000. Pomocí příkazu `kubectl get pods --namespace kube-system` ověřte, zda `tunnelfront` pod ním běží v oboru názvů *Kube-System* . Pokud ne, vynutí odstranění položky pod a restartuje se.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Snažím se upgradovat nebo škálovat a připravuje se zpráva "zpráva: Změna vlastnosti element imagereference není povolená". Návody tento problém vyřešit?

K této chybě může dojít, protože jste změnili značky v uzlech agentů v clusteru AKS. Úprava a odstranění značek a dalších vlastností prostředků ve skupině prostředků MC_ * může vést k neočekávaným výsledkům. Změna prostředků v rámci skupiny MC_ * v clusteru AKS přerušuje cíl na úrovni služby (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Zobrazují se chyby, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude vyřešeno.

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-cluster-failed*

K této chybě dojde v případě, že clustery vstupují do neúspěšného stavu z více důvodů. Použijte následující postup, chcete-li vyřešit neúspěšný stav clusteru před opakováním dříve nezdařené operace:

1. Dokud nebude cluster ne`failed` stav, `upgrade` a `scale` operace nebudou úspěšné. Mezi běžné kořenové problémy a jejich řešení patří:
    * Škálování s **nedostatečnou výpočetní (CRP) kvótou**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle těchto [kroků a vyžádejte si zvýšení kvóty výpočetních](../azure-portal/supportability/resource-manager-core-quotas-request.md) prostředků předtím, než se pokusíte o horizontální navýšení limitu kvóty.
    * Škálování clusteru pomocí pokročilých síťových a **nedostatečných podsítí (síťových) prostředků**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle [těchto kroků a vyžádejte si zvýšení kvóty prostředků](../azure-resource-manager/templates/error-resource-quota.md#solution) , než se pokusíte o horizontální navýšení kapacity nad rámec počáteční kvóty.
2. Jakmile se podkladová příčina selhání upgradu vyřeší, cluster by měl být v úspěšném stavu. Po ověření stavu úspěšného dokončení zopakujte původní operaci.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Zobrazují se chyby při pokusu o upgrade nebo škálování, že se cluster právě aktualizuje nebo se nezdařil upgrade

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-pending-upgrade*

Operace upgradu a škálování v clusteru s jedním fondem uzlů nebo clusterem s [více fondy uzlů](use-multiple-node-pools.md) se vzájemně vylučují. Cluster ani fond uzlů nemůžete současně upgradovat a škálovat. Místo toho musí být každý typ operace dokončen u cílového prostředku před dalším požadavkem na stejný prostředek. V důsledku toho jsou operace omezené, když dojde k aktivnímu upgradu nebo operacím škálování a následně došlo k selhání. 

Aby bylo možné diagnostikovat problém spuštění `az aks show -g myResourceGroup -n myAKSCluster -o table` k načtení podrobného stavu v clusteru. Na základě výsledku:

* Pokud se cluster aktivně upgraduje, počkejte, až se operace ukončí. Pokud byla úspěšná, zkuste znovu provést dříve neúspěšnou operaci.
* Pokud se upgrade clusteru nezdařil, postupujte podle kroků uvedených v předchozí části.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Můžu svůj cluster přesunout do jiného předplatného nebo mého předplatného s clusterem do nového tenanta?

Pokud jste svůj cluster AKS přesunuli na jiné předplatné nebo cluster vlastnící předplatné pro nového tenanta, cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančních objektů. **AKS nepodporuje přesouvání clusterů mezi předplatnými nebo klienty** z důvodu tohoto omezení.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Dochází k chybám při pokusu o použití funkcí, které vyžadují Virtual Machine Scale Sets

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-vmss-enablement*

Může dojít k chybám, které naznačují, že váš cluster AKS není na sadě škálování virtuálního počítače, jako je například následující příklad:

**Neznámá ' Neznámá ' má nastaveno automatické škálování jako povolené, ale není na Virtual Machine Scale Sets**

Pokud chcete používat funkce, jako je například automatické škálování clusteru nebo fondy více uzlů, je nutné vytvořit clustery AKS, které používají Virtual Machine Scale Sets. Pokud se pokusíte použít funkce, které závisí na virtuálních počítačích služby Virtual Machine Scale Sets, a zacílíte na běžný cluster AKS s nevirtuálními počítači, budou se vám vracet chyby.

Abyste mohli správně vytvořit cluster *AKS, postupujte* podle pokynů v příslušném dokumentu:

* [Použití automatického škálování clusteru](cluster-autoscaler.md)
* [Vytvoření a použití více fondů uzlů](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jaká omezení pro pojmenování se vynutila pro prostředky a parametry AKS?

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-naming-rules*

Omezení pojmenování jsou implementovaná platformou Azure i AKS. Pokud název prostředku nebo parametr zruší jedno z těchto omezení, bude vrácena chyba s výzvou k zadání jiného vstupu. Platí následující obecné pokyny pro pojmenovávání:

* Názvy clusterů musí být 1-63 znaků. Jediné povolené znaky jsou písmena, číslice, pomlčky a podtržítka. První a poslední znak musí být písmeno nebo číslo.
* Název skupiny prostředků AKS *MC_* kombinuje název skupiny prostředků a název prostředku. Automaticky generovaná syntaxe `MC_resourceGroupName_resourceName_AzureRegion` nesmí být větší než 80 znaků. V případě potřeby snižte délku názvu skupiny prostředků nebo názvu clusteru AKS.
* *Pole dnsprefix* musí začínat a končit alfanumerickými hodnotami a musí mít 1-54 znaků. Mezi platné znaky patří alfanumerické hodnoty a spojovníky (-). *Pole dnsprefix* nemůže obsahovat speciální znaky, jako je například tečka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Při pokusu o vytvoření, aktualizaci, škálování, odstranění nebo upgrade clusteru dochází k chybám. Tato operace není povolená, protože probíhá jiná operace.

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-pending-operation*

Operace clusteru jsou omezené, když stále probíhá předchozí operace. Chcete-li získat podrobný stav clusteru, použijte příkaz `az aks show -g myResourceGroup -n myAKSCluster -o table`. Podle potřeby použijte vlastní skupinu prostředků a název clusteru AKS.

Na základě výstupu stavu clusteru:

* Pokud je cluster v jakémkoli stavu zřizování než *úspěšný* nebo *neúspěšný*, počkejte na ukončení operace (*upgrade/aktualizace/vytvoření/škálování/odstranění/migrace*). Po dokončení předchozí operace zkuste znovu vyzkoušet nejnovější operaci clusteru.

* Pokud dojde k selhání upgradu clusteru, postupujte podle kroků uvedených v části mi dochází k [chybám, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude opraveno](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Zobrazují se chyby, které se při pokusu o vytvoření nového clusteru nenašly u svého instančního objektu, aniž by se musel předávat existující cluster.

Při vytváření clusteru AKS vyžaduje instanční objekt k vytváření prostředků vaším jménem. AKS nabízí možnost mít v době vytváření clusteru nově vytvořenou možnost, ale to vyžaduje Azure Active Directory k úplnému rozšíření nového instančního objektu v přiměřené době, aby cluster byl úspěšně vytvořen. Pokud toto rozšíření trvá příliš dlouho, cluster se nedaří ověřit a vytvořit, protože nedokáže najít dostupný instanční objekt. 

Použijte následující alternativní řešení:
1. Použijte existující instanční objekt, který už je šířený v různých oblastech a existuje k předání do AKS v době vytváření clusteru.
2. Pokud používáte skripty pro automatizaci, přidejte časovou prodlevu mezi vytvořením instančního objektu a vytvořením clusteru AKS.
3. Pokud používáte Azure Portal, vraťte se do nastavení clusteru během vytváření a zkuste stránku ověření zopakovat po několika minutách.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Po omezení odchozího provozu mi dochází k chybám

Při omezení odchozího provozu z clusteru AKS se [vyžadují a volitelné Doporučené](limit-egress-traffic.md) Odchozí porty/pravidla sítě a plně kvalifikovaný název domény nebo pravidla použití pro AKS. Pokud jsou nastavení v konfliktu s některým z těchto pravidel, možná nebudete moci spouštět určité `kubectl` příkazy. Při vytváření clusteru AKS můžete také zobrazit chyby.

Ověřte, že nastavení nejsou v konfliktu s žádným z požadovaných nebo volitelných odchozích portů/síťových pravidel a plně kvalifikovaného názvu domény nebo pravidel pro aplikace.

## <a name="azure-storage-and-aks-troubleshooting"></a>Řešení potíží s Azure Storage a AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jaké jsou doporučené stabilní verze Kubernetes pro disk Azure? 

| Verze protokolu Kubernetes | Doporučená verze |
| -- | :--: |
| 1,12 | 1.12.9 nebo novější |
| 1,13 | 1.13.6 nebo novější |
| 1,14 | 1.14.2 nebo novější |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Jaké verze Kubernetes mají v rámci svrchovaného cloudu podporu disků Azure?

| Verze protokolu Kubernetes | Doporučená verze |
| -- | :--: |
| 1,12 | 1.12.0 nebo novější |
| 1,13 | 1.13.0 nebo novější |
| 1,14 | 1.14.0 nebo novější |


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

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,10 | 1.10.2 nebo novější |
| 1,11 | 1.11.0 nebo novější |
| 1,12 a novější | Není k dispozici |

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

Problém můžete zmírnit jedním z následujících způsobů:

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
  > Vzhledem k tomu, že GID a UID jsou ve výchozím nastavení připojeny jako kořen nebo 0. Pokud jsou GID nebo UID nastavené jako jiné než kořenové, například 1000, Kubernetes použije `chown` ke změně všech adresářů a souborů v tomto disku. Tato operace může být časově náročná a může způsobit velmi pomalé připojení disku.

* K nastavení GID a UID použijte `chown` v initContainers. Příklad:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Chyba při odstraňování služby Azure disk PersistentVolumeClaim, která se používá pod

Pokud se pokusíte odstranit službu Azure disk PersistentVolumeClaim, kterou používá část pod, může se zobrazit chyba. Příklad:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

V Kubernetes verze 1,10 a novější je ve výchozím nastavení povolená funkce PersistentVolumeClaim Protection, aby se zabránilo této chybě. Pokud používáte verzi Kubernetes, která není pro tento problém k dispozici, můžete tento problém zmírnit odstraněním pod použití PersistentVolumeClaim před odstraněním PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Chyba "při připojování disku k uzlu nejde najít logickou jednotku (LUN) pro disk"

Při připojování disku k uzlu se může zobrazit následující chyba:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,10 | 1.10.10 nebo novější |
| 1,11 | 1.11.5 nebo novější |
| 1,12 | 1.12.3 nebo novější |
| 1,13 | 1.13.0 nebo novější |
| 1,14 a novější | Není k dispozici |

Pokud používáte verzi Kubernetes, která není pro tento problém k dispozici, můžete problém zmírnit tím, že počkáte několik minut a zkusíte to znovu.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Chyba připojení a odpojení disku Azure, problémy s připojením nebo vstupně-výstupní chyby během několika operací připojení/odpojení

Počínaje verzí 1.9.2 se při souběžném spouštění několika operací připojení a odpojení můžou v důsledku nestandardní mezipaměti virtuálních počítačů zobrazit následující problémy s diskem:

* Selhání připojení a odpojení disku
* Chyby v/v disku
* Neočekávané odpojení disku z virtuálního počítače
* Spuštění virtuálního počítače v neúspěšném stavu kvůli připojení neexistujícího disku

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,10 | 1.10.12 nebo novější |
| 1,11 | 1.11.6 nebo novější |
| 1,12 | 1.12.4 nebo novější |
| 1,13 | 1.13.0 nebo novější |
| 1,14 a novější | Není k dispozici |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete problém zmírnit tím, že vyzkoušíte následující:

* Pokud disk čeká na odpojení po dlouhou dobu, zkuste disk odpojit ručně.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disk Azure, který čeká na odpojení po neomezenou dobu

V některých případech platí, že pokud se při prvním pokusu operace odpojení disku Azure nepovede, nebude se opakovat operace odpojení a zůstane připojená k virtuálnímu počítači s původním uzlem. K této chybě může dojít při přesunu disku z jednoho uzlu do druhého. Příklad:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,11 | 1.11.9 nebo novější |
| 1,12 | 1.12.7 nebo novější |
| 1,13 | 1.13.4 nebo novější |
| 1,14 a novější | Není k dispozici |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete tento problém zmírnit ručním odpojením disku.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Selhání odpojení disku Azure vedlo k potenciálnímu problému s podmínkou časování a neplatnému seznamu datových disků.

Když se disk s Azure nepovede odpojit, zopakuje se pokus o odpojení disku pomocí exponenciálního pozadí. Bude také obsahovat zámek na úrovni uzlu v seznamu datových disků po dobu přibližně 3 minut. Pokud se seznam disků v tomto časovém intervalu aktualizuje ručně, například ruční operace připojit nebo odpojit, způsobí to, že seznam disků uchovávaný zámkem na úrovni uzlu bude zastaralý a způsobil nestabilitu virtuálního počítače uzlu.

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,12 | 1.12.9 nebo novější |
| 1,13 | 1.13.6 nebo novější |
| 1,14 | 1.14.2 nebo novější |
| 1,15 a novější | Není k dispozici |

Pokud používáte verzi Kubernetes, která není pro tento problém k dispozici, a váš virtuální počítač uzlu má zastaralý seznam disků, můžete problém zmírnit tím, že z virtuálního počítače odpojíte všechny neexistující disky jako jedinou hromadnou operaci. **Samostatné odpojení neexistujících disků může selhat.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Velký počet disků Azure způsobuje pomalé připojení a odpojení.

Pokud je počet disků Azure připojených k virtuálnímu počítači uzlu větší než 10, můžou operace připojení a odpojení být pomalé. Tento problém je známý a v tuto chvíli neexistují žádná alternativní řešení.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Selhání odpojení disku Azure vedlo k potenciálnímu virtuálnímu počítači uzlu ve stavu selhání

V některých hraničních případech může odpojení disku Azure částečně selhat a opustit virtuální počítač uzlu ve stavu selhání.

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,12 | 1.12.10 nebo novější |
| 1,13 | 1.13.8 nebo novější |
| 1,14 | 1.14.4 nebo novější |
| 1,15 a novější | Není k dispozici |

Pokud používáte verzi Kubernetes, která není pro tento problém k dispozici, a virtuální počítač uzlu je ve stavu selhání, můžete problém zmírnit tím, že ručně aktualizujete stav virtuálního počítače pomocí jedné z níže uvedených akcí:

* Pro cluster založený na sadě dostupnosti:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Pro cluster založený na VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Řešení potíží se soubory Azure a AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jaké jsou doporučené stabilní verze Kubernetes pro soubory Azure?
 
| Verze protokolu Kubernetes | Doporučená verze |
| -- | :--: |
| 1,12 | 1.12.6 nebo novější |
| 1,13 | 1.13.4 nebo novější |
| 1,14 | 1.14.0 nebo novější |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Jaké verze Kubernetes mají podporu souborů Azure ve službě svrchovaného cloudu?

| Verze protokolu Kubernetes | Doporučená verze |
| -- | :--: |
| 1,12 | 1.12.0 nebo novější |
| 1,13 | 1.13.0 nebo novější |
| 1,14 | 1.14.0 nebo novější |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jaké jsou výchozí mountOptions při používání služby soubory Azure?

Doporučené nastavení:

| Verze protokolu Kubernetes | hodnota fileMode a dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 a novější | 0777 |

Pokud používáte cluster s Kuberetes verze 1.8.5 nebo vyšší a dynamicky vytváříte trvalý svazek s třídou úložiště, můžete v objektu třídy úložiště zadat možnosti připojení. Následující příklad nastaví *0777*:

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

Pokud se odstraní Windows s připojením služby soubory Azure a pak se naplánuje jeho opětovné vytvoření ve stejném uzlu, připojení se nezdaří. Příčinou této chyby je to, že došlo k selhání příkazu `New-SmbGlobalMapping`, protože připojení k souborům Azure je už na uzlu připojené.

Například se může zobrazit chyba podobná této:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Tento problém byl opraven v následujících verzích Kubernetes:

| Verze protokolu Kubernetes | Pevná verze |
| -- | :--: |
| 1,12 | 1.12.6 nebo novější |
| 1,13 | 1.13.4 nebo novější |
| 1,14 a novější | Není k dispozici |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Připojení k souborům Azure selhalo kvůli změně klíče účtu úložiště.

Pokud se váš klíč účtu úložiště změnil, může se zobrazit chyba připojení souborů Azure.

Problém můžete zmírnit ruční aktualizací pole *azurestorageaccountkey* v tajných souborech Azure pomocí klíče účtu úložiště s kódováním base64.

K zakódování klíče účtu úložiště ve formátu base64 můžete použít `base64`. Příklad:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

K aktualizaci tajného souboru Azure použijte `kubectl edit secret`. Příklad:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po několika minutách uzel agenta znovu pokusí službu Azure File Mount s aktualizovaným klíčem úložiště.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Automatické škálování clusteru se nepovedlo škálovat. Chyba při opravě velikosti skupin uzlů

Pokud automatický škálování clusteru nemění vertikální navýšení nebo snížení kapacity a v [protokolech automatického škálování clusteru][view-master-logs]se zobrazí chyba podobná následující.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Tato chyba je způsobená nepodmíněným konfliktem automatického škálování clusteru, kde automatické škálování clusteru končí jinou hodnotou, než je ta, která je ve skutečnosti v clusteru. Pokud se chcete dostat z tohoto stavu, stačí zakázat a znovu povolit [Automatické škálování clusteru][cluster-autoscaler].

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
