---
title: Tabulky Azure – řešení VMware podle CloudSimple – brány firewall
description: Další informace o tabulky CloudSimple privátního cloudu brány firewall a pravidla brány firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577342"
---
# <a name="firewall-tables-overview"></a>Přehled brány firewall tabulky

Brána firewall tabulka uvádí pravidla pro filtrování síťového provozu do a z prostředky privátního cloudu. Můžete je použít na síti VLAN a podsítě. Pravidla pak řídit provoz sítě mezi zdrojovou síť nebo IP adresu a cílové síti nebo IP adresu.

## <a name="firewall-rules"></a>Pravidla brány firewall

Následující tabulka popisuje parametry v pravidlu brány firewall.

| Vlastnost | Podrobnosti |
| ---------| --------|
| **Název** | Název, který jednoznačně identifikuje pravidlo brány firewall a její účel. |
| **Priorita** | Číslo rozsahu od 100 do 4096 se 100, přičemž nejvyšší prioritou. Pravidla se zpracovávají v pořadí podle priority. Když provoz přichází napříč pravidlo shody, zastaví zpracování pravidla. V důsledku toho nebudou zpracovány žádné existující pravidla s nižší prioritou, které mají stejné atributy jako pravidla s vyšší prioritou.  Snažte se vyhnout konfliktní pravidla. |
| **Stav sledování** | Sledování může být bezstavové (privátní Cloud, Internet nebo síť VPN) nebo stavová (veřejná IP adresa).  |
| **Protokol** | Mezi možnosti patří všechny, TCP nebo UDP. Pokud potřebujete protokol ICMP, použijte některý. |
| **Směr** | Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz. |
| **Akce** | Povolí nebo zakáže pro typ provozu definovaným v pravidle. |
| **Zdroj** | IP adresu, classless Inter-Domain routing (CIDR) bloku (například 10.0.0.0/24) nebo žádný.  Zadání rozsahu, značky služby nebo skupiny zabezpečení aplikace umožňuje vytvářet méně pravidel zabezpečení. |
| **Zdrojový Port** | Port, ze které síti provoz pochází.  Můžete zadat jednotlivé port nebo rozsah portů, jako je například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. |
| **cíl** | IP adresu, classless Inter-Domain routing (CIDR) bloku (například 10.0.0.0/24) nebo žádný.  Zadání rozsahu, značky služby nebo skupiny zabezpečení aplikace umožňuje vytvářet méně pravidel zabezpečení.  |
| **Cílový Port** | Port, na který síťový provoz toky.  Můžete zadat jednotlivé port nebo rozsah portů, jako je například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení.|

### <a name="stateless"></a>Bezstavová

Bezstavová pravidla zabývá pouze jednotlivé pakety a filtruje je podle pravidla.  
Další pravidla mohou být vyžadovány pro tok přenosů, v opačném směru.  Použijte Bezstavová pravidla pro provoz mezi následující body:

* Podsítě privátních Cloudů
* Místní podsítě a podsíť privátního cloudu
* Internetový provoz z privátních Cloudů

### <a name="stateful"></a>Stavová

 Stavové pravidlo je seznámen připojení, která prochází ho. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku.  Toto pravidlo se používá pro veřejné IP adresy k filtrování provozu z Internetu.

### <a name="default-rules"></a>Výchozí pravidla

Následující výchozí pravidla se vytvoří v každé tabulce brány firewall.

|Priorita|Name|Stav sledování|Direction|Typ přenosu|Protocol|source|Zdrojový port|Cíl|Cílový port|Akce|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stavová|Odchozí|Veřejné IP adresy nebo internet provozu|Vše|Jakýkoli|Jakýkoli|Jakýkoli|Jakýkoli|Povolit|
|65001|deny-all-from-internet|Stavová|Příchozí|Veřejné IP adresy nebo internet provozu|Vše|Jakýkoli|Jakýkoli|Jakýkoli|Jakýkoli|Odepřít|
|65002|allow-all-to-intranet|Bezstavová|Odchozí|Interní privátní Cloud nebo přenosy VPN|Vše|Jakýkoli|Jakýkoli|Jakýkoli|Jakýkoli|Povolit|
|65003|allow-all-from-intranet|Bezstavová|Příchozí|Interní privátní Cloud nebo přenosy VPN|Vše|Jakýkoli|Jakýkoli|Jakýkoli|Jakýkoli|Povolit|

## <a name="next-steps"></a>Další postup

* [Nastavení brány firewall na tabulky a pravidel](https://docs.azure.cloudsimple.com/firewall/)