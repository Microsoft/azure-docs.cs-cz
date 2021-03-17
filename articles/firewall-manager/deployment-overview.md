---
title: Přehled nasazení Azure Firewall Manageru
description: Seznamte se s kroky nasazení na vysoké úrovni, které vyžaduje nástroj Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079094"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Přehled nasazení Azure Firewall Manageru

Je k dispozici více než jeden způsob nasazení Azure Firewall Manageru, ale doporučujeme následující obecný postup.

## <a name="general-deployment-process"></a>Obecný proces nasazení

### <a name="hub-virtual-networks"></a>Centrální virtuální sítě

1.  Vytvoření zásady brány firewall

    - Vytvoření nové zásady
<br>*nebo*<br>
    - Odvození základní zásady a přizpůsobení místních zásad
<br>*nebo*<br>
    - Importuje pravidla z existující Azure Firewall. Nezapomeňte odebrat pravidla překladu adres (NAT) ze zásad, které by se měly použít napříč několika branami firewall.
1. Vytvoření architektury hub a paprsků
   - Vytvoření centra Virtual Network s použitím partnerského vztahu virtuálních sítí pomocí správce Azure Firewall a virtuálních sítí s partnerským paprskem
<br>*nebo*<br>
    - Vytvoření virtuální sítě a přidání připojení virtuální sítě a virtuálních sítí s partnerským paprskem do ní pomocí partnerského vztahu virtuálních sítí

3. Vyberte zprostředkovatelé zabezpečení a přidružte zásady brány firewall. V současné době je jako podporovaný zprostředkovatel jenom Azure Firewall.

   - To se provádí při vytváření Virtual Network centra.
<br>*nebo*<br>
    - Převeďte stávající virtuální síť na Virtual Network centra. Je také možné převést více virtuálních sítí.

4. Nakonfigurujte uživatelsky definované trasy pro směrování provozu do vašeho centra Virtual Network firewall.


### <a name="secured-virtual-hubs"></a>Zabezpečená virtuální centra

1. Vytvoření architektury hub a paprsků

   - Vytvořte zabezpečeného virtuálního rozbočovače pomocí správce Azure Firewall a přidejte připojení k virtuální síti.<br>*nebo*<br>
   - Vytvořte virtuální síť WAN hub a přidejte připojení k virtuální síti.
2. Vybrat poskytovatele zabezpečení

   - Při vytváření zabezpečeného virtuálního rozbočovače se dokončila.<br>*nebo*<br>
   - Převeďte stávající virtuální centrum sítě WAN na zabezpečené virtuální centrum.
3. Vytvoření zásady brány firewall a její přidružení ke svému centru

   - Platí pouze při použití Azure Firewall.
   - Zásady zabezpečení jako služby (SECaaS) třetích stran jsou konfigurovány prostřednictvím prostředí pro správu partnerů.
4. Konfigurace nastavení směrování pro směrování provozu do zabezpečeného centra

   - Snadná směrování provozu do zabezpečeného centra pro filtrování a protokolování bez uživatelsky definovaných tras (UDR) ve virtuálních sítích rozbočovače pomocí stránky nastavení trasy zabezpečeného virtuálního počítače.

> [!NOTE]
> - Pro každou virtuální síť WAN na jednu oblast nemůžete mít více než jedno rozbočovač. Můžete ale do této oblasti přidat víc virtuálních sítí WAN, abyste to dosáhli.
> - Pro rozbočovače v vWAN se nedají překrývat adresní prostory IP adres.
> - Připojení k virtuální síti rozbočovače musí být ve stejné oblasti jako centrum.
>
> Další známé problémy najdete v tématu [co je Azure firewall Manager?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Převod virtuálních sítí

Pokud převedete stávající virtuální síť na virtuální síť rozbočovače, platí následující informace:

- Pokud má virtuální síť existující Azure Firewall, vyberte zásadu brány firewall, kterou chcete přidružit k existující bráně firewall. Stav zřizování brány firewall se aktualizuje, zatímco zásady brány firewall nahradí pravidla brány firewall. Během stavu zřizování brána firewall pokračuje v zpracování provozu a nedochází k výpadkům. Stávající pravidla můžete importovat do zásad brány firewall pomocí Správce brány firewall nebo Azure PowerShell.
- Pokud virtuální síť nemá přidruženou Azure Firewall, je nasazená brána firewall a k nové bráně firewall je přidružená zásada brány firewall.

## <a name="next-steps"></a>Další kroky

- [Kurz: zabezpečení cloudové sítě pomocí nástroje Azure Firewall Manager pomocí Azure Portal](secure-cloud-network.md)