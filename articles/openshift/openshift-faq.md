---
title: Nejčastější dotazy k Azure Red Hat OpenShift
description: Tady najdete odpovědi na běžné otázky týkající se Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619485"
---
# <a name="azure-red-hat-openshift-faq"></a>Nejčastější dotazy k OpenShift azure red hat

Tento článek se zabývá nejčastějšími dotazy týkajícími se Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Které oblasti Azure jsou podporované?

Seznam globálních oblastí, kde je Azure Red Hat OpenShift podporován, najdete v tématu [Podporované prostředky.](supported-resources.md#azure-regions)

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Můžu nasadit cluster do existující virtuální sítě?

Ne. Ale můžete připojit cluster Azure Red Hat OpenShift k existující virtuální síti prostřednictvím partnerského vztahu. Podrobnosti najdete [v tématu Připojení virtuální sítě clusteru k existující virtuální síti.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="what-cluster-operations-are-available"></a>Jaké operace clusteru jsou k dispozici?

Můžete pouze vertikálně navýšit nebo snížit počet výpočetních uzlů. Po vytvoření `Microsoft.ContainerService/openShiftManagedClusters` prostředku nejsou povoleny žádné další změny. Maximální počet výpočetních uzlů je omezen na 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jaké velikosti virtuálních strojů mohu použít?

Seznam velikostí virtuálních strojů virtuálních strojů [Azure Red Hat OpenShift,](supported-resources.md#virtual-machine-sizes) které můžete použít v clusteru Azure Red Hat OpenShift, najdete v tématu Velikosti virtuálních strojů Azure Red Hat.

## <a name="is-data-on-my-cluster-encrypted"></a>Jsou data v clusteru šifrovaná?

Ve výchozím nastavení je šifrování v klidovém stavu. Platforma Azure Storage automaticky zašifruje vaše data před jejich uchováním a dešifruje data před načtením. Podrobnosti [najdete v tématu Šifrování služby Azure Storage Service pro data v klidovém stavu.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Mohu ke sledování svých žádostí používat Prometheus/Grafana?

Ano, aplikaci Prometheus můžete nasadit do oboru názvů a sledovat aplikace v oboru názvů.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Mohu použít Prometheus/Grafana ke sledování metrik souvisejících se stavem a kapacitou clusteru?

Ne, ne v současné době.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Je registr Docker umocněn externě, abych mohl používat nástroje, jako je Jenkins?

Registr Dockeru je `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` k dispozici v šak, záruka odolnosti silné úložiště není k dispozici. Můžete také použít [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Je síť mezi názvy podporována?

Správci projektu pro zákazníky a jednotlivé projekty mohou pomocí `NetworkPolicy` objektů přizpůsobit sítě mezi názvy (včetně odepření) pro jednotlivé projekty.

## <a name="can-an-admin-manage-users-and-quotas"></a>Může správce spravovat uživatele a kvóty?

Ano. Správce Azure Red Hat OpenShift může spravovat uživatele a kvóty kromě přístupu ke všem projektům vytvořeným uživateli.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Můžu omezit cluster na jenom určité uživatele Azure AD?

Ano. Můžete omezit, které uživatelé Azure AD můžete přihlásit do clusteru konfigurací aplikace Azure AD. Podrobnosti najdete [v tématu Postup: Omezení aplikace na sadu uživatelů](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Mohu uživatelům zakázat vytváření projektů?

Ano. Přihlaste se ke svému clusteru jako správce Azure Red Hat OpenShift a spusťte tento příkaz:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Další informace naleznete v dokumentaci OpenShift o [zakázání samozřivatí .](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Může mít cluster výpočetní uzly ve více oblastech Azure?

Ne. Všechny uzly v clusteru Azure Red Hat OpenShift musí pocházet ze stejné oblasti Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Jsou hlavní uzly a uzly infrastruktury abstrahované tak, jak jsou se službou Azure Kubernetes Service (AKS)?

Ne. Všechny prostředky, včetně hlavního clusteru, běží v rámci vašeho zákaznického předplatného. Tyto typy prostředků jsou umístěny ve skupině prostředků jen pro čtení.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Je podporován a podporován služby Open Service Broker pro Azure (OSBA)?

Ano. OSBA můžete použít s Azure Red Hat OpenShift. Další informace najdete v [tématu Open Service Broker for Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Snažím se napojit do virtuální sítě v `Failed to get vnet CIDR` jiném předplatném, ale dostat chybu.

V předplatném, které má virtuální síť, nezapomeňte zaregistrovat `Microsoft.ContainerService` poskytovatele s`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Co je proces údržby Azure Red Hat OpenShift (ARO)?

Existují tři typy údržby pro ARO: upgrady, zálohování a obnovení etcd dat a údržbu inicionoui poskytovatele cloudu.

+ Inovace zahrnují upgrady softwaru a CVE. CVE náprava dochází při `yum update` spuštění spuštěním a poskytuje okamžité zmírnění.  Souběžně bude vytvořeno nové sestavení bitové kopie pro budoucí vytvoření clusteru.

+ Zálohování a správa dat etcd je automatizovaný proces, který může vyžadovat prostoje clusteru v závislosti na akci. Pokud je databáze etcd obnovena ze zálohy, dojde k prostojům. My zálohovat etcd každou hodinu a udržet posledních 6 hodin záloh.

+ Údržba iniciovaná poskytovatelem cloudu zahrnuje výpadky sítě, úložiště a místní výpadky. Údržba je závislá na poskytovateli cloudu a spoléhá na aktualizace poskytované poskytovatelem.

## <a name="what-is-the-general-upgrade-process"></a>Jaký je obecný proces upgradu?

Spuštění upgradu by mělo být bezpečný proces ke spuštění a nemělby narušit clusterové služby. SRE může spustit proces upgradu, když jsou k dispozici nové verze nebo CVEs jsou vynikající.

Dostupné aktualizace jsou testovány ve fázi prostředí a pak použity na produkční clustery. Při použití je dočasně přidán nový uzel a uzly jsou aktualizovány rotujícím způsobem tak, aby pody udržovat počty replik. Následující doporučené postupy pomáhají zajistit minimální až žádné prostoje.

V závislosti na závažnosti čekající inovace nebo aktualizace se proces může lišit v tom, že aktualizace mohou být použity rychle ke zmírnění vystavení služby CVE. Nová bitová kopie bude vytvořena asynchronně, testována a zavedena jako upgrade clusteru. Kromě toho není žádný rozdíl mezi nouzovou a plánovanou údržbou. Plánovaná údržba není předem naplánována se zákazníkem.

Oznámení mohou být zaslána prostřednictvím ICM a e-mailem, pokud je vyžadována komunikace se zákazníkem.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>A co nouzová vs. plánovaná okna údržby?

Nerozlišujeme mezi těmito dvěma typy údržby. Naše týmy jsou k dispozici 24/7/365 a nepoužívají tradiční plánovaná okna údržby mimo pracovní dobu.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Jak bude hostitelský operační systém a software OpenShift aktualizován?

Hostitelský operační systém a software OpenShift jsou aktualizovány prostřednictvím našeho obecného procesu upgradu a sestavení image.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Jaký je proces restartování aktualizovaného uzlu?

To by mělo být zpracováno jako součást upgradu.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Jsou data uložená v etcd šifrována na ARO?

Není šifrována na úrovni etcd. Možnost jeho zapnutí není v současné době podporována. OpenShift tuto funkci podporuje, ale k tomu, aby se to stalo na cestovní mapě, je nutné inženýrské úsilí. Data jsou šifrována na úrovni disku. Další informace naleznete [v části Šifrování dat ve vrstvě úložiště dat.](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Mohou být protokoly podkladových virtuálních společností přenášeny datovým proudem do systému analýzy protokolu zákazníků?

Syslog, protokoly dockeru, deník a dmesg jsou zpracovány spravovanou službou a nejsou vystaveny zákazníkům.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Jak může zákazník získat přístup k metrikám, jako je procesor/paměť na úrovni uzlu, aby mohl provést akci v měřítku, problémy s laděním atd. Zdá se, `kubectl top` že nemohu spustit na clusteru ARO.

Zákazníci mohou přistupovat k metrikám PROCESORU/Paměti `oc adm top nodes` na `kubectl top nodes` úrovni uzlu pomocí příkazu nebo pomocí clusteru správce zákazníka.  Zákazníci mají také přístup k `pods` metrikám `oc adm top pods` CPU/paměti pomocí příkazu nebo`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Jaká je výchozí konfigurace plánovače podu pro ARO?

ARO používá výchozí plánovač, který je dodáván v OpenShift. Existuje několik dalších mechanismů, které nejsou podporovány v ARO. Další podrobnosti naleznete ve [výchozí dokumentaci plánovače](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) a [hlavní dokumentaci plánovače.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

Rozšířené/vlastní plánování není v současné době podporováno. Další podrobnosti naleznete v dokumentaci k [plánování.](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Pokud navýšineme kapacitu nasazení, jak se domény selhání Azure mapovat do umístění pod zajistit všechny pody pro službu nedostanou vyřazenselhání v jedné doméně selhání?

Při použití škálovacích sad virtuálních strojů v Azure je ve výchozím nastavení ve výchozím nastavení pět domén selhání. Každá instance virtuálního počítače ve škálovací sadě se umístí do jedné z těchto domén selhání. Tím zajistíte, že aplikace nasazené do výpočetních uzlů v clusteru budou umístěny v samostatných doménách selhání.

Další podrobnosti najdete v části [Výběr správného počtu domén selhání pro škálovací sadu virtuálních strojů.](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)

## <a name="is-there-a-way-to-manage-pod-placement"></a>Existuje způsob, jak spravovat umístění pod?

Zákazníci mají možnost získat uzly a zobrazit popisky jako správce zákazníka.  To poskytne způsob, jak cílit na libovolný virtuální virtuální ms ve škálovací sadě.

Při používání určitých štítků je třeba dbát opatrnosti:

- Název hostitele nesmí být použit. Hostname se často střídá s upgrady a aktualizacemi a je zaručeno, že se změní.

- Pokud má zákazník požadavek na konkrétní popisky nebo strategii nasazení, může to být provedeno, ale bude to vyžadovat inženýrské úsilí a není dnes podporováno.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Jaký je maximální počet podů v clusteru ARO?Jaký je maximální počet podů na uzel v ARO?

 Azure Red Hat OpenShift 3.11 má limit 50 pod na uzel s [ARO s limitem 20 výpočetních uzlů](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), takže omezuje maximální počet podů podporovaných v clusteru ARO na 50*20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Můžeme určit rozsahy IP adres pro nasazení v privátní virtuální síti, abychom se vyhnuli kolizím s jinými podnikovými virtuálními sítěmi, které se po vzájemném vztahu?

Azure Red Hat OpenShift podporuje partnerský vztah virtuální sítě a umožňuje zákazníkovi poskytovat virtuální síť pro peer s a VNET CIDR, ve kterém bude fungovat síť OpenShift.

Virtuální síť vytvořená společností ARO bude chráněna a nebude přijímat změny konfigurace. Virtuální síť, která je partnerský vztah je řízen zákazníkem a sídlí v jejich předplatném.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Je cluster umístěn v předplatném zákazníka? 

Spravovaná aplikace Azure se nakládá v uzamčené skupině prostředků s předplatným zákazníka. Zákazník může zobrazit objekty v tomto RG, ale neupravovat.

## <a name="is-the-sdn-module-configurable"></a>Je modul SDN konfigurovatelný?

SDN je openshift-ovs-networkpolicy a není konfigurovatelný.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Která práva systému UNIX (v IaaS) jsou k dispozici pro hlavní servery/uzly Infra/App?

Nevztahuje se na tuto nabídku. Přístup k uzlu je zakázán.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jaká práva OCP máme? Správce clusteru? Správce projektu?

Podrobnosti najdete v [přehledu správy clusteru](https://docs.openshift.com/aro/admin_guide/index.html)Azure Red Hat OpenShift .

## <a name="which-kind-of-federation-with-ldap"></a>Jaký druh federace s LDAP?

Toho by bylo dosaženo prostřednictvím integrace Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Existuje nějaký prvek v ARO sdílené s ostatními zákazníky? Nebo je všechno nezávislé?

Každý cluster Azure Red Hat OpenShift je vyhrazenpro daného zákazníka a žije v rámci předplatného zákazníka. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Můžeme si vybrat nějaké trvalé úložné řešení, jako je OCS? 

K dispozici jsou dvě třídy úložiště: Azure Disk a Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Jak se aktualizuje cluster (včetně velkých a nezletilých z důvodu zranitelnosti)?

Viz [Jaký je obecný proces upgradu?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Co Azure Balancer používá ARO?Je to standardní nebo základní a je konfigurovatelné?

ARO používá standardní Azure Balancer a není konfigurovatelný.

## <a name="can-aro-use-netapp-based-storage"></a>Může ARO používat úložiště založené na aplikaci NetApp?

Momentálně jsou jedinými podporovanými možnostmi úložiště Azure Disk a Azure File Storage. 


