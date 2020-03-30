---
title: Logika zpracování pravidel Azure Firewall
description: Informace o logice zpracování pravidel brány Azure Firewall
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518200"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall

Azure Firewall má pravidla NAT, pravidla sítě a pravidla aplikací. Pravidla jsou zpracována podle typu pravidla.

## <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla aplikací

Nejprve se použijí pravidla sítě a poté pravidla aplikací. Pravidla se ukončují. Pokud je tedy shoda nalezena v síťových pravidlech, nebudou pravidla aplikace zpracována.  Pokud se nenajde shoda s žádným pravidlem sítě a protokol paketu je HTTP nebo HTTPS, paket se vyhodnotí podle pravidel aplikace. Pokud stále není nalezena žádná shoda, je paket vyhodnocen proti kolekci pravidel infrastruktury. Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="nat-rules"></a>Pravidla NAT

Příchozí připojení lze povolit konfigurací překladu cílové síťové adresy (DNAT), jak je popsáno v [kurzu: Filtrování příchozích přenosů pomocí DNAT brány Azure firewall pomocí portálu Azure](../firewall/tutorial-firewall-dnat.md). Nejprve se použijí pravidla DNAT. Pokud je nalezena shoda, je přidáno implicitní odpovídající síťové pravidlo umožňující přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Pro tato připojení nejsou použita žádná pravidla aplikace.

## <a name="inherited-rules"></a>Zděděná pravidla

Kolekce síťových pravidel zděděné z nadřazené zásady jsou vždy upřednostněny nad kolekcemi pravidel sítě, které jsou definovány jako součást nové zásady. Stejná logika platí také pro kolekce pravidel aplikace. Kolekce síťových pravidel jsou však vždy zpracovány před kolekcemi pravidel aplikace bez ohledu na dědičnost.

Ve výchozím nastavení vaše zásada dědí je to nadřazený režim analýzy hrozeb zásad. Tuto možnost můžete přepsat nastavením režimu inteligence hrozeb na jinou hodnotu na stránce nastavení zásad. Je možné přepsat pouze s přísnější hodnotou. Pokud je například nadřazená zásada nastavena pouze na *možnost Výstrahy*, můžete tuto místní zásadu nakonfigurovat tak, aby *byla upozornění a odepřena*, ale nelze ji vypnout.

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure Firewall Manager Preview](overview.md)