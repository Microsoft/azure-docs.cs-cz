---
title: Práce s virtuálními počítači a skupin zabezpečení sítě v Azure bastionu
description: Tento článek popisuje, jak začlenit přístup NSG pomocí Azure bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087271"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Práce s NSG přístupem a Azure bastionu

Při práci s Azure bastionu můžete použít skupiny zabezpečení sítě (skupin zabezpečení sítě). Další informace najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md). 

![Architektura](./media/bastion-nsg/nsg-architecture.png)

V tomto diagramu:

* Hostitel bastionu je nasazený ve virtuální síti.
* Uživatel se připojí k Azure Portal pomocí libovolného prohlížeče HTML5.
* Uživatel přejde k virtuálnímu počítači Azure na RDP/SSH.
* Propojení integrace – relace RDP/SSH v prohlížeči jediným kliknutím
* Na virtuálním počítači Azure se nevyžaduje žádná veřejná IP adresa.

## <a name="nsg"></a>Skupiny zabezpečení sítě

V této části se dozvíte, jak se síťový provoz mezi uživatelem a službou Azure bastionu a kdy do cílových virtuálních počítačů ve vaší virtuální síti:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure bastionu se nasazuje konkrétně pro AzureBastionSubnet.

* **Příchozí přenos dat:**

   * **Příchozí přenos dat z veřejného Internetu:** Azure bastionu vytvoří veřejnou IP adresu, která potřebuje port 443 povolený ve veřejné IP adrese pro přenos příchozích dat. Na AzureBastionSubnet se nevyžaduje otevřít port 3389/22.
   * **Příchozí přenos dat z roviny ovládacího prvku Azure bastionu:** Pro připojení řídicí roviny povolte port 443 příchozí ze značky služby **GatewayManager** . To umožňuje, aby řídicí plocha, kterou správce bran, mohla komunikovat se službou Azure bastionu.

* **Odchozí přenos dat:**

   * **Odchozí přenos dat do cílových virtuálních počítačů:** Azure bastionu se dostane k cílovým virtuálním počítačům přes privátní IP adresu. Skupin zabezpečení sítě musí umožňovat přenos odchozího provozu do jiných cílových podsítí virtuálních počítačů pro porty 3389 a 22.
   * **Odchozí přenos dat do jiných veřejných koncových bodů v Azure:** Azure bastionu musí být schopný se připojit k různým veřejným koncovým bodům v rámci Azure (například pro ukládání protokolů diagnostiky a měřičů měření). Z tohoto důvodu Azure bastionu potřebuje pro značku služby **AzureCloud** odchozí až 443.

* **Cílová podsíť virtuálních počítačů:** Jedná se o podsíť, která obsahuje cílový virtuální počítač, ke kterému chcete protokol RDP/SSH.

   * **Příchozí přenos dat z Azure bastionu:** Azure bastionu se bude přistihnout k cílovému virtuálnímu počítači přes privátní IP adresu. Porty RDP/SSH (porty 3389/22) se musí na cílové straně virtuálního počítače otevřít přes soukromou IP adresu. Osvědčeným postupem je v tomto pravidle přidat rozsah IP adres podsítě Azure bastionu, aby bylo možné otevřít tyto porty na cílových virtuálních počítačích v podsíti cílového virtuálního počítače jenom v bastionu.

## <a name="apply"></a>Použít skupin zabezpečení sítě na AzureBastionSubnet

Pokud vytvoříte a použijete NSG na ***AzureBastionSubnet***, ujistěte se, že jste do svého NSG přidali následující pravidla. Pokud tato pravidla nepřidáte, vytvoření nebo aktualizace NSG se nezdaří:

* **Konektivita řídicí roviny:** Příchozí na 443 z GatewayManager
* **Protokolování diagnostiky a další:** Odchozí na 443 až AzureCloud. Regionální značky v této značce služby se zatím nepodporují.
* **Cílový virtuální počítač:** Odchozí pro 3389 a 22 až VirtualNetwork

Příklad pravidla NSG je k dispozici pro referenci v této [šabloně pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Další kroky

Další informace o Azure bastionu najdete v [nejčastějších dotazech](bastion-faq.md).
