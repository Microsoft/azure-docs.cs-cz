---
title: Co je zabezpečené virtuální centrum?
description: Další informace o zabezpečených virtuálních centrech
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518434"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co je zabezpečené virtuální centrum?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Virtuální rozbočovač je virtuální síť spravovaná Microsoftem, která umožňuje připojení z jiných prostředků. Při vytvoření virtuálního rozbočovače z virtuální sítě WAN v Azure Portal se jako své součásti vytvoří virtuální síť virtuálního centra a brány (volitelné).

*Zabezpečený* virtuální rozbočovač je [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) s přidruženými zásadami zabezpečení a směrování nakonfigurovanými nástrojem Azure firewall Manager. Pomocí zabezpečených virtuálních rozbočovačů můžete snadno vytvářet hvězdicové a přenositelné architektury s nativními službami zabezpečení pro řízení provozu a ochranu. 

Zabezpečené virtuální rozbočovače můžete použít jako spravovanou centrální síť VNet bez připojení on-Prem. Nahrazuje centrální virtuální síť, která byla dříve nutná pro nasazení Azure Firewall. Vzhledem k tomu, že zabezpečené virtuální rozbočovače poskytuje automatizované směrování, není nutné konfigurovat vlastní udr (trasy definované uživatelem) pro směrování provozu přes bránu firewall.

V rámci plné architektury virtuální sítě WAN je také možné používat zabezpečená virtuální centra. Tato architektura poskytuje zabezpečené, optimalizované a automatizované připojení větví k Azure. Můžete zvolit služby, které budou chránit a řídit síťový provoz, včetně Azure Firewall a dalších poskytovatelů zabezpečení jako služby (SECaaS) třetích stran.

## <a name="create-a-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního rozbočovače

Pomocí Správce brány firewall v Azure Portal můžete buď vytvořit nový zabezpečený virtuální rozbočovač, nebo převést stávající virtuální rozbočovač, který jste dříve vytvořili pomocí Azure Virtual WAN.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit zabezpečený virtuální rozbočovač a použít ho k zabezpečení a řízení sítě rozbočovače a paprsků, přečtěte si téma [kurz: zabezpečení cloudové sítě pomocí Azure firewall Manageru pomocí Azure Portal](secure-cloud-network.md).