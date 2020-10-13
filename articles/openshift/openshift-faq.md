---
title: Nejčastější dotazy k Azure Red Hat OpenShift
description: Tady jsou odpovědi na běžné otázky týkající se Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a8b5ec48b64341ad9eabd087d7ee20bb703198c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816231"
---
# <a name="azure-red-hat-openshift-faq"></a>Nejčastější dotazy k Azure Red Hat OpenShift

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Instalace a upgrade

### <a name="which-azure-regions-are-supported"></a>Které oblasti Azure jsou podporované?

Seznam podporovaných oblastí pro Azure Red Hat OpenShift 4. x najdete v tématu [dostupné oblasti](https://docs.openshift.com/aro/4/welcome/index.html#available-regions).

Seznam podporovaných oblastí pro Azure Red Hat OpenShift 3,11 najdete v tématu [Dostupné produkty v jednotlivých oblastech](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Jaké velikosti virtuálních počítačů můžu použít?

Seznam podporovaných velikostí virtuálních počítačů pro Azure Red Hat OpenShift 4 najdete v tématu [podporované prostředky pro Azure Red Hat OpenShift 4](support-policies-v4.md).

Seznam podporovaných velikostí virtuálních počítačů pro Azure Red Hat OpenShift 3,11 najdete v tématu [podporované prostředky pro Azure Red Hat OpenShift 3,11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Jaký je maximální počet lusků v clusteru Azure Red Hat OpenShift?  Jaký je maximální počet lusků na uzel v Azure Red Hat OpenShift?

Skutečný počet podporovaných lusků závisí na požadavcích aplikace na paměť, procesor a úložiště.

Azure Red Hat OpenShift 4. x má limit 250 pod 1 a 100 limit počtu výpočetních uzlů. Tato Verzálky maximální počet lusků podporovaných v clusteru až 250 &times; 100 = 25 000.

Azure Red Hat OpenShift 3,11 má limit 50 pod uzlem a 20 omezení výpočetních uzlů. Tato Verzálky maximální počet lusků podporovaných v clusteru až 50 &times; 20 = 1 000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Může cluster počítat výpočetní uzly napříč několika oblastmi Azure?

Ne. Všechny uzly v clusteru Azure Red Hat OpenShift musí pocházet ze stejné oblasti Azure.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Dá se cluster nasadit napříč několika zónami dostupnosti?

Ano. K tomu dojde automaticky v případě, že je váš cluster nasazený do oblasti Azure, která podporuje zóny dostupnosti. Další informace najdete v tématu [zóny dostupnosti](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Jsou uzly řídicí plochy abstraktní, protože se jedná o službu Azure Kubernetes Service (AKS)?

Ne. Všechny prostředky, včetně hlavních uzlů clusteru, se spouštějí v rámci zákaznického předplatného. Tyto typy prostředků jsou vloženy do skupiny prostředků jen pro čtení.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Je cluster umístěn v rámci předplatného zákazníka? 

Spravovaná aplikace Azure bydlí v uzamčené skupině prostředků s předplatným zákazníka. Zákazníci si můžou zobrazit objekty v dané skupině prostředků, ale nemůžou je upravovat.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Je nějaký element v Azure Red Hat OpenShift sdílený s ostatními zákazníky? Nebo je vše nezávislé?

Každý cluster Azure Red Hat OpenShift je vyhrazený pro daného zákazníka a v rámci předplatného zákazníka. 

### <a name="are-infrastructure-nodes-available"></a>Jsou k dispozici uzly infrastruktury?

V clusterech Azure Red Hat OpenShift 4. x nejsou uzly infrastruktury aktuálně k dispozici.

Ve výchozím nastavení jsou v clusterech Azure Red Hat OpenShift 3,11 zahrnuté uzly infrastruktury.

## <a name="how-do-i-handle-cluster-upgrades"></a>Návody se obsluhují upgrady clusteru?

Informace o upgradech, údržbě a podporovaných verzích najdete v [Průvodci životního cyklu podpory](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Jak se bude aktualizovat hostitelský operační systém a OpenShift software?

Hostitelský operační systém a OpenShift software se aktualizují, protože Azure Red Hat OpenShift spotřebovává drobné verze vydaných verzí a opravy z nadřazeného rozhraní kontejneru OpenShift.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Jaký je proces restartování aktualizovaného uzlu?

Uzly se restartují jako součást upgradu.

## <a name="cluster-operations"></a>Operace clusteru

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Můžu použít Prometheus k monitorování mých aplikací?

Prometheus je součástí předinstalovaného a nakonfigurovaného pro clustery Azure Red Hat OpenShift 4. x. Přečtěte si další informace o [monitorování clusteru](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html).

Pro clustery Azure Red Hat OpenShift 3,11 můžete v oboru názvů nasadit Prometheus a monitorovat aplikace ve vašem oboru názvů. Další informace najdete v tématu [nasazení instance Prometheus v clusteru Azure Red Hat OpenShift](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Můžu pomocí Prometheus monitorovat metriky související s stavem a kapacitou clusteru?

V Azure Red Hat OpenShift 4. x: Ano.

V Azure Red Hat OpenShift 3,11: ne.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Můžou se do systému pro analýzu protokolů zákazníků zasílat protokoly podkladových virtuálních počítačů?

Protokoly z podkladových virtuálních počítačů jsou zpracovávány spravovanou službou a nejsou vystaveny zákazníkům.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Jak může zákazník získat přístup k metrikám, jako je CPU/paměť na úrovni uzlu, aby bylo možné provést akci škálování, problémů ladění atd.? Nemůžu spustit kubectl v clusteru Azure Red Hat OpenShift.

Pro clustery Azure Red Hat OpenShift 4. x obsahuje webová konzola OpenShift všechny metriky na úrovni uzlu. Další informace najdete v dokumentaci k Red Hat o [zobrazení informací o clusteru](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html).

Pro clustery Azure Red Hat OpenShift 3,11 můžou zákazníci získat přístup k metrikám CPU nebo paměti na úrovni uzlu pomocí příkazu `oc adm top nodes` nebo `kubectl top nodes` s rolí clusteru Customer-admin. Zákazníci můžou k metrikám CPU nebo paměti přistoupit také `pods` pomocí příkazu `oc adm top pods` nebo `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Pokud jsme nastavili horizontální navýšení kapacity, jak se namapuje domény selhání Azure na umístění pod tím, aby se vykrojly selhání v jedné doméně selhání?

K dispozici jsou výchozí pět domén selhání při používání služby Virtual Machine Scale Sets v Azure. Každá instance virtuálního počítače v sadě škálování se dostane do jedné z těchto domén selhání. Tím se zajistí, že se aplikace nasazené do výpočetních uzlů v clusteru umístí do samostatných domén selhání.

Další informace najdete v tématu [Volba správného počtu domén selhání pro sadu škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Existuje způsob, jak spravovat umístění pod.

Zákazníci mají možnost získávat uzly a zobrazovat popisky jako správce zákazníka. Díky tomu bude možné cílit na libovolný virtuální počítač v sadě škálování.

Při použití určitých popisků se musí použít upozornění:

- Název hostitele nesmí být použit. Název hostitele se často otáčí s upgrady a aktualizacemi a má zaručenou změnu.
- Pokud zákazník obsahuje požadavek na konkrétní štítky nebo strategii nasazení, může to být dosaženo, ale vyžaduje technické úsilí a ještě dnes není podporovaný.

Další informace najdete v tématu [řízení umístění pod](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Je registr image externě dostupný, takže můžu používat nástroje, jako je Jenkinse?

U clusterů 4. x je nutné vystavit zabezpečený registr a nakonfigurovat ověřování. Další informace najdete v následující dokumentaci k Red Hat:

- [Zveřejňuje se registr.](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [Přístup k registru](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

V případě clusterů 3,11 je k dispozici registr Docker image. Registr Docker je k dispozici z `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Můžete také použít Azure Container Registry.

## <a name="networking"></a>Sítě

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Můžu nasadit cluster do existující virtuální sítě?

V clusterech se 4. x můžete nasadit cluster do existující virtuální sítě.

V clusterech 3,11 nemůžete nasadit cluster do existující virtuální sítě. Cluster Azure Red Hat OpenShift 3,11 můžete připojit k existující virtuální síti prostřednictvím partnerského vztahu.

### <a name="is-cross-namespace-networking-supported"></a>Je síť mezi obory názvů podporována?

Zákazníci a jednotliví Správci projektu mohou přizpůsobit sítě mezi obory názvů (včetně jejich odepření) na základě jednotlivých projektů pomocí `NetworkPolicy` objektů.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Snažím se vytvořit partnerský vztah k virtuální síti v jiném předplatném, ale při neúspěšném získání chyby CIDR virtuální sítě.

V předplatném, které má virtuální síť, nezapomeňte zaregistrovat `Microsoft.ContainerService` poskytovatele pomocí následujícího příkazu: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Můžeme zadat rozsahy IP adres pro nasazení na privátní virtuální síti a vyhnout se konfliktům s ostatními podnikovými virtuální sítě po sobě partnerských vztahů?

V clusterech se 4. x můžete zadat vlastní rozsahy IP adres.

V clusterech 3,11 Azure Red Hat OpenShift podporuje partnerský vztah virtuálních sítí a umožňuje zákazníkovi poskytnout virtuální síť pro partnerský vztah a virtuální síť CIDR, ve které bude síť OpenShift fungovat.

Virtuální síť vytvořená pomocí Azure Red Hat OpenShift bude chráněná a nebude přijímat změny konfigurace. Virtuální síť, která je v partnerském vztahu, je řízená zákazníkem a nachází se ve svém předplatném.

### <a name="is-the-software-defined-network-module-configurable"></a>Je modul softwarově definované sítě konfigurovatelný?

Softwarově definovaná síť je `openshift-ovs-networkpolicy` a nelze ji konfigurovat.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Který nástroj pro vyrovnávání zatížení Azure používá Azure Red Hat OpenShift?  Je Standard nebo Basic a je konfigurovatelná?

Azure Red Hat OpenShift používá standardní Azure Load Balancer a nedá se konfigurovat.

## <a name="permissions"></a>Oprávnění

### <a name="can-an-admin-manage-users-and-quotas"></a>Může správce spravovat uživatele a kvóty?

Ano. Správce Azure Red Hat OpenShift může kromě přístupu ke všem vytvořeným projektům spravovat i uživatele a kvóty.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Můžu cluster omezit jenom na určité uživatele Azure AD?

Ano. Pomocí konfigurace aplikace Azure AD můžete omezit, kteří uživatelé Azure AD se můžou přihlašovat ke clusteru. Podrobnosti najdete v tématu [Postup: omezení aplikace na sadu uživatelů](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Můžu uživatelům zabránit v vytváření projektů?

Ano. Přihlaste se ke svému clusteru jako správce a spusťte tento příkaz:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Další informace najdete v dokumentaci OpenShift o zakázání samoobslužného zřizování pro vaši verzi clusteru:

- [Zakázání samoobslužného zřizování v clusterech 4,3](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Zakázání samoobslužného zřizování v clusterech 3,11](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Která práva systému UNIX (v IaaS) jsou k dispozici pro uzly Master/infrastruktura/aplikace?

V případě clusterů 4. x je přístup k uzlu dostupný prostřednictvím role Správce clusteru. Další informace najdete v tématu [Přehled RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

V případě clusterů 3,11 je přístup k uzlu zakázán.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jaká práva OCP máme? Správce clusteru? Projekt – správce?

Pro clustery s frekvencí 4. x je k dispozici role Správce clusteru. Další informace najdete v tématu [Přehled RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Clustery 3,11 najdete v tématu [Přehled správy clusteru](https://docs.openshift.com/aro/admin_guide/index.html) , kde najdete další podrobnosti.

### <a name="which-identity-providers-are-available"></a>Kteří zprostředkovatelé identity jsou k dispozici?

U clusterů 4. x nakonfigurujete vlastního poskytovatele identity. Další informace najdete v dokumentaci k Red Hat týkající se [Konfigurace identity prodivers](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html).

Pro clustery 3,11 můžete použít integraci Azure AD. 

## <a name="storage"></a>Storage

### <a name="is-data-on-my-cluster-encrypted"></a>Jsou data v clusteru zašifrovaná?

Ve výchozím nastavení jsou data v klidovém stavu šifrovaná. Platforma Azure Storage automaticky šifruje vaše data před jejich uložením a dešifruje data před jejich načtením. Další informace najdete v tématu [šifrování služby Azure Storage pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Jsou data uložená v etcd zašifrovaná na Azure Red Hat OpenShift?

Pro clustery Azure Red Hat OpenShift 4 nejsou data ve výchozím nastavení zašifrovaná, ale máte možnost povolit šifrování. Další informace najdete v příručce k [šifrování etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html).

V případě clusterů 3,11 nejsou data na úrovni etcd šifrovaná. Možnost zapnout šifrování není momentálně podporovaná. OpenShift podporuje tuto funkci, ale k jejímu vytváření na mapě se vyžadují technické úsilí. Data se šifrují na úrovni disku. Další informace najdete [v tématu šifrování dat ve vrstvě úložiště dat](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Můžeme zvolit trvalé řešení úložiště, jako je třeba OCS? 

Pro clustery 4. x je disk Azure (Premium_LRS) nakonfigurovaný jako výchozí třída úložiště. Další poskytovatele úložiště a podrobnosti o konfiguraci (včetně souboru Azure) najdete v dokumentaci k Red Hat v [trvalém úložišti](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html).

Pro clustery 3,11 jsou ve výchozím nastavení k dispozici dvě třídy úložiště: jeden pro disk Azure (Premium_LRS) a jeden pro soubor Azure.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>Ukládá ARO všechna zákaznická data mimo oblast clusteru?

Ne. Všechna data vytvořená v clusteru ARO se udržují v rámci oblasti clusteru.