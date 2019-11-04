---
title: Logika zpracování pravidel Azure Firewall
description: Další informace o logice zpracování pravidel Azure Firewall
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518200"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall

Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.

## <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla pro aplikace

Nejdřív se aplikují Síťová pravidla a pak pravidla aplikace. Pravidla se ukončí. Takže pokud se v síťových pravidlech najde shoda, pravidla aplikací se nezpracují.  Pokud se nenajde shoda s žádným pravidlem sítě a protokol paketu je HTTP nebo HTTPS, paket se vyhodnotí podle pravidel aplikace. Pokud se pořád nenajde žádná shoda, vyhodnotí se paket na základě kolekce pravidel infrastruktury. Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="nat-rules"></a>Pravidla NAT

Příchozí připojení je možné povolit konfigurací překladu cílové sítě (DNAT), jak je popsáno v [kurzu: filtrování příchozího provozu pomocí Azure firewall DNAT pomocí Azure Portal](../firewall/tutorial-firewall-dnat.md). Jako první se aplikují DNAT pravidla. Pokud se najde shoda, přidá se implicitní odpovídající síťové pravidlo, které povolí přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Pro tato připojení nejsou použita žádná pravidla použití aplikace.

## <a name="inherited-rules"></a>Zděděná pravidla

Kolekce pravidel sítě zděděné z nadřazené zásady mají vždycky přednost před kolekcemi pravidel sítě, které jsou definované jako součást vašich nových zásad. Stejná logika platí také pro kolekce pravidel aplikace. Kolekce pravidel sítě se ale vždycky zpracovávají před kolekcemi pravidel aplikace bez ohledu na dědičnost.

Ve výchozím nastavení vaše zásada zdědí režim analýzy hrozeb v nadřazené zásadě. To můžete přepsat nastavením režimu analýzy hrozeb na jinou hodnotu na stránce nastavení zásad. Lze přepsat pouze přísnější hodnotou. Například pokud máte nadřazenou zásadu nastavenou na *výstrahu*, můžete tuto místní zásadu nakonfigurovat na možnost *výstrahy a odepřít*, ale nemůžete ji vypnout.

## <a name="next-steps"></a>Další kroky

- [Další informace o nástroji Azure Firewall Manager Preview](overview.md)