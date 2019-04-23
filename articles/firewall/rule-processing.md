---
title: Logika zpracování Azure pravidlo brány Firewall
description: Další informace o zpracování logiky pravidla Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193641"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování Azure pravidlo brány Firewall
Brány Firewall Azure má pravidla NAT, pravidel sítě a pravidla aplikací. Pravidla se zpracovávají podle typ pravidla.


## <a name="network-rules-and-applications-rules"></a>Sítě a pravidel aplikace 
Pravidla sítě jsou použitá pravidla první a aplikace. Pravidla se ukončuje. Takže pokud se najde shoda v pravidlech sítě, nebudou zpracovány pravidla aplikací.  Pokud se nenajde shoda s žádným pravidlem sítě a protokol paketu je HTTP nebo HTTPS, paket se vyhodnotí podle pravidel aplikace. Je-li stále nalezena žádná shoda nenajde, paket vyhodnocení proti kolekci pravidel infrastruktury. Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="nat-rules"></a>Pravidla NAT
Příchozí připojení se dá nastavit pomocí konfigurace cílové síťové adresy překladu (DNAT), jak je popsáno v [kurzu: Filtrovat příchozí provoz s DNAT brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-dnat.md). Nejdříve jsou použita pravidla DNAT. Pokud se najde shoda, je přidáno implicitní odpovídající pravidlo sítě přeložené provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Žádná pravidla aplikací se použijí pro tato připojení.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nasazení a konfiguraci brány Firewall Azure](tutorial-firewall-deploy-portal.md).