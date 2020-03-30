---
title: Azure VMware Solution by CloudSimple – tabulky brány firewall
description: Přečtěte si o tabulkách brány firewall privátního cloudu Cloud a pravidlech brány firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025040"
---
# <a name="firewall-tables-overview"></a>Přehled tabulek brány firewall

Tabulka brány firewall obsahuje pravidla pro filtrování síťového provozu do a z prostředků privátního cloudu. Tabulky brány firewall můžete použít na síť VLAN/podsíť. Pravidla řídí síťový provoz mezi zdrojovou sítí nebo adresou IP a cílovou sítí nebo adresou IP.

## <a name="firewall-rules"></a>Pravidla brány firewall

Následující tabulka popisuje parametry v pravidle brány firewall.

| Vlastnost | Podrobnosti |
| ---------| --------|
| **Název** | Název, který jednoznačně identifikuje pravidlo brány firewall a jeho účel. |
| **Priorita** | Číslo mezi 100 a 4096, přičemž 100 je nejvyšší prioritou. Pravidla jsou zpracována v pořadí podle priority. Když se provoz setká s odpovídající pravidlem, zpracování pravidel se zastaví. V důsledku toho nejsou zpracována pravidla s nižšími prioritami, která mají stejné atributy jako pravidla s vyššími prioritami.  Dávejte pozor, abyste se vyhnuli protichůdným pravidlům. |
| **Sledování stavu** | Sledování může být bezstavové (Privátní cloud, Internet nebo VPN) nebo stavové (Veřejná IP).  |
| **Protokol** | Mezi možnosti patří libovolné, TCP nebo UDP. Pokud požadujete ICMP, použijte Libovolný. |
| **Směru** | Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz. |
| **Akce** | Povolit nebo odepřít pro typ provozu definovaného v pravidle. |
| **Zdroj** | Beztřídní blok směrování mezi doménami (CIDR) (například 10.0.0.0/24) nebo Any.  Zadání rozsahu, značky servisu nebo skupiny zabezpečení aplikace umožňuje vytvořit méně pravidel zabezpečení. |
| **Zdrojový port** | Port, ze kterého pochází síťový provoz.  Můžete určit jednotlivé porty nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. |
| **Cíl** | Beztřídní blok směrování mezi doménami (CIDR) (například 10.0.0.0/24) nebo Any.  Zadání rozsahu, značky servisu nebo skupiny zabezpečení aplikace umožňuje vytvořit méně pravidel zabezpečení.  |
| **Cílový port** | Port, do kterého proudí síťový provoz.  Můžete určit jednotlivé porty nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení.|

### <a name="stateless"></a>Bezstavová

Pravidlo bez stavů vyhledá pouze jednotlivé pakety a filtruje je na základě pravidla.  
Pro dopravní tok v opačném směru mohou být vyžadována další pravidla.  Použijte bezstavová pravidla pro provoz mezi následujícími body:

* Podsítě privátních cloudů
* Místní podsíť a podsíť privátního cloudu
* Internetový provoz ze soukromých cloudů

### <a name="stateful"></a>Stavové

 Stavové pravidlo si je vědoma připojení, která procházejí. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku.  Tento typ pravidla použijte pro veřejné adresy IP k filtrování provozu z Internetu.

### <a name="default-rules"></a>Výchozí pravidla

Následující výchozí pravidla jsou vytvořena v každé tabulce brány firewall.

|Priorita|Name (Název)|Sledování stavu|Směr|Typ provozu|Protocol (Protokol)|Zdroj|Zdrojový port|Cíl|Cílový port|Akce|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|povolení k internetu|Stavové|Odchozí|Veřejná IP adresa nebo internetový provoz|Všechny|Všechny|Všechny|Všechny|Všechny|Povolit|
|65001|odepřít-all-from-internet|Stavové|Příchozí|Veřejná IP adresa nebo internetový provoz|Všechny|Všechny|Všechny|Všechny|Všechny|Odepřít|
|65002|povolit vše na intranet|Bezstavová|Odchozí|Interní provoz privátního cloudu nebo sítě VPN|Všechny|Všechny|Všechny|Všechny|Všechny|Povolit|
|65003|povolit vše z intranetu|Bezstavová|Příchozí|Interní provoz privátního cloudu nebo sítě VPN|Všechny|Všechny|Všechny|Všechny|Všechny|Povolit|

## <a name="next-steps"></a>Další kroky

* [Nastavení pravidel a tabulek bran firewall](firewall.md)
