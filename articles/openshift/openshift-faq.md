---
title: Nejčastější dotazy k Azure Red Hat OpenShift
description: Tady jsou odpovědi na běžné otázky týkající se Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619485"
---
# <a name="azure-red-hat-openshift-faq"></a>Nejčastější dotazy k Azure Red Hat OpenShift

Tento článek se věnuje nejčastějším dotazům o Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Které oblasti Azure jsou podporované?

Seznam globálních oblastí, kde se podporuje Azure Red Hat OpenShift, najdete v tématu [podporované prostředky](supported-resources.md#azure-regions) .

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Můžu nasadit cluster do existující virtuální sítě?

Ne. Můžete ale připojit cluster Azure Red Hat OpenShift k existující virtuální síti prostřednictvím partnerského vztahu. Podrobnosti najdete v tématu [připojení virtuální sítě clusteru k existující virtuální síti](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="what-cluster-operations-are-available"></a>Jaké operace clusteru jsou k dispozici?

Navýšení nebo snížení kapacity můžete provést pouze v rámci počtu výpočetních uzlů. Po vytvoření nejsou povoleny žádné další úpravy prostředku `Microsoft.ContainerService/openShiftManagedClusters`. Maximální počet výpočetních uzlů je omezený na 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jaké velikosti virtuálních počítačů můžu použít?

Seznam velikostí virtuálních počítačů, které můžete použít v clusteru Azure Red Hat OpenShift, najdete v tématu [velikosti virtuálních počítačů Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) .

## <a name="is-data-on-my-cluster-encrypted"></a>Jsou data v clusteru zašifrovaná?

Ve výchozím nastavení je šifrování v klidovém stavu. Platforma Azure Storage automaticky šifruje vaše data před jejich uložením a dešifruje data před jejich načtením. Podrobnosti najdete v tématu [šifrování služby Azure Storage pro data v klidovém provozu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) .

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Můžu pomocí Prometheus/Grafana monitorovat své aplikace?

Ano, Prometheus můžete nasadit do svého oboru názvů a monitorovat aplikace ve vašem oboru názvů.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Můžu pomocí Prometheus/Grafana monitorovat metriky související s stavem a kapacitou clusteru?

Ne, v současné době.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Je registr Docker dostupný externě, takže můžu používat nástroje, jako je Jenkinse?

Registr Docker je dostupný z `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` ale není k dispozici silnou záruku odolného úložiště. Můžete také použít [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Je síť mezi obory názvů podporována?

Zákazníci a jednotliví Správci projektu mohou přizpůsobit síť mezi obory názvů (včetně jejich odepření) na základě jednotlivých projektů pomocí `NetworkPolicy` objektů.

## <a name="can-an-admin-manage-users-and-quotas"></a>Může správce spravovat uživatele a kvóty?

Ano. Správce Azure Red Hat OpenShift může kromě přístupu ke všem vytvořeným projektům spravovat i uživatele a kvóty.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Můžu cluster omezit jenom na určité uživatele Azure AD?

Ano. Pomocí konfigurace aplikace Azure AD můžete omezit, kteří uživatelé Azure AD se můžou přihlašovat ke clusteru. Podrobnosti najdete v tématu [Postup: omezení aplikace na skupinu uživatelů.](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Můžu uživatelům zabránit v vytváření projektů?

Ano. Přihlaste se ke svému clusteru jako správce Azure Red Hat OpenShift a spusťte tento příkaz:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Další informace najdete v dokumentaci k OpenShift o [vypnutí samoobslužného zřizování](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Může cluster počítat výpočetní uzly napříč několika oblastmi Azure?

Ne. Všechny uzly v clusteru Azure Red Hat OpenShift musí pocházet ze stejné oblasti Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Jsou hlavní uzly a uzly infrastruktury abstraktní, protože se jedná o službu Azure Kubernetes Service (AKS)?

Ne. Všechny prostředky, včetně hlavního serveru clusteru, se spouštějí v rámci zákaznického předplatného. Tyto typy prostředků jsou vloženy do skupiny prostředků jen pro čtení.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Je otevřená Service Broker pro Azure (OSBA) podporovaná?

Ano. OSBA můžete použít s Azure Red Hat OpenShift. Další informace najdete v tématu [otevření Service Broker pro Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) .

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Snažím se vytvořit partnerský vztah do virtuální sítě v jiném předplatném, ale při `Failed to get vnet CIDR` chybě.

V předplatném, které má virtuální síť, nezapomeňte zaregistrovat poskytovatele `Microsoft.ContainerService` s `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Co je proces údržby Azure Red Hat OpenShift (ARO)?

Existují tři typy údržby pro společnosti ARO: upgrady, zálohování a obnovení dat etcd a údržba iniciovaná poskytovatelem cloudu.

+ Mezi upgrady patří upgrady softwaru a CVEs. CVE napravení probíhá při spuštění spuštěním `yum update` a poskytuje okamžité zmírnění.  V paralelním případě se vytvoří nové sestavení image pro budoucí vytváření clusterů.

+ Zálohování a správa dat etcd je automatizovaný proces, který může vyžadovat výpadky clusteru v závislosti na akci. Pokud se databáze etcd obnovuje ze zálohy, dojde k výpadkům. Zálohujte etcd každou hodinu a zachovejte posledních 6 hodin zálohování.

+ Údržba iniciovaná poskytovatelem cloudu zahrnuje sítě, úložiště a oblastní výpadky. Údržba závisí na poskytovateli cloudu a spoléhá na aktualizace poskytované zprostředkovatelem.

## <a name="what-is-the-general-upgrade-process"></a>Jaký je obecný proces upgradu?

Spuštění upgradu by mělo být bezpečný proces ke spuštění a nemělo by rušit Clusterové služby. SRE může aktivovat proces upgradu, pokud jsou k dispozici nové verze nebo pokud CVEs nejsou v oběhu.

Dostupné aktualizace se testují v prostředí fáze a pak se aplikují na produkční clustery. Při použití je nový uzel dočasně přidán a uzly budou aktualizovány rotujícím způsobem tak, aby lusky zachovaly počty replik. Následující osvědčené postupy pomáhají zajistit minimální a žádné výpadky.

V závislosti na závažnosti nedokončeného upgradu nebo aktualizace se tento proces může lišit v tom, že se aktualizace dají rychle použít ke zmírnění ohrožení služby na CVE. Novou bitovou kopii bude sestavena asynchronně, testována a zahrnuta jako upgrade clusteru. Kromě toho se nejedná o rozdíl mezi mimořádnou a plánovanou údržbou. Plánovaná údržba není u zákazníka předplánována.

Oznámení se můžou posílat pomocí ICM a e-mailu, pokud se vyžaduje komunikace se zákazníkem.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Co je v nouzi a v plánovaných oknech údržby?

Mezi těmito dvěma typy údržby nerozlišujeme. Naši týmy jsou k dispozici 24/7/365 a nepoužívají tradiční plánovaná časová období údržby "mimo špičku".

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Jak se bude hostovat operační systém a OpenShift software se aktualizuje?

Hostitelský operační systém a OpenShift software se aktualizují prostřednictvím našeho obecného procesu sestavení pro upgrade a image.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Jaký je proces restartování aktualizovaného uzlu?

To by mělo být zpracováno jako součást upgradu.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Jsou data uložená v etcd zašifrovaná v ARO společnosti?

Není zašifrovaný na úrovni etcd. Možnost zapnutí této funkce je aktuálně Nepodporovaná. OpenShift podporuje tuto funkci, ale k jejímu vytváření na mapě se vyžadují technické úsilí. Data se šifrují na úrovni disku. Další informace najdete [v tématu šifrování dat ve vrstvě úložiště dat](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Můžou se do systému pro analýzu protokolů zákazníků zasílat protokoly podkladových virtuálních počítačů?

Služba Syslog, protokoly Docker, deník a dmesg se zpracovávají pomocí spravované služby a nezveřejňují se zákazníkům.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Jak může zákazník získat přístup k metrikám, jako je CPU/paměť na úrovni uzlu, aby bylo možné provést akci škálování, problémů ladění atd. Nemohu spustit `kubectl top` v clusteru ARO.

Zákazníci mohou získat přístup k metrikám CPU nebo paměti na úrovni uzlu pomocí příkazu `oc adm top nodes` nebo `kubectl top nodes` pomocí clusterrole Customer-Administrator.  Zákazníci můžou ke metrikám CPU nebo paměti `pods` také přistupovat pomocí příkazového `oc adm top pods` nebo `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Jaká je výchozí konfigurace plánovače pro ARO společnosti?

Služba ARO používá výchozí Plánovač, který je dodáván v OpenShift. V ARO společnosti se nepodporují několik dalších mechanismů. Další podrobnosti najdete v dokumentaci k [výchozímu plánovači](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) a v [dokumentaci k hlavnímu plánovači](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) .

Rozšířené nebo vlastní plánování nejsou aktuálně podporovány. Další podrobnosti najdete v [dokumentaci plánování](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) .

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Pokud jsme nastavili horizontální navýšení kapacity, jak se namapuje domény selhání Azure na umístění pod tím, aby se vykrojly selhání v jedné doméně selhání?

K dispozici jsou výchozí pět domén selhání při používání služby Virtual Machine Scale Sets v Azure. Každá instance virtuálního počítače v sadě škálování se dostane do jedné z těchto domén selhání. Tím se zajistí, že se aplikace nasazené do výpočetních uzlů v clusteru umístí do samostatných domén selhání.

Další podrobnosti najdete [v tématu Volba správného počtu domén selhání pro sadu škálování virtuálního počítače](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) .

## <a name="is-there-a-way-to-manage-pod-placement"></a>Existuje způsob, jak spravovat umístění pod.

Zákazníci mají možnost získávat uzly a zobrazovat popisky jako správce zákazníka.  Díky tomu bude možné cílit na libovolný virtuální počítač v sadě škálování.

Při použití určitých popisků se musí použít upozornění:

- Název hostitele nesmí být použit. Název hostitele se často otáčí s upgrady a aktualizacemi a má zaručenou změnu.

- Pokud zákazník obsahuje požadavek na konkrétní štítky nebo strategii nasazení, může to být dosaženo, ale vyžaduje technické úsilí a ještě dnes není podporovaný.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Jaký je maximální počet lusků v clusteru ARO?  Jaký je maximální počet lusků na jeden uzel v ARO společnosti?

 Služba Azure Red Hat OpenShift 3,11 má limit 50 na uzel pod na jeden uzel s [limitem 20 výpočetních uzlů](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), aby nedošlo k omezení maximálního počtu lusků podporovaných v clusteru ARO na 50 * 20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Můžeme zadat rozsahy IP adres pro nasazení na privátní virtuální síti a vyhnout se konfliktům s ostatními podnikovými virtuální sítě po sobě partnerských vztahů?

Azure Red Hat OpenShift podporuje partnerský vztah virtuálních sítí a umožňuje zákazníkům poskytnout virtuální síť pro partnerský vztah a virtuální síť CIDR, ve které bude síť OpenShift fungovat.

Virtuální síť, kterou vytvořila služba ARO společnosti, bude chráněná a nebude přijímat změny konfigurace. Virtuální síť, která je v partnerském vztahu, je řízená zákazníkem a nachází se ve svém předplatném.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Je cluster umístěn v rámci předplatného zákazníka? 

Spravovaná aplikace Azure bydlí v uzamčené skupině prostředků s předplatným zákazníka. Zákazník může zobrazit objekty v tomto RG, ale ne upravovat.

## <a name="is-the-sdn-module-configurable"></a>Je modul SDN konfigurovatelný?

SDN je OpenShift-OVS-networkpolicy a nedá se konfigurovat.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Která práva systému UNIX (v IaaS) jsou k dispozici pro uzly Master/infrastruktura/aplikace?

Neplatí pro tuto nabídku. Přístup k uzlu je zakázaný.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jaká práva OCP máme? Správce clusteru? Projekt – správce?

Podrobnosti najdete v tématu [Přehled správy clusteru](https://docs.openshift.com/aro/admin_guide/index.html)Azure Red Hat OpenShift.

## <a name="which-kind-of-federation-with-ldap"></a>Jaký druh federace se službou LDAP?

To se dá dosáhnout prostřednictvím integrace služby Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Sdílí se s ostatními zákazníky nějaký prvek v ARO společnosti? Nebo je vše nezávislé?

Každý cluster Azure Red Hat OpenShift je vyhrazený pro daného zákazníka a v rámci předplatného zákazníka. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Můžeme zvolit trvalé řešení úložiště, jako je třeba OCS? 

K dispozici jsou dvě třídy úložiště pro výběr z disku: disk Azure a soubor Azure.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Jak se cluster aktualizoval (včetně hlavních a podřízených) z důvodu ohrožení zabezpečení?

Podívejte [se, jaký je obecný proces upgradu?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Co služba pro vyrovnávání zatížení Azure používá v ARO?  Je Standard nebo Basic a je konfigurovatelná?

ARO používá standardní Azure Load Balancer a nedá se konfigurovat.

## <a name="can-aro-use-netapp-based-storage"></a>Můžou ARO používat úložiště založené na NetApp?

V současné době jsou jedinou podporovanou možností úložiště třídy úložiště disků a souborů Azure. 


