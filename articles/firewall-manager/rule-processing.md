---
title: Logika zpracování pravidel Azure Firewall
description: Další informace o logice zpracování pravidel Azure Firewall
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 70026173d1cb932d30a59ea2b876ef22217a81bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563679"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall

Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.

## <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla pro aplikace

Nejdřív se aplikují Síťová pravidla a pak pravidla aplikace. Pravidla se ukončí. Pokud se tedy v síťových pravidlech najde shoda, pravidla aplikace se nezpracují.  Pokud se neshoduje žádné pravidlo sítě a protokol paketů je HTTP/HTTPS, paket se pak vyhodnotí podle pravidel aplikace. Pokud se pořád nenajde žádná shoda, vyhodnotí se paket na základě kolekce pravidel infrastruktury. Pokud se ještě neshodují, pak je ve výchozím nastavení povolený paket.

## <a name="nat-rules"></a>Pravidla překladu adres (NAT)

Příchozí připojení je možné povolit konfigurací překladu cílové sítě (DNAT), jak je popsáno v [kurzu: filtrování příchozího provozu pomocí Azure firewall DNAT pomocí Azure Portal](../firewall/tutorial-firewall-dnat.md). Jako první se aplikují DNAT pravidla. Pokud se najde shoda, přidá se implicitní odpovídající síťové pravidlo, které povolí přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Pro tato připojení nejsou použita žádná pravidla použití aplikace.

## <a name="inherited-rules"></a>Zděděná pravidla

Kolekce pravidel sítě zděděné z nadřazené zásady mají vždycky přednost před kolekcemi pravidel sítě, které jsou definované jako součást vašich nových zásad. Stejná logika platí také pro kolekce pravidel aplikace. Kolekce pravidel sítě se ale vždycky zpracovávají před kolekcemi pravidel aplikace bez ohledu na dědičnost.

Ve výchozím nastavení zdědí vaše zásady svůj nadřazený režim analýzy hrozeb. To můžete přepsat nastavením režimu analýzy hrozeb na jinou hodnotu na stránce nastavení zásad. Je možné ji přepsat pouze přísnější hodnotou. Například pokud máte nadřazenou zásadu nastavenou na *výstrahu*, můžete tuto místní zásadu nakonfigurovat na možnost *výstrahy a odepřít*, ale nemůžete ji vypnout.

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure Firewall Manageru](overview.md)