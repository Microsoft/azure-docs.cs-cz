---
title: Azure Bastionu | Dokumentace Microsoftu
description: Další informace o Azure Bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 06/17/2019
ms.author: cherylmc
ms.openlocfilehash: d153c876366c8c747e06bf50a0ea26bcdcea10eb
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303597"
---
# <a name="what-is-azure-bastion-preview"></a>Co je Azure Bastionu? (Preview)

Služba Azure Bastionu je nová PaaS služba plně spravovaná platforma, kterou zřídíte v rámci vaší virtuální sítě. Bezpečné a bezproblémové připojení RDP/SSH pro virtuální počítače přímo na webu Azure portal poskytuje přes protokol SSL. Když se připojíte přes Azure Bastion, virtuální počítače nepotřebují veřejnou IP adresu.

 Bastionu poskytuje zabezpečené připojení pomocí protokolu RDP a SSH pro všechny virtuální počítače ve virtuální síti, ve které je zřízená. Pomocí Azure Bastionu chrání virtuální počítače z vystavení portů RDP/SSH k vnějším světem při stálém poskytování zabezpečeného přístupu pomocí protokolu RDP nebo SSH. S Azure Bastionu připojíte se k virtuálnímu počítači přímo z portálu Azure portal. Není nutné další klienta, agent nebo softwarového produktu.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektura

Azure Bastionu je nasazené ve vaší virtuální síti a po nasazení, pro všechny virtuální počítače ve službě virtual network poskytuje zabezpečené prostředí pro RDP/SSH. Po zřízení služby Azure Bastionu ve vaší virtuální síti, je k dispozici pro všechny vaše virtuální počítače ve stejné virtuální síti prostředí RDP/SSH. Je nasazení na virtuální síť, ne podle předplatného nebo účtu nebo virtuální počítač.

Protokol RDP a SSH jsou některé základní prostředky, pomocí kterého se můžete připojit k vaší úlohy běžící v Azure. Vystavení portů RDP/SSH přes Internet není žádoucí a za vážné hrozbě povrchu. To je často způsobeno slabá místa protokolů. Tak, aby obsahovala tuto plochu před internetovými útoky, můžete nasadit hostitele bastionu (označované také jako jump servery) na straně veřejné hraniční síti. Bastionu hostitelské servery jsou navržené a nakonfigurované odolat útoku. U chráněných serverů také poskytovat připojení RDP a SSH zátěží za bránou bastionu, jakož i další uvnitř sítě.

![Architektura](./media/bastion-overview/architecture.png)

Tento obrázek ukazuje architekturu nasazení služby Azure Bastionu. V tomto diagramu:

* Bastion host se nasadí ve virtuální síti.
* Uživatel se připojí k webu Azure portal pomocí libovolného prohlížeče HTML5.
* Uživatel vybere virtuálního počítače pro připojení k.
* Jedním kliknutím se v prohlížeči otevře relaci RDP/SSH.
* Žádná veřejná IP adresa musí být ve virtuálním počítači Azure.

## <a name="key-features"></a>Klíčové funkce

Vyzkoušet verzi public preview k dispozici jsou následující funkce:

* **Protokol RDP a SSH přímo na webu Azure portal:** Můžete získat přímo k relaci RDP a SSH přímo na portálu Azure portal pomocí bezproblémového prostředí jediným kliknutím.
* **Vzdálená relace přes SSL a brány firewall procházení pro RDP/SSH:** Azure Bastionu používá klienta HTML5 na základě web, který je automaticky Streamovat do vašeho místního zařízení tak, aby získat relaci RDP/SSH přes protokol SSL na portu 443 umožňuje bezpečně procházení podnikové brány firewall.
* **Na virtuálním počítači Azure potřeba žádná veřejná IP adresa:** Azure Bastionu otevře připojení RDP/SSH k virtuálnímu počítači Azure pomocí privátních IP na virtuálním počítači. Není nutné veřejnou IP adresu ve virtuálním počítači.
* **Žádné zároveň museli namáhavě spravovat skupiny zabezpečení sítě:** Azure Bastionu je plně spravovaná platforma PaaS služby z Azure, která je Posílená interně, která zajišťuje že zabezpečené připojení RDP/SSH. Nemusíte použít žádné skupiny Nsg v podsíti Azure Bastionu. Vzhledem k tomu Azure Bastionu se připojí k vašim virtuálním počítačům přes privátní IP adresa, můžete vytvořit skupiny Nsg k povolení protokolu RDP nebo SSH z Azure Bastionu pouze. Tím zároveň museli namáhavě spravovat skupiny zabezpečení sítě pokaždé, když budete potřebovat pro zabezpečené připojení k vašim virtuálním počítačům.
* **Ochrana proti skenování portu:** Protože nemusíte vystavit vašich virtuálních počítačů do veřejného Internetu, virtuálních počítačů jsou chráněné proti podvodným a uživateli se zlými úmysly nacházejících se mimo virtuální síť skenováním portů.
* **Ochrana před zneužitím nultého dne. Posílení zabezpečení na jenom jednom místě:** Azure Bastionu je plně spravovaná platforma služba PaaS. Protože se nachází v hraniční síti vaší virtuální sítě, není nutné starat o posílení zabezpečení všech virtuálních počítačů ve vaší virtuální síti. Platforma Azure chrání před zneužitím nultého dne udržováním Bastionu Azure posílené a vždy aktuální za vás.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Další postup

* [Vytvořte prostředek Azure Bastion host](bastion-create-host-portal.md).
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
