---
title: Řešení běžných problémů se službou Azure Kubernetes
description: Zjistěte, jak řešit a řešit běžné problémy při používání služby Azure Kubernetes Service (AKS).
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368328"
---
# <a name="aks-troubleshooting"></a>Řešení potíží s AKS

Při vytváření nebo správě clusterů služby Azure Kubernetes Service (AKS) se občas může vyskytnout problémy. Tento článek podrobně popisuje některé běžné problémy a kroky řešení potíží.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Obecně platí, kde najdu informace o ladění problémů Kubernetes?

Vyzkoušejte [oficiálního průvodce odstraňováním problémů s kubernetesclustery](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
K dispozici je také [průvodce odstraňováním potíží](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), který publikoval technik společnosti Microsoft pro řešení potíží pody, uzly, clustery a další funkce.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Při vytváření nebo upgradu se zobrazuje chyba "kvóta byla překročena". Co bych měl/a dělat? 

Musíte [požádat o jader](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Jaké je maximální nastavení podů na uzel pro AKS?

Maximální pods na uzel nastavení je 30 ve výchozím nastavení, pokud nasadíte cluster AKS na webu Azure Portal.
Maximální pods na uzel nastavení je 110 ve výchozím nastavení, pokud nasadíte clusterAKS v Azure CLI. (Ujistěte se, že používáte nejnovější verzi azure CLI). Toto výchozí nastavení lze `–-max-pods` změnit pomocí `az aks create` příznaku v příkazu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Při nasazování clusteru AKS s pokročilou sítí se zobrazuje chyba insufficientSubnetSize. Co bych měl/a dělat?

Pokud se používá Azure CNI (rozšířené sítě), AKS přiděluje IP adresy na základě "max-pods" na nakonfigurovaný uzel. Na základě nakonfigurovaných maximálních podů na uzel musí být velikost podsítě větší než součin počtu uzlů a nastavení max pod na uzel. Následující rovnice popisuje toto:

Velikost podsítě > počet uzlů v clusteru (s přihlédnutím k budoucím požadavkům na škálování) * maximální pody na sadu uzlů.

Další informace naleznete v [tématu Plánování adresování IP adres pro váš cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Můj pod je přilepená v režimu CrashLoopBackOff. Co bych měl/a dělat?

V tomto režimu mohou být různé důvody pro to, aby se modul zasekl. Můžete se podívat do:

* Pod sám, pomocí `kubectl describe pod <pod-name>`.
* Protokoly, pomocí `kubectl logs <pod-name>`.

Další informace o řešení problémů podu naleznete v tématu [Ladění aplikací](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Pokouším se povolit RBAC v existujícím clusteru. Jak to mám udělat?

Bohužel povolení řízení přístupu na základě rolí (RBAC) na existující clustery není podporována v tomto okamžiku. Je nutné explicitně vytvořit nové clustery. Pokud používáte rozhraní se konkrečním klem, RBAC je povolena ve výchozím nastavení. Pokud používáte portál AKS, přepínací tlačítko pro povolení RBAC je k dispozici v pracovním postupu vytváření.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Vytvořil jsem cluster s povoleným RBAC pomocí azure cli s výchozí nebo portál Azure a teď vidím mnoho upozornění na řídicím panelu Kubernetes. Řídicí panel fungoval bez varování. Co bych měl/a dělat?

Důvodem upozornění na řídicím panelu je, že cluster je nyní povolen a RBAC a přístup k němu byl ve výchozím nastavení zakázán. Obecně platí, že tento přístup je osvědčeným postupem, protože výchozí expozice řídicího panelu všem uživatelům clusteru může vést k ohrožení zabezpečení. Pokud stále chcete řídicí panel povolit, postupujte podle pokynů v [tomto příspěvku blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nejde se připojit k řídicímu panelu. Co bych měl/a dělat?

Nejjednodušší způsob, jak získat přístup ke službě `kubectl proxy`mimo cluster, je spustit , které požadavky proxy odeslané na port localhost 8001 na server rozhraní API Kubernetes. Odtud může server API proxy pro `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`vaši službu: .

Pokud řídicí panel Kubernetes nevidíte, zkontrolujte, jestli `kube-proxy` `kube-system` je pod spuštěný v oboru názvů. Pokud není v běžícím stavu, odstraňte pod a restartuje se.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nelze získat protokoly pomocí kubectl protokoly nebo nemohu připojit k serveru API. Dostávám "Chyba ze serveru: chyba vytáčení back-end: vytočit tcp...". Co bych měl/a dělat?

Ujistěte se, že výchozí skupina zabezpečení sítě není změněna a že port 22 a 9000 jsou otevřeny pro připojení k serveru rozhraní API. Zkontrolujte, `tunnelfront` zda pod je spuštěn v oboru `kubectl get pods --namespace kube-system` názvů *kube systému* pomocí příkazu. Pokud tomu tak není, vynutit odstranění pod ua. a restartuje.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Snažím se upgradovat nebo měřítko a dostávám "zpráva: Změna vlastnosti 'imageReference' není povolena" chyba. Jak tento problém vyřeším?

Tato chyba se může zvývat, protože jste upravili značky v uzlech agenta v clusteru AKS. Úprava a odstranění značek a dalších vlastností prostředků ve skupině prostředků MC_* může vést k neočekávaným výsledkům. Úprava prostředků v rámci skupiny MC_* v clusteru AKS přeruší cíl na úrovni služby (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Přizískávání chyb, že je cluster ve stavu selhání a inovace nebo škálování nebude fungovat, dokud nebude opraven

*Tato pomoc při řešení potíží je směrována zhttps://aka.ms/aks-cluster-failed*

K této chybě dochází, když clustery zadají stav selhání z několika důvodů. Chcete-li vyřešit stav selhání clusteru před opakováním dříve neúspěšné operace, postupujte podle následujících kroků:

1. Dokud není cluster `failed` mimo `upgrade` stav `scale` a operace nebudou úspěšné. Mezi běžné kořenové problémy a řešení patří:
    * Škálování s **kvótou crp (nedostupné výpočetní prostředky).** Chcete-li vyřešit, nejprve škálování clusteru zpět do stabilního stavu cíle v rámci kvóty. Potom postupujte podle [následujících kroků požádat o zvýšení výpočetní kvóty](../azure-portal/supportability/resource-manager-core-quotas-request.md) před pokusem o navýšení kapacity znovu nad rámec počáteční kvóty.
    * Škálování clusteru s pokročilými sítěmi a **nedostatečnými prostředky podsítě (sítě).** Chcete-li vyřešit, nejprve škálování clusteru zpět do stabilního stavu cíle v rámci kvóty. Potom postupujte [podle následujících kroků požádat o zvýšení kvóty prostředků](../azure-resource-manager/templates/error-resource-quota.md#solution) před pokusem o navýšení kapacity znovu nad rámec počáteční kvóty.
2. Po vyřešení základní příčiny selhání upgradu by měl být cluster v úspěšném stavu. Po ověření úspěšného stavu opakujte původní operaci.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Při pokusu o inovaci nebo škálování stavu clusteru se aktuálně inovuje nebo se nepodařilo upgradovat, došlo k chybám

*Tato pomoc při řešení potíží je směrována zhttps://aka.ms/aks-pending-upgrade*

Operace upgradu a škálování v clusteru s fondem jednoho uzlu nebo clusteru s [více fondy uzlů](use-multiple-node-pools.md) se vzájemně vylučují. Fond clusteru nebo uzlu nelze současně inovovat a škálovat. Místo toho musí každý typ operace dokončit cílový prostředek před dalším požadavkem na stejný prostředek. V důsledku toho jsou operace omezené, když dochází k aktivnímu upgradu nebo škálování a následně se nezdařilo. 

Chcete-li diagnostikovat `az aks show -g myResourceGroup -n myAKSCluster -o table` problém spustit načíst podrobný stav v clusteru. Na základě výsledku:

* Pokud cluster aktivně upgraduje, počkejte, dokud operace neskončí. Pokud byla úspěšná, opakujte předchozí neúspěšnou operaci.
* Pokud se upgrade clusteru nezdařil, postupujte podle pokynů uvedených v předchozí části.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Můžu přesunout cluster na jiné předplatné nebo předplatné s clusterem do nového tenanta?

Pokud jste přesunuli cluster AKS do jiného předplatného nebo clusteru vlastnícího předplatné na nového klienta, cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv na instanční objekty. **AKS nepodporuje přesunutí clusterů mezi předplatnými nebo klienty** z důvodu tohoto omezení.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Při pokusu o použití funkcí, které vyžadují škálovací sady virtuálních strojů, se při pokusu o použití funkcí

*Tato pomoc při odstraňování potíží je směrována z aka.ms/aks-vmss-enablement*

Můžete obdržet chyby, které označují, že váš cluster AKS není ve škálovací sadě virtuálních strojů, například v následujícím příkladu:

**Fond agentpoolu nastavil automatické škálování jako povolené, ale není v škálovacích sadách virtuálních strojů.**

Chcete-li používat funkce, jako je například automatické škálování clusteru nebo více fondů uzlů, je nutné vytvořit clustery AKS, které používají škálovací sady virtuálních strojů. Chyby jsou vráceny, pokud se pokusíte použít funkce, které závisí na škálovacísady virtuálních strojů a cílíte na běžný cluster AKS sady škálování než virtuální počítače.

Postupujte *podle kroků Před zahájením* v příslušném dokumentu správně vytvořit cluster AKS:

* [Použití automatického škálování clusteru](cluster-autoscaler.md)
* [Vytvoření a použití více fondů uzlů](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jaká omezení pojmenování jsou vynucena pro prostředky a parametry AKS?

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-naming-rules*

Omezení pojmenování implementuje platforma Azure i AKS. Pokud název prostředku nebo parametr přeruší jedno z těchto omezení, je vrácena chyba, která vás požádá o zadání jiného vstupu. Platí následující běžné pokyny pro pojmenování:

* Názvy clusterů musí mít 1-63 znaků. Jedinými povolenými znaky jsou písmena, čísla, pomlčky a podtržítka. První a poslední znak musí být písmeno nebo číslo.
* Název skupiny prostředků aks *MC_* kombinuje název skupiny prostředků a název prostředku. Automaticky generovaná syntaxe `MC_resourceGroupName_resourceName_AzureRegion` nesmí být větší než 80 znaků. V případě potřeby zkraťte délku názvu skupiny prostředků nebo názvu clusteru AKS.
* *DnsPrefix* musí začínat a končit alfanumerickými hodnotami a musí být mezi 1-54 znaky. Platné znaky zahrnují alfanumerické hodnoty a pomlčky (-). *DnsPrefix* nemůže obsahovat speciální znaky, jako je například tečka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Při pokusu o vytvoření, aktualizaci, škálování, odstranění nebo upgradu clusteru se při odstraňování chyb při získávání chyb, tato operace není povolena, protože probíhá jiná operace.

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-pending-operation*

Operace clusteru jsou omezené, pokud předchozí operace stále probíhá. Chcete-li načíst podrobný stav `az aks show -g myResourceGroup -n myAKSCluster -o table` clusteru, použijte příkaz. Podle potřeby použijte vlastní skupinu prostředků a název clusteru AKS.

Na základě výstupu stavu clusteru:

* Pokud je cluster v jakémkoli stavu zřizování jiné než *Succeeded* nebo *Failed*, počkejte, až operace (*Upgrade / Aktualizace / Vytváření / Škálování / Odstranění / Migrace*) ukončí. Po dokončení předchozí operace zkuste znovu nejnovější operaci clusteru.

* Pokud má cluster neúspěšný upgrade, postupujte podle pokynů [popsaných v případě, že se zobrazuje chyby, že je cluster ve stavu selhání a inovace nebo škálování nebude fungovat, dokud](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)nebude opraveno .

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Při pokusu o vytvoření nového clusteru bez předání existujícího objektu zabezpečení se při pokusu o vytvoření nového clusteru nezobrazuje teprve hlavní povinný.

Při vytváření clusteru AKS vyžaduje instanční objekt k vytvoření prostředků vaším jménem. AKS nabízí možnost mít nový vytvořený v době vytvoření clusteru, ale to vyžaduje, aby služba Azure Active Directory plně rozšíří nový instanční objekt v přiměřené době, aby bylo možné clusteru úspěšně vytvářet. Pokud toto šíření trvá příliš dlouho, clusteru se nezdaří ověření vytvořit, protože nemůže najít k dispozici instanční objekt, aby tak učinily. 

Pro tento postup použijte následující zástupná řešení:
1. Použijte existující instanční objekt, který se již rozšířil napříč oblastmi a existuje pro předání do AKS v čase vytvoření clusteru.
2. Pokud používáte automatizační skripty, přidejte časové prodlevy mezi vytvořením instančního objektu a vytvořením clusteru AKS.
3. Pokud používáte portál Azure, vraťte se do nastavení clusteru během vytváření a opakujte ověřovací stránku po několika minutách.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Po omezení odchozího přenosu se mi zobrazuje chyby

Při omezení odchozích přenosů z clusteru AKS jsou [vyžadována a volitelně doporučená](limit-egress-traffic.md) pravidla odchozích portů / sítě a pravidla fQDN / aplikace pro AKS. Pokud jsou nastavení v konfliktu s některým z těchto pravidel, `kubectl` nemusí být možné spustit určité příkazy. Při vytváření clusteru AKS se také mohou zobrazit chyby.

Ověřte, zda vaše nastavení nejsou v konfliktu s některou z požadovaných nebo volitelných doporučených odchozích portů / pravidel sítě a pravidel fQDN / aplikace.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage a Řešení potíží s AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jaké jsou doporučené stabilní verze kubernetes pro disk Azure? 

| Kubernetes verze | Doporučená verze |
| -- | :--: |
| 1.12 | 1.12.9 nebo novější |
| 1.13 | 1.13.6 nebo novější |
| 1.14 | 1.14.2 nebo novější |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Jaké verze Kubernetes mají podporu Azure Disk na suverénním cloudu?

| Kubernetes verze | Doporučená verze |
| -- | :--: |
| 1.12 | 1.12.0 nebo novější |
| 1.13 | 1.13.0 nebo novější |
| 1.14 | 1.14.0 nebo novější |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach se nezdařilo pro Azure Disk: analýza "/dev/disk/azure/scsi1/lun1": neplatná syntaxe

V Kubernetes verze 1.10 MountVolume.WaitForAttach může selhat s Azure Disk remount.

V Linuxu se může zobrazit chyba nesprávného formátu DevicePath. Například:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

V systému Windows se může zobrazit chybná chyba čísla DevicePath(LUN). Například:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.10 | 1.10.2 nebo novější |
| 1.11 | 1.11.0 nebo novější |
| 1.12 a novější | Není dostupné. |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Selhání při nastavování uid a gid v mountOptions pro Disk Azure

Azure Disk používá souborový systém ext4,xfs ve výchozím nastavení a mountOptions jako uid = x, gid = x nelze nastavit v době připojení. Například pokud jste se pokusili nastavit mountOptions uid = 999, gid = 999, by vidět chybu, jako je:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Problém můžete zmírnit jedním z následujících akcí:

* [Nakonfigurujte kontext zabezpečení pro pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) nastavením uid v runAsUser a gid v fsGroup. Například následující nastavení nastaví pod spustit jako root, aby byl přístupný pro libovolný soubor:

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
  > Vzhledem k tomu, gid a uid jsou připojeny jako kořen nebo 0 ve výchozím nastavení. Pokud gid nebo uid jsou nastaveny jako non-root, například 1000, Kubernetes bude používat `chown` ke změně všech adresářů a souborů pod tímto diskem. Tato operace může být časově náročná a může velmi pomalá montáž disku.

* Použití `chown` v initContainers nastavit gid a uid. Například:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Chyba při odstraňování azure disk persistentvolumeclaim v použití pod

Pokud se pokusíte odstranit azure disk persistentvolumeclaim, který je používán pod, může se zobrazit chyba. Například:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

V Kubernetes verze 1.10 a novější je funkce ochrany PersistentVolumeClaim ve výchozím nastavení povolena, aby se zabránilo této chybě. Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete tento problém zmírnit odstraněním podu pomocí PersistentVolumeClaim před odstraněním PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Při připojování disku k uzlu došlo k chybě Nelze najít lun pro disk.

Při připojování disku k uzlu se může zobrazit následující chyba:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.10 | 1.10.10 nebo novější |
| 1.11 | 1.11.5 nebo novější |
| 1.12 | 1.12.3 nebo novější |
| 1.13 | 1.13.0 nebo novější |
| 1.14 a novější | Není dostupné. |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete tento problém zmírnit čekáním na několik minut a opakováním.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure Disk připojit nebo odpojit selhání, připojení problémy nebo vstupně-v., chyby během více operací připojit/odpojit

Počínaje Kubernetes verze 1.9.2, při spuštění více připojit nebo odpojit operace paralelně, může se zobrazit následující problémy s diskem z důvodu špinavé mezipaměti virtuálního počítače:

* Selhání připojení/odpojení disku
* Chyby vstupně-i/v. disku
* Neočekávané odpojení disku od virtuálního počítače
* Virtuální ms spuštěndo stavu selhání z důvodu připojení neexistující ho disku

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.10 | 1.10.12 nebo novější |
| 1.11 | 1.11.6 nebo novější |
| 1.12 | 1.12.4 nebo novější |
| 1.13 | 1.13.0 nebo novější |
| 1.14 a novější | Není dostupné. |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete tento problém zmírnit tak, že se pokusíte následující:

* Pokud disk čeká na delší dobu odpojení, zkuste disk odpojit ručně.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disk Azure čeká na odpojení na neurčito

V některých případech pokud operace odpojení disku Azure selže na první pokus, nebude opakovat operaci odpojení a zůstane připojen k původnímu virtuálnímu počítači uzlu. K této chybě může dojít při přesouvání disku z jednoho uzlu do druhého. Například:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.11 | 1.11.9 nebo novější |
| 1.12 | 1.12.7 nebo novější |
| 1.13 | 1.13.4 nebo novější |
| 1.14 a novější | Není dostupné. |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému, můžete tento problém zmírnit ručním odpojením disku.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Selhání odpojení disku Azure, což vede k potenciálnímu problému spor a seznamu neplatných datových disků

Když disk Azure se nezdaří odpojit, bude opakovat až šestkrát odpojit disk pomocí exponenciální zpět. Bude také obsahovat zámek na úrovni uzlu v seznamu datových disků po dobu asi 3 minut. Pokud je seznam disků během tohoto časového období aktualizován ručně, například ruční připojení nebo odpojení operace, to způsobí, že seznam disků v držení zámku na úrovni uzlu být zastaralé a způsobit nestabilitu na virtuálním počítači uzlu.

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.12 | 1.12.9 nebo novější |
| 1.13 | 1.13.6 nebo novější |
| 1.14 | 1.14.2 nebo novější |
| 1.15 a novější | Není dostupné. |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému a váš uzel virtuální počítač má zastaralý seznam disků, můžete zmírnit problém odpojením všech neexistujících disků z virtuálního počítače jako jediné hromadné operace. **Jednotlivě odpojení neexistující disky může selhat.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Velký počet disků Azure způsobuje pomalé připojení nebo odpojení

Když je počet disků Azure připojených k virtuálnímu počítači uzlu větší než 10, operace připojení a odpojení může být pomalé. Tento problém je známý problém a neexistují žádná řešení v tomto okamžiku.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Selhání odpojení disku Azure, což vede k potenciálnímu virtuálnímu počítači uzlu ve stavu selhání

V některých okrajových případech může odpojení disku Azure částečně selhat a ponechat virtuální počítač uzlu ve stavu selhání.

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.12 | 1.12.10 nebo novější |
| 1.13 | 1.13.8 nebo novější |
| 1.14 | 1.14.4 nebo novější |
| 1.15 a novější | Není dostupné. |

Pokud používáte verzi Kubernetes, která nemá opravu tohoto problému a váš uzel virtuální počítač je ve stavu selhání, můžete zmírnit problém ruční aktualizací stavu virtuálního počítače pomocí jednoho z následujících:

* Pro cluster založený na dostupnosti:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Pro cluster založený na VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Soubory a Řešení potíží AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jaké jsou doporučené stabilní verze Kubernetes pro soubory Azure?
 
| Kubernetes verze | Doporučená verze |
| -- | :--: |
| 1.12 | 1.12.6 nebo novější |
| 1.13 | 1.13.4 nebo novější |
| 1.14 | 1.14.0 nebo novější |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Jaké verze Kubernetes mají azure soubory podporu na suverénní cloud?

| Kubernetes verze | Doporučená verze |
| -- | :--: |
| 1.12 | 1.12.0 nebo novější |
| 1.13 | 1.13.0 nebo novější |
| 1.14 | 1.14.0 nebo novější |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jaké jsou výchozí mountOptions při použití souborů Azure?

Doporučená nastavení:

| Kubernetes verze | hodnota fileMode a dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 a novější | 0777 |

Pokud používáte cluster s Kubernetes verze 1.8.5 nebo vyšší a dynamicky vytváří teprve trvalý svazek s třídou úložiště, lze zadat možnosti připojení na objekt třídy úložiště. Následující příklad nastaví *0777*:

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

Některé další užitečné *mountOptions* nastavení:

* *mfsymlinks* bude Azure Files připojit (cifs) podporu symbolických odkazů
* *nobrl* zabrání odesílání požadavků na uzamčení rozsahu bajtů na server. Toto nastavení je nezbytné pro některé aplikace, které se přeruší s cifs styl povinné uzamčení rozsahu bajtů. Většina serverů CIFS ještě nepodporuje požadavek na uzamčení rozsahu poradních bajtů. Pokud *nepoužíváte nobrl*, aplikace, které se přeruší s povinným uzamčením rozsahu bajtů stylu CIFS, mohou způsobit chybové zprávy podobné:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Při používání souborů Azure se chyba "nelze změnit oprávnění".

Při spuštění PostgreSQL na azure soubory plugin, může se zobrazit chyba podobná:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Tato chyba je způsobena pluginem Azure Files pomocí protokolu cifs/SMB. Při použití protokolu CIFS/SMB nelze oprávnění k souborům a adresářům po připojení změnit.

Chcete-li tento problém vyřešit, použijte *subPath* společně s pluginem Azure Disk. 

> [!NOTE] 
> Pro typ disku ext3/4 je po naformátování disku adresář lost+found.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Soubory Azure má vysokou latenci ve srovnání s Azure Disk při zpracování mnoha malých souborů

V některých případech, jako je například zpracování mnoha malých souborů, může dojít k vysoké latenci při použití souborů Azure ve srovnání s Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Chyba při povolení nastavení "Povolit přístup z vybrané sítě" v účtu úložiště

Pokud *povolíte přístup z vybrané sítě* na účet úložiště, který se používá pro dynamické zřizování v AKS, zobrazí se chyba, když AKS vytvoří sdílenou složku:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Tato chyba je z důvodu Kubernetes *trvalýřadič svazku* není v síti zvolené při *nastavování povolit přístup z vybrané sítě*.

Tento problém můžete zmírnit pomocí [statického zřizování se soubory Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Soubory Azure se nedaří znovu připojit v podu Windows

Pokud pod systému Windows s připojením Azure Files se odstraní a pak naplánované znovu na stejném uzlu, připojení se nezdaří. Tato chyba je `New-SmbGlobalMapping` z důvodu selhání příkazu, protože připojení Azure Files je již připojeno v uzlu.

Může se například zobrazit chyba podobná:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Tento problém byl vyřešen v následujících verzích Kubernetes:

| Kubernetes verze | Pevná verze |
| -- | :--: |
| 1.12 | 1.12.6 nebo novější |
| 1.13 | 1.13.4 nebo novější |
| 1.14 a novější | Není dostupné. |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Připojení souborů Azure se nezdaří kvůli změně klíče účtu úložiště

Pokud se klíč účtu úložiště změnil, může se zobrazit selhání připojení souborů Azure.

Problém můžete zmírnit ruční aktualizací pole *azurestorageaccountkey* ručně v tajném klíči souboru Azure pomocí klíče účtu úložiště s kódováním base64.

Chcete-li kódovat klíč účtu úložiště v `base64`base64, můžete použít . Například:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Chcete-li aktualizovat tajný `kubectl edit secret`soubor Azure, použijte . Například:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po několika minutách uzel agenta bude opakovat připojení souborů Azure s aktualizovanýklíč úložiště.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Automatické škálování clusteru se nezdaří škálování s chybou nepodařilo opravit velikosti skupin uzlů

Pokud se automatický škálovací systém clusteru nezvětšuje nebo nezvětšuje a v [protokolech automatického škálování clusteru][view-master-logs]se zobrazí chyba jako níže .

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Tato chyba je způsobena stavem automatického škálování clusteru, kde automatický škálovač clusteru končí jinou hodnotou než ten, který je ve skutečnosti v clusteru. Chcete-li se dostat z tohoto stavu, jednoduše zakažte a znovu povolte [automatický škálovací program clusteru][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Příloha pomalého disku, GetAzureDiskLun trvá 10 až 15 minut a zobrazí se chyba

Ve verzích Kubernetes **starších než 1.15.0** se může zobrazit chyba, jako je **například chyba WaitForAttach Nelze najít Lun pro disk**.  Řešení pro toto je čekat přibližně 15 minut a opakujte.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
