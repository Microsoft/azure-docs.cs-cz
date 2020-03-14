---
title: Logika zpracování pravidel Azure Firewall
description: Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264775"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall
Pravidla překladu adres (NAT), pravidla sítě a aplikace můžete nakonfigurovat na Azure Firewall. Pravidla se zpracovávají podle typu pravidla. 

> [!NOTE]
> Pokud povolíte filtrování na základě logiky hrozeb, tato pravidla mají nejvyšší prioritu a jsou vždy zpracována jako první. Filtrování logiky hrozeb může před zpracováním všech nakonfigurovaných pravidel odepřít provoz. Další informace najdete v tématu [Azure firewall filtrování na základě logiky hrozeb](threat-intel.md).

## <a name="outbound"></a>Odchozí

### <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla pro aplikace

Pokud konfigurujete Síťová pravidla a pravidla pro aplikace, budou se Síťová pravidla aplikována v pořadí podle priority před pravidly aplikací. Pravidla se ukončí. Takže pokud se shoda najde v síťovém pravidle, nezpracovávají se žádná další pravidla.  Pokud se neshoduje žádné pravidlo sítě, a pokud je protokol HTTP, HTTPS nebo MSSQL, pak se paket vyhodnotí podle pravidel aplikace v pořadí podle priority. Pokud se pořád nenajde žádná shoda, vyhodnotí se paket na základě [kolekce pravidel infrastruktury](infrastructure-fqdns.md). Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="inbound"></a>Příchozí

### <a name="nat-rules"></a>Pravidla překladu adres (NAT)

Příchozí připojení k Internetu můžete povolit konfigurací překladu cílové sítě (DNAT), jak je popsáno v [kurzu: filtrování příchozího provozu pomocí Azure firewall DNAT pomocí Azure Portal](tutorial-firewall-dnat.md). Pravidla překladu adres (NAT) se aplikují přednostně před pravidly sítě. Pokud se najde shoda, přidá se implicitní odpovídající síťové pravidlo, které povolí přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu.

Pro příchozí připojení se nepoužijí pravidla aplikací. Takže pokud chcete filtrovat příchozí přenosy HTTP/S, měli byste použít Firewall webových aplikací (WAF). Další informace najdete v tématu [co je firewall webových aplikací Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Příklady

Následující příklady znázorňují výsledky některých z těchto kombinací pravidel.

### <a name="example-1"></a>Příklad 1

Připojení k google.com je povolené z důvodu odpovídajícího síťového pravidla.

**Síťové pravidlo**

- Akce: povolení


|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolení – Web     |TCP|IP adresa|*|IP adresa|*|80,443

**Pravidlo aplikace**

- Akce: Odepřít

|jméno  |Typ zdroje  |Zdroj  |Protokol: port|Cílové plně kvalifikované názvy domén|
|---------|---------|---------|---------|----------|----------|
|Odepřít – Google     |IP adresa|*|http: 80, https: 443|google.com

**Vyústit**

Připojení k google.com je povoleno, protože paket se shoduje s pravidlem " *Povolení-webové* sítě". Zpracování pravidla se v tomto okamžiku zastaví.

### <a name="example-2"></a>Příklad 2

Přenosy SSH se zamítly, protože kolekce *síťových pravidel* s vyšší prioritou je blokuje.

**Kolekce pravidel sítě 1**

- Název: Allow-Collection
- Priorita: 200
- Akce: povolení

|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolení – SSH     |TCP|IP adresa|*|IP adresa|*|22

**Kolekce pravidel sítě 2**

- Název: Deny-Collection
- Priorita: 100
- Akce: Odepřít

|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Odepřít – SSH     |TCP|IP adresa|*|IP adresa|*|22

**Vyústit**

Připojení SSH jsou odepřena, protože je zablokovala kolekce pravidel sítě s vyšší prioritou. Zpracování pravidla se v tomto okamžiku zastaví.

## <a name="rule-changes"></a>Změny pravidla

Pokud změníte pravidlo na odepřít dříve povolený provoz, všechny relevantní existující relace budou zrušeny.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).