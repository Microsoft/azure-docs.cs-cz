---
title: Ověřte provoz s IP Adresou sledovací proces sítě Azure tok ověření - portálu Azure | Microsoft Docs
description: Tento článek popisuje, jak zkontrolovat, pokud je povolené nebo zakázané přenosy do nebo z virtuálního počítače
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b6d929f025c8b95709b7c0eb28ee78310e5f12a5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809949"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Zkontrolujte, jestli je provoz povolen nebo odepřen do nebo z virtuálního počítače s tok ověření IP součást sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Tok IP ověřte, že je funkce sledovací proces sítě, která vám umožní ověřit, pokud provoz je povolený do nebo z virtuálního počítače. Ověření lze spustit pro příchozí nebo odchozí provoz. Tento scénář je vhodný pro zjištění aktuálního stavu o tom, jestli virtuální počítač může kontaktovat na externí prostředek nebo jiný prostředek. Ověřte toku IP umožňuje ověřit, pokud vaše skupina zabezpečení sítě (NSG) pravidla jsou správně nakonfigurovány a vyřešit toky, kteří jsou Blokovaní pravidla NSG. Dalším důvodem pro použití IP tok ověření, je potřeba zajistit provoz, který chcete blokovat, je správně blokován nastavením NSG.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě nebo máte existující instanci sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Tento scénář používá k ověření, pokud virtuální počítač může kontaktovat k jinému počítači přes port 443 tok ověření IP. Pokud je odepřená provoz, vrátí pravidlo zabezpečení, které je odepřen, aby provoz. Další informace o toku ověřte IP, navštivte [přehled tok ověření IP](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Ověření spuštění toku IP

Přejděte do vaší sledovací proces sítě a klikněte na tlačítko **IP tok ověření**. Vyberte virtuální počítač a chcete ověřit provoz z síťové rozhraní. Zadejte veškeré další filtrování informace a klikněte na tlačítko **zkontrolujte**.

Po kliknutí na tlačítko **zkontrolujte**, se kontroluje toku na základě kritérií, které jste zadali. Výsledkem je buď **povoleného přístupu** nebo **byl odepřen přístup**. Pokud byl odepřen přístup, je k dispozici skupina zabezpečení sítě (NSG) a zabezpečení pravidlo, které blokování provozu. Pokud odmítnutí provoz je očekávané chování, pravidlo bylo úspěšné.

> [!NOTE]
> Tok IP ověření vyžaduje, aby prostředků virtuálního počítače je přidělená.

Jak je vidět na následujícím obrázku, byla povolena odchozí provoz HTTPS.

![tok IP ověřte – přehled][1]

Jak je vidět na následujícím obrázku, provoz se změní na příchozí a příchozí port změnit tak, aby 123. Provoz je nyní odepřen, zprávy poskytnuté společně s pravidlo zabezpečení sítě skupiny a zabezpečení, odepřít provoz "Přístup byl odepřen".

![výsledky toku IP][2]

## <a name="next-steps"></a>Další postup

Pokud je blokován provoz a neměl by být, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou definovány.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













