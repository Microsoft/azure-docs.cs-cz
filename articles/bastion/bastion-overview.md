---
title: Azure Bašta | Dokumenty společnosti Microsoft
description: Další informace o Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411105"
---
# <a name="what-is-azure-bastion"></a>Co je Azure Bastion?

Služba Azure Bastion je nová plně platformně spravovaná služba PaaS, kterou zřídíte ve virtuální síti. Poskytuje bezpečné a bezproblémové připojení RDP/SSH k vašim virtuálním počítačům přímo na portálu Azure přes TLS. Když se připojíte přes Azure Bastion, virtuální počítače nepotřebují veřejnou IP adresu.

Bašta poskytuje zabezpečené připojení RDP a SSH ke všem virtuálním počítačům ve virtuální síti, ve které je zřízena. Použití Azure Bastion chrání vaše virtuální počítače před vystavením portů RDP/SSH vnějšímu světu a zároveň poskytuje tezi zabezpečený přístup pomocí RDP/SSH. S Azure Bastion se k virtuálnímu počítači připojíte přímo z portálu Azure. Nepotřebujete dalšího klienta, agenta nebo software.

## <a name="architecture"></a>Architektura

Nasazení Azure Bastion se používá pro virtuální síť, ne na předplatné nebo účet nebo virtuální počítač. Jakmile zřídíte službu Azure Bastion ve vaší virtuální síti, prostředí RDP/SSH je k dispozici všem vašim virtuálním počítačům ve stejné virtuální síti.

RDP a SSH jsou některé ze základních prostředků, pomocí kterých se můžete připojit k vašim úlohám spuštěným v Azure. Vystavení portů RDP/SSH přes Internet není žádoucí a je považováno za významný povrch hrozby. To je často způsobeno chybami zabezpečení protokolu. Chcete-li tento povrch ohrožení obsahovat, můžete naveřejnit hostitele bašty (označované také jako skokové servery) na veřejné straně hraniční sítě. Hostitelské servery bastionu jsou navrženy a nakonfigurovány tak, aby odolaly útokům. Servery bastionu také poskytují připojení RDP a SSH k úlohám, které sedí za baštou, stejně jako dále uvnitř sítě.

![Architektura](./media/bastion-overview/architecture.png)

Tento obrázek znázorňuje architekturu nasazení Azure Bastion. V tomto diagramu:

* Hostitel Bastion je nasazený ve virtuální síti.
* Uživatel se připojí k portálu Azure pomocí libovolného prohlížeče HTML5.
* Uživatel vybere virtuální počítač, ke kterému se má připojit.
* Jediným kliknutím se v prohlížeči otevře relace RDP/SSH.
* Na virtuálním počítači Azure se nevyžaduje žádná veřejná IP adresa.

## <a name="key-features"></a>Klíčové funkce

K dispozici jsou následující funkce:

* **RDP a SSH přímo na webu Azure Portal:** Přímo se můžete dostat k relaci RDP a SSH přímo na webu Azure Portal pomocí bezproblémového prostředí pro jedno kliknutí.
* **Vzdálená relace přes TLS a průchod brány firewall pro PROTOKOL RDP/SSH:** Azure Bastion používá webového klienta založeného na HTML5, který se automaticky streamuje do místního zařízení, takže se vaše relace RDP/SSH přes TLS dostanete přes TLS na portu 443, který umožňuje bezpečně procházet podnikové brány firewall.
* **Na virtuálním počítači Azure není vyžadována žádná veřejná IP adresa:** Azure Bastion otevře připojení RDP/SSH k virtuálnímu počítači Azure pomocí privátní IP adresy na vašem virtuálním počítači. Nepotřebujete veřejnou IP adresu ve virtuálním počítači.
* **Žádné potíže se správou nevládních tahů:** Azure Bastion je plně spravovaná služba PaaS z Azure, která je interně posílená, aby vám zajistila zabezpečené připojení RDP/SSH. V podsíti Azure Bastion není nutné použít žádné skupiny zabezpečení sítě. Vzhledem k tomu, že Azure Bastion se připojuje k vašim virtuálním počítačům přes privátní IP adresy, můžete nakonfigurovat nsgs povolit RDP/SSH z Azure Bastion jenom. Tím se odstraní potíže se správou nevládních společností pokaždé, když potřebujete bezpečně připojit k virtuálním počítačům.
* **Ochrana proti skenování portů:** Vzhledem k tomu, že není nutné vystavit virtuální počítače na veřejný Internet, vaše virtuální počítače jsou chráněny proti prohledávání portů neautorizovanými a škodlivými uživateli umístěnými mimo vaši virtuální síť.
* **Chraňte před zero-day exploity. Kalení posílení na jednom místě:** Azure Bastion je plně platformně spravovaná služba PaaS. Vzhledem k tomu, že je na obvodu vaší virtuální sítě, nemusíte se starat o posílení zabezpečení každého virtuálního počítače ve vaší virtuální síti. Platforma Azure chrání před zneužitím zero-day tím, že udržuje Azure Bastion zpevněný a vždy aktuální pro vás.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvořte hostitelský prostředek Azure Bastion](bastion-create-host-portal.md).
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
