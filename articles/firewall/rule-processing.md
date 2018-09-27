---
title: Logika zpracování Azure pravidlo brány Firewall
description: Další informace o zpracování logiky pravidla Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228300"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování Azure pravidlo brány Firewall
Brány Firewall Azure má pravidla NAT, pravidel sítě a pravidla aplikací. Pravidla se zpracovávají podle typ pravidla.


## <a name="network-rules-and-applications-rules"></a>Sítě a pravidel aplikace 
Pravidla sítě jsou použitá pravidla první a aplikace. Pravidla se ukončuje. Takže pokud se najde shoda v pravidlech sítě, nebudou zpracovány pravidla aplikací.  Pokud neexistuje žádná shoda pravidla sítě a že paketů se používá protokol HTTP/HTTPS, paket se pak vyhodnocuje podle pravidel aplikace. Je-li stále nalezena žádná shoda nenajde, paket vyhodnocení proti kolekci pravidel infrastruktury. Pokud ještě není nalezena žádná shoda, je ve výchozím nastavení zakázaný paketu.

## <a name="nat-rules"></a>Pravidla NAT
Příchozí připojení se dá nastavit pomocí konfigurace cílové síťové adresy překladu (DNAT), jak je popsáno v [kurz: filtrování příchozího provozu s DNAT brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-dnat.md). Nejdříve jsou použita pravidla DNAT. Pokud se najde shoda, je přidáno implicitní odpovídající pravidlo sítě přeložené provoz. Toto chování můžete přepsat tak, že explicitně přidáte kolekci pravidel sítě pravidlům odepřít, které odpovídají přeložené provoz. Žádná pravidla aplikací se použijí pro tato připojení.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nasazení a konfiguraci brány Firewall Azure](tutorial-firewall-deploy-portal.md).