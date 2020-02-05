---
title: Řešení Azure VMware (AVS) – tabulky brány firewall
description: Přečtěte si informace o tabulkách firewallu privátního cloudu a pravidlech brány firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025040"
---
# <a name="firewall-tables-overview"></a>Přehled tabulek brány firewall

Tabulka brány firewall obsahuje seznam pravidel pro filtrování síťového provozu do a z prostředků privátního cloudu služby AVS. Můžete použít tabulky brány firewall na síť VLAN nebo podsíť. Pravidla řídí síťový provoz mezi zdrojovou sítí nebo IP adresou a cílovou sítí nebo IP adresou.

## <a name="firewall-rules"></a>Pravidla brány firewall

Následující tabulka popisuje parametry v pravidle brány firewall.

| Vlastnost | Podrobnosti |
| ---------| --------|
| **Název** | Název, který jedinečně identifikuje pravidlo brány firewall a jeho účel. |
| **Priorita** | Číslo mezi 100 a 4096 a 100 je nejvyšší prioritou. Pravidla se zpracovávají v pořadí podle priority. Když provoz narazí na pravidlo shodu, zpracování pravidla se zastaví. V důsledku toho nejsou zpracovávána pravidla s nižšími prioritami, která mají stejné atributy jako pravidla s vyššími prioritami. Dbejte na to, abyste se vyhnuli konfliktům pravidel. |
| **Sledování stavu** | Sledování může být bezstavové (funkce AVS privátní cloud, Internet nebo VPN) nebo stavová (veřejná IP adresa).  |
| **Protokol** | Mezi možnosti patří všechny, TCP nebo UDP. Pokud budete vyžadovat protokol ICMP, použijte libovolný. |
| **Směr** | Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz. |
| **Akce** | Povolí nebo zakáže typ provozu definovaný v pravidle. |
| **Zdroj** | IP adresa, blok směrování mezi doménami bez tříd (CIDR) (10.0.0.0/24, například) nebo jakákoli IP adresa.  Určení rozsahu, značky služby nebo skupiny zabezpečení aplikací vám umožní vytvořit méně pravidel zabezpečení. |
| **Zdrojový port** | Port, ze kterého pochází síťový provoz.  Můžete zadat jednotlivý port nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. |
| **Tabulka** | IP adresa, blok směrování mezi doménami bez tříd (CIDR) (10.0.0.0/24, například) nebo jakákoli IP adresa.  Určení rozsahu, značky služby nebo skupiny zabezpečení aplikací vám umožní vytvořit méně pravidel zabezpečení.  |
| **Cílový port** | Port, na který se budou toky provozu v síti.  Můžete zadat jednotlivý port nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení.|

### <a name="stateless"></a>Bezstavová

Bezstavové pravidlo vyhledává pouze jednotlivé pakety a filtruje je na základě pravidla. Pro tok přenosů v opačném směru můžou být nutná další pravidla. Pro přenosy dat mezi následujícími body používejte pravidla bez stavu:

* Podsítě privátních cloudů pro funkci AVS
* Místní podsíť a podsíť privátního cloudu služby AVS
* Internetový provoz z privátních cloudů služby AVS

### <a name="stateful"></a>Uzlů

 Stavové pravidlo ví o připojeních, která je předána. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku. Tento typ pravidla použijte pro veřejné IP adresy k filtrování provozu z Internetu.

### <a name="default-rules"></a>Výchozí pravidla

V každé tabulce brány firewall jsou vytvořena následující výchozí pravidla.

|Priorita|Name (Název)|Sledování stavu|Směr|Typ provozu|Protocol (Protokol)|Zdroj|Zdrojový port|Cíl|Cílový port|Akce|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Uzlů|Odchozí|Veřejná IP adresa nebo internetový provoz|Všechno|Všechny|Všechny|Všechny|Všechny|Povolit|
|65001|Odepřít – vše z Internetu|Uzlů|Příchozí|Veřejná IP adresa nebo internetový provoz|Všechno|Všechny|Všechny|Všechny|Všechny|Zamítnout|
|65002|allow-all-to-intranet|Bezstavová|Odchozí|Příchozí přenos dat privátního cloudu VYUŽÍVAJÍCÍm funkci AVS|Všechno|Všechny|Všechny|Všechny|Všechny|Povolit|
|65003|allow-all-from-intranet|Bezstavová|Příchozí|Příchozí přenos dat privátního cloudu VYUŽÍVAJÍCÍm funkci AVS|Všechno|Všechny|Všechny|Všechny|Všechny|Povolit|

## <a name="next-steps"></a>Další kroky

* [Nastavení tabulek a pravidel brány firewall](firewall.md)
