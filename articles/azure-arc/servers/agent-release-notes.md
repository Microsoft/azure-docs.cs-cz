---
title: Co je nového u agenta s povolenými servery Azure ARC (Preview)
description: V tomto článku najdete poznámky k verzi pro agenta serverů s podporou Azure ARC (Preview). Pro mnohé z souhrnných problémů existují odkazy na další podrobnosti.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236638"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Co je nového u agenta s povolenými servery Azure ARC (Preview)

Server s podporou Azure ARC (ve verzi Preview) přijímá nepřetržité vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb

## <a name="august-2020"></a>Srpen 2020

Verze: 0,11

- Podpora pro Ubuntu 20,04.

- Vylepšení spolehlivosti pro nasazení rozšíření.

### <a name="known-issues"></a>Známé problémy

Pokud používáte starší verzi agenta pro Linux a nakonfigurovali jste ho, aby používal proxy server, musíte po upgradu překonfigurovat nastavení proxy server. Pokud to chcete provést, spusťte příkaz `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným ARC (ve verzi Preview) na více hybridních počítačích si přečtěte téma [Přehled agenta připojení počítačů](agent-overview.md) , abyste pochopili požadavky, technické podrobnosti o agentovi a metody nasazení.