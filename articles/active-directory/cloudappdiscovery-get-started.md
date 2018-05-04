---
title: Nastavení služby Cloud App Discovery v Azure Active Directory | Microsoft Docs
description: Najít a spravovat aplikace s Cloud App Discovery poskytnout možné použít informace o použití cloudu a stínové IT.
services: active-directory
keywords: cloud app discovery,. Správa aplikací
documentationcenter: ''
author: curtand
manager: mtillman
tags: ignite
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/25/2018
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: a4ba0e384c17845a1298d283a947a4e04396d6da
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2018
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Nastavení ve službě Azure AD Cloud App Discovery

Cloud App Discovery ve službě Azure AD je nyní založená na integraci s daty, které jsou k dispozici z Microsoft Cloud App Security. Poskytnout průběžné informace o použití cloudu a stínové IT, porovná Cloud App Discovery protokolů přenosů na Cloud App Security katalog více než 15 000 cloudových aplikací. Tento článek popisuje postup nastavení a obsahuje odkazy na podrobné informace o jednotlivých kroků. Také popisuje informace o brány firewall a proxy server a protokolu podpora souborů.

## <a name="prerequisites"></a>Požadavky

* Vaše organizace musí mít licenci Azure AD Premium P1 pro používání tohoto produktu. Další informace najdete v tématu [ceny služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Pokud chcete nastavit Cloud App Discovery, musí být globální správce nebo čtečku zabezpečení v Azure Active Directory.

## <a name="setup-steps"></a>Postup instalace

1. [Nastavit snímek sestavy](cloudappdiscovery-set-up-snapshots.md) Zkontrolujte formát zajistěte, aby vaše protokoly poskytují využitelné informace k Cloud App Discovery. Mohou také poskytnout přehled ad-hoc protokolů přenosů, který nahrajete ručně z brány firewall a proxy servery.

2. [Nastavit průběžné reporting](https://docs.microsoft.com/cloud-app-security/discovery-docker) k analýze všechny protokoly, které jsou předávány z vaší sítě pomocí kolektoru protokolů Cloud App Security. Můžete je používat k identifikaci nových aplikací a trendů využití.

3. Pokud vaše protokoly nejsou aktuálně podporovány, [nastavit vlastní protokol analyzátor](https://docs.microsoft.com/cloud-app-security/custom-log-parser) tak, aby Cloud App Discovery můžete analyzovat.
  
## <a name="log-processing-flow"></a>Zpracování toku protokolu

Ho může trvat několik minut několik hodin ke generování sestav v závislosti na množství dat. Zde je, co se analyzují:

* **Nahrát** protokoly webových přenosů z vaší sítě se odešlou na portál.
* **Analyzovat** Cloud App Security rozloží a extrahuje údaje o provozu z protokolů přenosů pomocí vyhrazeného analyzátoru pro každý datový zdroj.
* **Analýza** přenosy dat se analyzují na základě katalogu Cloud App Security k identifikaci víc než 15 000 cloudových aplikací. V rámci analýzy se také identifikují aktivní uživatelé a IP adresy.
* **Generování sestav** Cloud App Security generuje sestavy týkající se dat extrahovaných ze souborů protokolů a je k dispozici Cloud App Discovery.

> [!NOTE]
> * Průběžné sestavy data se analyzují dvakrát denně.
> * Kolektor protokolů komprimaci dat, před odesláním. Odchozí přenosy na kolektoru protokolů je přibližně 10 % velikosti protokolů přijatých přenosů.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Použití protokolů přenosů pro Cloud App Discovery

Cloud App Discovery používá data v protokolů přenosů. Další podrobnosti můžete přidat do protokolu, lepší viditelnost, které máte. Cloud App Discovery vyžaduje webové přenosy dat s následujícími atributy:

* Datum transakce
* Zdrojová IP adresa
* Zdrojový uživatel **doporučená**
* Cílová IP adresa
* Cílová adresa URL **doporučená** (adresy URL poskytují další přesnost pro zjišťování cloudových aplikací přesnější než IP adresy)
* Celkové množství dat
* Množství nahrál nebo stažených dat pro přehled o způsobu použití cloudové aplikace
* Akce (povolené nebo blokované)

Cloud App Discovery. nelze zobrazit nebo analyzovat atributy, které nejsou zahrnuté do protokolů. Například **brány Firewall Cisco ASA** formát standardního protokolu neobsahuje **nahrané počet bajtů v jedné transakci**, **uživatelské jméno**, nebo **cílová adresa URL**  , ale pouze cílovou IP adresu. Proto může mít menší přehled cloudových aplikací z tohoto zdroje. Pro brány firewall Cisco ASA nastavte úroveň informace na 6.1.

Aby bylo možné úspěšně vygenerovat sestavu Cloud App Discovery, protokolů přenosů musí splňovat následující podmínky:

1.  Zdroj dat je [podporované brány firewall nebo proxy server](#supported-firewalls-and-proxies).
2.  Formát protokolu odpovídá Očekávaný standardní formát. Je toto políčko zaškrtnuto během nahrávání. K optimalizaci formát protokolu uour, najdete v části [vytvořit snímek sestavy Cloud App Discovery](cloudappdiscovery-set-up-snapshots.md).
3.  Události nejsou starší než 90 dní.
4.  Soubor protokolu je platný a obsahuje informace odchozí provoz.

## <a name="supported-firewalls-and-proxy-servers"></a>Podporovaných bran firewall a proxy servery

* Barracuda - brány Firewall webových aplikací (W3C)
* Blue Coat Proxy SG – protokol přístupu (W3C)
* Check Point
* Cisco ASA FirePOWER
* Brány Firewall Cisco ASA (Cisco ASA brány firewall, nastavte úroveň informace na 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – adresy URL protokolu
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Digitální přístroje i filtru
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee zabezpečené webové brány
* Microsoft Forefront Threat Management Gateway (W3C)
* Řada Palo Alto brány Firewall
* Sophos SG
* Sophos Cyberoam
* Olihně (společný)
* Olihně (nativní)
* Websense – Web Security Solutions – sestava vyšetřování podrobností (CSV)
* Websense – Web Security Solutions – protokol internetové aktivity (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery podporuje adresy IPv4 a IPv6.

Pokud váš protokol není podporovaný, vyberte **jiných** jako **zdroj dat** a zadejte zařízení a protokolu, které se pokoušíte nahrát. Protokol je zkontrolovat tým analytiků cloudu Cloud App Security. Pokud podpora pro váš typ protokolu se přidá, pošleme vám oznámení, ale místo toho můžete definovat vlastní analyzátor, který odpovídá vaší formát protokolu. Další informace najdete v tématu [pomocí vlastního protokolu analyzátoru](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Atributy datového (podle dokumentaci od dodavatele)

| Zdroj dat         | Adresa URL cílové aplikace | Cílové aplikace IP adresu | Uživatelské jméno | Původní IP adresy | Celkový přenos | Odeslané bajty |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Ano**        | **Ano**       | **Ano**  | **Ano**   | Ne            | Ne             |
| Blue Coat                               | **Ano**        | Ne            | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Kontrolní bod                              | Ne             | **Ano**       | Ne       | **Ano**   | Ne            | Ne             |
| Cisco ASA                               | Ne             | **Ano**       | Ne       | **Ano**   | **Ano**       | Ne             |
| Cisco FWSM                              | Ne             | **Ano**       | Ne       | **Ano**   | **Ano**       | Ne             |
| Cisco Ironport WSA                      | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Cisco Meraki                            | **Ano**        | **Ano**       | Ne       | **Ano**   | Ne            | Ne             |
| Clavister NGFW (Syslog)                 | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Dell SonicWall                          | **Ano**        | **Ano**       | Ne       | **Ano**   | **Ano**       | **Ano**        |
| FortiGate                               | Ne             | **Ano**       | Ne       | **Ano**   | **Ano**       | **Ano**        |
| Juniper SRX                             | Ne             | **Ano**       | Ne       | **Ano**   | **Ano**       | **Ano**        |
| Juniper SSG                             | Ne             | **Ano**       | Ne       | **Ano**   | **Ano**       | **Ano**        |
| McAfee SWG                              | **Ano**        | Ne            | Ne       | **Ano**   | **Ano**       | **Ano**        |
| MS TMG                                  | **Ano**        | Ne            | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Palo Alto Networks                      | Ne             | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Sophos                                  | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | Ne             |
| Olihně (společný)                          | **Ano**        | Ne            | **Ano**  | **Ano**   | Ne            | **Ano**        |
| Olihně (nativní)                          | **Ano**        | Ne            | **Ano**  | **Ano**   | Ne            | **Ano**        |
| Websense – investigativní sestavy (CSV)   | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Websense – protokol internetové aktivity (CEF)  | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |
| Zscaler                                 | **Ano**        | **Ano**       | **Ano**  | **Ano**   | **Ano**       | **Ano**        |


## <a name="next-steps"></a>Další postup
Chcete-li pokračovat nastavit Cloud App Discovery ve službě Azure AD pomocí následujících odkazů.

* [Vytvoření sestav snímků](cloudappdiscovery-set-up-snapshots.md)
* [Konfigurace průběžného vytváření sestav](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Použití vlastního analyzátoru protokolů](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
