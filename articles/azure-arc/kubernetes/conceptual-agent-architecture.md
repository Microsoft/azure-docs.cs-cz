---
title: Architektura agenta Kubernetes povoleného ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek poskytuje přehled architektury agentů Kubernetes s povoleným ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: ec95efdfef871777e7f53617b057529e301739dd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953064"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architektura agenta Kubernetes povoleného ARC Azure

[Kubernetes](https://kubernetes.io/) může na svém vlastním nasazení v prostředí s hybridními a víceúčelovými cloudy nasazovat i kontejnerové úlohy. Kubernetes s podporou Azure ARC ale funguje jako centralizovaná, konzistentní Řídicí rovina, která spravuje zásady, zásady správného řízení a zabezpečení napříč heterogenní prostředími. Tento článek obsahuje:

* Přehled architektury připojení clusteru ke službě Azure ARC.
* Vzor připojení následovaný agenty.
* Popis dat vyměňovaných mezi prostředím clusteru a Azure.

## <a name="deploy-agents-to-your-cluster"></a>Nasazení agentů do clusteru

Většina Prem Datacenter vynutila striktní Síťová pravidla, která zabraňují příchozí komunikaci v bráně firewall hraniční sítě. Kubernetes s povoleným nastavením ARC Azure pracuje s těmito omezeními, protože nevyžaduje příchozí porty v bráně firewall a povoluje koncové body pro odchozí komunikaci jenom pro selektivní výstup. Agenti Kubernetes s povolenou službou Azure ARC zahájí tuto odchozí komunikaci. 

![Přehled architektury](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Připojit cluster ke službě Azure ARC

1. Vytvořte cluster Kubernetes podle vaší volby infrastruktury (VMware vSphere, Amazon Web Services, Google Cloud Platform atd.). 

    > [!NOTE]
    > Vzhledem k tomu, že Kubernetes s podporou Azure ARC v současné době podporuje pouze připojení stávajících clusterů Kubernetes ke službě Azure ARC, zákazníci musí vytvořit a spravovat životní cyklus Kubernetes clusteru.  

1. Spusťte registraci ARC Azure pro váš cluster pomocí Azure CLI.
    * Azure CLI používá Helm k nasazení grafu Helm agenta v clusteru.
    * Uzly clusteru inicializují odchozí komunikaci s [Microsoft Container Registry](https://github.com/microsoft/containerregistry) a nažádají image potřebné k vytvoření následujících agentů v `azure-arc` oboru názvů:

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Kubernetes s povoleným ARC Azure aktuálně podporuje jenom [identity přiřazené systémem](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` Inicializuje první odchozí komunikaci. Tato první komunikace načte certifikát Identita spravované služby (MSI), který používají jiní agenti pro komunikaci s Azure. |
        | `deployment.apps/config-agent` | Sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru. Aktualizuje stav dodržování předpisů. |
        | `deployment.apps/controller-manager` | Operátor obsluhy, který orchestruje interakce mezi komponentami ARC Azure. |    
        | `deployment.apps/metrics-agent` | Shromažďuje metriky jiných agentů ARC k ověření optimálního výkonu. |
        | `deployment.apps/cluster-metadata-operator` | Shromažďuje metadata clusteru včetně verze clusteru, počtu uzlů a verze agenta Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Synchronizuje výše uvedená metadata clusteru do Azure. |
        | `deployment.apps/flux-logs-agent` | Shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu. |
    
1. Jakmile budou všechna okna s povoleným Kubernetes agentem Azure v `Running` stavech, ověřte, že je váš cluster připojený ke službě Azure ARC. Měli byste vidět:
    * Prostředek Kubernetes s povoleným ARC Azure v [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure tento prostředek sleduje jako projekci clusteru Kubernetes spravovaného zákazníkem, nikoli vlastního clusteru Kubernetes.
    * Metadata clusteru (jako verze Kubernetes, verze agenta a počet uzlů) se zobrazí v prostředku Kubernetes s povoleným jako metadaty.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Výměna dat mezi prostředím clusteru a Azure

| Datový typ | Scenario | Režim komunikace |
| --------- | -------- | ------------------ |
| Verze clusteru Kubernetes | Metadata clusteru | Agent se přenáší do Azure. |
| Počet uzlů v clusteru | Metadata clusteru | Agent se přenáší do Azure. |
| Verze agenta | Metadata clusteru | Agent se přenáší do Azure. |
| Typ distribuce Kubernetes | Metadata clusteru | Rozhraní příkazového řádku Azure CLI se doručí do Azure |
| Typ infrastruktury (AWS/GCP/vSphere/...) | Metadata clusteru | Rozhraní příkazového řádku Azure CLI se doručí do Azure |
| vCPU počet uzlů v clusteru | Fakturace | Rozhraní příkazového řádku Azure CLI se doručí do Azure |
| Prezenční signál agenta | Resource Health | Agent se přenáší do Azure. |
| Spotřeba prostředků (paměť/procesor) od agentů | Diagnostika a podpora | Agent se přenáší do Azure. |
| Protokoly všech kontejnerů agentů | Diagnostika a podpora | Agent se přenáší do Azure. |
| Dostupnost upgradu agenta | Upgrade agenta | Agent se vyžádá z Azure |
| Požadovaný stav konfigurace: adresa URL úložiště Git, parametry operátoru toku, privátní klíč, známý obsah hostitele, uživatelské jméno, token nebo heslo HTTPS | Konfigurace | Agent se vyžádá z Azure |
| Stav instalace operátoru toku | Konfigurace | Agent se přenáší do Azure. |
| Azure Policy přiřazení vyžadující vynucení serveru gatekeeper v rámci clusteru | Azure Policy | Agent se vyžádá z Azure |
| Stav auditování a dodržování předpisů u vynucení zásad clusteru | Azure Policy | Agent se přenáší do Azure. |
| Metriky a protokoly pro úlohy zákazníků | Azure Monitor | Agent se přenáší do Log Analytics prostředku pracovního prostoru v tenantovi a předplatném zákazníka. |

## <a name="connectivity-status"></a>Stav připojení

| Status | Popis |
| ------ | ----------- |
| Připojovaného | Prostředek Kubernetes s povoleným ARC Azure se vytvoří v Azure Resource Manager, ale služba zatím neobdržela prezenční signál agenta. |
| Připojeno | Služba Kubernetes s podporou ARC Azure obdržela během posledních 15 minut signál agenta. |
| Offline | Prostředek Kubernetes s povolenou službou Azure ARC byl dříve připojen, ale služba neobdržela žádný prezenční signál agenta po dobu 15 minut. |
| Platnost vypršela | Certifikát MSI má po vystavení vypršení časového intervalu 90 dní. Po vypršení platnosti certifikátu se prostředek posuzuje `Expired` a všechny funkce, jako je konfigurace, monitorování a zásady, přestanou na tomto clusteru fungovat. Další informace o tom, jak vyřešíte vypršet platnost prostředků Kubernetes s podporou Azure ARC, najdete [v článku Nejčastější dotazy](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Principy režimů připojení

| Režim připojení | Popis |
| ----------------- | ----------- |
| Plně připojeno | Agenti můžou konzistentně komunikovat s Azure s malým zpožděním při rozšiřování konfigurací GitOps, vynucování zásad Azure Policy a gatekeeper a shromažďování metrik úloh a protokolů v Azure Monitor. |
| Částečně připojeno | Certifikát MSI vydaný v `clusteridentityoperator` je platný až 90 dní před vypršením platnosti certifikátu. Po vypršení platnosti přestane prostředek Kubernetes s povoleným ARC Azure fungovat. Pokud chcete znovu aktivovat všechny funkce ARC Azure v clusteru, odstraňte a znovu vytvořte prostředky a agenty s povoleným Kubernetes ARC Azure. Během 90 dnů připojte cluster alespoň jednou za 30 dní. |
| Odpojeno | Clustery Kubernetes v odpojených prostředích nemůžou získat přístup k Azure, který je v současné době povolený Kubernetes ARC Azure. Pokud vás tato funkce bude zajímavá, pošlete nám svůj názor na fórum služby UserVoice pro [Azure ARC](https://feedback.azure.com/forums/925690-azure-arc)a můžete na něj hlasovat.

## <a name="next-steps"></a>Další kroky

* Projděte si náš rychlý Start, který [připojí cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* Přečtěte si další informace o vytváření připojení mezi clusterem a úložištěm Git jako [prostředku konfigurace s povoleným Kubernetesm Azure ARC](./conceptual-configurations.md).