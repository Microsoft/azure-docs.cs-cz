---
title: Úvod
description: Seznamte se s funkcemi a výhodami řešení Azure VMware pro nasazení a správu úloh na bázi VMware v Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: c01f7a21b4a7c46469c3b6a63b0c426715da896d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332793"
---
# <a name="what-is-azure-vmware-solution"></a>Co je Azure VMware Solution?

Řešení Azure VMware poskytuje privátní cloudy v Azure. Tyto privátní cloudy obsahují clustery vSphere a využívají vyhrazenou infrastrukturu holých počítačů Azure. Clustery v privátních cloudech můžete škálovat na 3 až 16 hostitelů a v jednom privátním cloudu můžete mít více clusterů. Všechny privátní cloudy se zřídí s vCenter Server, síti vSAN, vSphere a NSX-T. Do privátních cloudů můžete migrovat úlohy z místních prostředků, můžete v nich vytvářet nebo nasazovat nové virtuální počítače a využívat v nich služby Azure.

Řešení Azure VMware je ověřované řešení VMware s průběžným ověřováním a testováním vylepšení a upgradů. Infrastruktura a software privátního cloudu spravuje a udržuje společnost Microsoft a umožňuje zaměřit se na vývoj a spouštění úloh v privátních cloudech.

Následující diagram znázorňuje vztah mezi soukromými cloudy a virtuální sítě v Azure, službami Azure a místním prostředí. Přístup k síti z privátních cloudů do služeb Azure nebo virtuální sítě zajišťuje integraci koncových bodů služeb Azure řízených smlouvou SLA. Přístup k privátnímu cloudu z místních prostředí používá Global Reach ExpressRoute pro privátní a zabezpečené připojení.

![Obrázek sousedního cloudu řešení Azure VMware s Azure a místním prostředím](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hostitelé, clustery a privátní cloudy

Privátní cloudy a clustery řešení Azure VMware jsou postavené z holého hostitele infrastruktury Azure, který se konverguje pomocí technologie Hyper-v. Hostitelé s vysokým konečným objemem jsou 576 – GB paměti RAM a duální procesory Intel 18 Core a 2,3 GHz. Hostitelé mají dvě síti vSAN diskgroups s 15,36 celkovou úrovní kapacity síti vSAN TB (SSD) a 3,2 TB (NVMe) síti vSAN úrovně mezipaměti.

Nové privátní cloudy se nasazují prostřednictvím Azure Portal nebo Azure CLI.

## <a name="networking"></a>Sítě

[!INCLUDE [avs-networking-description](includes/avs-networking-description.md)]

Další informace o sítích a vzájemné propojení najdete v článku [Koncepty sítě](concepts-networking.md) .

## <a name="access-and-security"></a>Přístup a zabezpečení

Pro zvýšení zabezpečení používají privátní cloudy řešení VMware Azure řízení přístupu na základě role vSphere. vSphere jednotné přihlašování LDAP je možné integrovat s Azure Active Directory. Další informace o identitě a oprávněních najdete v článku [koncepce přístupu a identit](concepts-identity.md) .

Síti vSAN šifrování na REST je ve výchozím nastavení povolené a používá se k zajištění zabezpečení úložiště síti vSAN. Podrobněji se popisuje v článku [Koncepty úložiště](concepts-storage.md) .

## <a name="host-and-software-lifecycle-maintenance"></a>Údržba životního cyklu hostitele a softwaru

Pravidelná upgrade privátního cloudu řešení Azure VMware a softwaru VMware zajišťuje, že se v privátních cloudech spustí nejnovější sady funkcí zabezpečení, stability a funkcí. Další podrobnosti o údržbě a údržbě platforem jsou k dispozici v článku [Koncepty upgradu](concepts-upgrades.md) .

## <a name="monitoring-your-private-cloud"></a>Monitorování privátního cloudu

Po nasazení řešení Azure VMware do svého předplatného [Azure monitor protokoly](../azure-monitor/overview.md) se generují automaticky. Kromě toho můžete shromažďovat protokoly na všech virtuálních počítačích v rámci privátního cloudu. [Agenta MMA můžete stáhnout a nainstalovat](../azure-monitor/platform/log-analytics-agent.md#installation-options) na virtuální počítače se systémem Linux a Windows, které běží v privátních cloudech řešení Azure VMware, a povolit [rozšíření Azure Diagnostics](../azure-monitor/platform/diagnostics-extension-overview.md). Můžete dokonce spouštět stejné dotazy, které obvykle spouštíte na virtuálních počítačích. Další informace o vytváření dotazů najdete v tématu [Postup zápisu dotazů](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries). Vzorce monitorování v řešení Azure VMware jsou podobné Azure Virtual Machines v rámci platformy IaaS. Další informace a postupy najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Další kroky

Dalším krokem je seznámit se s klíčovým [koncepcím privátního cloudu a clusteru](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
