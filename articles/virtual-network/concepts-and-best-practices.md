---
title: Azure Virtual Network – koncepty a osvědčené postupy
description: Přečtěte si o konceptech a osvědčených postupech pro Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: d279516c1c9c08512c850a0f70eb84c0c1f63166
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111151"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Koncepce a osvědčené postupy pro Azure Virtual Network

Tento článek popisuje klíčové koncepty a osvědčené postupy pro Azure Virtual Network (VNet).

## <a name="vnet-concepts"></a>Koncepty virtuální sítě

- **Adresní prostor:** Při vytváření virtuální sítě musíte zadat vlastní adresní prostor privátních IP adres pomocí veřejných a privátních adres (RFC 1918). Azure přiřazuje prostředkům ve virtuální síti privátní IP adresy z přiřazeného adresního prostoru. Pokud například nasadíte virtuální počítač ve virtuální síti s adresním prostorem 10.0.0.0/16, bude virtuálnímu počítači přiřazená privátní IP adresa, jako je 10.0.0.4.
- **Podsítě:** Podsítě umožňují segmentovat virtuální síť do jedné nebo více podsítí a přidělit část adresního prostoru virtuální sítě ke každé podsíti. Prostředky Azure pak můžete nasadit v konkrétní podsíti. Stejně jako v tradiční síti vám podsítě umožní segmentovat adresní prostor virtuální sítě do segmentů, které jsou vhodné pro interní síť organizace. Tím se také zlepšuje efektivita přidělování adres. Prostředky v rámci podsítí můžete zabezpečit pomocí skupin zabezpečení sítě. Další informace najdete v tématu [skupiny zabezpečení sítě](security-overview.md).
- **Oblasti**: virtuální síť je vymezená na jednu oblast nebo umístění; víc virtuálních sítí z různých oblastí ale můžete propojit společně pomocí Virtual Network partnerských vztahů.
- **Předplatné:** Virtuální síť je vymezená pro předplatné. V každém [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure a v každé [oblasti](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure můžete implementovat několik virtuálních sítí.

## <a name="best-practices"></a>Osvědčené postupy

Při sestavování sítě v Azure je důležité mít na paměti následující principy univerzálního návrhu:

- Ujistěte se, že adresní prostory nejsou překrývající. Ujistěte se, že se adresní prostor virtuální sítě (blok CIDR) nepřekrývá s dalšími rozsahy sítě vaší organizace.
- Vaše podsítě by neměly zahrnovat celý adresní prostor virtuální sítě. Naplánujte si předem a vyhraďte si pro budoucnost nějaký adresní prostor.
- Doporučuje se, abyste měli méně velké virtuální sítě místo několika malých virtuální sítě. Tím se zabrání režie správy.
- Zabezpečte svou virtuální síť přiřazením skupin zabezpečení sítě (skupin zabezpečení sítě) k podsítím, které jsou pod nimi.

## <a name="next-steps"></a>Další kroky

 Pokud chcete začít používat virtuální síť, nějakou vytvořte, nasaďte do ní několik virtuálních počítačů a navažte komunikaci mezi těmito virtuálními počítači. Informace o postupu najdete v rychlém startu [Vytvoření virtuální sítě](quick-create-portal.md).
