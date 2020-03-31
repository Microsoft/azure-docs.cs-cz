---
title: Přehled nasazení Nástroje pro nasazení správce azure firewallu
description: Seznamte se s kroky nasazení na vysoké úrovni, které jsou vyžadovány pro Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443121"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Přehled nasazení Nástroje pro nasazení správce azure firewallu

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Existuje více než jeden způsob nasazení Azure Firewall Manager Preview, ale následující obecný proces se doporučuje.

## <a name="general-deployment-process"></a>Obecný proces nasazení

### <a name="hub-virtual-networks"></a>Centrální virtuální sítě

1.  Vytvoření zásad brány firewall

    - Vytvoření nové zásady
<br>*Nebo*<br>
    - Odvození základní zásady a přizpůsobení místních zásad
<br>*Nebo*<br>
    - Importpravidel z existující brány Azure Firewall. Nezapomeňte odebrat pravidla NAT ze zásad, které by měly být použity ve více branách firewall.
1. Vytvořte si architekturu rozbočovače a paprsku
   - Vytvoření virtuální sítě hubu pomocí Správce azure firewallu a virtuálních sítí s partnerským paprskem pomocí partnerského vztahu virtuální sítě
<br>*Nebo*<br>
    - Vytvoření virtuální sítě a přidání připojení k virtuální síti a virtuálních sítí s partnerským paprskem pomocí partnerského vztahu virtuální sítě

3. Vyberte zprostředkovatele zabezpečení a přidružte zásady brány firewall. V současné době je podporovaným poskytovatelem pouze azure firewall.

   - To se provádí při vytváření virtuální sítě Hub
<br>*Nebo*<br>
    - Převeďte existující virtuální síť na virtuální síť rozbočovače. Je také možné převést více virtuálních sítí.

4. Nakonfigurujte uživatele definovat trasy pro směrování provozu do brány firewall virtuální sítě hubu.


### <a name="secured-virtual-hubs"></a>Zabezpečené virtuální rozbočovače

1. Vytvořte si architekturu rozbočovače a paprsku

   - Vytvořte zabezpečené virtuální rozbočovač pomocí Správce brány Azure firewall a přidejte připojení virtuálních sítí.<br>*Nebo*<br>
   - Vytvořte centrum Virtuální síť WAN a přidejte připojení k virtuální síti.
2. Výběr poskytovatelů zabezpečení

   - Hotovo při vytváření zabezpečeného virtuálního rozbočovače.<br>*Nebo*<br>
   - Převeďte existující virtuální rozbočovač WAN na zabezpečené virtuální centrum.
3. Vytvoření zásad brány firewall a přidružení k rozbočovači

   - Platí pouze v případě, že používáte Azure Firewall.
   - Zásady zabezpečení jako služby (SECaaS) třetích stran jsou konfigurovány prostřednictvím prostředí pro správu partnerů.
4. Konfigurace nastavení trasy pro směrování provozu do zabezpečeného rozbočovače

   - Pomocí stránky Nastavení trasy zabezpečeného virtuálního rozbočovače můžete snadno směrovat provoz do zabezpečeného rozbočovače pro filtrování a protokolování bez uživatelem definovaných tras (UDR) v virtuálních sítích s paprsky.

> [!NOTE]
> - Nemůžete mít více než jeden rozbočovač na virtuální wan na oblast. Ale můžete přidat více virtuálních WAN v oblasti k dosažení tohoto cíle.
> - V programu vWAN nelze mít překrývající se ip prostory pro rozbočovače.
> - Připojení virtuální sítě rozbočovače musí být ve stejné oblasti jako rozbočovač.

## <a name="next-steps"></a>Další kroky

- [Kurz: Zabezpečení cloudové sítě pomocí Azure Firewall Manager Preview pomocí portálu Azure](secure-cloud-network.md)