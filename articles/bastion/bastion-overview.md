---
title: Azure bastionu | Microsoft Docs
description: Další informace o Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 4cee16a4b6d4f87c8f09ad742d9a504cfc5fc394
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498332"
---
# <a name="what-is-azure-bastion"></a>Co je Azure Bastion?

Služba Azure bastionu je nová plně spravovaná služba PaaS spravovaná platformou, kterou zřizujete v rámci vaší virtuální sítě. Poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo v Azure Portal přes SSL. Když se připojíte pomocí Azure Bastionu, vaše virtuální počítače nepotřebují veřejnou IP adresu.

 Bastionu zajišťuje zabezpečené připojení RDP a SSH ke všem virtuálním počítačům ve virtuální síti, ve které se zřídí. Použití Azure bastionu chrání vaše virtuální počítače před vystavení portů RDP/SSH na vnějším světě a zároveň zajišťuje zabezpečený přístup pomocí protokolu RDP/SSH. S Azure bastionu se připojujete k virtuálnímu počítači přímo z Azure Portal. Nepotřebujete dalšího klienta, agenta ani software.

## <a name="architecture"></a>Architektura

Azure bastionu je nasazený ve vaší virtuální síti a po nasazení poskytuje zabezpečené prostředí RDP/SSH pro všechny virtuální počítače ve vaší virtuální síti. Po zřízení služby Azure bastionu ve vaší virtuální síti bude prostředí RDP/SSH dostupné pro všechny vaše virtuální počítače ve stejné virtuální síti. Nasazení je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

RDP a SSH jsou některé ze základních prostředků, pomocí kterých se můžete připojit ke svým úlohám, které běží v Azure. Vystavení portů RDP/SSH přes Internet se nepožaduje a zobrazuje se jako významná hladina hrozeb. To je často způsobeno chybami zabezpečení protokolu. Aby tato hrozba obsahovala tyto hrozby, můžete na veřejné straně hraniční sítě nasadit hostitele bastionu (označované také jako servery skoků). Hostitelské servery bastionu jsou navržené a nakonfigurované tak, aby odolaly útokům. Servery bastionu také poskytují připojení RDP a SSH k úlohám, které se probírají za bastionu, a dále v síti.

![Architektura](./media/bastion-overview/architecture.png)

Na tomto obrázku vidíte architekturu nasazení Azure bastionu. V tomto diagramu:

* Hostitel bastionu je nasazený ve virtuální síti.
* Uživatel se připojí k Azure Portal pomocí libovolného prohlížeče HTML5.
* Uživatel vybere virtuální počítač, ke kterému se má připojit.
* Jediným kliknutím se v prohlížeči otevře relace RDP/SSH.
* Na virtuálním počítači Azure se nevyžaduje žádná veřejná IP adresa.

## <a name="key-features"></a>Klíčové funkce

K dispozici jsou následující funkce:

* **RDP a SSH přímo v Azure Portal:** K relaci RDP a SSH se můžete přímo dostat přímo v Azure Portal pomocí jediného prostředí s možností bezproblémového kliknutí.
* **Vzdálená relace přes protokol SSL a procházení brány firewall pro RDP/SSH:** Azure bastionu využívá webového klienta založeného na technologii HTML5, který se automaticky streamuje na vaše místní zařízení, takže relaci RDP/SSH přes SSL na portu 443 umožníte zabezpečeně procházet podnikové brány firewall.
* **Na virtuálním počítači Azure není potřeba žádná veřejná IP adresa:** Azure bastionu otevře připojení RDP/SSH k virtuálnímu počítači Azure pomocí privátní IP adresy na vašem virtuálním počítači. Na virtuálním počítači nepotřebujete veřejnou IP adresu.
* **Žádné nepříjemnosti při správě skupin zabezpečení sítě:** Azure bastionu je plně spravovaná služba platformy PaaS z Azure, která je posílená interně, aby poskytovala zabezpečené připojení RDP/SSH. Nemusíte používat žádné skupin zabezpečení sítě v podsíti Azure bastionu. Vzhledem k tomu, že se Azure bastionu připojuje k virtuálním počítačům přes soukromou IP adresu, můžete nakonfigurovat skupin zabezpečení sítě tak, aby povoloval jenom RDP/SSH jenom z Azure bastionu. Tím se eliminují starosti se správou skupin zabezpečení sítě pokaždé, když se budete muset bezpečně připojit k virtuálním počítačům.
* **Ochrana proti kontrole portů:** Vzhledem k tomu, že virtuální počítače nemusíte zveřejňovat pro veřejný Internet, jsou vaše virtuální počítače chráněné před kontrolou portů neautorizovanými a zlomyslnými uživateli, kteří se nacházejí mimo vaši virtuální síť.
* **Chraňte proti neoprávněným zneužitím. Posílení zabezpečení pouze na jednom místě:** Azure bastionu je plně spravovaná služba PaaS spravovaná platformou. Vzhledem k tomu, že se nachází na hraničních sítích vaší virtuální sítě, nemusíte se starat o posílení zabezpečení každého virtuálního počítače ve vaší virtuální síti. Platforma Azure chrání před neoprávněným zneužitím tím, že zajišťuje posílení zabezpečení Azure bastionu a vždycky aktuální.

## <a name="faq"></a>Časté otázky

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvořte prostředek hostitele Azure bastionu](bastion-create-host-portal.md).
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
