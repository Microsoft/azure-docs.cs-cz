---
title: Základní hodnoty zabezpečení Azure pro Azure Kubernetes Service
description: Základní hodnoty zabezpečení služby Azure Kubernetes poskytují pokyny a zdroje informací pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5ca792c6dbc3c08847315b916913e8c38909a7a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637132"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Základní hodnoty zabezpečení Azure pro Azure Kubernetes Service

Základní plán zabezpečení Azure pro službu Azure Kubernetes obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview-v1).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné**materiály: ve výchozím nastavení se automaticky vytvoří skupina zabezpečení sítě a směrovací tabulka s vytvořením clusteru služby Microsoft Azure KUBERNETES (AKS). AKS automaticky mění skupiny zabezpečení sítě pro příslušný tok provozu, protože služby Vyrovnávání zatížení, mapování portů nebo trasy příchozího přenosu dat se vytvářejí. Skupina zabezpečení sítě je automaticky přidružena k virtuálním síťovým kartám na zákaznických uzlech a směrovací tabulce s podsítí ve virtuální síti. 

Pomocí zásad sítě AKS můžete omezit síťový provoz tím, že definujete pravidla pro příchozí a odchozí provoz mezi v systémech Linux v clusteru na základě výběru oborů názvů a selektorů popisků. Použití zásad sítě vyžaduje modul plug-in Azure CNI s definovanou virtuální sítí a podsítěmi a dá se povolit jenom při vytváření clusteru. Nelze je nasadit v existujícím clusteru AKS.

Můžete implementovat privátní cluster AKS, abyste zajistili, že síťový provoz mezi serverem API AKS a fondy uzlů zůstane jenom v privátní síti. Rovina ovládacího prvku nebo serveru rozhraní API se nachází v předplatném Azure spravovaném v AKS a používá interní (RFC1918) IP adresy, ale cluster nebo fond uzlů zákazníka je ve svém vlastním předplatném. Server a cluster nebo fond uzlů spolu navzájem komunikují pomocí služby privátního propojení Azure ve virtuální síti serveru API a privátního koncového bodu, který je vystavený v podsíti clusteru AKS zákazníka.  Případně můžete použít veřejný koncový bod pro Server rozhraní AKS API, ale omezte přístup pomocí funkce s povolenými rozsahy IP adres serveru rozhraní API AKS. 

- [Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes (AKS)](concepts-security.md)

- [Zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Vytvoření privátního clusteru služby Azure Kubernetes](private-clusters.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Pokyny**: použijte Security Center a postupujte podle doporučení pro ochranu sítě a zabezpečte síťové prostředky používané clustery Azure Kubernetes Service (AKS). 

Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Protokoly toku můžete také odeslat do pracovního prostoru Log Analytics a potom pomocí Analýza provozu poskytnout přehled o vzorech provozu v cloudu Azure, abyste mohli vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů dat a odhalit nesprávné konfigurace sítě.

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak povolit protokoly toku zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: k zajištění další úrovně zabezpečení pomocí filtrování příchozího provozu do webových aplikací použijte firewall webové aplikace s podporou služby Azure Application Gateway (WAF). Azure WAF používá sadu pravidel, která je k dispozici v otevřeném projektu OWASP (Web Application Security), pro útoky, jako je například skriptování mezi lokalitami nebo poškození souborů cookie proti tomuto provozu. 

Použijte bránu API pro ověřování, autorizaci, omezování, ukládání do mezipaměti, transformaci a monitorování rozhraní API používaných ve vašem prostředí AKS. Brána API slouží jako přední dveře ke mikroslužbám, odděluje klienty od vašich mikroslužeb a snižuje složitost vašich mikroslužeb tím, že odstraňuje zatížení, které zpracovává obavy z hlediska vzájemného průřezu.

- [Vysvětlení osvědčených postupů pro připojení k síti a zabezpečení v AKS](operator-best-practices-network.md)

- [Application Gateway adaptér příchozího přenosu dat ](../application-gateway/ingress-controller-overview.md)

- [Použití Azure API Management s mikroslužbami nasazenými ve službě Azure Kubernetes](../api-management/api-management-kubernetes.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné**materiály: ve virtuálních sítích, ve kterých jsou nasazené součásti Azure Kubernetes Service (AKS), povolte pro ochranu proti útokům DDoS standardní ochranu DDoS (Microsoft Distributed-of-of-Service).
Nainstalujte modul zásad sítě a vytvořte zásady sítě Kubernetes, abyste mohli řídit tok přenosů mezi lusky v AKS, jako ve výchozím nastavení je povolen veškerý provoz mezi těmito lusky. Zásady sítě by se měly používat jenom pro uzly se systémem Linux a lusky v AKS. Pro zvýšení zabezpečení definujte pravidla, která omezují komunikaci pod. 

Vyberte možnost povolit nebo zakázat provoz na základě nastavení, jako jsou například přiřazené popisky, obor názvů nebo přenosový port. Požadované zásady sítě se dají automaticky použít, protože v clusteru AKS se dynamicky vytvoří lusky. 

- [Zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Jak nakonfigurovat DDoS Protection](../virtual-network/manage-ddos-protection.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné**materiály: použijte zachytávání paketů Network Watcher, jak je potřeba k prozkoumání aktivity neobvyklé. 

Při vytváření nebo aktualizaci virtuální sítě v rámci předplatného je Network Watcher automaticky zapnutá v oblasti vaší virtuální sítě. Můžete také vytvořit nové instance Network Watcher pomocí prostředí PowerShell, rozhraní příkazového řádku Azure CLI, REST API nebo metody ARMClient

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné**materiály: zabezpečení clusteru Azure Kubernetes Service (AKS) pomocí služby Azure Application Gateway povolené pomocí brány firewall webových aplikací (WAF). 

Pokud zjišťování nebo prevence vniknutí není v závislosti na kontrole datové části nebo na analýze chování nutný, je možné Azure Application Gateway s WAF použít a nakonfigurovat v "režimu detekce" k protokolování výstrah a hrozeb nebo "režim prevence" k aktivnímu blokování zjištěných vniknutí a útoků.

- [Vysvětlení osvědčených postupů pro zabezpečení clusteru AKS pomocí WAF](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Jak nasadit Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: k filtrování příchozího provozu použijte Firewall webových aplikací s podporou Application Gateway Azure (WAF) před clusterem AKS. Projekt OWASP (Open Web Application Security) poskytuje sadu pravidel, která se používají ve službě Azure WAF ke sledování útoků, jako jsou skriptování mezi weby nebo poškození souborů cookie.

Použijte plně kvalifikované značky domény (FQDN) pro aplikace, které je možné snadno použít při nastavování pravidel aplikací v rámci skupiny zabezpečení sítě. Po nastavení síťových pravidel. Přidejte pravidlo aplikace pomocí značky plně kvalifikovaného názvu domény, například AzureKubernetesService, která zahrnuje všechny požadované plně kvalifikované názvy domén přístupné prostřednictvím portu TCP 443 a portu 80. 

- [Vysvětlení osvědčených postupů pro připojení k síti a zabezpečení v AKS](operator-best-practices-network.md)

- [Zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Jak nasadit Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti u skupin zabezpečení sítě přidružených k instancím služby Azure Kubernetes Service (AKS). Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení pro povolení nebo zamítnutí provozu pro odpovídající službu zadáním názvu značky služby. 

Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Použijte značku Azure pro fondy uzlů v clusteru AKS. Liší se od značek služby virtuální sítě a jsou aplikovány na každý uzel v rámci fondu uzlů a trvaly prostřednictvím upgradů. 

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

- [Principy skupin zabezpečení sítě pro AKS](support-policies.md)

- [Řízení přenosů dat pro uzly clusteru ve službě Azure Kubernetes (AKS)](limit-egress-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pomocí Azure Policy pro síťové prostředky přidružené k vašim clusterům Azure Kubernetes Service (AKS). Pomocí aliasů Azure Policy v oborech názvů Microsoft. ContainerService a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich clusterů AKS. 

Můžete také použít předdefinované definice zásad týkající se AKS, například:

• Pro služby Kubernetes by měly být definované rozsahy povolených IP adres.

• Vynutil příchozí přenos HTTPS v clusteru Kubernetes

• Zajistěte, aby služby naslouchaly jenom na povolených portech v clusteru Kubernetes

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](/azure/governance/policy/samples/#network)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro skupiny zabezpečení sítě a další prostředky pro přenos toků do a z clusterů Azure Kubernetes Service (AKS). Použijte pole Popis pro jednotlivá pravidla skupiny zabezpečení sítě, abyste určili potřebu firmy nebo dobu trvání a tak dále, pro všechna pravidla, která umožňují přenos do/ze sítě.
Použijte některou z předdefinovaných Azure Policy definicí souvisejících s označováním, například "vyžadovat značku a její hodnotu", které zajistí, aby byly všechny prostředky vytvořené pomocí značek a přijímaly oznámení pro existující neoznačené prostředky.

Zvolte, že chcete povolit nebo zamítnout konkrétní síťové cesty v rámci clusteru založené na oborech názvů a selektorech popisků se zásadami sítě. Tyto obory názvů a popisky použijte jako popisovače pro pravidla konfigurace provozu. Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure (CLI) můžete vyhledat nebo provádět akce s prostředky na základě jejich značek.

- [Azure Policy pomocí rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/policy?view=azure-cli-latest)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s clustery Azure Kubernetes Service (AKS). 

Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích. Jakékoli položky z AzureContainerService uživatele v protokolech aktivit se zaznamenávají jako akce platformy. 

Protokoly Azure Monitor můžete použít k povolení a dotazování protokolů z AKS hlavních komponent, Kube-apiserver a Kube-Controller-Manager. Vytvářejte a spravujte uzly, které spouštějí kubelet, s modulem runtime kontejneru a nasaďte své aplikace prostřednictvím spravovaného serveru rozhraní Kubernetes API. 

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Povolení a kontrola protokolů hlavních uzlů Kubernetes ve službě Azure Kubernetes Service (AKS)](view-master-logs.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: uzly služby Azure Kubernetes Service (AKS) používají NTP.Ubuntu.com pro synchronizaci času spolu s portem UDP 123 a protokolem NTP (Network Time Protocol). 

Pokud používáte vlastní servery DNS, ujistěte se, že uzly clusteru mají přístup k serverům NTP. 

- [Principy požadavků na doménu a porty NTP pro uzly clusteru AKS](limit-egress-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: Povolte protokoly auditu ze služeb Azure Kubernetes Services (AKS) Master Components, Kube-apiserver a Kube-Controller-Manager, které jsou k dispozici jako spravovaná služba. 

• Kube-auditaksService: zobrazovaný název v protokolu auditu pro operaci správy roviny (z hcpService) 

• MasterClient: zobrazovaný název v protokolu auditu pro MasterClientCertificate, certifikát, ze kterého získáte AZ AKS Get-Credentials 

• nodeclient: zobrazovaný název pro ClientCertificate, který se používá v uzlech agentů

Povolte také další protokoly auditu, jako je Kube-audit. 

Exportujte tyto protokoly do Log Analytics nebo jiné úložné platformy. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Povolte a zapněte tato data do Azure Sentinel nebo SIEM třetí strany na základě podnikových požadavků vaší organizace.

- [Tady si přečtěte schéma protokolu včetně rolí protokolů.](view-master-logs.md)

- [Principy Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-overview.md)

- [Postup povolení Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-onboard.md)

- [Povolení a kontrola protokolů hlavních uzlů Kubernetes ve službě Azure Kubernetes Service (AKS)](view-master-logs.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: pomocí protokolů aktivit můžete monitorovat akce v prostředcích Azure Kubernetes Service (AKS) a zobrazit všechny aktivity a jejich stav. Určete, jaké operace byly provedeny u prostředků ve vašem předplatném pomocí protokolů aktivit: kdo tuto operaci zahájil.

Při výskytu operace

stav operace

hodnoty dalších vlastností, které vám mohou při průzkumu operace.

Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, rozhraní příkazového řádku Azure (CLI), Azure REST API nebo Azure Portal. 

Povolte protokoly auditu pro hlavní komponenty AKS, například: 

• Kube-auditaksService: zobrazovaný název v protokolu auditu pro operaci správy roviny (z hcpService) 

• MasterClient: zobrazovaný název v protokolu auditu pro MasterClientCertificate, certifikát, ze kterého získáte AZ AKS Get-Credentials 

• nodeclient: zobrazovaný název pro ClientCertificate, který se používá v uzlech agentů

Zapněte také další protokoly auditu, jako je Kube-audit. 

- [Jak povolit a zkontrolovat protokoly hlavního uzlu Kubernetes v AKS](view-master-logs.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: povolte automatickou instalaci agentů Log Analytics pro shromažďování dat z uzlů clusteru AKS. Automatické zřizování Azure Security Center služby Azure Log Analytics Monitoring Agent taky můžete zapnout automaticky, protože je ve výchozím nastavení Automatické zřizování vypnuté. Agenta je také možné nainstalovat ručně. Při automatickém zřizování Security Center nasadí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Security Center shromažďuje data ze služby Azure Virtual Machines (VM), virtuálních počítačů Scale Sets a IaaS kontejnerů, jako jsou například uzly clusteru Kubernetes, které slouží ke sledování chyb zabezpečení a hrozeb. Data se shromažďují pomocí agenta Azure Log Analytics, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Shromažďování dat je nutné, aby poskytovala přehled o chybějících aktualizacích, nesprávně nakonfigurovaných nastavení zabezpečení operačního systému, stavu aplikace Endpoint Protection a detekci hrozeb.

- [Postup povolení automatického zřizování agenta Log Analytics](../security-center/security-center-enable-data-collection.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: zprovoznění instancí služby Azure KUBERNETES (AKS) pro Azure monitor a nastavení odpovídající doby uchování pracovního prostoru Azure Log Analytics podle požadavků vaší organizace na dodržování předpisů. 

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné**materiály: zprovoznění instancí služby Azure KUBERNETES (AKS) pro Azure monitor a konfigurace nastavení diagnostiky pro váš cluster. 

Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Protokoly Azure Monitor jsou povolené a spravované v Azure Portal nebo prostřednictvím rozhraní příkazového řádku a pracují s clustery řízení přístupu na základě role (Azure RBAC) a s povolenými AKS clustery bez RBAC.

Podívejte se na protokoly generované hlavními komponentami AKS (Kube-apiserver a Kube-controllermanager) pro řešení potíží s aplikací a službami. Povolte a připojte data do Azure Sentinel nebo SIEM třetí strany pro centralizovanou správu protokolů a monitorování.

- [Jak povolit a zkontrolovat protokoly hlavního uzlu Kubernetes v AKS](view-master-logs.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: pomocí služby Azure Kubernetes Service (AKS) společně s Security Center získáte hlubší přehled o AKS uzlech. Zkontrolujte Security Center upozornění na hrozby a škodlivou aktivitu zjištěnou na hostiteli a na úrovni clusteru. Security Center implementuje průběžnou analýzu nezpracovaných událostí zabezpečení, ke kterým došlo v clusteru AKS, jako jsou síťová data, vytváření procesů a protokol auditu Kubernetes. Určete, zda má tato aktivita očekávané chování nebo zda se aplikace nechová. Pomocí metrik a protokolů v Azure Monitor doložit vaše závěry. 

- [Pochopení integrace služby Azure Kubernetes Services pomocí Security Center](/azure/security-center/azure-kubernetes-service-integration)

- [Postup povolení Azure Security Center úrovně Standard](../security-center/security-center-get-started.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: Nainstalujte a povolte Microsoft Anti-malware pro Azure do virtuálních počítačů AKS (Azure Kubernetes Service) a na uzly sady škálování virtuálních počítačů. Zkontrolujte výstrahy v Security Center k nápravě.

- [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

- [Referenční příručka k výstrahám zabezpečení](../security-center/alerts-reference.md)

- [Výstrahy pro kontejnery – clustery služby Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: Služba Azure Kubernetes Service (AKS) používá projekt CoreDNS pro správu a řešení DNS clusteru.

Povolte protokolování dotazů DNS tak, že v coredns vlastní ConfigMap použijete dokumentovaný konfiguraci. 

- [Přizpůsobení CoreDNS pomocí služby Azure Kubernetes Service](coredns-custom.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: pomocí kubectl a klienta příkazového řádku ve službě Azure Kubernetes Service (AKS) můžete spravovat cluster Kubernetes a získat jeho protokoly z uzlu AKS pro účely řešení potíží. Kubectl je již nainstalován, pokud používáte Azure Cloud Shell. Pokud chcete nainstalovat kubectl místně, použijte rutinu Install-AzAksKubectl.

- [Rychlý Start – nasazení clusteru služby Azure Kubernetes pomocí prostředí PowerShell](kubernetes-walkthrough-powershell.md)

- [Získání protokolů kubelet z uzlů clusteru Azure Kubernetes Service (AKS)](kubelet-logs.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: Služba Azure KUBERNETES (AKS) sama o sobě neposkytuje řešení pro správu identit, které ukládá běžné uživatelské účty a hesla. Díky integraci služby Azure Active Directory (Azure AD) můžete uživatelům nebo skupinám udělit přístup k prostředkům Kubernetes v rámci oboru názvů nebo napříč clusterem. 

Provádění dotazů ad hoc pro zjišťování účtů, které jsou členy skupin pro správu AKS, pomocí modulu Azure AD PowerShell

Použijte Azure CLI pro operace, jako je získání přihlašovacích údajů pro přístup ke spravovanému clusteru Kubernetes, a pomůže vám tak pravidelně sjednotit přístup. Implementujte tento proces, abyste zachovali aktualizovaný inventář účtů služeb, což je jiný primární uživatel typu v AKS. Vynutila doporučení pro správu identit a přístupu Security Center.

- [Jak integrovat AKS s Azure AD](/azure/aks/azure-ad-integration)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Služba Azure KUBERNETES (AKS) neobsahuje koncept běžných výchozích hesel a neposkytuje řešení pro správu identit, ve kterém je možné ukládat běžné uživatelské účty a hesla. Díky integraci služby Azure Active Directory (Azure AD) můžete udělit přístup na základě role k prostředkům AKS v rámci oboru názvů nebo napříč clusterem. 

Provádění dotazů ad hoc pro zjišťování účtů, které jsou členy skupin pro správu AKS, pomocí modulu Azure AD PowerShell

- [Pochopení možností přístupu a identit pro AKS](concepts-identity.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: Integrujte ověřování uživatelů pro clustery Azure Kubernetes Service (AKS) s Azure Active Directory (Azure AD). Přihlaste se ke clusteru AKS pomocí ověřovacího tokenu Azure AD. Nakonfigurujte Kubernetes řízení přístupu na základě role (RBAC) pro přístup správce k informacím a oprávněním pro správu (kubeconfig) a k prostředkům, oborům názvů a prostředkům clusteru. 

Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu. Implementujte Security Center doporučení pro správu identit a přístupu.

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

- [Řízení přístupu k prostředkům clusteru](azure-ad-rbac.md)

- [Použití řízení přístupu na základě role v Azure](control-kubeconfig-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: použití jednotného přihlašování pro službu Azure Kubernetes Service (AKS) Azure Active Directory s integrovaným ověřováním služby Azure AD pro cluster AKS.

- [Jak zobrazit protokoly Kubernetes, události a metriky pod v reálném čase](../azure-monitor/insights/container-insights-livedata-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: integrace ověřování pro službu Azure Kubernetes Service (AKS) s Azure Active Directory (Azure AD). 

Povolte Azure AD Multi-Factor Authentication (MFA) a sledujte doporučení pro správu identit a přístupu Security Center.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md) 

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné**materiály: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s konfigurací Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení k zadaným clusterům Azure Kubernetes Service (AKS) a souvisejícím prostředkům.
- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) s ověřováním Azure AD – integrované ověřování pro službu Azure Kubernetes Service (AKS). Výstrahy mohou být generovány v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Security Center můžete monitorovat aktivitu identity a přístupu.

- [Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu Umožněte přístup ke clusterům Azure Kubernetes Service (AKS) jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. K tomu je potřeba integrované ověřování pro AKS s Azure Active Directory (Azure AD).

Omezte přístup k serveru rozhraní AKS API z omezené sady rozsahů IP adres, protože přijímá požadavky na provádění akcí v clusteru k vytváření prostředků nebo škálování počtu uzlů. 

- [Zabezpečený přístup k serveru rozhraní API pomocí rozsahů povolených IP adres ve službě Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro službu Azure KUBERNETES (AKS). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a zapisující a soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.

Použití předdefinovaných rolí AKS s řízením přístupu na základě role Azure (Azure RBAC) – přispěvatel a vlastník zásad prostředků pro operace přiřazení zásad do vašeho clusteru Kubernetes

- [Přehled služby Azure Policy](../governance/policy/overview.md)

- [Jak integrovat Azure AD s AKS](/azure/aks/azure-ad-integration) 

- [Integrace služby Azure AD spravované AKS](managed-aad.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) s ověřováním Azure AD – integrované ověřování pro službu Azure Kubernetes Service (AKS). Vyhledejte v protokolech Azure AD, které vám pomůžou zjistit zastaralé účty. 

Pomocí kontrol přístupu k identitám Azure můžete efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Opravte doporučení identity a přístupu z Security Center.

Uvědomte si role používané pro účely podpory nebo řešení potíží. Například akce clusteru podniknuté podporou Microsoftu (s souhlasem uživatele) se provádějí v rámci předdefinované role Kubernetes "Upravit" v názvu AKS-support-rolebinding. Podpora AKS je u této role povolená pro úpravu konfigurace clusteru a prostředků za účelem řešení potíží a diagnostikování problémů s clustery. Tato role ale nemůže měnit oprávnění ani vytvářet role ani vazby rolí. Přístup k této roli je povolený jenom v aktivních lístkech podpory s přístupem JIT (just-in-time).
 
- [Možnosti identit a přístupu pro Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

- [Postup sledování aktivity identity a přístupu uživatele v Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: Integrujte ověřování uživatelů pro službu Azure Kubernetes Service (AKS) s Azure Active Directory (Azure AD). Vytvořte nastavení diagnostiky pro Azure AD a odešlete protokoly auditu a přihlašování do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy (například při pokusu o přihlášení k deaktivovanému účtu) v pracovním prostoru Azure Log Analytics.
- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak vytvářet, zobrazovat a spravovat výstrahy protokolu pomocí Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: Integrujte ověřování uživatelů pro službu Azure Kubernetes Service (AKS) s Azure Active Directory (Azure AD). Pomocí funkce zjišťování rizik a Identity Protection pro Azure AD můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Ingestujte data do Azure Sentinel pro další šetření na základě obchodních potřeb.

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: neplatí pro službu Azure Kubernetes Service (AKS), protože není podporovaná Customer Lockbox.
- [Seznam podporovaných služeb Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: používejte značky v materiálech týkajících se nasazení služby Azure Kubernetes Service (AKS), které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Aktualizace značek pro spravované clustery](/rest/api/aks/managedclusters/updatetags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: logicky izolujte týmy a úlohy ve stejném clusteru se službou Azure Kubernetes Service (AKS), aby poskytovaly nejnižší počet oprávnění vymezených na prostředky, které každý tým vyžaduje. 

Použijte obor názvů v Kubernetes k vytvoření logické hranice izolace. Zvažte implementaci dalších funkcí Kubernetes pro izolaci a víceklientské architektury, jako je například plánování, sítě, ověřování/autorizace a kontejnery.

Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a produkční prostředí. Oddělte clustery AKS pomocí sítě tak, že je nasadíte do různých virtuálních sítí, které jsou opatřeny odpovídajícím způsobem.

- [Další informace o osvědčených postupech pro izolaci clusteru v AKS](operator-best-practices-cluster-isolation.md)

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Vysvětlení osvědčených postupů pro připojení k síti a zabezpečení v AKS](operator-best-practices-network.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: použití řešení třetí strany z Azure Marketplace na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a blokují takové přenosy při upozornění na odborníky na zabezpečení informací.

Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Seznam požadovaných portů, adres a názvů domén pro funkci AKS](limit-egress-traffic.md)

- [Postup konfigurace nastavení diagnostiky pro Azure Firewall](/azure/firewall/tutorial-diagnostics)

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Vytvořte kontroler příchozího přenosu HTTPS a použijte vlastní certifikáty TLS (případně Zašifrujte) pro nasazení služby Azure KUBERNETES (AKS). 

Ve výchozím nastavení je Kubernetes výstupní přenos dat zašifrovaný přes HTTPS/TLS. Projděte si potenciálně nešifrovaný provoz odchozího přenosu dat z vašich AKS instancí, abyste mohli další monitorování. To může zahrnovat přenos dat NTP, přenosy DNS, přenosy HTTP pro načítání aktualizací v některých případech. 

- [Vytvoření kontroleru příchozího přenosu HTTPS na AKS a použití vlastních certifikátů TLS](ingress-own-tls.md)

- [Postup vytvoření kontroleru příchozího přenosu HTTPS v AKS s využitím šifrování let](ingress-tls.md)

- [Seznam potenciálních portů a protokolů, které používá AKS](limit-egress-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.
Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. 

Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití autorizačního systému řízení přístupu na základě role Azure (Azure RBAC), který je založený na Azure Resource Manager k zajištění jemně odstupňované správy přístupu prostředků Azure.

Nakonfigurujte službu Azure Kubernetes Service (AKS), aby používala Azure Active Directory (Azure AD) pro ověřování uživatelů. Přihlaste se ke clusteru AKS pomocí ověřovacího tokenu Azure AD pomocí této konfigurace. 

Použití předdefinovaných rolí AKS s Azure RBAC – přispěvatelem a vlastníkem zásad prostředků pro operace přiřazení zásad do vašeho clusteru AKS

- [Jak řídit přístup k prostředkům clusteru pomocí Azure RBAC a identit Azure AD v AKS](azure-ad-rbac.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.
Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: dva primární typy úložiště poskytované pro svazky ve službě Azure Kubernetes Service (AKS) se zálohují na disky Azure nebo soubory Azure. Oba typy úložiště používají rozhraní SSE (Azure Storage Service Encryption), které zašifruje data v klidovém prostředí a zvyšuje zabezpečení. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem.

Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem je k dispozici pro šifrování operačního systému i datových disků v clusterech AKS pro další kontrolu nad šifrovacími klíči. Zákazníci, kteří mají odpovědnost za aktivity správy klíčů, jako je například zálohování a rotace klíčů. Disky se momentálně nedají šifrovat pomocí Azure Disk Encryption na úrovni uzlu AKS.

- [Osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes (AKS)](operator-best-practices-storage.md)

- [Přineste si vlastní klíče (BYOK) s disky Azure ve službě Azure Kubernetes Service (AKS).](azure-disk-customer-managed-keys.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor pro kontejnery monitorujte výkon úloh kontejneru nasazených na spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). 

Nakonfigurujte výstrahy pro proaktivní oznamování nebo vytváření protokolů, když využití procesoru a paměti na uzlech nebo kontejnerech překročí definované prahové hodnoty, nebo když v clusteru dojde ke změně stavu v infrastruktuře nebo souhrnu stavu uzlů. 

Pomocí protokolu aktivit Azure můžete monitorovat clustery AKS a související prostředky na nejvyšší úrovni. Integrací s Prometheus můžete zobrazit metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí dotazů pro vytváření vlastních výstrah, řídicích panelů a podrobných podrobných analýz.

- [Principy Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-overview.md)

- [Postup povolení Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-onboard.md)

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny**: pomocí Security Center můžete monitorovat Azure Container Registry včetně instancí služby Azure Kubernetes Service (AKS) pro ohrožení zabezpečení. Povolte sadu registrů kontejnerů v nástroji Security Center, aby bylo zajištěno, že Security Center je připravená na skenování imagí, které se připravují do registru.

Když se na řídicím panelu Security Center objeví problémy po Security Center zkontroluje Image pomocí Qualys. Funkce svazku Container Registry poskytuje hlubší přehled o chybách zabezpečení imagí používaných v registrech založených na Azure Resource Manager. 

Použijte Security Center pro užitečná doporučení pro každou chybu zabezpečení. Tato doporučení zahrnují klasifikaci závažnosti a pokyny k nápravě. 

- [Osvědčené postupy pro správu a zabezpečení imagí kontejneru ve službě Azure Kubernetes (AKS)](/azure/security-center/azure-container-registry-integration)

- [Principy osvědčených postupů pro správu a zabezpečení imagí kontejneru v AKS](operator-best-practices-container-image-management.md)

- [Vysvětlení integrace registru kontejneru s Azure Security Center](/azure/security-center/azure-container-registry-integration)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: aktualizace zabezpečení se automaticky aplikují na uzly Linux, aby se chránily clustery Azure Kubernetes Service (AKS) zákazníka. Tyto aktualizace zahrnují opravy zabezpečení operačního systému nebo aktualizace jádra. 

Všimněte si, že proces udržování uzlů Windows serveru v aktuálním stavu se liší od uzlů, na kterých běží Linux, protože uzly Windows serveru neobdrží každodenní aktualizace. Místo toho zákazníci potřebují provést upgrade v fondech uzlů Windows serveru ve svých clusterech AKS, které nasazují nové uzly s nejnovější imagí a opravami základního serveru základní okno pomocí ovládacích panelů Azure nebo rozhraní příkazového řádku Azure CLI. Tyto aktualizace obsahují vylepšení zabezpečení nebo funkcí AKS.

- [Pochopení způsobu použití aktualizací na uzlech clusteru AKS se systémem Linux](node-updates-kured.md)

- [Postup upgradu fondu uzlů AKS pro clustery AKS, které používají uzly Windows serveru](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Upgrade imagí uzlu služby Azure Kubernetes Service (AKS)](node-image-upgrade.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Pokyny**: implementace ručního procesu k zajištění, aby aplikace třetích stran uzlu clusteru služby Azure KUBERNETES (AKS) zůstaly opravené po dobu životnosti clusteru. To může vyžadovat povolení automatických aktualizací, monitorování uzlů nebo provádění pravidelných restartování.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: exportovat Security Center výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. 

Pomocí rutiny PowerShellu Get-AzSecurityTask můžete automatizovat načítání úloh zabezpečení, které Security Center doporučuje, abyste mohli posílit své výsledky kontroly ohrožení zabezpečení stav a nápravy.

- [Jak pomocí PowerShellu zobrazit chyby zjištěné v Azure Security Center](https://docs.microsoft.com/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné**materiály: použití hodnocení závažnosti poskytovaného Security Center k určení priorit nápravy chyb zabezpečení. 

Pokud používáte integrovaný nástroj pro posouzení ohrožení zabezpečení (například Qualys nebo Rapid7, který nabízí Azure), použijte běžný systém pro vyhodnocování chyb (CVSS) (nebo jiné systémy bodování poskytované skenovacím nástrojem).

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť atd.). Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky založené na Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure s metadaty k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. 

Při vytváření fondu uzlů AKS (Azure Kubernetes Service) použijte značky, popisky nebo značky. Všechny uzly v tomto fondu uzlů budou také dědit tuto značku, popisek nebo značku.

Značky, štítky nebo značky lze použít k pravidelnému sjednocení inventáře a zajištění včasného odstranění neautorizovaných prostředků z předplatných.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření značek a uživatelských značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Spravované clustery – značky aktualizace](/rest/api/aks/managedclusters/updatetags)

- [Určení značky, značky nebo značky pro fond uzlů](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Definujte seznam schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky na základě obchodních potřeb organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
-   Žádné povolené typy prostředků 

-   Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Zajistěte, aby všechny prostředky Azure přítomné v tomto prostředí byly schválené na základě obchodních požadavků organizace.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: použití funkcí Azure Automation Change Tracking a inventáře k nalezení softwaru, který je nainstalovaný ve vašem prostředí. 

Shromažďování a zobrazování inventáře softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows na vašich počítačích a monitorování neschválených softwarových aplikací. 

Sledujte konfigurace vašich počítačů, které vám pomůžou odhalit provozní problémy v celém prostředí a lépe pochopit stav vašich počítačů.

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: použití funkcí Azure Automation Change Tracking a inventáře k nalezení softwaru, který je nainstalovaný ve vašem prostředí. 

Shromažďování a zobrazování inventáře softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows na vašich počítačích a monitorování neschválených softwarových aplikací. 

Sledujte konfigurace vašich počítačů, které vám pomůžou odhalit provozní problémy v celém prostředí a lépe pochopit stav vašich počítačů.

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

- [Jak používat monitorování integrity souborů](../security-center/security-center-file-integrity-monitoring.md)

- [Pochopení Change Tracking Azure](../automation/change-tracking.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: použití funkcí Azure Automation Change Tracking a inventáře k nalezení softwaru, který je nainstalovaný ve vašem prostředí. 

Shromažďování a zobrazování inventáře softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows na vašich počítačích a monitorování neschválených softwarových aplikací. 

Sledujte konfigurace vašich počítačů, které vám pomůžou odhalit provozní problémy v celém prostředí a lépe pochopit stav vašich počítačů.

Povolte adaptivní analýzu aplikací v Security Center pro aplikace, které existují ve vašem prostředí.

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

 
Jak používat Azure Security Center adaptivní aplikace
- [Ovládací prvky](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Pokyny**: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných pomocí integrovaných definic zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.
- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Pokyny**: Služba Azure Kubernetes Service (AKS) sama o sobě neposkytuje řešení pro správu identit, ve kterém jsou uložené běžné uživatelské účty a hesla. Místo toho použijte jako integrované řešení identit pro clustery AKS Azure Active Directory (Azure AD). 

Udělte uživatelům nebo skupinám přístup k prostředkům Kubernetes v rámci oboru názvů nebo napříč clusterem pomocí integrace služby Azure AD. 

Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy AKS skupin pro správu. pravidelně odsouhlaste přístup. Použijte rozhraní příkazového řádku Azure CLI pro operace, jako je získání přihlašovacích údajů pro přístup spravovaného clusteru Kubernetes. Implementujte Security Center doporučení pro správu identit a přístupu.

- [Správa AKS pomocí Azure CLI](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest)

- [Porozumění integraci AKS a Azure AD](concepts-identity.md)

- [Jak integrovat AKS s Azure AD](/azure/aks/azure-ad-integration)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Pokyny**: použití funkcí služby Azure Kubernetes Service (AKS) k logické izolaci týmů a úloh ve stejném clusteru pro nejnižší počet oprávnění vymezených na prostředky, které každý tým vyžaduje. 

Implementujte obor názvů v Kubernetes k vytvoření logické hranice izolace. Pomocí aliasů Azure Policy v oboru názvů Microsoft. ContainerService můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Azure Kubernetes Service (AKS). 

Kontrola a implementace dalších funkcí Kubernetes a důležitých informací pro izolaci a víceklientské architektury zahrnuje následující oblasti: plánování, sítě, ověřování, autorizace a kontejnery. Pro vývoj, testování a produkci použijte také samostatné odběry nebo skupiny pro správu. Oddělte clustery AKS s virtuálními sítěmi, podsítěmi, které jsou označené správně, a zabezpečené pomocí firewallu webových aplikací (WAF).

- [Další informace o osvědčených postupech pro izolaci clusteru v AKS](operator-best-practices-cluster-isolation.md)

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vysvětlení osvědčených postupů pro připojení k síti a zabezpečení v AKS](operator-best-practices-network.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. ContainerService můžete vytvořit vlastní zásady pro auditování nebo prosazování konfigurace instancí Azure Kubernetes Service (AKS). Použijte předdefinované definice Azure Policy.

Příklady předdefinovaných definic zásad pro AKS zahrnují:

• Vynutil příchozí přenos HTTPS v clusteru Kubernetes

• Pro služby Kubernetes by měly být definované rozsahy povolených IP adres.

V Kubernetes službách by se měla používat i na základě Access Control (RBAC).

• Zajistěte, aby v clusteru Kubernetes byly jenom povolené image kontejnerů.

Exportujte šablonu konfigurace AKS v JavaScript Object Notation (JSON) s Azure Resource Manager. Pravidelně ho kontrolujte, abyste měli jistotu, že tyto konfigurace splňují požadavky na zabezpečení vaší organizace. Použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro vaše prostředky Azure. 

- [Jak konfigurovat a spravovat zásady zabezpečení AKS pod](use-pod-security-policies.md)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: clustery clusterů Azure KUBERNETES (AKS) se nasazují na virtuální počítače hostitele s OPTIMALIZOVANÝM operačním systémem pro zabezpečení. Hostitelský operační systém má další kroky pro posílení zabezpečení, které jsou v něm obsažené, aby se snížila plocha oblasti útoku a aby bylo možné zabezpečené nasazení kontejnerů. 

Azure používá každodenní opravy (včetně oprav zabezpečení) k AKS hostitelům virtuálních počítačů s některými opravami, které vyžadují restart. Zákazníci zodpovídají za plánování restartování hostitele virtuálních počítačů AKS podle potřeby. 

- [Posílení zabezpečení pro hostitelský operační systém uzlu agenta AKS](security-hardened-vm-host-image.md)

- [Principy posílení zabezpečení v hostitelích virtuálních počítačů s AKS](security-hardened-vm-host-image.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: Zabezpečte svůj cluster služby Azure KUBERNETES (AKS) pomocí zásad zabezpečení pod.  Omezte počet lusků, které je možné naplánovat, aby se zlepšilo zabezpečení clusteru. 

Lusky, které požadují nepovolené prostředky, nejde spustit v clusteru AKS. 

Použijte taky Azure Policy [odepřít] a [nasazení, pokud neexistují] důsledky pro vymáhání zabezpečených nastavení pro prostředky Azure související s nasazeními AKS (například virtuální sítě, podsítě, brány firewall Azure, účty úložiště atd.). 

Vytvářejte vlastní definice Azure Policy pomocí aliasů z následujících oborů názvů: 

• Microsoft. ContainerService

• Microsoft. Network

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Pokyny**: clustery Azure Kubernetes Service (AKS) se nasazují na virtuální počítače hostitele s OPTIMALIZOVANÝM operačním systémem pro zabezpečení. Hostitelský operační systém má další kroky pro posílení zabezpečení, které jsou v něm obsažené, aby se snížila plocha oblasti útoku a aby bylo možné zabezpečené nasazení kontejnerů. 

Přečtěte si seznam ovládacích prvků centra pro Internet Security (CIS), které jsou součástí hostitelského operačního systému.  

- [Posílení zabezpečení pro hostitelský operační systém uzlu agenta AKS](security-hardened-vm-host-image.md)

- [Principy konfigurace stavu clusterů AKS](concepts-clusters-workloads.md#control-plane)

- [Principy posílení zabezpečení v hostitelích virtuálních počítačů s AKS](security-hardened-vm-host-image.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: použijte Azure Repos k bezpečnému ukládání a správě konfigurací, pokud používáte vlastní definice Azure Policy. Exportujte šablonu konfigurace Azure Kubernetes Service (AKS) v JavaScript Object Notation (JSON) s Azure Resource Manager. Pravidelně ji kontrolujte, abyste měli jistotu, že konfigurace splňují požadavky na zabezpečení vaší organizace. 

Implementujte řešení třetích stran, například Terraformu, k vytvoření konfiguračního souboru, který deklaruje prostředky pro cluster Kubernetes. Nasazení AKS můžete posílit tím, že implementujete osvědčené postupy zabezpečení a uložíte konfiguraci jako kód na bezpečném místě.

- [Definování clusteru Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

Posílení zabezpečení pro hostitelský operační systém uzlu agenta AKS

security-hardened-vm-host-image.md

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: neplatí pro službu Azure KUBERNETES (AKS). AKS poskytuje standardně optimalizovaný hostitelský operační systém (OS). K dispozici není žádná aktuální možnost pro výběr alternativního nebo vlastního operačního systému.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných, pomocí integrovaných definic zásad a aliasů Azure Policy v oboru názvů Microsoft. ContainerService. 

Vytvořte vlastní zásady pro audit a vynuťte konfiguraci systému. Vývoj procesu a kanálu pro správu výjimek zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Pokyny**: clustery Azure Kubernetes Service (AKS) se nasazují na virtuální počítače hostitele s OPTIMALIZOVANÝM operačním systémem pro zabezpečení. Hostitelský operační systém má další kroky pro posílení zabezpečení, které jsou v něm obsažené, aby se snížila plocha oblasti útoku a aby bylo možné zabezpečené nasazení kontejnerů. 

Přečtěte si seznam ovládacích prvků centra pro Internet Security (CIS), které jsou součástí AKS hostitelů.  

- [Posílení zabezpečení pro hostitelský operační systém uzlu agenta AKS](security-hardened-vm-host-image.md)

- [Principy posílení zabezpečení v hostitelích virtuálních počítačů s AKS](security-hardened-vm-host-image.md)

- [Principy konfigurace stavu clusterů AKS](concepts-clusters-workloads.md#control-plane)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné**materiály: použijte Security Center k provádění kontrol směrného plánu pro prostředky související s nasazeními služby Azure KUBERNETES (AKS). Příklady prostředků zahrnují, ale nejsou omezené na samotný cluster AKS, virtuální síť, ve které byl cluster AKS nasazený, účet Azure Storage, který se používá ke sledování stavu Terraformu, nebo Azure Key Vault instance používané pro šifrovací klíče pro operační systém a datové disky clusteru AKS.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: použití Security Centerch doporučení pro kontejnery v části COMPUTE &amp; Apps k provádění kontrol standardních hodnot pro clustery služby Azure Kubernetes (AKS). Když se zjistí problémy s konfigurací nebo chyby zabezpečení, dostanete se na řídicím panelu Security Center upozornění. To vyžaduje povolení volitelné sady kontejnerů registrů, které umožní Security Center skenování image.  

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](/azure/security-center/security-center-container-recommendations)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: integrujte Azure Key Vault s clusterem AKS (Azure Kubernetes Service) pomocí jednotky FlexVolume. Azure Key Vault můžete použít k ukládání a pravidelnému střídání tajných kódů, jako jsou přihlašovací údaje, klíče účtu úložiště nebo certifikáty. Ovladač FlexVolume umožňuje, aby cluster AKS nativně načítal přihlašovací údaje od Key Vault a bezpečně poskytoval pouze žadatelům pod. Pomocí spravované identity pod ní můžete požádat o přístup k Key Vault a načíst požadované přihlašovací údaje prostřednictvím ovladače FlexVolume. Ujistěte se, že je povolené Key Vault obnovitelné odstranění. 

Omezte vystavení přihlašovacích údajů tím, že v kódu aplikace nedefinujete přihlašovací údaje. 

Vyhněte se použití pevných nebo sdílených přihlašovacích údajů. 

- [Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes (AKS)](concepts-security.md)

- [Použití Key Vault s clusterem AKS](developer-best-practices-pod-security.md#limit-credential-exposure)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: v kódu aplikace nedefinujte přihlašovací údaje jako osvědčený postup zabezpečení. Využijte spravované identity prostředků Azure, aby se mohl sám ověřit na jakékoli službě v Azure, která to podporuje, včetně Azure Key Vault. Pod je přiřazena identita Azure pro ověření pro Azure Active Directory (Azure AD) a příjem digitálního tokenu, který se dá předkládat ostatním službám Azure, které kontrolují, jestli má oprávnění k přístupu ke službě a provádění požadovaných akcí. 

Upozorňujeme, že pod spravovanými identitami jsou určené jenom pro systémy Linux a image kontejnerů. Zřídí Azure Key Vault ukládání a načítání digitálních klíčů a přihlašovacích údajů. Klíče, jako jsou ty, které slouží k šifrování disků s operačním systémem, můžou AKS data clusteru ukládat v Azure Key Vault.

Instanční objekty se dají používat taky v clusterech AKS. Clustery s použitím instančních objektů se ale můžou dorazit na stav, ve kterém se musí obnovit instanční objekt, aby se cluster mohl udržovat v provozu. Správa instančních objektů přináší složitost, což je důvod, proč je místo toho snazší použít spravované identity. Stejné požadavky oprávnění platí pro instanční objekty i spravované identity.

- [Pochopení spravovaných identit a Key Vault pomocí služby Azure Kubernetes Service (AKS)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Identita Azure Active Directory pod](https://github.com/Azure/aad-pod-identity)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Kontrola přihlašovacích údajů také podporuje přesun zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault s doporučeními.

Omezte vystavení přihlašovacích údajů tím, že v kódu aplikace nedefinujete přihlašovací údaje. a nepoužívejte sdílené přihlašovací údaje. Azure Key Vault by se měly používat k ukládání a načítání digitálních klíčů a přihlašovacích údajů. Pomocí spravovaných identit pro prostředky Azure umožněte, aby váš odkaz vyžádal přístup k dalším prostředkům. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Osvědčené postupy pro vývojáře pod zabezpečením](developer-best-practices-pod-security.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Doprovodné**materiály: AKS spravuje životní cyklus a operace uzlů agentů vaším jménem. Změna prostředků IaaS přidružených k uzlům agentů se nepodporuje. Pro uzly se systémem Linux ale můžete použít sady démonů k instalaci vlastního softwaru, jako je antimalwarové řešení.

- [Referenční příručka k výstrahám zabezpečení](../security-center/alerts-reference.md)

- [Výstrahy pro kontejnery – clustery služby Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS sdílené zodpovědnosti a sady démonů](support-policies.md#shared-responsibility)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné**materiály: předběžná kontrola všech souborů odeslaných do vašich AKS prostředků Pokud používáte účet Azure Storage jako úložiště dat nebo ke sledování stavu Terraformu pro cluster AKS, použijte detekci hrozeb Security Center pro datové služby. 

- [Vysvětlení detekce hrozeb Azure Security Center pro datové služby](/azure/security-center/security-center-alerts-data-services)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované

**Doprovodné**materiály: AKS spravuje životní cyklus a operace uzlů agentů vaším jménem. Změna prostředků IaaS přidružených k uzlům agentů se nepodporuje. Pro uzly se systémem Linux ale můžete použít sady démonů k instalaci vlastního softwaru, jako je antimalwarové řešení.

- [Referenční příručka k výstrahám zabezpečení](../security-center/alerts-reference.md)

- [Výstrahy pro kontejnery – clustery služby Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS sdílené zodpovědnosti a sady démonů](support-policies.md#shared-responsibility)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Zálohujte svá data pomocí vhodného nástroje pro typ úložiště, jako je například Velero, který může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. Pravidelně Ověřte integritu a zabezpečení těchto záloh. 

Před zálohováním odeberte stav z vašich aplikací. V případech, kdy to nejde udělat, zálohujte data z trvalých svazků a pravidelně testujte operace obnovení, abyste ověřili integritu dat a požadované procesy.

- [Osvědčené postupy pro úložiště a zálohování v AKS](operator-best-practices-storage.md)

- [Osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii v AKS](operator-best-practices-multi-region.md)

- [Pochopení Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Nastavení Velero v Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: Zálohujte svá data pomocí vhodného nástroje pro typ úložiště, jako je například Velero, který může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. 

Příkazy PowerShellu umožňují pravidelné automatizované zálohování Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů. 

Například:

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup – AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Postup zálohování certifikátů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Postup zálohování Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Postup zálohování Key Vault tajných kódů](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Postup povolení Azure Backup](/azure/backup)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné**materiály: pravidelně provádějte obnovování dat v rámci zálohování Velero. V případě potřeby testujte obnovení do izolované virtuální sítě.

Pomocí příkazů PowerShellu pravidelně provádějte obnovení dat Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů. 

Například:

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Postup obnovení certifikátů Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Postup obnovení klíčů Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Postup obnovení Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Postup obnovení tajných kódů Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Zálohujte svá data pomocí vhodného nástroje pro typ úložiště, jako je například Velero, který může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. 

Povolit obnovitelné odstranění v Key Vault k ochraně klíčů před náhodným nebo škodlivým odstraněním, pokud se Azure Key Vault používá pro nasazení služby Azure Kubernetes Service (AKS).

- [Principy šifrování služby Azure Storage](../storage/common/storage-service-encryption.md)

- [Jak povolit obnovitelné odstranění v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Určete, které výstrahy je třeba nejprve prozkoumat s Security Center přiřazení závažnosti k výstrahám. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.
Jasně označte předplatná (například produkci, neprodukční) a vytvořte názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů. 
- [Plány a možnosti](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: exportujte výstrahy Security Center a doporučení pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Můžete také zvolit datový konektor Security Center pro streamování výstrah do Azure Sentinel podle obchodních požadavků organizace.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
