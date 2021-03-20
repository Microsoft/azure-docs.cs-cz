---
title: Co je zabezpečené virtuální centrum?
description: Další informace o zabezpečených virtuálních centrech
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948064"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co je zabezpečené virtuální centrum?

Virtuální rozbočovač je virtuální síť spravovaná Microsoftem, která umožňuje připojení z jiných prostředků. Při vytvoření virtuálního rozbočovače z virtuální sítě WAN v Azure Portal se jako své součásti vytvoří virtuální síť virtuálního centra a brány (volitelné).

*Zabezpečený* virtuální rozbočovač je [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) s přidruženými zásadami zabezpečení a směrování nakonfigurovanými nástrojem Azure firewall Manager. Pomocí zabezpečených virtuálních rozbočovačů můžete snadno vytvářet hvězdicové a přenositelné architektury s nativními službami zabezpečení pro řízení provozu a ochranu. 

Pomocí zabezpečeného virtuálního rozbočovače můžete filtrovat provoz mezi virtuálními sítěmi (V2V), virtuálními sítěmi a pobočkami (B2V) a přenosem na Internet (B2I/V2I). Zabezpečené virtuální rozbočovače poskytuje automatizované směrování. Není potřeba konfigurovat vlastní udr (trasy definované uživatelem) pro směrování provozu přes bránu firewall.

Můžete zvolit požadované poskytovatele zabezpečení pro ochranu a řízení provozu v síti, včetně Azure Firewall, poskytovatelů zabezpečení jako služby (SECaaS) třetích stran nebo obou. V současné době zabezpečené centrum nepodporuje filtrování a filtrování větví do větví (B2B) na více rozbočovačích. Další informace najdete v tématu [co je Azure firewall Manager?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního rozbočovače

Pomocí Správce brány firewall v Azure Portal můžete buď vytvořit nový zabezpečený virtuální rozbočovač, nebo převést stávající virtuální rozbočovač, který jste dříve vytvořili pomocí Azure Virtual WAN.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit zabezpečený virtuální rozbočovač a použít ho k zabezpečení a řízení sítě rozbočovače a paprsků, přečtěte si téma [kurz: zabezpečení cloudové sítě pomocí Azure firewall Manageru pomocí Azure Portal](secure-cloud-network.md).