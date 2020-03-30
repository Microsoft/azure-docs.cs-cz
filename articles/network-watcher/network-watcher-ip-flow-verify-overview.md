---
title: Úvod k ověření toku IP v Azure Network Watcher | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje přehled funkce ověření toku IP sledování sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845002"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Úvod k ověření toku IP v Azure Network Watcher

Ověření toku IP ověřuje, zda je paket povolen nebo odepřen virtuálnímu počítači nebo z ní. Informace se skládají ze směru, protokolu, místní IP adresy, vzdálené IP adresy, místního portu a vzdáleného portu. Pokud je paket skupinou zabezpečení odmítnut, je vrácen název pravidla, které paket odepřel. Zatímco lze zvolit libovolnou zdrojovou nebo cílovou IP adresu IP, ověření toku IP pomáhá správcům rychle diagnostikovat problémy s připojením z nebo k internetu a z nebo do místního prostředí.

Ověření toku IP se zabývá pravidly pro všechny skupiny zabezpečení sítě (NSG) použitými pro síťové rozhraní, jako je například podsíť nebo síťová síť virtuálního počítače. Tok provozu je pak ověřen na základě nakonfigurovaného nastavení do nebo z tohoto síťového rozhraní. Ověření toku IP je užitečné při potvrzování, pokud pravidlo ve skupině zabezpečení sítě blokuje příchozí nebo odchozí provoz do nebo z virtuálního počítače.

Instance sledovacího programu sítě je třeba vytvořit ve všech oblastech, které plánujete spustit ověření toku IP. Sledování sítě je místní služba a lze je schovat pouze proti prostředkům ve stejné oblasti. Použitá instance nemá vliv na výsledky ověření toku IP, protože všechny trasy přidružené k síťové křenici nebo podsíti jsou stále vráceny.

![1][1]

## <a name="next-steps"></a>Další kroky

Navštivte následující článek se dozvíte, pokud je paket povolen nebo odepřen pro konkrétní virtuální počítač prostřednictvím portálu. [Zkontrolujte, jestli je povolen provoz na virtuálním počítači s IP flow Ověřit pomocí portálu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

