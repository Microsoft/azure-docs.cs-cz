---
title: Přehled nasazení Preview Azure Firewall Manageru
description: Seznamte se s kroky nasazení na nejvyšší úrovni, které jsou nezbytné pro Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77443121"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Přehled nasazení Preview Azure Firewall Manageru

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Je k dispozici více než jeden způsob nasazení Azure Firewall Manager Preview, ale doporučuje se tento obecný postup.

## <a name="general-deployment-process"></a>Obecný proces nasazení

### <a name="hub-virtual-networks"></a>Centrální virtuální sítě

1.  Vytvoření zásady brány firewall

    - Vytvoření nové zásady
<br>*ani*<br>
    - Odvození základní zásady a přizpůsobení místních zásad
<br>*ani*<br>
    - Importuje pravidla z existující Azure Firewall. Nezapomeňte odebrat pravidla překladu adres (NAT) ze zásad, které by se měly použít napříč několika branami firewall.
1. Vytvoření architektury hub a paprsků
   - Vytvoření centra Virtual Network s použitím partnerského vztahu virtuálních sítí pomocí správce Azure Firewall a virtuálních sítí s partnerským paprskem
<br>*ani*<br>
    - Vytvoření virtuální sítě a přidání připojení virtuální sítě a virtuálních sítí s partnerským paprskem do ní pomocí partnerského vztahu virtuálních sítí

3. Vyberte zprostředkovatelé zabezpečení a přidružte zásady brány firewall. V současné době je jako podporovaný zprostředkovatel jenom Azure Firewall.

   - To se provádí při vytváření Virtual Network centra.
<br>*ani*<br>
    - Převeďte stávající virtuální síť na Virtual Network centra. Je také možné převést více virtuálních sítí.

4. Nakonfigurujte uživatelsky definované trasy pro směrování provozu do vašeho centra Virtual Network firewall.


### <a name="secured-virtual-hubs"></a>Zabezpečená virtuální centra

1. Vytvoření architektury hub a paprsků

   - Vytvořte zabezpečeného virtuálního rozbočovače pomocí správce Azure Firewall a přidejte připojení k virtuální síti.<br>*ani*<br>
   - Vytvořte virtuální síť WAN hub a přidejte připojení k virtuální síti.
2. Vybrat poskytovatele zabezpečení

   - Při vytváření zabezpečeného virtuálního rozbočovače se dokončila.<br>*ani*<br>
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

## <a name="next-steps"></a>Další kroky

- [Kurz: zabezpečení cloudové sítě pomocí nástroje Azure Firewall Manager ve verzi Preview pomocí Azure Portal](secure-cloud-network.md)