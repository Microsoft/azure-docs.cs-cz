---
title: Řešení běžných problémů se službou Azure Kubernetes
description: Přečtěte si, jak řešit problémy a řešit běžné problémy při používání služby Azure Kubernetes Service (AKS).
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 50bb26aa1a29dc8b1454fadec416aceea76405b2
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844260"
---
# <a name="aks-troubleshooting"></a>Řešení potíží s AKS

Když vytváříte nebo spravujete clustery Azure Kubernetes Service (AKS), může občas docházet k problémům. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Kde se obecně naleznou informace o ladění problémů s Kubernetes?

Vyzkoušejte si [oficiální Průvodce odstraňováním potíží s clustery Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Je zde také [Průvodce odstraňováním potíží](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), který publikoval pracovník Microsoftu pro řešení potíží s lusky, uzly, clustery a dalšími funkcemi.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Zobrazuje se chyba překročení kvóty při vytváření nebo upgradu. Co bych měl/a dělat? 

Musíte [požádat o jádra](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Jaké je nastavení maximálního počtu lusků na uzel pro AKS?

Pokud nasadíte cluster AKS do Azure Portal, je nastavení maximálního počtu lusků na jeden uzel ve výchozím nastavení 30.
Pokud nasadíte cluster AKS v rozhraní příkazového řádku Azure, je nastavení maximálního počtu lusků na jeden uzel standardně 110. (Ujistěte se, že používáte nejnovější verzi rozhraní příkazového řádku Azure CLI). Toto výchozí nastavení lze změnit pomocí `–-max-pods` příznaku `az aks create` v příkazu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Při nasazování clusteru AKS s pokročilými sítěmi se zobrazuje chyba insufficientSubnetSize. Co bych měl/a dělat?

Pokud se používá Azure CNI (pokročilé sítě), AKS předem přidělí IP adresu, která je určená na základě "Max-lusků" na uzel nakonfigurovaný. Počet uzlů v clusteru AKS může být kdekoli v rozmezí od 1 do 110. V závislosti na nastaveném maximálním počtu lusků na uzel by velikost podsítě měla být větší než "součin počtu uzlů a maximum pod na uzel". Následující základní rovnice popisuje toto:

Velikost podsítě > počet uzlů v clusteru (berou v úvahu budoucí požadavky na škálování) * max. lusky na uzel.

Další informace najdete v tématu [plánování adresování IP adres pro váš cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Můj pod je zablokovaný v CrashLoopBackOff režimu. Co bych měl/a dělat?

V tomto režimu mohou být v případě, že se zablokuje, k dispozici různé důvody. Můžete se podívat na:

* Sám pod sebou, pomocí `kubectl describe pod <pod-name>`.
* Protokoly pomocí `kubectl log <pod-name>`.

Další informace o řešení problémů v nástroji najdete v tématu [ladění aplikací](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>V existujícím clusteru se snažím povolit RBAC. Jak to můžu udělat?

V současné době bohužel není podporováno povolení řízení přístupu na základě role (RBAC) u existujících clusterů. Je nutné explicitně vytvořit nové clustery. Pokud použijete rozhraní příkazového řádku, bude ve výchozím nastavení povolena možnost RBAC. Pokud použijete portál AKS, je k dispozici přepínací tlačítko pro povolení RBAC v pracovním postupu vytváření.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Vytvořili jste cluster s povolenou RBAC pomocí rozhraní příkazového řádku Azure s výchozími hodnotami nebo Azure Portal a teď se na řídicím panelu Kubernetes zobrazí mnoho upozornění. Řídicí panel, který se používá pro práci bez upozornění. Co bych měl/a dělat?

Důvodem upozornění na řídicím panelu je to, že cluster je teď povolený pomocí RBAC a přístup k němu je ve výchozím nastavení zakázaný. Obecně platí, že tento přístup je dobrým zvykem, protože výchozí expozicí řídicího panelu všem uživatelům clusteru může vést k bezpečnostním hrozbám. Pokud přesto chcete řídicí panel povolit, postupujte podle kroků v [tomto blogovém příspěvku](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nemůžu se připojit k řídicímu panelu. Co bych měl/a dělat?

Nejjednodušší způsob, jak získat přístup ke službě mimo cluster, je spustit `kubectl proxy`, které požadavky proxy odeslaly na port místního hostitele 8001 na server rozhraní Kubernetes API. Odtud může Server API na vaši službu proxy: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Pokud řídicí panel Kubernetes nevidíte, zkontrolujte, jestli `kube-proxy` je pod `kube-system` oborem názvů spuštěný. Pokud není ve spuštěném stavu, odstraňte ho a restartuje se.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nemůžu získat protokoly pomocí protokolů kubectl nebo se nemůžu připojit k serveru rozhraní API. Zobrazuje se chyba ze serveru: Chyba při vytáčení back-endu: vytočit TCP... Co bych měl/a dělat?

Ujistěte se, že výchozí skupina zabezpečení sítě není upravená a že jsou pro připojení k serveru rozhraní API otevřené porty 22 a 9000. `tunnelfront` Pomocí`kubectl get pods --namespace kube-system` příkazu ověřte, zda je pod spuštěným v oboru názvů *Kube-System* . Pokud ne, vynutí odstranění položky pod a restartuje se.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Snažím se upgradovat nebo škálovat a připravuje se zpráva: Změna vlastnosti ' element imagereference ' není povolena, chyba. Návody tento problém vyřešit?

K této chybě může dojít, protože jste změnili značky v uzlech agentů v clusteru AKS. Úprava a odstranění značek a dalších vlastností prostředků ve skupině prostředků MC_ * může vést k neočekávaným výsledkům. Změna prostředků v rámci skupiny MC_ * v clusteru AKS přerušuje cíl na úrovni služby (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Zobrazují se chyby, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude vyřešeno.

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-cluster-failed*

K této chybě dojde v případě, že clustery vstupují do neúspěšného stavu z více důvodů. Použijte následující postup, chcete-li vyřešit neúspěšný stav clusteru před opakováním dříve nezdařené operace:

1. Dokud nebude cluster mimo `failed` `upgrade` stav a `scale` operace nebudou úspěšné. Mezi běžné kořenové problémy a jejich řešení patří:
    * Škálování s **nedostatečnou výpočetní (CRP) kvótou**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle těchto [kroků a vyžádejte si zvýšení kvóty výpočetních](../azure-supportability/resource-manager-core-quotas-request.md) prostředků předtím, než se pokusíte o horizontální navýšení limitu kvóty.
    * Škálování clusteru pomocí pokročilých síťových a **nedostatečných podsítí (síťových) prostředků**. Pokud chcete řešení vyřešit, nejprve škálovat cluster zpátky do stabilního stavu cíle v rámci kvóty. Pak postupujte podle [těchto kroků a vyžádejte si zvýšení kvóty prostředků](../azure-resource-manager/resource-manager-quota-errors.md#solution) , než se pokusíte o horizontální navýšení kapacity nad rámec počáteční kvóty.
2. Jakmile se podkladová příčina selhání upgradu vyřeší, cluster by měl být v úspěšném stavu. Po ověření stavu úspěšného dokončení zopakujte původní operaci.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Zobrazují se chyby při pokusu o upgrade nebo škálování, že se cluster právě aktualizuje nebo se nezdařil upgrade

*Tato pomoc při řešení potíží je směrována z https://aka.ms/aks-pending-upgrade*

Operace upgradu a škálování v clusteru s jedním fondem uzlů nebo clusterem s [více fondy uzlů](use-multiple-node-pools.md) se vzájemně vylučují. Cluster ani fond uzlů nemůžete současně upgradovat a škálovat. Místo toho musí být každý typ operace dokončen u cílového prostředku před dalším požadavkem na stejný prostředek. V důsledku toho jsou operace omezené, když dojde k aktivnímu upgradu nebo operacím škálování a následně došlo k selhání. 

Aby bylo možné diagnostikovat potíže `az aks show -g myResourceGroup -n myAKSCluster -o table` s tím, že se v clusteru načtou podrobné informace o stavu. Na základě výsledku:

* Pokud se cluster aktivně upgraduje, počkejte, až se operace ukončí. Pokud byla úspěšná, zkuste znovu provést dříve neúspěšnou operaci.
* Pokud se upgrade clusteru nezdařil, postupujte podle kroků uvedených v předchozí části.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Můžu svůj cluster přesunout do jiného předplatného nebo mého předplatného s clusterem do nového tenanta?

Pokud jste svůj cluster AKS přesunuli na jiné předplatné nebo cluster vlastnící předplatné pro nového tenanta, cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančních objektů. **AKS nepodporuje přesouvání clusterů mezi předplatnými nebo klienty** z důvodu tohoto omezení.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Dochází k chybám při pokusu o použití funkcí, které vyžadují Virtual Machine Scale Sets

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-vmss-enablement*

Může dojít k chybám, které naznačují, že váš cluster AKS není na sadě škálování virtuálního počítače, jako je například následující příklad:

**Neznámá ' Neznámá ' má nastaveno automatické škálování jako povolené, ale není na Virtual Machine Scale Sets**

Pokud chcete používat funkce, jako je například automatické škálování clusteru nebo fondy více uzlů, je nutné vytvořit clustery AKS, které používají Virtual Machine Scale Sets. Pokud se pokusíte použít funkce, které závisí na virtuálních počítačích služby Virtual Machine Scale Sets, a zacílíte na běžný cluster AKS s nevirtuálními počítači, budou se vám vracet chyby. Podpora sady škálování virtuálních počítačů je v současné době ve verzi Preview v AKS.

Postupujte podle *pokynů v* příslušném dokumentu, aby se správně zaregistrovala funkce Virtual Machine Scale set Preview a vytvořil se cluster AKS:

* [Použití automatického škálování clusteru](cluster-autoscaler.md)
* [Vytvoření a použití více fondů uzlů](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jaká omezení pro pojmenování se vynutila pro prostředky a parametry AKS?

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-naming-rules*

Omezení pojmenování jsou implementovaná platformou Azure i AKS. Pokud název prostředku nebo parametr zruší jedno z těchto omezení, bude vrácena chyba s výzvou k zadání jiného vstupu. Platí následující obecné pokyny pro pojmenovávání:

* Název skupiny prostředků AKS *MC_* kombinuje název skupiny prostředků a název prostředku. Automaticky generovaná syntaxe `MC_resourceGroupName_resourceName_AzureRegion` nesmí být větší než 80 znaků. V případě potřeby snižte délku názvu skupiny prostředků nebo názvu clusteru AKS.
* *Pole dnsprefix* musí začínat a končit alfanumerickými hodnotami. Mezi platné znaky patří alfanumerické hodnoty a spojovníky (-). *Pole dnsprefix* nemůže obsahovat speciální znaky, jako je například tečka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Při pokusu o vytvoření, aktualizaci, škálování, odstranění nebo upgrade clusteru dochází k chybám. Tato operace není povolená, protože probíhá jiná operace.

*Tato pomoc při řešení potíží je směrována z aka.ms/aks-pending-operation*

Operace clusteru jsou omezené, když stále probíhá předchozí operace. Chcete-li získat podrobný stav clusteru, použijte `az aks show -g myResourceGroup -n myAKSCluster -o table` příkaz. Podle potřeby použijte vlastní skupinu prostředků a název clusteru AKS.

Na základě výstupu stavu clusteru:

* Pokud je cluster v jakémkoli stavu zřizování než *úspěšný* nebo *neúspěšný*, počkejte na ukončení operace (*upgrade/aktualizace/vytvoření/škálování/odstranění/migrace*). Po dokončení předchozí operace zkuste znovu vyzkoušet nejnovější operaci clusteru.

* Pokud dojde k selhání upgradu clusteru, postupujte podle kroků uvedených v části mi dochází k [chybám, že můj cluster je ve stavu selhání a upgrade nebo škálování nebude fungovat, dokud nebude opraveno](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Zobrazují se chyby, které se při pokusu o vytvoření nového clusteru nenašly u svého instančního objektu, aniž by se musel předávat existující cluster.

Při vytváření clusteru AKS vyžaduje instanční objekt k vytváření prostředků vaším jménem. AKS nabízí možnost mít v době vytváření clusteru nově vytvořenou možnost, ale to vyžaduje Azure Active Directory k úplnému rozšíření nového instančního objektu v přiměřené době, aby cluster byl úspěšně vytvořen. Pokud toto rozšíření trvá příliš dlouho, cluster se nedaří ověřit a vytvořit, protože nedokáže najít dostupný instanční objekt. 

Použijte následující alternativní řešení:
1. Použijte existující instanční objekt, který už je šířený v různých oblastech a existuje k předání do AKS v době vytváření clusteru.
2. Pokud používáte skripty pro automatizaci, přidejte časovou prodlevu mezi vytvořením instančního objektu a vytvořením clusteru AKS.
3. Pokud používáte Azure Portal, vraťte se do nastavení clusteru během vytváření a zkuste stránku ověření zopakovat po několika minutách.