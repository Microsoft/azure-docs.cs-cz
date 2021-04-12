---
title: Úvod do ověřování toků IP adres v Azure Network Watcher | Microsoft Docs
description: Tato stránka poskytuje přehled možností ověření toku Network Watcher protokolu IP.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: d6f6fe81c121f547f79fa34be77aab1fb6c0875a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899350"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Úvod do ověřování toků IP adres v Azure Network Watcher

Ověření toku protokolu IP kontroluje, jestli je paket povolený nebo zakázaný z virtuálního počítače. Informace se skládají ze směru, protokolu, místní IP adresy, vzdáleného protokolu IP, místního portu a vzdáleného portu. Pokud je paket zakázán skupinou zabezpečení, je vrácen název pravidla, které paket zamítlo. I když se dá zvolit kterákoli zdrojová nebo cílová IP adresa, ověřování toku IP pomáhá správcům rychle diagnostikovat problémy s připojením z Internetu a z místního prostředí nebo do něj.

Ověření toku protokolu IP prohlíží pravidla pro všechny skupiny zabezpečení sítě (skupin zabezpečení sítě) použité pro síťové rozhraní, jako je například podsíť nebo síťová karta virtuálního počítače. Tok přenosů se pak ověří na základě nakonfigurovaného nastavení do nebo z tohoto síťového rozhraní. Ověření toku protokolu IP je užitečné při potvrzení, jestli pravidlo ve skupině zabezpečení sítě blokuje příchozí nebo odchozí provoz do nebo z virtuálního počítače.

Instance Network Watcher musí být vytvořená ve všech oblastech, ve kterých plánujete spustit ověřování toku IP adres. Network Watcher je místní služba a může být spuštěná jenom pro prostředky ve stejné oblasti. Použitá instance nemá vliv na výsledky ověřování toků IP, protože se pořád vrací jakákoli trasa přidružená k síťovému rozhraní nebo podsíti.

![1][1]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jestli je paket povolený nebo zakázaný pro konkrétní virtuální počítač prostřednictvím portálu, najdete v následujícím článku. [Zkontrolujte, jestli je povolený provoz na virtuálním počítači s ověřením toku IP pomocí portálu.](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

