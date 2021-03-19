---
title: Řešení Azure VMware podle CloudSimple – tabulky brány firewall
description: Přečtěte si o CloudSimple tabulkách firewallu privátního cloudu a pravidlech brány firewall, včetně výchozích pravidel, která se vytvářejí v každé tabulce brány firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140866"
---
# <a name="firewall-tables-overview"></a>Přehled tabulek brány firewall

Tabulka brány firewall obsahuje seznam pravidel pro filtrování síťového provozu do a z prostředků privátního cloudu. Můžete použít tabulky brány firewall na síť VLAN nebo podsíť. Pravidla řídí síťový provoz mezi zdrojovou sítí nebo IP adresou a cílovou sítí nebo IP adresou.

## <a name="firewall-rules"></a>Pravidla brány firewall

Následující tabulka popisuje parametry v pravidle brány firewall.

| Vlastnost | Podrobnosti |
| ---------| --------|
| **Název** | Název, který jedinečně identifikuje pravidlo brány firewall a jeho účel. |
| **Priorita** | Číslo mezi 100 a 4096 a 100 je nejvyšší prioritou. Pravidla se zpracovávají v pořadí podle priority. Když provoz narazí na pravidlo shodu, zpracování pravidla se zastaví. V důsledku toho nejsou zpracovávána pravidla s nižšími prioritami, která mají stejné atributy jako pravidla s vyššími prioritami.  Dbejte na to, abyste se vyhnuli konfliktům pravidel. |
| **Sledování stavu** | Sledování může být bezstavové (privátní cloud, Internet nebo VPN) nebo stavová (veřejná IP adresa).  |
| **Protokol** | Mezi možnosti patří všechny, TCP nebo UDP. Pokud budete vyžadovat protokol ICMP, použijte libovolný. |
| **Směr** | Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz. |
| **Akce** | Povolí nebo zakáže typ provozu definovaný v pravidle. |
| **Zdroj** | IP adresa, blok směrování mezi doménami bez tříd (CIDR) (10.0.0.0/24, například) nebo Any.  Určení rozsahu, značky služby nebo skupiny zabezpečení aplikací vám umožní vytvořit méně pravidel zabezpečení. |
| **Zdrojový port** | Port, ze kterého pochází síťový provoz.  Můžete zadat jednotlivý port nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. |
| **Cíl** | IP adresa, blok směrování mezi doménami bez tříd (CIDR) (10.0.0.0/24, například) nebo Any.  Určení rozsahu, značky služby nebo skupiny zabezpečení aplikací vám umožní vytvořit méně pravidel zabezpečení.  |
| **Cílový port** | Port, na který se budou toky provozu v síti.  Můžete zadat jednotlivý port nebo rozsah portů, například 443 nebo 8000-8080. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení.|

### <a name="stateless"></a>Bezstavová

Bezstavové pravidlo vyhledává pouze jednotlivé pakety a filtruje je na základě pravidla.  
Pro tok přenosů v opačném směru můžou být nutná další pravidla.  Pro přenosy dat mezi následujícími body používejte pravidla bez stavu:

* Podsítě privátních cloudů
* Místní podsíť a podsíť privátního cloudu
* Internetový provoz z privátních cloudů

### <a name="stateful"></a>Stavové

 Stavové pravidlo ví o připojeních, která je předána. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku.  Tento typ pravidla použijte pro veřejné IP adresy k filtrování provozu z Internetu.

### <a name="default-rules"></a>Výchozí pravidla

V každé tabulce brány firewall jsou vytvořena následující výchozí pravidla.

|Priorita|Název|Sledování stavu|Směr|Typ provozu|Protokol|Zdroj|Zdrojový port|Cíl|Cílový port|Akce|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|povolení – vše pro Internet|Stavové|Odchozí|Veřejná IP adresa nebo internetový provoz|Vše|Všechny|Všechny|Všechny|Všechny|Povolit|
|65001|Odepřít – vše z Internetu|Stavové|Příchozí|Veřejná IP adresa nebo internetový provoz|Vše|Všechny|Všechny|Všechny|Všechny|Odepřít|
|65002|povolení – vše pro intranet|Bezstavová|Odchozí|Provoz interního cloudu nebo privátního cloudu|Vše|Všechny|Všechny|Všechny|Všechny|Povolit|
|65003|povolení – vše z intranetu|Bezstavová|Příchozí|Provoz interního cloudu nebo privátního cloudu|Vše|Všechny|Všechny|Všechny|Všechny|Povolit|

## <a name="next-steps"></a>Další kroky

* [Nastavení pravidel a tabulek bran firewall](firewall.md)
