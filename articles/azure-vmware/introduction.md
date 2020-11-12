---
title: Úvod
description: Seznamte se s funkcemi a výhodami řešení Azure VMware pro nasazení a správu úloh na bázi VMware v Azure.
ms.topic: overview
ms.date: 11/11/2020
ms.openlocfilehash: 57edfc5786dfc95070b66eb9c8e2e038bafdcd35
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534649"
---
# <a name="what-is-azure-vmware-solution"></a>Co je Azure VMware Solution?

Řešení Azure VMware poskytuje privátní cloudy, které obsahují vSphere clustery, sestavené z vyhrazené holé infrastruktury Azure. Minimální počáteční nasazení je tři hostitele, ale další hostitele je možné přidat najednou, maximálně 16 hostitelů na jeden cluster.  Všechny zřízené privátní cloudy mají vCenter Server, síti vSAN, vSphere a NSX-T. Můžete migrovat úlohy z místních prostředí, nasadit nové virtuální počítače a využívat služby Azure ze svých privátních cloudů.

Řešení Azure VMware je ověřované řešení VMware s průběžným ověřováním a testováním vylepšení a upgradů. Společnost Microsoft spravuje a udržuje privátní cloudovou infrastrukturu a software. Umožňuje soustředit se na vývoj a spouštění úloh v privátních cloudech. 

Diagram znázorňuje vztah mezi privátními cloudy a virtuální sítě v Azure, službami Azure a místním prostředí. Přístup k síti z privátních cloudů do služeb Azure nebo virtuální sítě zajišťuje integraci koncových bodů služeb Azure řízených smlouvou SLA. ExpressRoute Global Reach připojuje vaše místní prostředí k vašemu privátnímu cloudu řešení Azure VMware. 

![Obrázek sousedního cloudu řešení Azure VMware s Azure a místním prostředím](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hostitelé, clustery a privátní cloudy

Privátní cloudy a clustery řešení Azure VMware jsou postavené z holého hostitele infrastruktury Azure, který se konverguje pomocí technologie Hyper-v. Hostitelé s vysokým konečným objemem jsou 576 – GB paměti RAM a duální procesory Intel 18 Core a 2,3 GHz. Hostitelé mají dvě síti vSAN diskgroups s 15,36 TB (SSD) s nezpracovanými síti vSAN úrovněmi kapacity a 3,2 TB (NVMe) síti vSAN úrovně mezipaměti.

Nové privátní cloudy se nasazují prostřednictvím Azure Portal nebo Azure CLI.

## <a name="networking"></a>Sítě

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Další informace najdete v tématu [Koncepty sítě](concepts-networking.md).

## <a name="access-and-security"></a>Přístup a zabezpečení

Privátní cloudy řešení Azure VMware používají pro rozšířené zabezpečení vSphere řízení přístupu založené na rolích. Možnosti LDAP vSphere SSO můžete integrovat s Azure Active Directory. Další informace najdete v tématu [koncepce přístupu a identity](concepts-identity.md).  

Síti vSAN šifrování v klidovém stavu je ve výchozím nastavení povolené a používá se k zajištění zabezpečení úložiště síti vSAN. Další informace najdete v tématu [Koncepty úložiště](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Údržba životního cyklu hostitele a softwaru

Pravidelná upgrade privátního cloudu řešení Azure VMware a softwaru VMware zajišťuje, aby byly v privátních cloudech spuštěné nejnovější bezpečnostní, stabilní a funkční sady funkcí. Další informace najdete v tématu [aktualizace a upgrade privátního cloudu](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Monitorování privátního cloudu

Po nasazení řešení Azure VMware do svého předplatného [Azure monitor protokoly](../azure-monitor/overview.md) se generují automaticky. 

V privátním cloudu můžete:
- Shromažďovat protokoly na všech vašich virtuálních počítačích.
- [Stáhněte a nainstalujte agenta MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options) na virtuální počítače se systémy Linux a Windows.
- Povolte [rozšíření Azure Diagnostics](../azure-monitor/platform/diagnostics-extension-overview.md).
- [Vytvořte a spusťte nové dotazy](../azure-monitor/platform/data-platform-logs.md#log-queries).
- Spouštějte stejné dotazy, které obvykle spouštíte na virtuálních počítačích.

Vzorce monitorování v řešení Azure VMware jsou podobné virtuálním počítačům Azure v rámci platformy IaaS. Další informace a postupy najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Další kroky

Dalším krokem je seznámit se s klíčovým [koncepcím privátního cloudu a clusteru](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
