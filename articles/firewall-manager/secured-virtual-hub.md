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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518434"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co je zabezpečené virtuální centrum?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Virtuální rozbočovač je virtuální síť spravovaná společností Microsoft, která umožňuje připojení z jiných prostředků. Když se virtuální rozbočovač vytvoří z virtuální sítě WAN na webu Azure Portal, virtuální síť virtuálního rozbočovače a brány (volitelné) se vytvoří jako jeho součásti.

*Zabezpečené* virtuální rozbočovač je [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) s přidruženými zásadami zabezpečení a směrování nakonfigurovanými správcem Azure Firewall. Pomocí zabezpečených virtuálních rozbočovačů můžete snadno vytvářet architektury rozbočovače a paprsku a přenositelné architektury s nativními službami zabezpečení pro řízení provozu a ochranu. 

Zabezpečené virtuální rozbočovač můžete použít jako spravovanou centrální virtuální síť bez připojení k on-prem. Nahradí centrální virtuální síť, která byla dříve vyžadována pro nasazení Azure Firewall. Vzhledem k tomu, že zabezpečené virtuální rozbočovač poskytuje automatizované směrování, není nutné konfigurovat vlastní UDR (uživatelem definované trasy) pro směrování provozu přes bránu firewall.

Je také možné použít zabezpečené virtuální rozbočovače jako součást úplné architektury virtuální sítě WAN. Tato architektura poskytuje zabezpečené, optimalizované a automatizované připojení poboček do Azure a přes azure. Můžete si vybrat služby pro ochranu a řízení síťového provozu, včetně Azure Firewall a dalších poskytovatelů zabezpečení třetích stran jako služby (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního rozbočovače

Pomocí Správce brány firewall na webu Azure Portal můžete buď vytvořit nové zabezpečené virtuální rozbočovač, nebo převést existující virtuální rozbočovač, který jste dříve vytvořili pomocí Azure Virtual WAN.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit zabezpečené virtuální rozbočovač a použít ho k zabezpečení a řízení sítě rozbočovače a [paprsku, přečtěte si informace o tom, že pomocí Správce azure firewallu na webu Azure Portal najdete v tématu Zabezpečení cloudové sítě pomocí Správce brány Firewall Azure](secure-cloud-network.md).