---
title: Architektura agenta Kubernetes povoleného ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek poskytuje přehled architektury agentů Kubernetes s povoleným ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561467"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architektura agenta Kubernetes povoleného ARC Azure

[Kubernetes](https://kubernetes.io/) se dají použít k nasazení kontejnerových úloh v hybridním prostředí a ve více cloudových prostředích konzistentním způsobem. Kubernetes s povoleným obloukem Azure je možné použít jako centralizovanou rovinu řízení pro konzistentní správu zásad, zásad správného řízení a zabezpečení v těchto prostředích heterogenní. Tento článek obsahuje:

* Přehled architektury připojení clusteru ke službě Azure ARC.
* Vzor připojení následovaný agenty.
* Popis dat vyměňovaných mezi prostředím clusteru a Azure.

## <a name="deploy-agents-to-your-cluster"></a>Nasazení agentů do clusteru

Většina Prem Datacenter vynutila striktní Síťová pravidla, která zabraňují příchozí komunikaci v bráně firewall používané na hranici sítě. Kubernetes s povoleným nastavením ARC Azure pracuje s těmito omezeními, a to povolením koncových bodů odchozího přenosu pro odchozí komunikaci a nevyžadujících příchozí porty v bráně firewall. Agenti Kubernetes s povolenou službou Azure ARC iniciují odchozí připojení.

![Přehled architektury](./media/architectural-overview.png)

Připojte cluster ke ARC Azure pomocí následujících kroků:

1. Vytvořte cluster Kubernetes podle vaší volby infrastruktury (VMware vSphere, Amazon Web Services, Google Cloud Platform atd.). 

    > [!NOTE]
    > Zákazníci se musí k vytvoření a správě životního cyklu clusteru Kubernetes vytvořit a spravovat, protože Kubernetes s podporou ARC Azure momentálně podporuje připojení existujících clusterů Kubernetes ke službě Azure ARC.  

1. Inicializujte registraci Azure ARC pro váš cluster pomocí Azure CLI.
    * Azure CLI používá Helm k nasazení grafu Helm agenta v clusteru.
    * Uzly clusteru inicializují odchozí komunikaci s [Microsoft Container Registry](https://github.com/microsoft/containerregistry) a nažádají image potřebné k vytvoření následujících agentů v `azure-arc` oboru názvů:

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Kubernetes s povoleným ARC Azure aktuálně podporuje jenom [identity přiřazené systémem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator provede první odchozí komunikaci potřebnou k načtení certifikátu MSI (Managed Service identity) používaného jinými agenty pro komunikaci s Azure. |
        | `deployment.apps/config-agent` | Sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru a aktualizuje stav dodržování předpisů. |
        | `deployment.apps/controller-manager` | Operátor operátorů, který orchestruje interakce mezi komponentami ARC Azure |    
        | `deployment.apps/metrics-agent` | Shromažďuje metriky jiných agentů ARC, aby se zajistilo, že tito agenti vykazují optimální výkon. |
        | `deployment.apps/cluster-metadata-operator` | Shromažďuje metadata clusteru – verze clusteru, počet uzlů a verzi agenta Azure ARC. |
        | `deployment.apps/resource-sync-agent` | Synchronizuje výše uvedená metadata clusteru do Azure. |
        | `deployment.apps/flux-logs-agent` | Shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu. |
    
1. Jakmile se ve stavu povolí všechna Kubernetes agenta Azure `Running` , ověřte, že je váš cluster připojený ke službě Azure ARC. Měli byste vidět:
    * Prostředek Kubernetes s povoleným ARC Azure v [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tento prostředek se sleduje v Azure jako projekce clusteru Kubernetes spravovaného zákazníkem, nikoli skutečného samotného clusteru Kubernetes.
    * Metadata clusteru, jako je verze Kubernetes, verze agenta a počet uzlů, se zobrazí v prostředku Kubernetes s podporou Azure ARC jako metadata.

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
| Požadovaný stav konfigurace – adresa URL úložiště Git, parametry operátora toku, privátní klíč, známý obsah hostitele, uživatelské jméno HTTPS, token/heslo | Konfigurace | Agent se vyžádá z Azure |
| Stav instalace operátoru toku | Konfigurace | Agent se přenáší do Azure. |
| Azure Policy přiřazení vyžadující vynucení serveru gatekeeper v rámci clusteru | Azure Policy | Agent se vyžádá z Azure |
| Stav auditování a dodržování předpisů u vynucení zásad clusteru | Azure Policy | Agent se přenáší do Azure. |
| Metriky a protokoly pro úlohy zákazníků | Azure Monitor | Agent se přenáší do Log Analytics prostředku pracovního prostoru v tenantovi a předplatném zákazníka. |

## <a name="connectivity-status"></a>Stav připojení

| Status | Popis |
| ------ | ----------- |
| Připojovaného | V Azure Resource Manager byl vytvořen prostředek Kubernetes s podporou ARC Azure, ale služba zatím neobdržela prezenční signál agenta. |
| Připojeno | Služba Kubernetes s podporou ARC Azure obdržela během posledních 15 minut signál agenta. |
| Offline | Prostředek Kubernetes s povolenou službou Azure ARC byl dříve připojen, ale služba neobdržela žádný prezenční signál agenta po dobu 15 minut. |
| Platnost vypršela | Certifikát služby MSI (Managed Service identity) má po vystavení vypršení časového intervalu 90 dní. Po vypršení platnosti certifikátu se prostředek posuzuje `Expired` a všechny funkce, jako je konfigurace, monitorování a zásady, přestanou v tomto clusteru fungovat. Další informace o tom, jak vyřešíte vypršet platnost prostředků Kubernetes s povoleným obloukem Azure ARC, najdete [tady](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) . |

## <a name="understand-connectivity-modes"></a>Principy režimů připojení

| Režim připojení | Popis |
| ----------------- | ----------- |
| Plně připojeno | Agenti se vždycky můžou spojit s Azure. Prostředí je ideální v tomto případě, protože dochází ke trochu zpoždění při šíření konfigurací (pro GitOps), vynucování zásad (v Azure Policy a gatekeeper) a shromažďování metrik a protokolů úloh (v Azure Monitor). |
| Částečně připojeno | Certifikát MSI vydaný v `clusteridentityoperator` je platný po dobu 90 dnů, než vyprší platnost certifikátu. Po vypršení platnosti certifikátu přestane prostředek Kubernetes s povoleným ARC Azure fungovat. Odstraňte a znovu vytvořte prostředky a agenty Kubernetes s povoleným ARC Azure, abyste získali všechny funkce ARC pro práci na clusteru. Během 90 dnů se uživatelům doporučuje připojit cluster nejméně jednou za 30 dní. |
| Odpojeno | Kubernetes clustery v odpojených prostředích bez přístupu k Azure nejsou v současné době Kubernetes s podporou ARC Azure podporované. Pokud vás tato funkce bude zajímavá, pošlete nám svůj názor na fórum služby UserVoice pro [Azure ARC](https://feedback.azure.com/forums/925690-azure-arc)a můžete na něj hlasovat.

## <a name="next-steps"></a>Další kroky

* [Připojit cluster ke službě Azure ARC](./connect-cluster.md)
* [Koncepční přehled konfigurací](./conceptual-configurations.md)